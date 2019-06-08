---
layout: post
title: C++ Primer 第十二章
categories: C++
description: C++ Primer
keywords: C++, Primer
---

动态内存

---

## 第十二章

### 动态内存与智能指针

- shared_ptr,unique_ptr和weak_ptr三种智能指针，存储在memory头文件中。

- 如果将shared_ptr放入一个容器中，而后不需要全部元素，只需要其中的一部分，要记得用erase删除不再需要的元素

- 由于与变量初始化相同的原因，对于动态分配的对象进行初始化通常是个好主意。

- new可以和auto一起使用，前提是可以通过参数列表推断出分配对象的类型，括号中包括初始化器
```c++
auto p1 = new auto(obj);//p指向一个与obj类型相同的对象
auto p2 = new auto{a,b,c};//错误，括号中只能有单个初始化器
```
- new可以与const一起使用，前提是必须初始化，如果没有默认初始化，必须提供显式初始化。
```c+++
const int* pc1 = new const int(1024);
const string* pcs = new const string;
```
- 当内存被耗尽的时候，new可能会失败，如果失败，会返回一个bad_alloc异常。
```c++
int *p1 = new int;//如果失败，会抛出bad_alloc异常
int *p2 = new (nothrow) int;//如果失败，不会抛出异常，返回一个空指针
```

- 出于一种保护，delete内存之后应该将指针设定为nullptr。

- 能够接受指针参数的智能指针构造函数是explicite的。不能讲一个内置指针隐式转换为一个智能指针，必须使用直接初始换形式。
```c++
shared_ptr<int> p1 = new int(1024)；//不能隐式的将int*转化
shared_ptr<int> p2(new int(1024));//正确，使用了直接初始化形式
```

- 将一个shared_ptr绑定到一个普通指针时，就应该讲内存的管理指针交个shared_ptr，不应该使用内置指针来访问shared_ptr指向的内存。特别需要注意的是，在函数出中按照值传递内置指针给智能指针的时候，很可能在离开函数作用域的时候，智能指针对内置指针指向的内存进行了delete操作，这样内置指针就悬空了！

- 智能指针的get用来将指针的访问权限传递给代码，只有在确定代码不会delete指针的情况下，才能使用get。特别的，永远不要用get初始化另一个智能指针或者为另一个智能指针赋值。

- 智能指针基本规范：
   - 不使用相同的内置指针初始化(或者reset)多个智能指针
   - 不delete get()返回的指针
   - 不适用get()初始化或者reset另一个智能指针
   - 如果使用get(）返回的指针，记住当最后一个对应的智能指针销毁后，指针就悬空了
   - 如果智能指针管理的资源不是new分配的内存，记住传递给它一个删除器

- 某个时刻只有一个unique_ptr指向一个给定的对象，定义一个unique_ptr时，需要将其绑定到一个new返回的指针上，没有make_shared初始化，初始化unique_ptr必须采用直接初始化，不支持普通的拷贝或者赋值

- 可以通过release或者reset将指针的所有权从一个(非const)unique_ptr转移给另一个unique：
```c++
unique_ptr<string> p2(p1.release());//release将p1置为空
unique_ptr<string> p3(new string("Text"));
p2.reset(p3.release());//reset释放了p2原来指向的内存，p3将指针转移给了p2
```

- 不能拷贝unique_ptr的规则有一个例外：可以拷贝或者赋值一个将要销毁的unique_ptr。
```c++
unique_ptr<int> clone(int p)
{
    return unique_ptr<int>(new int(p));//OK，从int*创建一个unique_ptr<int>
}
unique_ptr<int> clone(int p)
{
    unique_ptr<int> ret(new int (p));
    return ret;
}
```

- unique_ptr和shared_ptr类似，可以自定义删除器

- weak_ptr可以绑定到一个shared_ptr上，但不会改变shared_ptr的引用计数。当shared_ptr被销毁时，对象会被释放，即使有weak_ptr指向对象，对象还是会被释放。

- 创建一个weak_ptr时，要用一个shared_ptr来初始化
```c++
auto p make _shard<int>(42);
weak_ptr<int> wp(p); //wp弱共享p，p的引用计数未改变
```
由于对象可能不存在，不能直接使用weak_ptr直接访问对象，必须调用lock。此函数检查weak_ptr指向的对象是否存在。如果存在，lock返回一个指向共享对象的shared_ptr。只要shared_ptr存在，它所指向的底层对象也就一直存在
```c++
if(shared_ptr<int> np = wp.lock())//如果np不为空，则条件成立
{
    //在if中np和wp共享对象
}
```

### 动态数组

- new分配一个对象数组，要在类名之后跟一对方括号，其中声名要分配的对象的数目。方括号中必须为整数，但不必是常量。类型别名也可以：
```c++
typedef int arrT[42]; //arrT表示42个int的数组类型
int* p = new arrT;//分配一个42个int的数组，p指向第一个int
```

- 动态数组并不是数组类型，不能对动态数组调用begin和end，不能用范围for来处理其中的元素。

- 动态分配一个空数组是合法的
```c++
char arr[0];//错误：不能定义长度为0的数组
char *cp = new char[0];//正确，但是cp不能解引用
```
当用new分配一个大小为0的数组，new返回一个合法的非空指针。对于零长度数组，此指针就像尾后指针一样，可以像使用尾后迭代器一样使用这个指针，可以用此指针进行比较。

- 使用unique_ptr可以管理new分配的数组：
```
unique_ptr<int[]> up(new int [10]);
up.release();//自动用delete[] 销毁其指针
```
- 指向数组的unique_ptr不支持成员访问运算符，可以使用下标运算符来访问数组中的元素。shared_ptr不支持直接管理动态数组，如果要使用，必须自己定义删除器
```c++
shared_ptr<int> sp(new int[10],[](int* p} {delete[] p;}));
sp.reset();
```
如果没有提供删除器，这段代码将是未定义。默认情况shared_ptr调用delete销毁对象。

- new数组的弊端：将内存分配和对象构造组合在了一起，可能会导致不必要的浪费，数组后面的对象可能永远用不到。另外，没有默认构造函数的类不能动态分配数组。

- allocator将内存分配和对象构造隔离，它分配的内存是原始的，未构造的。construt成员函数接受一个指针和零个或者多个额外参数，在给定位置构造一个元素。使用完对象需要调用destroy来销毁他们。最后使用deallocator来释放内存。
```c++
allocator<string> alloc;
auto const p = alloc.allocate(n);//分配n个未初始化的string
auto q=p;
//构造
alloc.construct(q++);//*q为空字符串
alloc.construct(q++,10,'c');
alloc.construct(q++,"hi");
//析构
while(q!=p)
{
    alloc.destroy(--q);
}
//释放
alloc.deallocate(p,n);
```
使用未构造的内存，其行为是未定义的。同时，只能对真正构造了的元素进行destroy操作





















