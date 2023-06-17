---
title: c++智能指针
tags: [目录,c++]
copyright: true
toc: true
reward: false
date: 2023-06-15 15:18:20
---

## unique_ptr

### 作用

- 在任何时刻，只能有一个指针管理内存
- 当指针超出作用域，内存将自动释放
- 该指针不可Copy，只能Move

<!--more-->

### 创建

- 通过raw pointer 
- 通过`new`
- 通过`std::make_uniqu` （推荐）

### get()

获取地址

### 引用操作

实现以下两个操作符
"`->`"可以调用成员函数
"`*`"可以调用`derefering()`

### 例子

以下是对`Cat`类的完整声明和实现

#### cat.h

```cpp
#pragma once
#include <string>
#include <iostream>

class Cat {
public:
    Cat(std::string name);
    Cat() = default;
    ~Cat();
    void meow() const;

private:
    std::string name;
};
```

#### cat.cpp

```cpp
#include "cat.h"
#include <string>
#include <iostream>

Cat::Cat(std::string name) : name(name){}

void Cat::meow() const{
    std::cout << name + ": Meow~" << std::endl;
}

Cat::~Cat() {
    std::cout << name + ": was destoryed~" << std::endl;
}
```

由cat.h和cat.cpp实现了Cat的一个参数为string的构造方法，和一个打印提示信息的析构方法

#### unique_ptr.cpp

```cpp
#include <iostream>
#include "cat.h"

int main(int argc, char* args[]) {
    Cat *c_ptr1 = new Cat("Garfiled");
    Cat tom = Cat("Tom");
    return 0;
}
```

输出：

```shell
Tom: was destoryed~
```

可以看到，只有Tom的析构方法被调用了，Garfiled的析构方法被无视了。这里，就导致了内存泄漏，Garfiled的那片内存在整个程序结束前，它将不能再被调用而又无法被释放

正确的做法应当是手动进行`delete`

```cpp
delete c_ptr1;
```

在return语句的前加上这一句，现在的输出变成了

```shell
Garfiled: was destoryed~
Tom: was destoryed~
```

这样就没有问题了。
所以在项目规模比较小，逻辑比较简明时，几十几百行的代码认真细致一点，手动管理内存倒是也能够保证程序的正确稳定运行。但是人出错的概率可能很小，但绝对不为0

是不是只要new与delete成对地写，就可以避免所有问题了？当然不是，且看下面

```cpp
int main(int argc, char* args[]) {
    Cat *c_ptr1 = new Cat("Garfiled");
    Cat tom = Cat("Tom");
    //假装此处还有很多代码
    {
        c_ptr1 = new Cat("Garfiled2");
        delete c_ptr1;
    }
    //假装此处还有很多代码
    delete c_ptr1;
    return 0;
}
```

我承认阁下肉眼管理很强，但我若是犯蠢，来这么一段，阁下又该如何应对
`c_ptr1`被释放了两次，这会使得程序直接异常结束

c++的智能指针，牺牲一定的自由，来大大减少编码的错误，节省精力

#### std::make_unique()

```cpp
int main(int argc, char* args[]) {
    std::unique_ptr<Cat> Tom = std::make_unique<Cat>("Tom");
    Tom->meow();
    return 0;
}
```

输出:

```shell
Tom: Meow~
Tom: was destoryed~
```

我们并没有对指针进行显式释放，可它还是自己调用了析构方法，它真的，我哭死

我们用unique_ptr改写下那个二次释放的程序

```cpp
int main(int argc, char* args[]) {
    std::unique_ptr<Cat> Tom = std::make_unique<Cat>("Tom");
    {
        Tom = std::make_unique<Cat>("Carfiled");
    }
    Tom->meow();
    std::cout << "Tom, are you exist?" << std::endl;
    return 0;
}
```

```shell
Tom: was destoryed~
Carfiled: Meow~
Tom, are you exist?
Carfiled: was destoryed~
```

可以看到，当我们给一个unique_ptr赋新值时，旧值的内存会被直接释放

#### 初始化与赋值

`unique_ptr`的副本构造器与赋值操作函数是被定义为`delete`的,所以`unique_ptr`无法拷贝
不说人话就是智能指针只能进行右值拷贝，unique_ptr本身也可以作为右值被赋给shared_ptr，并且是move语义，此后所有权归shared_ptr，unique_ptr指针被清除

#### release()

释放，调用后智能指针和其所指向对象的联系再无联系，但是该内存仍然存在有效。它会返回裸指针，但是该智能指针被置空。  
返回的裸指针我们可以手工delete来释放，也可以用来初始化另外一个智能指针，或者给另外一个智能指针赋值。

#### 陷阱

```cpp
#include <iostream>
#include "cat.h"
#include <memory>

int main(int argc, char* args[]) {
    std::unique_ptr<Cat> Tom = std::make_unique<Cat>("Tom");
    std::unique_ptr<Cat> Garfiled = static_cast<std::unique_ptr<Cat>&&>(Tom);
    try {
        Tom->meow();
    }
    catch (std::exception e) {
        std::cout << e.what() << std::endl;
    }
    Garfiled->meow();
    return 0;
}
```

使用右值引用强行将Tom赋值给Garfiled，当执行Tom->meow时程序会直接崩溃，没有报错和异常

推测应该是禁用了拷贝构造和拷贝赋值，但实现了移动构造和移动赋值,见微软[learn.microsoft.com](https://learn.microsoft.com/zh-cn/cpp/cpp/move-constructors-and-move-assignment-operators-cpp?view=msvc-170)
