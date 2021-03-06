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

- type_info类必须定义在typeinfo类中。type_info类没有默认构造函数，而且它的拷贝和移动构造函数以及赋值运算符都被定义为删除的。无法定义或者拷贝type_info类型的对象，也不能为type_info类型的对象赋值。创建type_info对象的唯一途径就是使用typeid运算符。

- type_info类在不同的编译器上有所不同。有的编译器提供了额外的成员函数以提供程序中所有类型的额外信息。

### 枚举类型

- 枚举类型属于字面值常量类型。C++枚举有两种：限定作用域和不限定作用域的。C++11新标准引入了限定作用域的枚举类型。

- 限定作用域的枚举类型：首先是关键字enum class(或者 enum struct)，随后是枚举类型名字以及用花括号括起来的以逗号分隔的枚举成员，最后是一个分号：
```c++
enum class open_modes {input,output,append};
```
限定作用域的枚举，在引用的使用必须加上域限定符。

- 不限定作用域的枚举类型：
```c++
enum color {red,yellow,green};//不限定作用域的枚举类型
enum {floatPrec = 6,doublePrec = 10,double_doublePrec = 10};//未命名的、不限定作用域的枚举类型。

- 默认情况下，枚举值从0开始，依次加1。如果没有显式地提供初始值，则当前枚举成员的值等于之前枚举成员的值加1。

- 枚举成员是const，初始化时提供的初始值必须是常量表达式。一个不限定作用域的枚举类型的对象或枚举成员自动地转换成整形，而限定作用域的不行。

- C++11中，可以在enum的名字后加上冒号以及我们想在该enum中使用的类型：
```c++
enum intValues: unsigned long long {
    charTyp = 255, shortTyp = 65535, longTyp = 429512131UL
}
```
如果没有显式的指定enum的潜在类型，则默认情况下限定作用域的enum成员类型是int。对于不限定作用域的枚举类型来说，其枚举成员不存在默认类型，但是潜在类型足够大，能够容纳枚举值。

- C++11中允许enum的前置声明。enum前置声明必须指定其成员的大小：
```c++
enum intValue: unsigned long long;//不限定作用域，必须指定成员类型
enum class open_modes;//限定作用域的枚举类型可以使用默认成员类型int
```

- 要想初始化一个enum对象，必须使用该enum类型的另一个对象或者它的一个枚举成员。因此，即使某个整型值恰好与枚举成员的值相等，它也不能作为函数的enum实参类型。

### 类成员指针

### 嵌套类

- 一个类可以定义在另一个类的内部，前者称为嵌套类或者嵌套类型。嵌套类是一个独立的类，与外层类基本没有什么关系。外层类的对象和嵌套类的对象是相互独立的。外层类的对象和嵌套类的对象是相互独立的。在嵌套类的对象中不包含任何外层类定义的成员；类似的，在外层类的对象中也不包含任何嵌套类定义的成员。

- 嵌套类的名字在外层类作用域中是可见的，在外层类作用域之外不可见。嵌套类的名字不会和别的作用域中的同一个名字冲突。

### union：一种节省空间的类

- 一个union可以有多个数据成员，但是在任意时刻只有一个数据成员可以有值。当给union的某个成员赋值以后，该union的其他成员就变成未定义的状态了。

- union不能含有引用类型的成员。

- C++11中，含有构造函数或者析构函数的类类型也可以作为union的成员。union可以为其成员指定public、protected和private等保护标记。默认情况下，union的成员都是共有的，这一点与struct相似。

- 在使用union时，必须清楚地知道当前存储在union中的值到底是什么类型。如果使用了错误的数据成员或者为错误的数据成员赋值，则程序可能出现崩溃。

- 匿名union是一个未命名的union，一旦定义了一个匿名union，编译器就自动地为该union创建一个未命名的对象：
```c++
union {         //匿名union
    char cval;
    int ival;
    double dval;
};
cval='c';
ival=42;//之前的cval就无法使用了
```

- 匿名union不能包含受保护的成员或者私有成员，也不能定义成员函数。

- C++11中咋union中可以含有定义了构造函数或拷贝控制成员的类类型成员。

### 局部类

- 定义在某个函数内部的类。局部类的所有成员(包括函数在内)都必须完整定义在类的内部。因此，局部类的作用与嵌套类相比相差很远。局部类也不能定义静态成员。

- 局部类只能访问外层作用域定义的类型名、静态变量以及枚举成员。如果局部类定义在某个函数内部，则该函数的普通局部变量不能被该局部类使用。

- 常规的访问保护规则对局部类同样适用。

- 可以在局部类的内部再嵌套一个类。此时，嵌套类的定义可以出现在局部类之外。不过，嵌套类必须定义在局部类相同的作用域中。

### 固有的不可移植性的特性

- 不可移植性是指因机器而异的特性。C++从C继承来的不可移植性：位域和volatile限定符。还有链接指示extern "C"。

#### 位域

- 类可以将其(非静态)数成员定义成位域。位域在内存中的布局是与机器相关的。

- 位域的类型必须是整型或者枚举类型，通常情况下我们使用无符号类型保存一个位域。位域的声明形式是在成员名字之后紧跟一个冒号以及一个常量表达式，该表达式用于指定成员所占的二进制位数：
```c++
typedef unsigned int Bit;
class File {
    Bit mode:2;
    Bit modified:1;
    Bit prot_owner:3;
    Bit prot_group:3;
    Bit prot_world:3;
};
```

- 取地址运算符(&)不能作用于位域，因此任何指针都无法指向类的位域。

#### volatile限定符

- volatile的确切含义与机器有关，只能通过阅读编译器文档来理解。

- 直接处理硬件的程序尝尝包含这样的元素，它们的值由程序直接控制之外的过程控制，应该将这样的变量声明为volatile。关键字volatile告诉编译器不应该对这样的对象进行优化。

- volatile限定符的用法和const很类似，它起到对类型额外修饰的作用。const和volatile限定符互相没有什么影响。

- const和volatile的一个重要区别是不能使用合成的拷贝/移动构造函数以及赋值运算符初始化volatile对象或从volatile对象赋值。如果类希望拷贝、移动或赋值它的volatile对象，则该类必须自定义拷贝或者移动操作。

#### 链接指示：extern "C"

- 要想把C++代码和其他语言(包括C)编写的代码放在一起使用，要求我们必须有权访问该语言的编译器，并且这个编译器与当前的C++编译器是兼容的。

- 链接指示有两个形式：单个的或者复合的，链接指示不能出现在类定义或函数定义的内部。同样的链接指示必须在函数的每个声明中都出现。
```c++
extern "C" size_t strlen(const char*);//单个链接指示，<cstring>中的链接指示
extern "C"{
    int strcmp(const char*,const char*);
    char *strcat(char*,const char*);
}//复合语句链接指示
```
- 当一个#include指示被防止在复合链接指示的花括号中时，头文件中的所有普通函数声明都被认为是由链接指示的语言编写的。链接指示可以嵌套，因此如果头文件包含带自带链接指示的函数，则该函数的链接不受到影响。

- 指向extern "C"函数的指针
```C++
extern "C" void (*pf) (int);
```

- 链接指示对整个声明都有效

- 当使用链接指示时，不仅对函数有效，而且对作为返回类型或形参类型的函数指针也有效：
```c++
extern "C" void f1(void(*) (int));
```
其中f1的形参是函数指针，其中的函数接受一个int形参返回空。这个链接指示不仅对f1有效，对函数指针同样有效。当调用f1时，必须传给它一个C函数的名字或者指向C函数的指针。

- 如果希望给C++函数传入一个指向C函数的指针，必须使用类型别名：
```c++
extern "C" typedef void FC(int);//FC是一个指向C函数的指针
void f2(FC *);//f2是一个C++函数，该函数的形参是一个指向C函数的指针
```

- 通过使用链接指示对函数进行定义，可以令一个C++函数在其他语言编写的程序中可用：
```c++
extern "C" double calc(double dparm) { /* ...... */}
```
编译器将为该函数生成适合于指定语言的代码。

- 有时需要在C和C++中编译同一个源文件，为了实现这一目的，在编译C++版本的程序时预处理器定义__cplusplus(两个下划线)。利用这个变量，可以在编译C++程序的时候有条件地包含进来一些代码：
```c++
#ifdef __cplusplus
extern "C" //正确：我们正在编译C++程序
#endif
int strcmp(const char*, const char*);
```

- 链接指示与重载函数的相互作用依赖于目标语言。如果目标语言支持重载函数，则为该语言实现链接指示的编译器很可能也支持重载这些C++函数。C语言不支持函数重载，则如下声明是错误的：
```c++
//错误声明：两个extern "C"函数的名字相同
extern "C" void print(const char*);
extern "C" void print(int);
```










