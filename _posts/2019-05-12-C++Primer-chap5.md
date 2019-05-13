---
layout: post
title: C++ Primer 第五章
categories: C++
description: C++ Primer
keywords: C++, Primer
---

语句

---

## 第五章

### 条件语句
- switch结构中的case标签必须是整形常量表达式。
- 一般不要省略case分支最后的break语句。如果没写break语句，最好加一段注释说清楚程序的逻辑。
- switch的某个case中如果需要定义变量，则整个case必须放在一个代码块中。

### 转跳语句
- break语句的作用范围仅限于最近的循环或者switch语句。
- continue语句终止最近的循环中的当前迭代并立即开始下一次迭代。

### try语句块和异常处理
- throw表达式可用于引发一个异常，throw表达式包含关键字throw和紧随其后的一个表达式，其中表达式类型就是抛出的异常类型。
```c++
int f()
{
    throw runtime_error("Data convert error!");
}
```
- try语句块：
```c++
try
{
    inf();
}
catch(runtime_error err)
{
    cout << err.what();
}
```

- 标准异常定义在四个文件中：
   - exception头文件定义了最通用的异常类exception。只报告异常的发生。
   - stdexcept头文件定义了几个常用的异常类。比如:runtime_error,range_err等
   - new头文件定义了bad_alloc异常类型。
   - type_info头文件定义了bad_case异常类型。
只能以默认初始化的方法初始化exception,bad_alloc和bad_case对象，不能对这些对象提供初始化。其他类型都要使用string或者C风格字符串进行初始化。
- 异常类型只定义了一个what的成员函数，该函数没有任何参数，返回值是一个指向C风格字符串。


