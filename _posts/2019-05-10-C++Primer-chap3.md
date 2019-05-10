---
layout: post
title: C++ Primer 第三章
categories: C++
description: C++ Primer
keywords: C++, Primer
---

字符串、向量和数组

---

## 第三章

### 命名空间

- 头文件不应该包using声明

### string类型

- C++中的字符串字面值并不是标准库类型string的对象。字符串字面值和string是不同的类型。

### vector类型

- vector对象（以及string对象）的下标运算符可用于访问已存在的元素，而不能用于添加元素。

- 所有标准库容器的迭代器都定义了==和！=，但是它们当中的大多数都没有定义<运算符。所以能用==和！=，就别用<。

- begin和end返回的类型：如果对象是常量，返回const_iterator；如果对象不是常量，返回iterator。如果想一定返回const_iterator，则使用cbegin和cend。

- 谨记，但凡是使用了迭代器的循环体，都不要向迭代器所属的容器添加元素。

- 顺序容器支持迭代器和一个整数相加（相减），其返回值是向前（向后）移动了若干个位置的迭代器。还可以使用比较运算符对其进行比较。如果将两个迭代器相减，得到的结果是两个迭代器的距离。类型为difference_type，这个距离可正可负，所以difference_type是带符号的。

### 数组

- 和内置类型的变量一样，如果在函数内部定义了某种内置类型的数组，那么默认初始化会令数组含有未定义的值。
- 使用数组下标的时候，通常将其定义为size_t类型。
- 对数组的元素使用取地址符就能得到指向该元素的指针：
```c++
string nums[]={"one","two","three"};
string *p=&nums[0];
string *p2=nums;//等价于p2=&nums[0];
```
- 当使用数组作为一个auto变量的初始值时，推断得到的类型是指针，而不是数组。但是使用decltype时，这种转化不会发生。
- c++新标准引入了两个名为begin和end的函数，参数可以是一个数组，用于返回数组的首元素指针和尾元素下一个位置的指针，这两个函数定义在iterator头文件中。
- 两个指针相减的结果是ptrdiff_t类型，和size_t类型相同一样定义在cstddef中，也是有符号的类型。

### C风格字符串

- 允许字符串字面量来初始化string对象，但是不能直接用string来初始化一个字符串，需要用c_str函数：
```c++
string s("Hello World!");
char *str=s;//错误
const char *str=s.c_str();//OK
```
无法保证c_str返回的数组一直有效，如果后续操作改变了s的值，就可能让str失效。所以，最好将数组重新拷贝一份。
- 可以使用数组来初始化vector对象：
```c++
int int_arr[]={0,1,2,3,4,5};
vector<int> ivec(begin(int_arr),end(int_arr));//全部
vector<int> subVec(int_arr+1,int_arr+4);//部分，但是不包括int_arr[4]
```

### 多维数组

- 要使用范围for语句处理多维数组，除了最内层的循环外，其他所有循环的控制变量都应该是引用类型。
