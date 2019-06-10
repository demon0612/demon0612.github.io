---
layout: post
title: C++ Primer 第十四章
categories: C++
description: C++ Primer
keywords: C++, Primer
---

重载运算与类型转换

---

## 第十四章

### 基本概念

- 当一个重载运算符是成员函数时，this绑定到左侧运算对象。成员运算符的(显式)参数数量比运算对象的数量少一个。

- 对于一个运算符函数来说，它或者是类的成员，或者至少含有一个类类型的参数。不能重载的运算符，::，.*，., ?:。

- 通常情况下，不应该重载逗号(无法保证求值顺序)、取地址、逻辑与(无法利用短路)、逻辑或(无法利用短路)运算符

- 成员函数or非成员函数
   - 赋值(=)、下标([])、调用(())和成员访问箭头(->)运算符必须是成员。
   - 复合赋值运算符一般来说是成员，但并非必须，这一点与赋值运算符略有不同。
   - 改变对象状态的运算符或者与给定类型密切的运算符，如递增、递减和解引用运算符，通常应该是成员。
   - 具有对称性的运算符可能转换任意一端的运算对象，例如算术、相等性、关系和位运算符，因此它们应该是普通的非成员函数。

### 输入和输出运算符

- 通常，输出运算符应该主要负责打印对象的内容而非控制格式，输出运算符不应该打印换行符。

- 输入输出运算符必须是非成员函数。IO运算符通常需要读写类的非公有数据成员，所以IO运算符一般被声明为友元。

- 输入运算符必须处理输入可能失败的情况，而输出运算符不需要。当读取操作发生错误时，输入运算符应该负责从错误中恢复。
```c++
istream& operator<<(istream& is,Sales_data& item)
{
    double price;
    is >> item.bookNo >> item.units_sold >> price;
    if(is)
    {
        item.revenue = item.units_sold*price;
    }
    else
    {
        item = Sales_data();
    }
}
```

### 算术和关系运算符

- 通常情况下，会把算术和关系运算符定义成非成员函数以允许对左侧或者右侧的运算对象进行转换。因为这些运算符一般不需要改变运算对象的状态，所以形参通常都是常量引用。

- 如果类同时定义了算术运算符和相关的复合赋值运算符，通常情况下应该使用复合赋值来实现算术运算符。

- 如果类定义了operator==，则这个类也应该定义operator!=。

- 如果存在唯一一种逻辑可靠的<定义，则应该考虑为这个类定义<运算符。如果类同时还包含==，则当且仅当<的定义和==产生的结果一致时才定义<运算符。

- 如果重载赋值运算符，则无论形参的类型是什么，赋值运算都必须定义为成员函数。复合赋值运算符也应该定义为类的成员，这两类运算符都返回左侧运算对象的引用。

- 下标运算符必须是成员函数。

- 如果一个类包含下标运算符，通常要定义两个版本：一个返回普通引用，另一个是类的常量成员并返回常量引用。
```c++
class StrVec
{
public:
    std::string& operator[](std::size_t n)
    {
        return elements[n];
    }
    const std::string& operator[](std::size_t n) const
    {
        return elements[n];
    }
private:
    std::string* elements;
};
```

- 定义递增和递减运算符的类应该同时定义前置版本和后置版本。这些运算符通常被定义为类成员。
```c++
class StrBlobPrt
{
public:
    //前置版本，应该返回递增或者递减之后对象的引用
    StrBlobPtr& operator++();
    StrBlobPtr& operator--()；
    //后置版本，返回的是一个值而非引用。int类型的形参，这个形参的唯一作用就是区别前置版本和后置版本的函数，不会用到int参数，无需为其命名。
    StrBlobPtr operator++(int);
    StrBlobPtr operator--(int)；
};
```
显式的调用前置/后置递加/递减运算符，则方法如下：
```c++
StrBlobPtr p(al);
p.operator++(0);//调用后置版本的operator++
p.operator++();//调用前置版本的operator++
```

- 箭头运算符必须是类的成员，解引用运算符通常也是类的成员，尽管并非必须如此。

- 重载的箭头运算符必须返回类的指针或者自定义了箭头运算符的某个类的对象。

### 函数调用运算符

- 函数调用运算符必须是成员函数。一个类可以定义多个不同版本的调用运算符，相互之间应该在参数数量或类型上有所区别。

- 标准库在functional头文件中定义了一组算术运算符、关系运算符和逻辑运算符，例如plus执行加操作，modules类定义二元取余(%)操作，equal_to类执行==。
```c++
plus<int> intAdd;//执行int加法的函数
negate<int> intNegate;//对int取反
int sum = intAdd(10,20);// sum = 30
sum = intNegate(intAdd(10,20));//sum = -30
sum = intAdd(10,intNegate(10));//sum = 0 
```
可以直接在算法中使用标准库函数。例如，默认情况排序算法使用operator<将序列按照升序排列。如果要执行降序，可以传入一个greater类型：
```c++
sort(svec.begin(),svec.end(),greate<string>());
```
sort如果是对指针进行排序，将会得到错误结果，但是将指针传入标准库函数却可以：
```c++
vector<string*> nameTable;
sort(nameTable.begin(),nameTable.end(),[](string* a, string*b){return a<b;});//错误
sort(nameTabel.begin(),nameTable.end(),less<string*>());
```
关联容器使用less<key_type>对元素进行排序，可以定义一个指针的set或者在map中使用定义作为关键字而无须直接声明less。

- 调用形式指明了调用返回的类型以及传递给调用的实参类型。一种调用类型对应一个函数类型，例如：
```c++
int (int,int)
```
是一个函数类型，接受两个int，返回一个int。

不同该类型可能就有相同的调用形式：
```c++
//普通函数
int add(int i,int j) {return i+j;}
//lambda，产生一个未命名的函数对象类
auto mod = [](int i,int,j) {return i%j;};
//函数对象类
struct divide
{
    int operator() (int denominator,int divisor)
    {
        return denominator/divisor;
    }
};
```
以上这三个可调用对象，尽管类型不同，但是共享同一种调用形式。如果想用函数表用来存储这些可调用对象的指针，则可以使用map，定义如下：
```c++
map<string,int(*)(int,int)> binops;
binops.insert({"+",add});//OK
binops.insert({"%",mod});//错误的，因为mod不是一个函数指针。这时候需要用到标准库function类型
```

function定义在functional头文件中。function是一个模板。则上面的例子可以写成：
```c++
function<int(int,int)> f1= add;
function<int(int,int)> f2= divide();
function<int(int,int)> f3= [](int i,int j){return i*j;};
```
则map可以定义为：
```c++
map<string,function<int(int,int)> binops = {
    {"+",add},
    {"-",std::minus<int>()},
    {"/",divide()},
    {"*",[](int i,int j) {return i*j;}},
    {"%",mod}
};
```

### 重载、类型转换与运算符

- 转换构造函数和类型转换运算符共同决定了类类型转换，这样的转换有时被称作用户定义的类型转换。

- 类型转换运算符是类的一种特殊成员函数，声明如下：
```c++
operator type() const;
```
其中type表示某种类型。类型转换运算符可以面向任意类型(除了void)进行定义，只要改类型能作为函数的返回类型。因此，不允许转换成数组或者函数类型，单允许转换成指针（包括数组指针以及函数指针）或者引用类型。

- 一个类型转换函数必须是类的成员函数，不能声明返回类型，形参列表页必须为空，类型转换函数通常是const的。

```c++
class SmallInt
{
    public:
    SmallInt(int i=0):val(i)
    {
        if(i<0|| i>255)
            throw std::out_of_range("Bad SmallInt value");
    }
    operator int() const {return val;}

    private:
    std::size_t val;
};
```
则如下调用：
```c++
SmallInt si;
si = 4;//将4隐式转换成SmallInt，然后调用SmallInt::operator==
si + 3;//首先将si隐式转换成int，然后执行整数加法
```

- 为了防止异常类型转化，C++11新标准引入了显式的类型转换运算符,
```c++
classs SmallInt
{
    public:
    explicit operator int() const {return val;}//显式构造函数
}
SmallInt si = 3; //正确，SmallInt的构造函数不是显式的
si + 3;//错误，类无法进行隐式转化
static_cast<int>(si)+3;//正确，显式类型转换
```
以上情况存在一个例外，如果表达式被用作条件，则编译器会将显式的类型转换自动应用于它。

- 向bool的类型转换通常用在条件部分，因此operatro bool一般定义成explicit的。

- 14.9.2


