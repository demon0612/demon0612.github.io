---
layout: post
title: C++ Primer 第十三章
categories: C++
description: C++ Primer
keywords: C++, Primer
---

拷贝控制

---

## 第十三章

类的五种特殊成员函数：拷贝构造函数、拷贝赋值运算符、移动构造函数、移动赋值运算符和析构函数。

### 拷贝、赋值和销毁

- 拷贝构造函数：第一个参数是自身类类型的引用，且任何额外参数都有默认值，则此构造函数是拷贝构造函数。注意，其中第一个参数必须是自身引用，而且一般是const，拷贝构造函数不应该是explicit的。如果没有定义，编译器会自动生成。
```c++
class Foo
{
    public:
        Foo();//默认构造函数
        Foo(const Foo&);//拷贝构造函数
}
```

- 拷贝初始化不仅在用=定义变量时会发生，在以下情况也会发生：
   - 将一个对象作为实参传递给一个非引用类型的形参
   - 从一个返回类型为非引用类型的函数返回一个对象
   - 用花括号列表初始化一个数组中的元素或者一个聚合类中的成员。

- 如果类未定义自己的拷贝赋值运算符，编译器会生成一个。赋值运算符通常会返回一个指向其左侧运算对象的引用。
```c++
class Foo
{
    public:
        Foo& operator=(const Foo&);//赋值运算符
}
```

- 当指向一个对象的引用或者指针离开作用域时，析构函数不会执行。

- 如果一个雷需要自定义析构函数 ，几乎可以肯定它也需要自定义拷贝赋值运算符和拷贝构造函数。

- 如果一个类需要一个拷贝构造函数，几乎可以肯定，他也需要要给拷贝赋值运算符。反之亦然。无论是否需要拷贝构造函数还是需要拷贝赋值运算符都不必然意味着也需要析构函数。

- 可以通过将拷贝控制成员定义为=default(和定义默认构造函数一样)，要求编译器生成合成的版本。

- C++11可以定义删除的函数(=delete)，在函数参数列表后面加上=delete，来指出将它定义为删除的，任何函数都可以是删除的函数，除了析构函数。

- 如果想阻止拷贝构造函数和拷贝赋值运算符，应该讲声明定义为private。进一步为了方式成员函数或者友元调用，可以只声明但不定义这两个函数。最好使用=delete，而不应该声明为private。

### 拷贝控制和资源管理

#### 行为像值的类
- 赋值运算符通常组合了析构函数和构造函数的操作。赋值操作会销毁左侧运算符的资源(析构函数)，赋值操作会从右侧运算对象拷贝数据(拷贝构造函数)。这种操作顺序很重要。如果涉及到指针操作，在释放左侧的指针时，做好判断等号左侧指针与右侧指针指向的是不同的内存，否则会出现错误。
```c++
class HasPtr
{
public:
    //构造函数
    HasPtr(const std::string& s= std::string()):
        ps(new std::string(s),i(0){}
    /拷贝构造函数
    HasPtr(const HasPtr& p):
        ps(new std::string(*p.si)),i(p.i){}
    //拷贝赋值运算符
    HasPtr& operator=(const HasPtr&);
    //析构函数
    ~HasPtr() {delete ps;}
private:
    std::string *ps;
    int i;
};
//正确的拷贝构造函数
HasPtr& HasPtr::operator=(const HasPtr& rhs)
{
    auto newp = new string(*rhs.ps);
    delete ps;
    ps = newp;
    i=rhs.i;
    return *this;
}
//错误的代码
HasPtr& HasPtr::operator=(const HasPtr& rhs)
{
    delete ps;
    ps = new string(*(rhs.ps));
    i=rhs.i;
    return *this;
}
```

错误代码中，如果rhs和本对象是同一个对象，delete ps会释放*this和rhs指向的string，rhs就会变成一个悬空的指针。这个解决办法是，先判断rhs是否和this相容，如果相同，直接返回。

#### 定义行为像指针的类
- 令一个类展现类似指针的行为的最好方法就是使用shared_ptr来管理类中的资源。也可以使用引用计数器来编写类指针版本的HasPtr
```c++
class HasPtr
{
public:
    //构造函数
    HasPtr(const std::string &s= std::string()):
        ps(new std::string(s),i(0),use(new std::size_t(1)){}
    //拷贝构造函数
    HasPtr(const HasPtr& p):
        ps(p.ps),i(p.i),use(p.use){++*use;}
    ~HasPtr();
private:
    string:string *ps;
    int i;
    std::size_t *use;//记录多少个成员对象共享*ps
};
HasPtr::~HasPtr()
{
    if(--*use==0)
    {
        delete ps;
        delete use;
    }
}
HasPtr& HasPtr::operator=(const HasPtr& rhs)
{
    ++*rhs.use;//增加右侧运算对象的引用次数
    if(--*use==0)//递减本对象的引用次数，如果没有其他用户，释放本对象
    {
        delete ps;
        delete use;
    }
    ps = rhs.ps;//将数据从rhs拷贝到本对象
    i = rhs.i;
    use =rhs.use;
    return *this;
}
```

### 交换操作
- 如果一个类定义了自己的swap，那么算法将实用类自定义版本，否则使用std::swap。通常为了交换两个对象，需要一次拷贝和两次赋值。例如：
```c++
HasPtr tmpe = v1;
v1= v2;
v2=temp
```
这段代码中将v1中的string拷贝了两次，有些浪费资源，可以编写自己版本的swap：
```c++
class HasPtr{
    friend void swap(HasPtr&, HasPtr&);
};
inline void swap(HasPtr& lhs, HasPtr& rhs)
{
    using std::swap;
    swap(lhs.ps,rhs.ps);
    swap(lhs.i,rhs.i);
}
```
记住，很重要的一点，虽然我们声明了std::swap，但是不一定会使用std::swap！！如果存在类型特定的swap版本，swap调用会与之匹配，如果不存在特定版本，则使用std版本(假定作用域中有using声明)

拷贝并交换技术：
```c++
HasPtr& HasPtr::operator=(HasPtr rhs)
{
    swap(*this,rhs);
    return *this;
}
```
参数不是引用，rhs是右侧对象的一个部分，swap将this指向这个副本，而rhs指向了原先的this。rhs是局部变量，离开作用域时会析构。这个技术自动处理了自赋值情况且天然就是异常安全的。

### 对象移动
- 标准库容器、string和shared_ptr类既支持移动也支持拷贝。IO类和unique_ptr类可以移动但不能拷贝。

#### 右值引用
- 绑定到右值的引用就是右值引用，使用&&而不是&表示。右值应用有一个重要的性质，就是只能绑定到一个将要销毁的对象。
```c+++
int i=42;
int &r = i;//OK
int &&r = i;//错误，不能将一个右值引用绑定到一个左值上
int &r2 = i*42;//错误，i*42是一个右值
const int &r3 = i*42;//OK,可以将const引用绑定到一个右值上
int &&rr2 = i*42;//OK，将rr2绑定绑定到乘法结果上。
```
- 左值有持久的状态，而右值要么是字面常量，要么是在表达式求值中创建的临时变量。

- utility头文件中的std::move函数可以将一个左值转换成一个右值。可以销毁一个移后原对象，也可以赋予它新值，但是不能使用一个移后原对象的值。

- 定义移动构造或者移动赋值，通常不抛出任何异常。应该使用noexcept通知编译器，同时必须在头文件的声明和定义中都指定noexcept。
```c++
class StrVec
{
    public:
    StrVec(StrVec&&) noexcept;//移动构造函数
};
StrVec::StrVec(StrVec&& s) noexcept;
{}
```

- 移动赋值运算符执行与析构函数和移动构造函数相同的工作。类似拷贝赋值运算符，移动赋值运算符必须处理自赋值：
```c++
StrVec& StrVec::operator=(StrVec&& rhs) noexcept
{
    if(this!= &rhs)
    {
        free();
        elements=rhs.elements;
        first_free = rhs.first_free;
        cap = rhs.cap;
        rhs.elements = rhs.first_free = rhs.cap = nullptr;
    }
    return *this;
}
```

- 移动操作之后，移后源对象必须保持有效的，可析构的状态，但是用户不能对其值进行任何假定。

- 只有当一个类没有定义任何自己版本的拷贝控制成员，且它的所有数据成员都能移动构造或者移动赋值时，编译器才会为它合成移动构造函数或者移动赋值运算符。

- 定义了一个移动构造函数后者移动运算符的类必须也定义自己的拷贝操作。否则，这些成员默认地被定义为删除的。

- 区别移动和拷贝的重载函数通常有一个版本接受一个const T&，而另一个版本接受一个T&&。

#### 引用限定符
- 可以是&或者&&，分别指出this可以指向一个左值或右值，类似const限定符，引用限定符只能用于(非static)成员函数，且必须同时出现在函数的声明和定义中。
定义如下：
```c++
class Foo{
    public:
    Foo& operator=(const Foo&) &;//只能向可修改的左值进行赋值
};
Foo& Foo::operator=(const Foo& rhs) &
{
    return *this;
}
```
对于&限定的函数，只能讲它用于左值，对于&&限定的函数，只能用于右值：
```c++
Foo &retFoo();// 返回一个引用，retFoo调用是一个左值
Foo retVal();// 返回一个值；retVal调用是一个右值
Foo i,j;
i=j;//OK,i是左值
retFoo()=j;//正确，refFoo()返回一个左值
retVal()=j;//错误，retVal()返回一个右值
i=retVal();//正确，将一个右值作为赋值操作的右侧运算对象
```
可以同时使用const和引用限定，引用限定符必须跟随在const之后。
```c++
class Foo
{
    public:
    Foo someMen() const &;
};
```

引用限定符也可以用来区分重载版本。当定义const重载函数时，唯一的差别就是一个版本有const限定，而另一个没有。引用限定则不一样。如果定义两个或者两个以上具有相同名字和相同参数列表的成员函数，必须对所有函数都加上引用限定符，或者都不加：
```c++
class Foo
{
    public:
    Foo sorted() &&;
    Foo sorted() const;//错误，必须加上引用限定符
    using Comp =bool(const int&,const int*);
    Foo sorted(Comp*);//OK，不同的参数列表
    Foo sorted(Comp&) const;//OK，两个版本都没有引用限定符
};
```
如果一个成员函数有引用限定符，则具有相同参数列表的所有版本都必须有引用限定符。























