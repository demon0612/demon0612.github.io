---
layout: post
title: C++ Primer 第十九章
categories: C++
description: C++ Primer
keywords: C++, Primer
---

特殊工具与技术

---

## 第十九章

### 运行时类型识别

- 运行时类型识别(run-time type identification,RTTI)的功能由两个运算符实现：1、typeid运算符，用于返回表达式的类型；2、dynamic_cast运算符，用于将基类的指针或者引用安全地转换成派生类的指针或引用。

当我们将这两个运算符用于某种类型的指针或者引用，并且该类型含有虚函数时，运算符将使用指针活引用所绑定对象的动态类型。

- 使用RTTI必须加倍小心。在可能的情况下，最好定义虚函数而非直接接管类型管理的重任。

- dynamic_cast运算符的使用形式如下：
```c++
//以下type必须是类类型
dynamic_cast<type*>(e);//e是有效指针
dynamic_cast<type&>(e);//e必须是左值
dynamic_cast<type&&>(e);//e不能是左值
```
e的类型可能是：目标type的公有派生类、目标type的公有基类、目标type的类型。

- 如果一条dynamic_cast语句的转化目标是指针类型并且失败了，则结果为0.如果转换目标是引用类型并且失败了，则dynamic_cast运算符将抛出一个bad_cast异常。

- 可以对一个空指针执行dynamic_cast，结果是所需类型的空指针。

- 在条件部分执行dynamic_cast操作可以确保类型转换和结果检查在同一个条表达式中完成。
```c++
if (Derived *dp = dynamic_cast<Derived*>(bp))
{
    //使用dp指向的Derived对象
}
else//bp指向一个Base对象
{
    //使用bp指向的Base对象
}
```

- typeid运算符，返回对象的类型。使用方式typeid(e)，其中e是任意表达式或者类型的名字。typeid操作结果是一个常量对象的引用，该对象的类型是标准库类型type_info或者type_info的公有派生类型。type_info类定义在typeinfo头文件中。typeid忽略顶层const，但是不会执行数组向指针的标准类型转换。当运算对象不属于类类型或者是一个不包含任何虚函数的类时，typeid运算符指示的是运算对象的静态类型。而当运算对象是定义了至少一个虚函数的类的左值时，typeid的结果直到运行时才会求得。

- 当typeid作用于指针时(而非指针所指的对象)，返回的结果是该指针的静态编译时类型。

- 如果p是一个空指针，则typeid(*p)将抛出一个名为bad_typeid的异常。

- 19.2.3









