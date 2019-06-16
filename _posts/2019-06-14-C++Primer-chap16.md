---
layout: post
title: C++ Primer 第十六章
categories: C++
description: C++ Primer
keywords: C++, Primer
---

模板与泛型编程

---

## 第十六章

### 定义模板
- 函数模板定义以关键字template开始，后跟一个模板参数列表，这是一个逗号分隔的一个或者多个模板参数的列表，用<>包围起来。在模板定义中，模板参数列表不能为空。

- 类型参数可以用来指定返回类型或者函数的参数类型，以及在函数体内用于变量声明或者类型转换：
```c++
template <typename T>
T foo(T* p)
{
    T temp = *p;
    return temp;
}
```
类型参数前必须使用关键字class或者typename，在函数模板中这两个关键字的含义相同，参数列表可以有多个参数，但是每个参数必须分别声明为class或者typename。

- 非类型模板参数，一个非类型参数表示一个值而非一个类型，这些值必须是常量表达式，可以在编译时实例化模板。
```c++
template<unsigned N,unsigned M>
int compare(const char (&p1)[N],const char(&p2)[M])
{
    return strcmp(p1,p2);
}
```
当调用
```c++
compare("hi","mom");
```
相当于调用如下版本:
```c++
int compare(const char (&p1)[3],const char (&p2)[4]);
```

- 函数模板声明为inline或者constexpr时，inline或者constexpr说明符放在模板参数列表之后，返回类型之前。

- 函数模板和类模板成员函数的定义通常放在头文件中。

- 默认情况下，对于一个实例化了的类模板，其成员只有在使用时才被实例化。

- 在一个类模板的作用域内，可以直接使用模板名而不必指定模板实参。如果在类模板外，必须使用类模板名。

- 当一个类包含一个友元声明时，类与友元各自是否是模板是相互无关的。如果一个类模板包含一个非模板友元，则友元被授权可以访问所有模板实例。如果友元自身是模板，类可以授权给所有友元模板实例(typename与类不同)，也可以只授权给特定实例(例如，typename与类相同)。

- 由于模板不是一个类型，不能定义一个typedef引用一个模板。但是在C++11中可以为类模板定义一个类型别名：
```c++
template<typename T> using twin = pair<T,T>;
twin<stirng authors;//authors是一个pair<string,string>
```
当定义一个模板类型别名时，可以固定一个或者多个模板参数：
```c++
template<typename T> using partNo = pair<T,unsigned>;
partNo<string> books;//pair<stirng,unsigned>
```

- 在模板内不能重用模板参数名，所以一个模板参数名在一个特定模板参数列表中只能出现一次。

- 模板声明必须包含模板参数，一个特定文件所有需要的所有模板的声明通常一起放置在文件开始位置，出现于任何使用这些模板的代码之前。

- 当告知编译器一个名字表示类型时，必须使用关键字typename，而不能使用class。

- C++11中可以为函数模板和类模板提供默认实参，而更早的C++标准只能为类模板提供默认实参。
```c++
template <typename T, typename F = less<T>>
int compare(const T& t1, const T& t2,F f = F())
{
    if(f(v1,v2)) return -1;
    if(f(v2,v1)) return 1;
    return 0;
}
```
与函数默认实参一样，对于一个模板参数，只有当它右侧的所有参数都有默实参时，它才可以有默认实参。

- 无论何时使用类模板，都必须在模板名之后接上尖括号，尖括号指出类必须从一个模板实例化而来。特别的，如果一个类模板为其所有模板参数都提供了默认实参，且我们希望使用这些默认实参，就必须在模板名之后跟一个空的尖括号。
```c++
template<class T = int>
class Numbers
{
    Numbers(T v = 0):val(v){}
    private:
    T val;
}
Numbers<long double> lots_of precision;
Numbers<> average_precision;
```

- 一个类（无论是普通类还是类模板）可以包含本身是模板的成员函数。这种成员被称为成员模板。成员模板不能是虚函数。

- 类模板的成员模板：
```c++
template <typename T> class Blob
{
    template <typename It> Blob(It b,It e);
    //...
    private:
    std::shared_ptr<std::vector<T>> data;
};
```
则在类外定义时，形式如下：
```c++
template<typename T>
template<typename It>
Blob<T>::Blob<It b,It e>:data(std::make_shared<std::vector<T>>(b,e))
{
}
```

- 在大系统中，在多个文件中实例化相同模板的额外开销可能非常严重。C++11中可以通过显式实例化来避免这种开销。
```c++
extern template declaration；//实例化声明
template declaration;//实例化定义
```
由于编译器在使用一个模板时会自动对其实例化，因此extern声明必须在任何使用此实例化版本的代码之前。对每个实例化声明，在程序中某个位置必须有其显式的实例化定义。

- 在一个类模板的实例化定义中，所有类型必须能用于模板的所有成员函数。与处理类模板的普通实例化不同。

### 模板实参推断

- 将实参传递给带模板类型的函数形参时，能够自动应用的类型转换只有const转移以及数组或函数到指针的转换。

- 如果函数参数类型不是模板参数，则对实参进行正常的类型转化。

- 指定显示模板实参：
```c++
template <typename T1,typename T2,typnname T3>
T1 sum(T2,T3);
```
这个例子中，没有任何函数实参的类型可用来推断T1的类型。每次调用sum时调用者必须为T1提供一个显式模板实参。显式模板实参在尖括号中给出。
```c++
int i;
long lng;
auto val3 = sum<long long>(i,lng);// long long sum(int ,long)
```
显式模板实参按照从左到右的顺序与对应的模板参数匹配，只有尾部参数的显示模板实参才可以忽略，而且前提是它们可以从函数参数推断出来。

- 由于尾置返回类型可以出现在函数列表之后，它可以使用函数的参数：
```c++
template <typename It>
auto fcn(It beg,It end)->decltype(*beg)
{
    return *beg;//返回的是迭代器的引用
}
```

- 标准类型转换模板。可以用remove_reference来获得元素类型。remove_reference模板有一个模板类型参数和一个名为type的类型成员。例如，实例化remove_reference<int&>，则type将得到int类型。则上面的例子结合remove_reference就可以得到元素值的拷贝。
```c++
template<typename It>
auto func(It beg,It end)-> typename remove_reference<delctype(*beg)>::type
{
    return *beg;
}
```
标准类型转换模板列表如下：
![标注模板转换列表](/assets/images/chap16-convert.jpg)

- 当参数是一个函数模板实例的地址时，程序上下文必须满足：对每个模板参数，能唯一确定其类型或者值。

- 模板实参推断和引用。编译器会应用正常的引用绑定规则：const是底层的，不是顶层的。
   - 当模板类型参数是一个普通左值引用(T&)，只能传递一个左值。实参可以是const，也可以不是。如果实参是const，则T被推断为const。
   - 当模板类型参数是一个类型的const T&，可以传递一个对象，一个临时对象或者一个字面常量值。如果函数参数本身是const，T的推断类型的结果不会是const类型。const已经是函数参数类型的一部分。
   - 当模板类型参数是一个右值引用(T&&)，可以传递一个右值。类似普通左值引用函数参数的推断过程，推断出的T的类型是该右值实参的类型。
   ```c++
   template <typnname T> void f(T&&);
   f(42);//实参是一个int型右值，则T是int
   ```
   - 当传递一个左值给函数的右值引用参数，且此右值引用指向模板类型参数(T&&)时，编译器推断模板类型参数为实参的左值引用类型。则f(i)得到T是int&。通常不能定义引用的引用，但是通过类型别名或者通过模板类型参数可以间接定义。
   - 如果间接创建了一个引用的引用，这些引用会形成”折叠“，
      - X& & 、X& && 、和X&& &都折叠成X&
      - 类型X&& &&折叠成X&&。

- 使用右值应用的函数模板通常要重载函数，如下：
```c++
template <typename T> void f(T&&);//绑定到非const右值
template <typename T> void f(const T&);//左值和const右值
```

- std::move是使用右值引用的模板的一个很好的例子：
```c++
template <typename T>
typename <typename T>
typename remove_reference<T>::type&& move(T&& t)
{
    return static_cast<typename remove_reference<T>::type&&>(t);
}
```
从一个左值static_cast到一个右值引用是允许的。

- 如果一个函数参数是指向模板类型参数的右值引用(T&&)，它对应的实参的const属性和左值/右值属性将得以保持。

- forward可以保持原始实参的类型。类似move，forward也定义在utility中。与move不同，forward必须通过显式模板实参来滴啊用。forward返回该显式实参类型的右值引用。即，forward<T>的返回类型是T&&。

- 当用于一个指向模板参数类的右值引用函数参数(T&&)时，forward会保持实参类型的所有细节。

- 与std::move相同，对std::forward不使用using声明是一个好主意。

### 重载与模板

- 当有多个重载模板对一个调用提供同样好的匹配时，应该选择最特例化的版本。

- 对于一个调用，如果一个非函数模板与一个函数模板提供同样好的匹配，则选择非模板版本。

- 在定义任何函数之前，记得声明所有重载的函数版本。这样就不必担心编译器由于未遇到你希望调用的函数而实例化一个并非你需要的版本。

### 可变参数模板
- 一个可变参数模板是一个接受可变数目参数的模板函数或者模板类。可变数目的参数被称为参数包。存在两种参数包：模板参数包表示零个或者多个模板参数；函数参数包表示零个或者多个函数参数。
```c++
template<typename T,typename...Args>
void foo(const T& t,const Args& ...rest);
```
则如下的调用：
```c++
int i = 0;double d= 3.14 string s = "how now brown cow";
foo(i,s,42,d);//void foo(const int&,const string&,const int&,const double&)
foo(s,42,"hi");//void for(const string&,const int&,const char[3]&)
```

- 当需要知道包中有多少个元素时，可以使用sizeof...运算符。类似sizeof，sizeof...也返回常量表达式，而且不会对其实参求值：
```c++
template<typename ... Args> void g(Args ... args)
{
    cout << sizeof...(Args) << endl;//类型参数的数目
    cout << sizeof...(args) << endl;//函数参数的数目
}
```

- 可变参数函数通常是递归的。为了终止递归，需要定义一个非尅版参数的函数，以print为例：
```c++
template<typename T>
ostream& print(ostream& os ,const T& t)
{
    return os << t;
}
template<typename T,typename... Args>
ostream& print(ostream& os ,const T& t,const Args&...rest)
{
    os << t << ", ";
    return print(os,rest...);
}
```
当定义可变参数版本的print时，非可变参数版本的声明必须在作用域中。否则，可变参数版本会无限递归。
- print中的函数参数包扩展仅仅将包扩展为其构成元素，C++还允许更复杂的扩展模式。丽日：
```c++
template <typename... Args>
ostream& errorMsg(ostream&,const Args&... rest)
{
    return print(os,func(rest)...);
}
//如下调用：
errorMsg(cerr,funcName,code.num(),otherData,"other",item);//errorMsg(func(cerr),func(funcName),func(code.num()),func(otherData),func("other"),func(item));
```
注意print(os,func(rest)...)与print(os,func(rest...))的区别。

- C++11中，可以结合使用可变参数模板与forward机制来编写函数，实现将其实参不变地传递给其他函数。例子如下：
```c++
class StrVec
{
    public:
    template<typnname... Args>void emplace_back(Args&&...);
};
template<typename... Args>
inline
void StrVec::emplace_back(Args&&... args)
{
    chk_n_alloc();
    alloc.construct(first_free++,std::forward<Args>(args)...);
}
```

### 模板特例化
- 当特例化一个函数模板时，必须为圆模板中的每个模板参数都提供实参。为了指出我们正在实例化一个模板，应该使用关键字template后跟一个空尖括号对<>。空尖括号指出我们将为原模板的所有模板参数提供实参。
```c++
//特例化：template<typename T> int compare(const T&,const T&);
template<>
int compare<const char* const &p1,const char* const &p2)
{
    return strcmp(p1,p2);
}
```
在此例子中，T为const char*。一个指针类型的const版本是一个常量指针而不是指向const类型的指针。我们需要在特例化版本中使用的类型是const char* const&,即一个指向const char的const指针的引用。

- 特例化的本质是实例化一个模板，而非重载它。因此，特例化不影响函数匹配。

- 模板以及特例化版本应该声明在同一个头文件中。所有同名模板的声明应该放在前面，然后是这些模板的特例化版本。

- 与函数模板不同，类模板的特例化不必为所有模板参数提供实参。我们可以只指定一部分而非所有模板参数。

- 可以只特例化特定成员函数而不是特例化整个模板。如果Foo是一个模板类，包含一个成员Bar，可以只特例化该成员：
```c++
template<typename T> struct Foo
{
    Foo(const T& t = T()):mem(t){}
    void Bar(){}
    T mem;
};
template<>//正在特例化
void Foo<int>::Bar()
{
    //do some thing
}
```
本例中我们只特例化Foo<int>类的一个成员，其他成员将由Foo模板提供。






