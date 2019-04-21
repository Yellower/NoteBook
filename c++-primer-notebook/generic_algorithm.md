# 泛型算法

标准库为每个容器定义了一组泛型算法，它能够进行排序、搜索等操作，同时它们可以用于不同类型的元素和多种容器类型（包括**内置的数组类型**）。

## 1. 概述

大部分泛型算法定义在头文件algorithm中。这些算法不直接操作容器，面是遍历由两个迭代器指定的一个元素范围来操作（不包括第二个迭代器所指元素）。

**注意：算法永远不会执行容器的操作，永远不会直接添加或删除元素**

## 2. 只读算法

### find

```c++
int val = 42;
auto result = find(vec.cbegin(), vec.cend(), val);
```

如果范围内的元素与查找值匹配，则返回指向第一个等于查找值的迭代器。如果查找失败，则返回第二个迭代器。可以通过判断返回结果是否有第二个迭代器相等来判断是否查找成功。

find可以在数组中查找：

```++
int ia[] = {27, 210, 12, 47, 109, 83};
int val = 83;
int* result = find(begin(ia), end(ia), val);
```

### accumulate

定义在头文件numeric中。

```c++
int sum = accumulate(vec.cbegin(), vec.cend(), 0); //第三个参数为和的初值
```

```c++
string sum = accumulate(v.cbegin(), v.cend(), string("")); //显示的创建一个string，如果传递""，对象类型为const char*,而该类型没有定义+运算符
```

### 3. 写容器算法

**注意：算法不会检查写操作，向目的位置迭代器写入数据的算法假定目的位置足够大**

改变容器大小时可用`c.resize()`，不能使用`c.reserve()`，它只是分配内存空间，并不会初始化这么多个元素。

常用的的方法是使用**back_inserter**生成插入迭代器，通过插入迭代器来指明写操作的目的位置。

```c++
vector<int> vec;
auto it = back_inserter(vec); 
*it = 42; //对插入迭代器赋值会调用push_back添加元素

fill_n(back_inserter(vec), 10, 0); //插入10个0
```

### copy

也是写操作，所以必须保证目的容器至少有与输入容器一样多的元素。

```c++
int a1[] = {0,1,2,3,4,5,6,7,8,9};
int a2[sizeof(a1)/sizeof(*al)]; //a2与aq大小一样
auto ret = copy(begin(a1), end(a1), a2);
```

### replace与replace_copy

很多算法都提供了拷贝的版本，这种版本的函数名带有_copy。通过提供一个目的容器的迭代器，将算法的结果拷贝到目的容器中。

```c++
//replace
replace(ls.begin(), ls.end(), 0, 42);//直接在原序列上将所有的0替换成42
//replace_copy
replace_copy(ls.begin(), ls.end(), back_inserter(vec), 0, 42); //ls未变化，将替换后的结果拷贝到vec中了
```

## 4. 重排容器的算法

### sort和unique

```c++
vector<string> words={"the","quick","red","fox","jumps","over","the","slow","red","turtle"};
sort(words.begin(), words.end());
auto end_it = unique(words.begin(), words.end());//将重复的元素排在容器后面，返回指向第一个重复元素的迭代器
words.erase(end_it, words.end()); //算法不会添加或删除元素，必须调用容器的操作来删除
```

