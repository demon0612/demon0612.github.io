---
layout: post
title: C++ Primer 第七章
categories: C++
description: C++ Primer
keywords: C++, Primer
---

类

---

## 第七章

### 定义抽象数据类型 

- this是一个常量指针，不允许修改this中保存的指针。即this的类型为:class_type \*const，默认情况下不能将this指针绑定到一个常量对象上。

- 如果一个类的函数声明，以const结尾：
```c++
class class_name
{
    void func() const;
}
```
这个const表示this是一个指向常量的指针，即const class_type \* const。
这样的成员函数称为常量成员函数。因为this是指向常量的指针，所以常量成员函数不能改变调用它的对象的内容。

- 常量对象，以及常量对象的引用或者指针只能滴啊用常量成员函数。

- 如果需要返回this指针的函数：
```c++
class_type& class_type::func()
{
    return *this;
}
```

- IO函数不能被拷贝，想要使用IO来输出类信息，IO对象必须是引用，而且不能是const引用。

- C++11中如果需要默认的构造函数，可以在参数列表后面加上=default来要求编译器生成构造函数。
```c++
class class_type
{
    class_type()=default;
}
```
默认构造函数想要生效，必须在类内对内置类型提供初始化。如果编译器不支持类内初始化，则只能用函数初始化列表来显式初始化类的每个成员。

### 访问控制与封装

- 使用class和struct定义类唯一的区别就是默认的访问权限：struct默认是public，而class默认是private

- 如果类想把一个函数作为它的友元，则只需要增加一条以friend关键字开始的函数声明语句。友元声明可以出现在类的内部的任意位置。友元不是类的成员，也不受它所在区域控制级别的约束。

- 友元的声明仅仅指定了访问的权限，而非一个通常意义上的函数声明。如果我们希望类的用户能够调用某个友元函数，那么必须在友元声明之外再专门对函数进行一次声明。

### 类的其他特性














