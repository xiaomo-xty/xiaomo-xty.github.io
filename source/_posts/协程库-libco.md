---
title: 协程库-libco
tags: [目录,协程,c语言,操作系统]
copyright: true
toc: true
reward: false
date: 2023-07-30 21:02:43
---

这是蒋岩炎老师在2022春季操作系统课中给出的一个课程实验，以下算是我的实验笔记

<!--more-->

[实验手册](https://jyywiki.cn/OS/2022/labs/M2)

## 实验要求

实现协程库 `co.h` 中定义的 API：

`struct co *co_start(const char *name, void (*func)(void *), void *arg); void       co_yield(); void       co_wait(struct co *co);`

协程库的使用和线程库非常类似：

1. `co_start(name, func, arg)` 创建一个新的协程，并返回一个指向 `struct co` 的指针 (类似于 `pthread_create`)。
   - 新创建的协程从函数 `func` 开始执行，并传入参数 `arg`。新创建的协程不会立即执行，而是调用 `co_start` 的协程继续执行。
   - 使用协程的应用程序不需要知道 `struct co` 的具体定义，因此请把这个定义留在 `co.c` 中；框架代码中并没有限定 `struct co` 结构体的设计，所以你可以自由发挥。
   - `co_start` 返回的 `struct co` 指针需要分配内存。我们推荐使用 `malloc()` 分配。
2. `co_wait(co)` 表示当前协程需要等待，直到 `co` 协程的执行完成才能继续执行 (类似于 `pthread_join`)。
   - 在被等待的协程结束后、 `co_wait()` 返回前，`co_start` 分配的 `struct co` 需要被释放。如果你使用 `malloc()`，使用 `free()` 释放即可。
   - 因此，每个协程只能被 `co_wait` 一次 (使用协程库的程序应当保证除了初始协程外，其他协程都必须被 `co_wait` 恰好一次，否则会造成内存泄漏)。
3. `co_yield()` 实现协程的切换。协程运行后一直在 CPU 上执行，直到 `func` 函数返回或调用 `co_yield` 使当前运行的协程暂时放弃执行。`co_yield` 时若系统中有多个可运行的协程时 (包括当前协程)，你应当随机选择下一个系统中可运行的协程。
4. `main` 函数的执行也是一个协程，因此可以在 `main` 中调用 `co_yield` 或 `co_wait`。`main` 函数返回后，无论有多少协程，进程都将直接终止。

## 实验思路

实现**协程切换**需要将协程的寄存器状态和栈空间都备份下来，就像按下了暂停键，保留所有状态，下次恢复时完好如初

### 备份寄存器

首先应该思考如何保存寄存器状态
setjmp与longjmp函数好像就很可以

所以有这两个函数，很兴奋啊，应该能行~吗？

### 备份栈空间

一开始我想着直接复制栈空间，后面再覆盖回去，好蠢！细思一下，我tm一开始就直接让函数的栈空间使用我指定的就好了嘛

开干！怎么干？中间经过了很长时间的僵持，后来发现`jmp_buf`是被加密过的(详见[[FS寄存器]])，我没办法直接修改它存储的`%rsp`寄存器

自己用汇编写一个~~公平公开公正~~的`setjmp`,上代码 [DIY setjmp | 汤圆的小屋](https://littlesun.cloud/2023/07/27/DIY-setjmp/)
需要对函数栈帧有一些理解->[汇编层面的函数调用 | 汤圆的小屋](https://littlesun.cloud/2023/07/27/%E6%B1%87%E7%BC%96%E5%B1%82%E9%9D%A2%E7%9A%84%E5%87%BD%E6%95%B0%E8%B0%83%E7%94%A8/)

对于我自定义的东西(~~其实是抄的~~)，我当然可以修改它里面的值，然后就可以直接指定栈空间啦
栈空间向下生长，所以我们的首地址应该下调

#### stack_with_call

参考实验手册的这段话

> 分配堆栈是容易的：堆栈直接嵌入在 `struct co` 中即可，在 `co_start` 时初始化即可。但麻烦的是如何让 `co_start` 创建的协程，切换到指定的堆栈执行。AbstractMachine 的实现中有一个精巧的 `stack_switch_call` (`x86.h`)，可以用于切换堆栈后并执行函数调用，且能传递一个参数，请大家完成阅读理解 (对完成实验有巨大帮助)：

```c
>static inline void stack_switch_call(void *sp, void *entry, uintptr_t arg) {   asm volatile ( #if __x86_64__     "movq %0, %%rsp; movq %2, %%rdi; jmp *%1"       : : "b"((uintptr_t)sp), "d"(entry), "a"(arg) : "memory" #else     "movl %0, %%esp; movl %2, 4(%0); jmp *%1"       : : "b"((uintptr_t)sp - 8), "d"(entry), "a"(arg) : "memory" #endif   ); }
```

> 理解上述函数你需要的文档：[GCC-Inline-Assembly-HOWTO](http://www.ibiblio.org/gferg/ldp/GCC-Inline-Assembly-HOWTO.html)。当然，这个文档有些过时，如果还有不明白的地方，gcc 的官方手册是最佳的阅读材料。

以及文章[知乎专栏-libco](https://zhuanlan.zhihu.com/p/490475991)
嗯，就是直接搬的这篇文章的，中间自己尝试过仿照思路写出来了64位版本，但考虑到还要兼容32位，我对x86汇编真的不太熟，就直接用轮子了，嘿嘿，偷个小懒

```c
static __attribute__((always_inline)) inline void stack_switch_call(void *sp, void *entry, void *arg)
{
    asm volatile(
#if __x86_64__
        "movq %%rsp, -0x10(%0); leaq -0x10(%0), %%rsp; movq %2, %%rdi; call *%1"
        :
        : "b"((uintptr_t)sp), "d"((uintptr_t)entry), "a"((uintptr_t)arg)
#else
        "movl %%esp, -0x4(%0); leal -0x8(%0), %%esp; movl %2, -0x8(%0); call *%1"
        :
        : "b"((uintptr_t)sp), "d"((uintptr_t)entry), "a"((uintptr_t)arg)
#endif
    );
}


static __attribute__((always_inline)) inline void stack_switch_ret(void *sp)
{
    asm volatile(
#if __x86_64__
        "movq -0x10(%0), %%rsp;"
        :
        : "b"((uintptr_t)sp)
#else
        "movl -0x4(%0), %%esp"
        :
        : "b"((uintptr_t)sp)
#endif
    );
}
```

### bug

stack_switch_call函数一直不能正确返回

可以看到，在刚调用该函数时的`sp`寄存器值是`0x7fffffffd98e`(这里没显示，但就是这么多)，`(%rsp)`也就是`*0x7fffffffd98e`是`0x55555699`,对应`stack_switch_call`后面一条语句
![image.png](https://cdn.staticaly.com/gh/xiaomo-xty/picturehost@main/img/23/07/29/202307290954198.png)
(call之后的返回地址)

![image.png](https://cdn.staticaly.com/gh/xiaomo-xty/picturehost@main/img/23/07/29/202307290958379.png)
(返回地址对应语句)

### 定位

一路追踪发现，实在后面`cur_co`为`main`时，调用`setjmp`，该返回值被覆盖了，导致`stack_switch_call`无法正常返回

如下图，此时`%rsp`寄存器值为`0x7fffffffd98e`,`*0x7fffffffd98e`是`0x55555782`
![image.png](https://cdn.staticaly.com/gh/xiaomo-xty/picturehost@main/img/23/07/29/202307291002234.png)

![image.png](https://cdn.staticaly.com/gh/xiaomo-xty/picturehost@main/img/23/07/29/202307290951048.png)

在后面更细致的追踪中，发现不仅是调用`setjmp`,只要是调用函数就会修改该返回值，所以就是`longjmp`后`%rsp`被恢复到调用`stack_switch_call`之前的状态又调用了其他函数导致返回值改变

我甚至跑过去问别人博主，~~当然别人没有吊我 ~~
然后很郁闷啊，调了半天心里乱了，跑去打了几把游戏。心静下来之后，woc，我tm用`__attribute(always_inline)`强制内联，不就可以避免函数调用的压栈了吗，事实证明，是这样子的

然后，单协程测试成功！

## 单文件代码

因为实验手册里提到说不要将代码上传，所以我不直接给出那些实现细节，只给出别人已经公布过的代码细节

```c
#include "co.h"
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <assert.h>
#include <stdint.h>
#include <setjmp.h>

struct co *cur_co = NULL;

static __attribute__((always_inline)) inline void stack_switch_call(void *sp, void *entry, void *arg)
{
    asm volatile(
#if __x86_64__
        "movq %%rsp, -0x10(%0); leaq -0x10(%0), %%rsp; movq %2, %%rdi; call *%1"
        :
        : "b"((uintptr_t)sp), "d"((uintptr_t)entry), "a"((uintptr_t)arg)
#else
        "movl %%esp, -0x4(%0); leal -0x8(%0), %%esp; movl %2, -0x8(%0); call *%1"
        :
        : "b"((uintptr_t)sp), "d"((uintptr_t)entry), "a"((uintptr_t)arg)
#endif
    );
}


static __attribute__((always_inline)) inline void stack_switch_ret(void *sp)
{
    asm volatile(
#if __x86_64__
        "movq -0x10(%0), %%rsp;"
        :
        : "b"((uintptr_t)sp)
#else
        "movl -0x4(%0), %%esp"
        :
        : "b"((uintptr_t)sp)
#endif
    );
}
//main function is also a co
struct co_pool{
    struct co* cos;
    int size;
} co_pool;

void push_pool(struct co *co) {
//具体实现隐藏
}

struct co* co_fetch_by_name(const char* name) {
//具体实现隐藏
}

int pos = 0;
struct co* co_next() {
    if (cur_co != co_pool.cos || co_pool.size == 1)
        return co_pool.cos;
    struct co *t_co = co_pool.cos->next;

    for (int i = 0;i < pos; ++i)
        t_co = t_co->next;
    pos = (pos+1) % (co_pool.size-1);
    return t_co;
}


struct co *co_start(const char *name, const void (*func)(void *), void *arg) {
    struct co * new_co = (struct co*)malloc(sizeof(struct co));
    assert(new_co);

    new_co->arg = arg;
    new_co->func = func;
    new_co->name = name; 
    new_co->status = CO_NEW;
    new_co->waiter = NULL;

    //add to co pool
    push_pool(new_co);
    co_print_all();

    printf("\n");
    return new_co;
}

void co_print_all() {
    struct co * t_co = co_pool.cos;
    for (int i = 0; i < co_pool.size; ++i) {
        printf("%s\n", t_co->name);
        t_co = t_co->next;
    }
}


void co_yield() {
    assert(cur_co);
    assert(cur_co->status == CO_RUNNING);

    int isFirst = !setjmp(cur_co->context);

    if (isFirst) {
        struct co *t_co = co_next();

        if (t_co && t_co->status == CO_NEW) {
            //backup current co information, prepare for switch next co
            char curr_name[1024];
            memset(curr_name, 0, sizeof(curr_name));
            strcpy(curr_name, cur_co->name);
            cur_co->status = CO_WAITING; 

            //switch
            cur_co = t_co;

            ((struct co volatile *)cur_co)->status = CO_RUNNING;
            stack_switch_call(cur_co->stack+STACK_SIZE, cur_co->func, cur_co->arg);
            stack_switch_ret(cur_co->stack+STACK_SIZE);
            ((struct co volatile *)cur_co)->status = CO_DEAD;

            cur_co = co_fetch_by_name(curr_name);
            assert(cur_co->status == CO_WAITING);

            // current co continue
            ((struct co volatile *)cur_co)->status = CO_RUNNING;

        }
        else if (t_co && t_co->status == CO_WAITING) {
            cur_co->status = CO_WAITING;
            cur_co = t_co;

            ((struct co volatile *)cur_co)->status = CO_RUNNING;
            longjmp(t_co->context, 1);
        }
    }
    else {
        //ensure the co is running, than return and continue
        assert(cur_co->status == CO_RUNNING);
    }
}

void co_free(struct co* co) {
//具体实现隐藏
}

void co_wait(struct co *co) {
    assert(cur_co->status == CO_RUNNING);
    struct co *t_co;

    t_co = co_fetch_by_name(co->name);
    t_co->waiter = cur_co;

    while (t_co->status != CO_DEAD)
        co_yield();

    co_free(co);
}




// void __attribute__((constructor)) init() {
//     cur_co = co_start("main", main, NULL);
//     cur_co->status = CO_RUNNING;
// }

int count = 1;
void entry(void *arg) {
  for (int i = 0; i < 5; i++) {
    printf("%s[%d] ", (const char *)arg, count++);
    co_yield();
  }
}

int main() {
    cur_co = co_start("main", main, NULL);
    cur_co->status = CO_RUNNING;


    struct co *co1 = co_start("co1", entry, "a");
    struct co *co2 = co_start("co2", entry, "b");
    struct co *co3 = co_start("co3", entry, "c");

    co_wait(co1);
    co_wait(co2);
    co_wait(co3);
    printf("Done\n");
}
```

## 协程调度

是的，单协程测试通过了，但是呢，两个协程就又出问题了，然后又是一通打断点，看变量，发现是在`co1`设置为`CO_DEAD`并且已经被释放后还被我的`fetch_by_name`给取出来，取了个寂寞。

可以发现是因为`co2`首次是被协程`co1`切换出来的，所以`co2`执行完后会试图切回早已经释放了的`co1`,这是我的协程调度的问题，我的调度就是没有调度，我的协程池就是一个链表，将各个协程串起来，调度就是按链表`next`,所以会出现试图切出已经死亡的协程这种情况

我怎么解决？我tm直接强制每次其他协程yield后切回`main`协程，因为`main`协程不可能在任何其他协程之前`DEAD`,很蠢，很低效，但是很正确。~~至少能跑了~~，因为是自己捣鼓下原理，也没有更高的追求了，暂时到此为止

## 运行结果

测试代码：

```c
#include <stdio.h>
#include "co.h"
int count = 1;
void entry(void *arg) {
  for (int i = 0; i < 5; i++) {
    printf("%s[%d] ", (const char *)arg, count++);
    co_yield();
  }
}

int main() {
    cur_co = co_start("main", main, NULL);
    cur_co->status = CO_RUNNING;


    struct co *co1 = co_start("co1", entry, "a");
    struct co *co2 = co_start("co2", entry, "b");
    struct co *co3 = co_start("co3", entry, "c");

    co_wait(co1);
    co_wait(co2);
    co_wait(co3);
    printf("Done\n");
}
```

输出：

```shell
a[1] b[2] c[3] a[4] b[5] c[6] a[7] b[8] c[9] a[10] b[11] c[12] a[13] b[14] c[15] Done
```
