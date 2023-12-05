---
title: 最小动态加载器-minimal_loader
tags: [目录,c语言,操作系统]
copyright: true
toc: true
reward: true
date: 2023-09-06 16:53:15

---

一个最小的动态加载器

<!--more-->

### loader.c

```c
//Genered by GPT-3.5

#include <stdio.h>
#include <stdlib.h>
#include <sys/mman.h>

int main(int argc, char *argv[]) {
    if (argc != 2) {
        fprintf(stderr, "Usage: %s <binary_file>\n", argv[0]);
        exit(EXIT_FAILURE);
    }

    const char *binary_file = argv[1];

    // 打开二进制文件
    FILE *file = fopen(binary_file, "rb");
    if (!file) {
        perror("fopen");
        exit(EXIT_FAILURE);
    }

    // 获取文件大小
    fseek(file, 0, SEEK_END);
    size_t file_size = ftell(file);
    rewind(file);

    // 使用 mmap 将文件映射到内存
    void *binary_data = mmap(NULL, file_size, PROT_READ | PROT_EXEC, MAP_PRIVATE, fileno(file), 0);
    if (binary_data == MAP_FAILED) {
        perror("mmap");
        exit(EXIT_FAILURE);
    }

    // 关闭文件，不再需要它
    fclose(file);

    // 执行映射的二进制文件
    void (*entry_point)() = (void (*)())binary_data;
    entry_point();

    // 解除映射
    if (munmap(binary_data, file_size) == -1) {
        perror("munmap");
        exit(EXIT_FAILURE);
    }

    return 0;
}
```

这段加载器代码由chatgpt生成，简单地加载一个二进制文件到内存，然后将该文件开头作为函数入口，直接调用函数

### mininal.S

```nasm
#include <sys/syscall.h>
.globl _start
_start:
    movq $SYS_write, %rax
    movq $1, %rdi
    lea st(%rip), %rsi
    movq $(ed - st), %rdx
    syscall

    movq $SYS_exit, %rax
    movq $1, %rdi
    syscall

st:
    .ascii "Hello, OS World"
ed:
```

这是标准输出的系统调用，效果是在标准输出中打印"Hello, OS World"，是示例程序

### Makefile

```makefile
all: loader minimal

loader: loader.c
    gcc -ggdb $< -o $@

minimal: minimal.S
    gcc -S minimal.S > minimal.s
    as minimal.s -o minimal.o
    ld minimal.o -o $@
    objcopy -O binary --only-section=.text minimal minimal.bin
clean:
    rm -f loader minimal *.o *.s *.bin
```

`gcc -S`利用预处理器展开`minimal.S`中的宏，将结果输出到`minimal.s`，再使用as编译，ld链接

对于`objcopy -O binary --only-section=.text minimal minimal.bin`,chatgpt的解释是：

> 这条命令使用了 `objcopy` 工具将目标文件 `minimal` 中名为 `.text` 的节（section）提取出来，并将其转换为一个纯二进制文件 `minimal.bin`。
> 
> 让我们解释一下命令的各个部分：
> 
> - `objcopy`：这是 `objcopy` 工具的命令。
> 
> - `-O binary`：这部分选项 `-O` 指定了输出格式为二进制。这告诉 `objcopy` 将输入文件转换为纯二进制格式。
> 
> - `--only-section=.text`：这是一个额外的选项，它告诉 `objcopy` 只提取名为 `.text` 的节（通常包含程序的代码）。
> 
> - `minimal`：这是输入文件的名称，它包含了 `.text` 节以及可能的其他节和数据。
> 
> - `minimal.bin`：这是输出文件的名称。`objcopy` 将从输入文件中提取 `.text` 节的内容，并将其保存为一个纯二进制文件，文件名为 `minimal.bin`。
> 
> 综合起来，这个命令的目的是从 `minimal` 文件中提取程序代码（通常位于 `.text` 节中），并将其保存为一个独立的纯二进制文件 `minimal.bin`。这种操作可以在嵌入式系统中非常有用，例如将程序代码加载到微控制器的存储器中。、

## 执行效果

![image.png](https://cdn.staticaly.com/gh/xiaomo-xty/picturehost@main/img/23/09/06/202309061819405.png)
