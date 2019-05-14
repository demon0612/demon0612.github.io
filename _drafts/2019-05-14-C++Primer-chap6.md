---
layout: post
title: C++ Primer 第六章
categories: C++
description: C++ Primer
keywords: C++, Primer
---

函数

---

## 第六章

### 函数基础
- 局部静态变量在程序执行路径第一次经过对象定义语句时初始化，并且直到程序终止才被销毁，在此期间即使对象所在的函数结束执行也不会对它有影响。如果局部静态变量没有显式初始化，将执行默认初始化。内置类型的局部静态变量将会初始化为0。

### 参数传递
- 和其他初始化过程一样，当用实参初始化形参时会忽略掉顶层const。形参的顶层const被忽略，传递给它常量对象或者非常量对象都是可以的。
```c++
void func(const int i);//可以传入int类型，而不一定是const int
void func(int i);//错误，重新定义
```

- 数组作为形参有两个特殊性质
   1. 不允许拷贝数组
   2. 使用数组时通常会将其转换成指针
以下三种情况定义相同：
```c++
void print(const int*);
void print(const int[]);
void print(const int[10]);
```
- 管理指针形参有三种常用技术：
   1. 使用标记指定数组长度，例如C风格字符串
   2. 使用标准库规范，函数API要求输入两个指针，指针可以由begin()和end()函数获得。
   3. 显式传递一个表示数组大小的形参
- 数组引用形参：
形式如下：
```c++
void f(int (&arr)[10]);//正确，但是对于形参不是10个数的无法调用
void f(int& arr[10]);//错误，arr是一个数组，里面存着整数的引用
```

- 含有可变形参的函数
如果所有的实参类型相同，可以使用initializer_list标准库类型，如果实参类型不同，可以使用可变参数模板。还有一种特殊的形参类型，即省略号，为了和C函数兼容。
   - initializer_list中元素永远是常量值，无法改变。
   - 省略符形参应该仅仅用于C和C++通用的类型。大多数类类型的对象在传递给省略符形参时将无法正确拷贝。

### 返回类型和return语句
- 不要返回局部对象（包括局部常量对象）的引用或者指针
- 引用返回左值
调用一个返回引用的函数得到左值，其他返回类型得到右值。如果返回类型是常量引用，不能给调用的结果赋值。
- 列表初始化返回值
C++11中函数可以返回花括号包围的值的列表，可以对临时变量进行初始化。
```c++
vector<string> process()
{
    return {"one","two","three"};
}
```
对于内置类型，花括号最多包含一个值，而对于类类型，可以自己定义。
- main函数不能调用自己进行递归。

- 声明一个返回数组指针的函数
简单的方式是使用类型别名：
```c++
typedef int arrT[10];
using arrT= int[10];//效果同上
arrT* func(int i);//返回一个含有是个整数的数组指针
```
如果不使用类型别名，则声明如下：
```c++
int (*func(int i))[10];
```
两个括号不能省略。还是可以按照就近原则，从右至左，从内到外来解读这个声明。
C++11中，可以使用尾置返回类型，定义如下：
```c++
auto func(int i) -> int(*)[10];
```
还可以使用decltype来完成：
```c++
int odd[]={1,3,5,7,9};
int even[]={0,2,4,6,8};
decltype(odd) *arrPtr(int i)
{
    return (i%2) ? &odd:&even;
}
```
注意，decltype并不负责把数组类型转换成对应的指针，所以上面的例子需要加上\*符号。

### 函数重载
- 不区分顶层const，但是区分底层const
```c++
Record lookup(Phone);
Record lookup(const Phone);//错误

Record lookup(Phone*);
Record lookup(Phone* const);//错误

Record lookup(Account&);
Record lookup(const Account&);//OK

Record lookup(Account*);
Record lookup(const Account*);//OK
```

- C++中，名字查找发生在类型检查之前，局部声明的函数名或者变量，将全部覆盖掉局部外的声明，即使局部外的函数能更好的与形参类型符合。

### 特殊用途语言特性
















