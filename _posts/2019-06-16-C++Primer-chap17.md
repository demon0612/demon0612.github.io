---
layout: post
title: C++ Primer 第十七章
categories: C++
description: C++ Primer
keywords: C++, Primer
---

标准库特殊设施

---

## 第十七章

### tuple类型

- 类似pair。一个tuple中可以有任意数量的成员。每个确定的tuple类型的成员数目是固定的，但一个tuple类的成员数目可以与另一个tuple类型不同。

- tuple的构造函数是explicit的。
```c++
tuple<size_t,size_t,size_t> threeD{1,2,3};//正确
tuple<size_t,size_t,size_t> threeD={1,2,3};//错误
```
可以使用make_tuple函数使用初始值的类型来推断tuple的类型。
```c++
auto item = make_tuple("0-999-783455-X",3,20.00);//tuple<const char*,int,double>
```

- 访问tuple需要使用get函数，尖括号里面是index，从零开始：
```c++
auto book = get<0>(item);//返回item的第一个成员
get<2>(item) *=0.8;
```

- 查询tuple成员的数量和类型：
```c++
typedef decltype(item) trans;//trans是item的类型
size_t sz = tuple_size<trans>::value;//返回3
type_element<1,trans>::type cnt = get<1>(item);//cnt是一个int
```

### bitset类型

- bitset类是一个类模板，类似array类，具有固定大小。
```c++
bitset<32> bitvec(1U);//32位，低位是1，其他位是0
bitset<13> bitvec1(0xbeef);//二进制序列为1111011101111，高位被丢弃
bitset<20> bitvec2(0xbeef);//二进制序列为0001011111011101111,高位置0
bitset<128> bitvec3(~0ULL);//0~63位是1，64~127位是0。64位机器中，long long 0UL是64个0比特，因此~0ULL是64个1
bitset<32> bitvec4("1100");//2，3位为1，剩余两位是0
string str("1111111000000011001101");
bitset<32> bitvec5(str,5,4);//从str[5]开始的四个二进制位，1100
bitset<32> bitvec6(str,str.size()-4);//使用最后四位字符
```

- bitset支持的操作有any(),all(),none(),count(),size(),set(),reset(),flip(),to_ulong(),to_string()等操作

### 正则表达式

- C++正则表达式库(RE库)，Re库定义在头文件regex中。
- 一个例子，检查拼写规则："i除非在c之后，否则必须在e之前"的单词
```c++
string pattern("[^c]ei");//查找不在字符c之后的字符串ei
pattern = "[[:alpha:]]*"+pattern+"[[:alpha:]]*";
regex r(pattern);//构造一个用于查找模式的regex
smatch results;//定义一个对象保存搜索结果
string test_str="receipt freind theif receive";
if(regex_search(test_str,results,r))
{
    cout << results.str() << endl;
}
```
默认情况下，regex使用的正则表达式语言是ECMAScript。在ECMAScript中，模式[[::alpha:]]匹配任意字符，符号+和*分别表示"一个或者多个"或者”零个或者多个“匹配。

- 正则表达式的语言可以选择ECMAScript、basic(POSIX基本)、extended(POSIX扩展)、awk、grep、egrep的语法

- 一个正则表达式的语法是否正确是在运行时解析的。

- 输入序列和使用正则表达式类的对应关系如下：

输入序列|正则表达式类
:--: | :--:
string|regex、smatch、ssub_match和sregex_iterator
const char*|regex、cmatch、csub_match和cregex_iterator
wstring|wregex、wsmatch、wssub_match和wsregex_iterator
const wchar_t*|wregex、wcmatch、wcsub_match和wcregex_iterator

- 如果想得到正则表达式的所有匹配，需要使用迭代器。将一个sregex_iterator绑定到string和一个regex对象，迭代器自动定位到给定string的第一个匹配位置，递增迭代器查找下一个。在上面的例子进行改进：
```c++
string pattern("[^c]ei");//查找不在字符c之后的字符串ei
pattern = "[[:alpha:]]*"+pattern+"[[:alpha:]]*";
regex r(pattern,regex::icase);//构造一个用于查找模式的regex，匹配时忽略大小写
for(sregex_iterator it(file.begin(),file.end(),r),end_it;it !=end_it;++it)
{
    cout << it->str() << endl;
}
```
其中end_it是一个空的sregex_iterator，起到了尾迭代器作用。

- 匹配类型有两个名为prefix和suffix的成员，分别返回表示输入序列中当前匹配之前和之后不分的ssub_match对象。一个ssub_match对象有两个名为str和length的成员，分别返回匹配的string和该string的大小。

- 一个子表达式是模式的一部分，本身也有意义。
```c++
regex r("([[:alnum:]]+)\\.(cpp|cxx|cc)$",regex::icase);
```
其中([[:alnum:]]+)匹配一个活多个字符的序列，(cpp|cxx|cc)匹配文件名。
regex_search得到的smatch的str是一个数组，第一个子匹配位置为0，表示整个模式对应的匹配，随后是每个子表达式对应的匹配 。例如foo.cpp，则results.str(0)保存foo.cpp，results.str(1)保存foo，results(2)保存cpp。

- regex_replace可以在输入序列中查找并替换一个正则表达式。

### 随机数

- 定义在头文件random中的随机数库通过一组协作的类来解决这些问题。随机数引擎类和随机数分布类。引擎生成unsigned随机数序列，分布使用一个引擎类生成指定类型的、在给定范围内的、服从特定概率分布的随机数。

- 标准库定义了多个随机数引擎类，区别在于性能和随机性质量不同。每个编译器会定义其中一个作为default_random_engine类型。对于大多数场合，随机数引擎的输出是不能直接使用的。问题出在生成的随机数的值范围通常与我们需要的不相符，而正确转换随机数的返回是极其困难的。

- 均匀分布类型：uniform_distribution<unsigned> u(0,9)生成0到9（包含）均匀分布的随机数。
```c++
uniform_int_distribution<unsigned> u(0,9);
defautl_random_engine e;
for(size_t i =0;i<10;i++)
{
    cout << u(e) << " ";
}
```
传递给分布对象的是引擎对象本身，即u(e)。当我们说随机数发生器时，是指分布对象和引擎对象的组合。

- 一个给定的随机数发生器一定会生成相同的随机数序列。一个函数如果定义了局部的随机数发生器，应该将其（包括引擎和分布对象）定义为static的。否则，每次调用函数都会生成相同的序列。
```c++
vector<unsigned> good_randvec()
{
    static default_random_engine e;
    static uniform_int_distribution<unsigned> u(0,9);
    vector<unsigned> ret;
    for(size_t i = 0;i<100;i++)
        ret.push_back(u(e));
    return ret;
}
```
- 随机数发生器会生成相同的随机数序列这一特性在调试中很有用。但是，一旦我们的程序调试完毕，可以通过提供一个种子来使程序每次生成的结果不同。设置种子有两种方法：创建引擎的时候设置，或者调用seed函数设置。设置种子最常用的方法是调用系统函数time(定义在头文件ctime中)
```c++
default_randomw_engine e(time(0)）;//这种方式只适用于生成种子的间隔为秒级或者更长的应用。
```

- 使用uniform_real_distribution类型获得随机浮点数。uniform_real_distribution<double> u(0,1)

- 正态分布使用normal_distribution<> n(mean,sigma);//模板函数空尖括号，默认是double

- bernoulli_distribution是伯努利分布。它是一个普通类,不是函数模板，总返回一个bool值，概率默认值是0.5.

### IO库再探
三个IO库特性：格式控制、未格式化IO和随机访问

#### 格式化输入和输出

- 当操作符改变流的格式状态时，通常改变后的状态对所有后序IO都生效。

- 操作符hex、oct和dec只影响整型运算对象，浮点值的表示形式不受影响。

- 操作符setprecision和其他接受参数的操纵符都定义在头文件iomanip中。

#### 未格式化的输入/输出操作

- 标准库提供了一组低层操作，支持未格式化IO。这些操作允许我们将一个流当作一个无解释的字节序列来处理。

操作|解释
:--:|:--:
is.get(ch)|从istream is读取下一个字节存入字符ch中，返回is
os.put(ch)|将字符ch输出到ostream os。返回os
is.get()|将is的下一个字节作为int返回
is.putback(ch)|将字节ch放回is。返回is
is.unget()|将is向后移动一个字节。返回is
is.peek()|将下一个字节作为int返回，但不从流中删除它

#### 流随机访问

- 随机IO本质上是依赖于系统的。为了理解如何使用这些特性，必须查询系统文档。虽然标准库为所有流类型都定义了seek和tell函数。但是由于istream和ostream类型通常不支持随机访问，所以本节剩余内容只适用于fstream和sstream。

- seek函数可以给定一个位置来重定位流当前位置，tell可以返回标记的当前位置。

操作|解释
:--:|:--:
tellg()|返回一个输入流中标记的当前位置
tellp()|返回一个输出流中标记的当前位置
seekg(pos)|在一个输入流中标记重定位到给定的绝对地址。通常是前一个tellg()的返回值
seekp(pos)|在一个输出流中标记重定位到给定的绝对地址。通常是前一个tellp()的返回值

istream、ifstream和istringstream只能使用g版本，ostream、ofstream和ostringstream只能使用p版本，而iostream，fstream和stringstream既可以使用g版本又可以使用p版本。

- 标准库区分seek和tell，但它在一个流中只维护单一的标记，并不存在独立的读标记或者写标记。由于只有单一的标记，因此之哟啊我们在读写操作之间切换，就必须进行seek操作来重定位标记。


