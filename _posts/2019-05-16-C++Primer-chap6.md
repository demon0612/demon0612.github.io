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

Record lookup(Phone\*);
Record lookup(Phone* const);//错误

Record lookup(Account&);
Record lookup(const Account&);//OK

Record lookup(Account*);
Record lookup(const Account\*);//OK
```

- C++中，名字查找发生在类型检查之前，局部声明的函数名或者变量，将全部覆盖掉局部外的声明，即使局部外的函数能更好的与形参类型符合。

### 特殊用途语言特性

- constexpr函数是指能用于常量表达式的函数，要遵从几个约定：函数的返回类型以及所有形参的类型都必须是字符值类型，而且函数体中必须有且只有一条return语句。constexpr函数中也可以包含其他语句，只要这些语句在运行时不执行任何操作就可以。
```c++
constexpr int new_sz() {return 42;}
constexpr int foo = new_sz();
constexpr size_t scale(size_t cnt){return new_sz()*cnt;}
int arr[scale(2)];//可以，因为scale(2)是常量表达式
int i=2;
int a2[scale(i)];//错误，scale(i)不是常量表达式
```
- 把内敛函数和constexpr函数放在头文件中

### 调试帮助

- assert预处理宏，使用方式：
```c++
assert(expr);
```
首先对expr求值，如果表达式为假，assert输出信息并终止程序的执行。如果表达式为真，assert什么都不做。

- assert宏定义在cassert头文件中，程序不要用std::assert或者using声明。

- 特殊变量
   1. \_ \_func\_ \_ 输出当前调试的函数名字
   2. \_ \_FILE\_ \_ 存放文件名的字符串字面值
   3. \_ \_LINE\_ \_ 存放当前行号的整型字面值
   4. \_ \_TIME\_ \_ 存放文件编译时间的字符串字面值
   5. \_ \_DATE\_ \_ 存放文件编译日期的字符串字面值

### 函数匹配

- 匹配规则
   1. 精确匹配
      - 实参类型和形参类型相同
      - 实参从数组类型或者函数类型转换成对应的指针类型
      - 向实参添加顶层const或者从实参中删除顶层const
   2. 通过const转换实现的匹配
   3. 通过类型提升实现的匹配
   4. 通过算术类型转换实现的匹配
   5. 通过类类型转换实现的匹配

### 函数指针

函数指针指向某种特定类型。函数的类型由它的返回类型和形参类型共同决定。
```c++
bool lengthCompare(const string&, const string&);
bool (*pf)(const string& string&);//两个括号都不可省略，pf未初始化。
```

- 使用函数指针
两种初始化方式都可以：
```c++
pf = lengthCompare;
pf = &lengthCompare;
```
函数调用方式：
```c++
bool b1 = pf("hello","goodbye");
bool b2 = (*pf)("hello","goodbye");
bool b3 = lengthCompare("hello","goodbye");
```
指向不同函数类型的指针间不存在转换规则，强行赋值会出现错误。可以为函数指针赋值nullptr。

- 重载函数的指针：
两个重载函数：
```c++
void ff(int*);
void ff(unsigned int);
```
指针类型必须与重载函数中的某一个精确匹配。
```c++
void (*pf1)(unsigned int) = ff;//pf1指向ff(unsigned)
void (*pf2)(int) = ff;//错误，没有一个ff与该形参类型匹配
double (*pf3)(int*) = ff;//错误，ff和pf3的返回类型不匹配
```

- 函数指针形参
和数字类似，虽然不能定义函数类型的形参，但是形参可以是指向函数的指针。形参看起来是函数类型，实际上却当成指针使用
```c++
void useBigger(const string& s1,const string& s2,bool pf(const string& , const string&);//pf自动转换成函数的指针
void useBigger(const string& s1,const string& s2,bool (*pf)(const string& , const string&);//等价形式
```
调用方法如下：
```c++
useBigger(s1,s2,lengthCompare);
```
类型别名和decltype可以简化使用函数指针：
```c++
//Func 和 Func2是函数类型
typedef bool Func(const string& const string&);
typedef decltype(lengthCompare) Func2;
//FuncP和FuncP2是指向函数的指针
typedef bool(*FuncP)(const string&, const string&);
typedef decltype(lengthCompare) *FuncP2;
```

- 返回指向函数的指针
和数组类似，虽然不能返回一个函数，但是能返回指向函数类型的指针。我们必须把返回类型写成指针形式，编译器不会自动将函数返回类型当成队形的指针类型处理。
```c++
using F= int(int* ,int);//F是函数类型，不是指针
using PF = int(*)(int*,int);//PF是指针类型
```
必须显式的将返回类型指定为指针：
```c++
PF f1(int);//OK:PF是函数指针，f1可以声明返回函数指针
F f1(int);//错误:F是函数类型，f1不能返回一个函数
F *f1(int);//OK:显式地指定返回类型是指向函数的指针
```
以下两种声明相同：
```c++
inf (*f1(int))(int*,int);
auto f1(int)-> int(*)(int*,int);
```

- 将auto和decltype用于函数指针类型
类似如下：
```c++
string::size_type sumLength(const string&, const string&);
decltype(sumLength) *getFunc(const string&);
```
声明getFcn需要注意一点，decltype作用于某个函数的时候，返回函数本身而不是指针，所以必须要显式地加上\*表示返回指针，而非函数本身。










