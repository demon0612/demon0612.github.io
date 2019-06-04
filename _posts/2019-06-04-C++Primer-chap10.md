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

### 重排容器算法
- 典型的是sort(iterBegin,iterEnd,predicate);按照predicate(微词)进行排序，默认func使用小于号进行排序。
- stable_sort如果predicate返回false，则保持两个元素的相对位置不变。
- unique接受一个迭代器范围，将相邻的重复项进行“消除”，注意泛型算法不删除元素，只是将元素替换到容器的尾部。unique返回的迭代器指向最后一个不重复元素之后的位置。如需要删除之后的元素，需要使用erase方法。

### 定制操作

- 谓词，就是一个可调用的表达式，分为一元谓词和二元谓词。sort需要二元谓词版本。
- lambda表达式：未命名的内联函数。形式如下：
```
[capture list](parameter list)->return type{function body}
```
其中参数列表和返回类型可以忽略。如果lambda的函数体包含任意单一return语句之外的内容，且未指定返回类型，则返回void。
- lambda表达式不能有默认参数。
- 捕获列表只用于局部非static变量，lambda可以直接使用局部static变量和在它所在函数之外声明的名字。
- 当以引用方式捕获一个变量时，必须保证在lambda执行时变量是存在的。
- find_if(iterBegin,iterEnd,predicate);用来找到符合谓词的iter位置。
- for_each(iterBegin,iterEnd,predicate);对迭代器范围内的所有元素调用谓词。

#### 参数绑定
如果想使用find_if来对string和一个给定大小进行比较，如果编写二元谓词，则无法直接调用find_if，因为find_if的第三个参数只接受一元谓词。使用lambda可以解决这个问题，另外一种方法是使用C++11中的bind标准库函数，它定义在functional中。可以将bind函数看做一个通用的函数适配器，接受一个可调用对象，生成一个新的可调用对象，形式如下：
```c++
auto newCallable = bind(callable,arg_list);
```
 以刚才的find_if为例子：
 ```c++
 bool check_size(const string& s,string::size_type sz)
 {
     return s.size()>=sz;
 }

 find_if(vec.begin(),vec.end(),check_size);//错误，不接受二元谓词
 
 find_if(vec.begin(),vec.end(),[&sz](const string& s){return s.size()>=sz;});//OK,lambda表达式

auto check6 = bind(check_size,_1,6);//将二元谓词转换成一元谓词，sz=6
bool b1 = check6(s);//相当于check_size(s,6);
find_if(vec.begin(),vec.end(),bind(check_size,_1,sz));//OK
```
名字_n都定义在一个名额外placeholders的命名空间中，如果需要使用，需要声明：
```c++
using namespace std::placeholders;
```
- 使用bind重排参数顺序：
```c++
sort(words.begin(),words.end(),isShorter);//按单词长度由短到长排序
sort(words.begin(),words.end(),bind(isShorter,_2,_1));//按单词长度由长到短
```
- bind绑定引用参数，可以使用标准库的ref函数
```c++
for_each(words.begin(),words.end(),[&os,c](const string &s){os << s<< c;});//os为标准输出流
ostream& print(ostream& os,const string& s,char c)
{
    return os << s << c;
}
for_each(words.begin(),words.end(),bind(print,ref(os),_1,' '));//使用ref返回一个ostream的引用对象。
```

### 再探迭代器

#### 插入迭代器：
- back_inserter创建一个使用push_back的迭代器
- front_inserter创建一个使用push_front的迭代器
- inserter创建一个使用insert的迭代器。这个函数接受第二个参数，这个参数必须是一个指向给定容器的迭代器，元素将被插入到给定迭代器所表示的元素之前。

#### iostream迭代器
- istream_iterator读取输入流，ostream_iterator向一个输出流写数据。
```c++
istream_iterator<int> in_iter(cin);//从cin读取int
istream_iterator<int> eof; //istream尾迭代器
while(in_iter!=eof)
{
    vec.push_back(*in_iter++);
}
```
可以重写为：
```c++
istream_iterator<int> in_iter(cin),eof;//从cin读取int
vector<int> vec(in_iter,eof);//从迭代器范围构造vec
```
- 使用算法操作流迭代器
```c++
istream_iterator<int> in_iter(cin),eof;//从cin读取int
cout<< accumulate(in,eof,0)<<endl;//将标准输入的int求和
```
- ostream_iterator输出流
```c++
ostream_iterator<int> out_iter(cout," ");
for(auto e:vec)
{
    *out_iter++=e;//也可以写成 out_iter = e;
}
cout << endl;
```
将vec中每一个元素写到cout中，每个元素之后追加一个空格。
更简单的方式：
```c++
copy(vec.begin(),vec.end(),out_iter);
cout << endl;
```
就可以完成对vec中元素的输出。

#### 反向迭代器
- 除了forward_list，其他容器都支持反向迭代。
```c++
string line ="FIRST,MIDDLE,LAST";
auto comma=find(line.cbegin(),line.cend(),",");
cout << string(line.cbegin(),comma) << endl;//输出：FIRST
auto rcomma = find(line.crbegin(),line.crend(),",");
cout << string(line.crbegin(),rcomma) << endl;//输出：TSAL
cout << stirng(rcomma.base(),line.cend()) << endl;//输出：LAST，值得注意
```













