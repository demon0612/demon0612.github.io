---
layout: post
title: C++ Primer 第四章
categories: C++
description: C++ Primer
keywords: C++, Primer
---

表达式

---

## 第四章

### 基础

- 当一个对象被用作右值的时候，用的是对象的值(内容);当对象被用作左值的时候，用的是对象的身份(在内存中的位置)。
一个重要的原则：在需要右值的地方可以用左值来提单，但是不能把右值当成左值(也就是位置)使用。当一个左值被当做右值使用时，使用的是它的内容(值)。
   - 赋值运算符需要一个()左值作为其左侧运算对象，得到的结果也依然是一个左值。
   - 取地址符作用于一个左值运算对象，返回一个指向该运算符的指针，这个指针是一个右值。
   - 内置解引用运算符、下标运算符、迭代器解引用运算符的求值结果是左值。
   - 内置类型和迭代器的递增递减运算符作用于左值运算对象，其前置版本所得的结果也是左值。

- 如果表达式的求值结果是左值，delctype作用于该表达式(不是变量)得到一个引用类型。p的类型是int*，则decltype(*p)的结果是int&。但是，decltype(&p)的结果却是int**， 取地址运算符生成右值，所以结果是一个指向整形指针的指针。

### 赋值运算符

- 赋值运算符满足右结合律
- 因为赋值运算符的优先级低于关系运算符的优先级，所以在条件语句中，赋值部分通常应该加上括号

### 位运算符
- 在位运算时，关于符号位如何处理没有明确的规定，所以强烈建议仅将位运算符用于处理无符号类型。
- 位移运算符满足左结合律

### sizeof运算符
- sizeof满足右结合律，返回一个size_t的常量表达式
- sizeof返回的是表达式结果类型的大小，不实际计算其运算对象的值。
- C++11允许使用作用域运算符获得类成员的大小。
- 对数组进行sizeof运算得到是整个数组所占空间大小，等价于对数组中所有元素执行一次sizeof运算然后把结果相加。

### 类型转换
- 显示强制类型转换形式：
```c++
cast-name<type>(expression)
```
type是要转换的目标类型，而expression是要转换的值，如果type是引用类型，则转换之后是左值。
- static_cast: 任何具有明确定义的类型，只要不包含底层const，都可以使用static_const
- const_cast:只能改变运算对象的底层const。如果对象本身不是一个常量，使用强制类型转换获得写权限是合法的行为，然而如果一个对象是常量，再使用const_cast执行写操作就是未定义的行为。
- reinterpret_cast:这种转换本质上依赖机器，想要安全地使用reinterpret_cast必须对涉及的类型和编译器实现转换的过程都非常了解。

- dynamic_cast：在19章再详细介绍






