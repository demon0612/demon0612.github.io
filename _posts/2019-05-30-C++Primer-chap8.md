---
layout: post
title: C++ Primer 第八章
categories: C++
description: C++ Primer
keywords: C++, Primer
---

IO库

---

## 第八章

### IO类

- IO对象无拷贝或者赋值。由于不能拷贝IO对象，所以不能将形参或者返回类型设置为流类型。进行IO操作的函数通常以引用方式传递和返回流。读写一个IO对象会改变其状态，所以传递和返回的引用也不能是const。

- flush可以立刻刷新缓冲区，但不输出任何额外的字符，ends向缓冲区插入一个空字符，然后刷新缓冲区
```c++
cout << "hi" << endl;
cout << "hi" << flush;
```
通过设置unitbuf来设置每次输出操作是否立刻刷新缓冲区：
```c++
cout << unitbuf;//所有输出操作后立刻刷新缓冲区
cout << nounitbuf;//回到正常方式
```
程序异常终止时，输出缓冲区可能不会被刷新。

- 当一个输入流关联到一个输出流时，则任何试图从输入流读取数据的操作都会先刷新相关联的输出流，标准库中的cout和cin关联。
```c++
cin.tie(&cout);//cout和cin关联
ostream *old_tie = cin.tie(nullptr);//cin不在与其他关联
cin.tie(&cerr);
cin.tie(old_tie);//重新建立cin与cout的链接，old_tie表示cin与cout的关联。
```

### 文件输入输出

- 当一个fstream对象被销毁时，close会自动被调用。
- 默认情况，当我们打开一个ofstream时，文件内容会被丢弃，阻止方式是mode指定为app模式
```c++
ofstream app("file1",ofstream::app);
```

### string流
- sstream头文件定义了三种内存IO用于向string写入或者读取数据。istringstream从string读取数据，ostringstream向string写入数据，stringstream既可以读也可以写数据。