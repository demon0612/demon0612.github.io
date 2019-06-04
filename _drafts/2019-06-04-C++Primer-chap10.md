---
layout: post
title: C++ Primer 第十章
categories: C++
description: C++ Primer
keywords: C++, Primer
---

泛型算法

---

## 第十章

### 概述
- 大多数算法都在algorithm中，标准库头文件numeric中还定义了一组数值泛型算法。
- 泛型算法本身不会执行容器的操作，只会运行于迭代器值还是那个，执行迭代器的操作。泛型算法永远不会改变底层容器的大小。算法可能改变容器中保存的元素的值，也可能在容器内移动元素，但永远不会插入或者删除元素。

### 初识泛型算法

#### 只读算法
```c++
acuumulate(iterBegin,iterEnd,initValue);//对迭代器求和
equal(iter1Begin,iter1End,iter2Begin);//迭代器元素定义了==操作，并且假定第二个序列至少与第一个序列一样长。
```

#### 写容器算法
```c++
fill(iterBegin,iterEnd,val);//迭代器范围内元素初始换为val
fill_n(iterBegin,size,val);//将val写入迭代器开始的size个元素中，开发者确保iterBegin的容器能装入size个元素
back_insert(reference);//接受一个指向容器的引用，返回一个与该容器绑定的插入迭代器
copy(iterBegin,iterEnd,destBegin);//返回destEnd之后的迭代器
replace(iterBegin,iterEnd,originVal,destVal);//将迭代器中originVal替换成destVal
replace_copy(iterBegin,iterEnd,destIter,originVal,destVal);//将迭代器中元素赋值到destIter中，并将originVal替换成destVal
```
例子：
```c++
fill_n(back_insert(vec),10,0);//添加10个元素到vec中
replace_copy(ilist.cbegin(),ilist.end(),back_inserter(ivec),0,42);
```
