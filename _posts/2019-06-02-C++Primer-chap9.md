---
layout: post
title: C++ Primer 第九章
categories: C++
description: C++ Primer
keywords: C++, Primer
---

顺序容器

---

## 第九章

### 顺序容器概述
顺序容器类型：

容器类型|特点
:--: | :--: 
vector|可变大小数组，支持快速随机访问，在尾部之外的位置插入或删除元素可能很慢
deque|双端队列，支持快速随机访问。在头尾插入删除元素很快
list|双向链表。只支持双向顺序访问，在list任何位置插入删除操作都很快
forward_list|单向链表。只支持单向顺序访问。在链表的任何位置进行插入删除操作都很块
array|固定大小，支持随机访问，不能添加删除元素
string|与vector相似，随机访问快，在尾部添加删除元素快。


- forward_list 和list不支持随机访问
- deque，vector，array支持随机访问
- forward_list和array是新C++的标准类型

### 容器库概览

- 只有顺序容器的构造函数才能接受大小参数，关联容器并不支持
- 顺序容器（array除外)有一个名为assign的成员，可以从一个不同但相容的类型赋值，或者从容器的一个子序列赋值。assign操作用参数所指定的元素（的拷贝）替换左边容器中的所有元素。例如：
```c++
list<string> names;
vector<const char*> oldstyle;
names=oldstyle;//错误，容器类型不匹配
names.assign(oldstyle.cbegin(),oldstyle.cend());//OK!
```
- forward_list支持max_size和empty，但是不支持size

### 顺序容器操作

- 向一个vector、string或者deque插入元素会使所有指向容器的迭代器、引用和指针失效。
- 调用emplace_back会直接在容器管理的内存空间中直接创建对象。而调用push_back则会创建一个局部临时对象，并将其压入容器。
- 删除deque中除首尾位置外的任何元素都会使所有迭代器、引用和指针失效。指向vector或者string中删除点之后位置的迭代器、引用和指针会失效。
- 对于顺序容器，erase都返回指向删除的（最后一个）元素之后位置的迭代器。
- 如果在一个循环中插入或者删除deque、string或者vector中的元素，不要缓存end返回的迭代器。

### 额外的stirng操作
- string搜索函数返回string::size_type值，该类型是一个unsigned类型，用一个inst或者其他带符号类型来保存这些函数的返回值不是一个好主意。
- to_string(val)，一组重载函数，可以将val转换成string。stoi(),stol()之类的函数可以将string转化成相应的数值类型。


### 容器适配器
- 标准库中有三个顺序容器适配器：stack、queue和priority_queue。适配器是标准库的一个通用概念，容器、迭代器和函数都是适配器。
- 默认情况下，stack和queue是基于deque实现的，priority_queue是基于vector实现的。创建适配器可以重载默认容器类型：
```c++
stack<string,vector<string>> str_stk;//在vector上实现空栈
```
- stack（尾部操作）不能是array和forward_list；queue（两端操作）可以用list和deque，不能是vector；priority_queue（随机访问）可以用vector或者deque，不能是list。



