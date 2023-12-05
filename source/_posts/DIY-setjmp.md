---
title: DIY setjmp
tags: [目录,汇编,c语言]
copyright: true
toc: true
reward: false
date: 2023-07-27 20:59:16
---

这个玩意是我在做蒋岩炎老师的操作系统课的M2实验时，实现协程库的过程中所需要的一部分知识。为了更充分地深入地理解，我就参考大佬博客DIY~~抄袭~~了一个

<!--more-->

参考文章：[彻底理解setjmp/longjmp并DIY一个简单的协程](https://blog.csdn.net/dog250/article/details/89742140)

## setjmp介绍

c语言有库函数`setjmp()`与`longjmp()`可以达到跨函数跳转的效果，其实它的原理就是`setjmp()`保存了各种寄存器状态,后续调用`longjmp()`来恢复寄存器来实现函数切换，像这样

```c
#include <setjmp.h>
#include <stdio.h>

jmp_buf bufa, bufb;
void funb() {
    int ret = 0;
    ret = setjmp(bufb);
    if (ret != 2)
    {
        printf("b1\n");
        longjmp(bufa, 1);
    }
    printf("b2\n");
    longjmp(bufa, 1);
}

void funa() {
    int ret = 1;
    printf("a1\n");
    ret = setjmp(bufa);

    if (ret == 0) funb();
    printf("a2\n");

    ret = setjmp(bufa);
    if (ret == 0) longjmp(bufb, 2);
}

int main(void) {
    funa();
    return 0;
}
```

输出结果

```shell
a1
b1
a2
b2
```

其实原理说起来简单，它的实现细节却很是繁杂，我理了好久，得理解[[汇编层面的函数调用]]

## DIY版本的实现-save and restore

### 汇编版本

```asmatmel
.global save, restore

#the param passed in is in %rdi 
save:
    #last rsp rsp+8 调用save函数的瞬间的rsp的值，因为call指令push了一个返回地址，rsp-8，所以我们+8即可取得原始值
    #这个值作为以后重新调用这个协程的rsp的值
    leaq 8(%rsp), %rdx
    movq %rdx, (0)(%rdi)
    #last rbp 直接把rbp存下
    movq %rbp, (8)(%rdi)
    #last rip 上次进栈的就是rip, 我们直接从(rsp)上就可以拿到`rip`
    movq (%rsp), %rdx
    movq %rdx, (16)(%rdi)
    #last r12
    movq %r12, (24)(%rdi)
    #last r13
    movq %r12, (32)(%rdi)
    #last r14
    movq %r14, (40)(%rdi)
    #last r15
    movq %r15, (48)(%rdi)

    #return 0
    movq $0, %rax
    retq

restore:
    movq (0)(%rdi), %rsp
    movq (8)(%rdi), %rbp
    movq (16)(%rdi), %rdx
    movq %rdx, (%rsp)
    movq (24)(%rdi), %r12
    movq (32)(%rdi), %r13
    movq (40)(%rdi), %r14
    movq (48)(%rdi), %r15

    #return 1
    movq $1, %rax
    retq
```

写成c语言函数定义应该就是

```c
long save(struct context *cxt);
long restore(struct context *cxt);
//固定返回值是1
```

### c语言内嵌汇编版本

就是将上面的汇编改写一份为内嵌汇编版本的，虽然原理相同，还是有很多细节的不同
c语言内嵌汇编实现的函数，编译器会一开始就对`%rbp`进行push，最后进行pop,如下图所示
![Pasted image 20230727150531](https://cdn.staticaly.com/gh/xiaomo-xty/picturehost@main/img/23/07/27/Pasted%20image%2020230727150531.png)

![Pasted image 20230727150603](https://cdn.staticaly.com/gh/xiaomo-xty/picturehost@main/img/23/07/27/Pasted%20image%2020230727150603.png)

那么这样就需要对`save`函数保存寄存器的代码进行一些修改，对`store`进行恢复时也要进行一些修改

#### `save`函数，寄存器保存

因为有

```asmatmel
pushq %rbp
mov %rsp, %rbp
```

这两句，导致现在的我们需要的`%rsp`的值是原来的`%rsp-16`得到的(两次push), 而`%rbp`就存在`%rsp`的位置，因为它是刚刚`push`进去的，其他的没变，所以我们调整储存`%rsp`和`%rbp`的代码为

```asmatmel
leaq 16(%%rsp), %%rdx; \
movq %%rdx, (0)(%0);   \
movq (%%rsp), %%rdx;  \
movq %%rdx, (8)(%0);   \
```

#### restore函数，寄存器恢复

![Pasted image 20230727145350](https://cdn.staticaly.com/gh/xiaomo-xty/picturehost@main/img/23/07/27/Pasted%20image%2020230727145350.png)
需要注意，因为多了一次`push %rbp`和一次`pop %rbp`，我们希望最后`%rbp` `%rip` 和`%rsp`的值是我们期望的值，我在中间加了`push %%rdx`(里面装的`%rip`的值),还有`push %%rbp`来平衡两次`pop`，以及让`pop %rbq`后`%rbq`里面是我想要的值

事实上，一开始就让`%%rsp`的值-8,然后`movq %rdx, (%rsp)`也是相同的效果

```asmatmel
    movq (0)(%0), %%rsp        ;\
    movq (8)(%0), %%rbp        ;\
    movq (16)(%0), %%rdx        ;\
    push %%rdx                  ;\
    movq (24)(%0), %%r12        ;\
    movq (32)(%0), %%r13        ;\
    movq (40)(%0), %%r14        ;\
    movq (48)(%0), %%r15        ;\
    push %%rbp                     ;\
```

### 测试

```c
/* test_saverestore.c */
/* gcc -c test_saverestore.c -o test_saverestore.o */
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
struct Context {
    unsigned long rsp;
    unsigned long rbp;
    unsigned long rip;
    unsigned long r12;
    unsigned long r13;
    unsigned long r14;
    unsigned long r15;
};


int save(struct Context *cxt) {
    asm volatile
    ("  \
        leaq 16(%%rsp), %%rdx; \
        movq %%rdx, (0)(%0);   \
        movq (%%rsp), %%rdx;  \
        movq %%rdx, (8)(%0);   \
        movq 8(%%rsp), %%rdx;   \
        movq %%rdx, (16)(%0);  \
        movq %%r12, (24)(%0);  \
        movq %%r12, (32)(%0);  \
        movq %%r14, (40)(%0);  \
        movq %%r15, (48)(%0);  \
        movq $0, %%rax;        "\
        ::"D"(cxt)
        :"memory");
    return 0;
}
struct Context ctx;

int restore(struct Context *cxt) {
    //push %rbp
    asm volatile
    (
        "\
        movq (0)(%0), %%rsp        ;\
        movq (8)(%0), %%rbp        ;\
        movq (16)(%0), %%rdx        ;\
        push %%rdx                  ;\
        movq (24)(%0), %%r12        ;\
        movq (32)(%0), %%r13        ;\
        movq (40)(%0), %%r14        ;\
        movq (48)(%0), %%r15        ;\
        push %%rbp                     ;\
        "\

        ::"D"(cxt)
        :"memory");
    return 1;
}

#define HEX(x) (x>>32 & 0xFFFFFFFF),(x & 0xFFFFFFFF)

void print_ctx() {
    printf("rsp : 0x%x%x\n", HEX(ctx.rsp));
    printf("rbp : 0x%x%x\n", HEX(ctx.rbp));
    printf("rip : 0x%x%x\n", HEX(ctx.rip));
    printf("r12 : 0x%x%x\n", HEX(ctx.r12));
    printf("r13 : 0x%x%x\n", HEX(ctx.r13));
    printf("r14 : 0x%x%x\n", HEX(ctx.r14));
    printf("r15 : 0x%x%x\n", HEX(ctx.r15));
}

int main()
{
    int ret; 
    ret =save(&ctx);
    print_ctx();
    if (ret == 0) {
        printf("from setjmp\n");
        restore(&ctx);
    } else {
        printf("from longjmp\n");
    }
}
```

输出：

```shell
rsp : 0x7fffffffd770
rbp : 0x7fffffffd780
rip : 0x55555555533d
r12 : 0x7fffffffd898
r13 : 0x7fffffffd898
r14 : 0x555555557db8
r15 : 0x7ffff7ffd040
from setjmp
rsp : 0x7fffffffd770
rbp : 0x7fffffffd780
rip : 0x55555555533d
r12 : 0x7fffffffd898
r13 : 0x7fffffffd898
r14 : 0x555555557db8
r15 : 0x7ffff7ffd040
from longjmp
```

***perfect！*** 也许吧
