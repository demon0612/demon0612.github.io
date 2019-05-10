---
layout: post
title: C++ Primer 第二章
categories: C++
description: C++ Primer
keywords: C++, Primer
---

变量和基本类型

---

## 第二章

### 基本内置类型

- 字符类型有三种：char，signed char和unsigned char。类型char和signed char并不一样。实际上字符表现形式只有两种：带符号和不带符号。类型char表现为哪一种取决于编译器。
- 有符号数字和无符号数字进行四则运算，会将有符号的数转化成无符号的数进行计算。如下形成死循环：
```c++
for(unsigned u=10;u>=0;u--)
    std::cout << u << std::endl;
```
可以修改成：
```c++
unsigned u=11;
while(u>0)
{
    --u;
    std::cout << u << std:: endl;
}
```

### 变量

- 初始化不是赋值，初始化的含义是创建变量时赋予其一个初始值，而赋值的含义是把对象的当前值擦除，而以一个新值来替代。

- 如果使用列表初始化且初始值存在丢失信息的风险，则编译器会报错。

### 引用
- 定义引用时，引用和他的初始值绑定在一起，而不是拷贝。引用无法重新进行绑定，引用必须初始化。引用不是对象，无法创建引用的引用。引用的类型要与绑定的对象的类型严格匹配。引用只能绑定到对象上，不能是字面值或者某个表达式的计算结果。
   - 第一种例外：初始化常量引用时可以用任意表达式作为初始值，只要该表达式的值可以转化成引用的类型。允许为一个常量绑定非常量的对象、字面值，甚至是一个表达式。
   ```c++
   int i=42;
   const int &r1 = i;
   const int &r2 = 42;
   const int &r3 = i*2;
   int &r4 = r1*2; //错误，r1为常量，r4位非常量的引用。
   ```

### 指针

- 指针是一个对象，可以拷贝和赋值。在块作用域如果没有初始化，和内置类型一样，也是有不确定的值。引用不是对象，无法创建引用的指针。但是可以创建指针的引用。
```c++
int i=42;
int *p;
int *&r=p;//r是指针p的引用。
r=&i;//r指向i的地址
*r=0;//修改i的值为0
```

### const

- 因为const对象创建之后其值不能改变，所以const对象必须初始化。默认情况下const对象仅在文件内有效。如果想在多个文件之间共享const对象，必须在变量的定义和多个声明之前添加extern关键字。

- 指针可以指向常量或者非常量。类似于常量引用，指向常量的指针不用改变其所指的对象。想要存放常量对象地址，只能使用常量指针。但是常量指针可以用非常量对象来初始化，只是没有办法通过这个指针来修改非常量的值。
```c++
const double pi = 3.14;
double *ptr = &pi; //错误
const double *cptr = &pi; //cptr不是常量，可以不用初始化
&cptr = 42; //错误
double dval = 3.14;
cptr = &dval; //正确
```

- 常量指针必须初始化，而且一旦初始化完成，则它的值就不能改变。常量指针指向的值能否改变，取决于指向的是常量还是非常量，而与指针类型无关。

- 指针本身是否是常量以及指针所指的是不是一个常量是两个相互独立的问题。顶层const表示指针本身是常量，而底层const表示指针所指对象是一个常量。

### constexpr

- 常量表达式是指值不会改变且在编译期就能得到计算结果的表达式。一个对象（或者表达式）是否是常量表达式由它的数据类型和初始值共同决定。如果需要编译期检查某个变量的值是否是常量表达式，可以使用constexpr关键词。如果在constexpr声明中定义了一个指针，限定符constexpr仅对指针有效，与指针所指对象无关。
```c++
const int *p = nullptr;//p是一个指向整形常量的指针
constexpr int *q=nullptr;//q是一个指向整数的常量指针
```
- 和其他常量指针类似，constexpr指针既可以指向常量也可以指向一个非常量。

### 类型别名

- 类型别名除了使用typedef之外，C++11引入了新的语法：
```c++
using SI=Sales_item;
```
- 如果使用类型别名定义了一个指针类型，则const+别名是指底层const，而不是顶层const。
```c++
typedef char *pstring;
const pstring cstr=0;//cstr是指向char的常量指针，而不是指向常量的指针
```

### auto

- auto定义的类型必须要有初始化。auto一般会忽略掉顶层const，而保留底层const。注意：对一个常量对象取地址是一种底层const。如果希望推断出的auto类型是一个顶层const，则需要明确指出。
```c++
int i=0;
const int ci = i;
auto f1=ci;//忽略const属性
const auto f2=ci;//必须手动添加const声明
```

### decltype

- decltype作用是返回操作数的数据类型。decltype处理顶层const和引用的方式与auto不一样。decltype返回该变量的类型，包括顶层const和引用在内。
有些表达式将想decltype返回一个引用类型：
```c++
int i=42, *p=&i, &r=i;
decltype(r+0) b;//正确，r的类型为int引用，r+0是表达式，值为int，所以b是一个未初始化的int类型
decltype(*p) c;//错误，c是int&，必须初始化
```
- decltype((variable))(注意是双括号)的结果永远是引用，而decltype(variable)结果只有当variable本身就是一个引用时才是引用。

