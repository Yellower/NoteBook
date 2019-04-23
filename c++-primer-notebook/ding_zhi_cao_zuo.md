# 定制操作

## 1.谓词（predicate）

很多泛型算法在默认情况下，使用元素类型的<或==运算符来完成比较。标准库为这些算法重载了其他版本，在这些版本中可以给泛型算法提供一个**谓词**参数，通过提供的谓词来定制比较运算符。

谓词是一个可调用的表达式。接受谓词参数的算法对输入序列中的元素调用谓词。

谓词分为**一元谓词**和**二元谓词**， 其中一元谓词只接受一个参数，二元谓词只接受二个参数。 通常，向算法的谓词参数传入一个函数，利用函数的定制比较操作。函数的参数数量要与算法定义的谓词能接受的数量一致。

## 2. lambda表达式

由于谓词的参数数量是严格固定的，只能传递相应数量的参数，但有些时候需要传递更多的参数，这时可以使用lambda表达式。

**lambda表达式**是c++中的一种可调用对象(另外三种可调用对象：函数、函数指针、重载了函数调用运算符的类)。

具体形式为：

```c++
[捕获列表](参数列表) -> 返回类型 {函数体}
```

其中参数列表和返回类型可以省略。

```c++
auto f= [] {return 42;};
cout<<f()<<endl;
```

**注意：函数体只有一个return语句时，返回类型从表达式推断出来；如果有其他内容且没有指定返回类型，则默认为void**

#### 使用参数

```c++
stable_sort(words.begin(), word.end(), [](const string &a, const string &b){return a.size()<b.size();});
```

#### 使用捕获列表

```c++
auto wc = find_if(words.begin, words.end(), [sz](const string &a){ return a.size() >= sz;});
```

## 3.捕获和返回

### 3.1值捕获与引用捕获

变量的捕获方式可以是值或者引用。

**注意：使用值时，被捕获变量的值在lambda创建时拷贝，不是在调用时拷贝。**

**尽量使用值捕获**

### 3.2隐式捕获

在捕获列表中写一个`＆`或者`=`，`=`采用捕获值的方式，`&`采用捕获引用的方式。

同时，在使用隐式捕获时，可以混合使用显示捕获。

```c++
for_each(words.begin(), words.end(), [=, &os](const string &s){os << s << c;});
```

### 3.3 指定返回类型

**必须使用尾置返回类型**

```c++
//传递一个lambda给transform，使其返回参数的绝对值
transform(vi.begin(), vi.end(), vi.begin(), [](int i) -> int {if(i<0) return -i; else return i;});
```

## 4.参数绑定

对于在一两个地方使用的简单操作，lambda表达式最有用。但如果需要在多个地方使用相同的操作，通常应该定义一个函数。这时又回到了参数数量不匹配的问题上。这里，可以使用c++提供的另一个解决方案--参数绑定。

### bind函数

调用bind的一般形式：

```c++
auto newCallable = bind(callable, arg_list);
```

其中，`newCallabel`是绑定参数后的可调用对象。当我们调用`newCallable`时，`newCallable`会调用`callable`并传入`arg_list`中的参数。 `arg_list`中可能包含_n形式的参数，它们是**占位符**，表示`newCallable`的参数。

```c++
bool check_size(const string &s, string::size_type sz)
{
    return s.size() >= sz;
}
```

调用bind函数绑定sz参数：

```c++
auto check = bind(check_size, _1, 6); //sz=6
string s = "hello";
bool b = check(s);//会调用check_size(s, 6)
```

**注意：使用占位符时需要声明命名空间`using namespace std::placeholders`



