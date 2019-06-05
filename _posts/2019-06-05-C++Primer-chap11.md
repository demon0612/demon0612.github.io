---
layout: post
title: C++ Primer 第十一章
categories: C++
description: C++ Primer
keywords: C++, Primer
---

关联容器

---

## 第十一章

### 关联容器概述 
- map可以使用列表初始化，pair用花括号括起来
```c++
map<string,string> authors={ {"A","1"},{"B","2"},{"C","3"}};
```
- 有序的map或者set，默认的排序使用的是小于等于，可以自定义。
```c++
bool compare(const struct_data& lhs,const struct_data& rhs)
{
    return lhs.iType < rhs.iType;
}
multiset<struct_data,decltype(compare)*> name(compare);
```

- pair类型定义在utility中，pair中的数据成员如果没有初始化，使用默认初始化。可以使用花括号进行初始化：
```c++
pair<string,string> autho{"James","Joyce"};
```
创建pair的函数：
```c++
pair<string,int> process(vector<string>& v)
{
    if(!v.empty())
    {
        return {v.back(),v.back(),size()};//C++11
        //return pair<string,int>(v.back(),v.back().size()); C++11之前的版本
    }
    else
    {
        return pair<stirng,int>();//隐式构造函数
    }
}
```

### 关联容器操作
- 关联容器额外的类别名称

类型|解释
:--: | :--:
key_value|此容器类型的关键字类型
mapped_value|每个关键字关联的类型，只用于map
value_type|对于set，与key_type相同，对于map，为pair<const key_type,mapped_type>

- set迭代器是const的，虽然有iterator和const_iterator类型，但是两个类型都只能访问set中的元素，因为set中没有value，只有key，set和map的key都是const，无法修改，只能读取。

- 泛型算法通常不用于关联容器，因为关键字是const，则泛型算法中修改或者重排容器元素的算法都不能使用。泛型算法find算法的速度不如关联容器定义的专用的find成员效率高，前者是遍历，后者是红黑树查找。

- insert或者emplace返回的值依赖于容器类型和参数，对于不包含重复关键字的容器，添加单一元素的insert和emplace返回一个pair，first是成员迭代器，指向给定关键字的源于，second是个bool，指出元素是否成功插入到容器中。如果关键字已经在容器中，则insert什么都不做，且返回值中的bool为false，如果关键字不存在，则元素插入容器中，且bool为true。

- erase对于map和set，返回总是0或者1，返回0表示erase的元素不在关联容器中。对于multiset或者multimap，erase的值可能大于1。

- set,multiset,unordered_set，unordered_multiset不支持下标，map和unordered_map可以下标，multimap和unordered_multimap也不支持下标操作。

- 当对map进行下标操作，会得到一个mapped_value,当解引用一个map迭代器，会得到一个value_type对象。

- find和count，对于不允许重复关键字的容器相同，对于可重复关键字的容器，count可以做更多事情。lower_bound和upper_bound不能用于无序容器(unordered)

- 如果lower_bound和upper_bound返回相同的迭代器，则给定关键字不在容器中。
- euqal_range，解释一个关键字，返回一个迭代器pair，若关键字存在，则第一个迭代器指向第一个与关键字匹配的元素，第二个迭代器指向最后一个匹配元素之后的位置。若未找到匹配元素，则两个迭代器都指向关键字可以插入的位置。
```c++
for(auto beg = authors.lower_bound(search_item),end=authors.upper_bound(search_item);beg!=end;++beg)
{
    cout << beg->second << end;
}
//以下的版本与上面相同
for(auto pos = authors.equal_range(serach_time);pos.first!=pos.second;++pos.first)
{
    cout << post.first->second << endl;
}
```

### 无序容器

- 无序容器在存储上组织为一个桶，每个桶保存零个或者多个元素。无序容器使用一个哈希函数将元素映射到桶。为了访问一个元素，容器首先计算元素的hash值，它指出应该搜索哪个桶。容器将具有一个特定哈希值的所有元素都保存在相同的桶中。如果容器允许重复关键字，所有具有相同关键字的元素也都会在同一个桶中。无序容器的性能依赖于哈希函数的质量和桶的数量和大小。

- 无序容器使用关键字类型的==运算符来比较元素，使用一个hash<key_type>类型的对象来生成每个元素的哈希值。标注库为内置类型、指针、string、智能指针提供了hash，对于自定义类型的无序容器，需要提供自己hash模板。
```c++
size_t hasher(const Sales_data& sd)
{
    return hash<string>()(sd.isbn());
}
bool eqOp(const Sales_data& lhs,const Sales_data& rhs)
{
    return lhs.isbn()==rhs.isbn();
}
// 自定义类型
using SD_Multiset = unordered_multiset(Sales_data,decltype(hasher)*,decltype(eqOp)*);
```

假设如果定义了Foo类型的hash函数FooHash，则
```c++
unordered_set<Foo,delctype(FooHash)*> fooSet(10,FooHash);
```
