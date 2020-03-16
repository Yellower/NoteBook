# explicit关键字

参考：[c++explicit详解](https://www.cnblogs.com/rednodel/p/9299251.html)

首先, **C++中的explicit关键字只能用于修饰只有一个参数的类构造函数, 它的作用是表明该构造函数是显示的, 而非隐式的**, 跟它相对应的另一个关键字是implicit, 意思是隐藏的,类构造函数默认情况下即声明为implicit(隐式)。

那么显示声明的构造函数和隐式声明的构造函数有什么区别呢？

```c++
class CxString  // 没有使用explicit关键字的类声明, 即默认为隐式声明  
{  
public:  
    char *_pstr;  
    int _size;  
    CxString(int size)  
    {  
        _size = size;                // string的预设大小  
        _pstr = malloc(size + 1);    // 分配string的内存  
        memset(_pstr, 0, size + 1);  
    }  
    CxString(const char *p)  
    {  
        int size = strlen(p);  
        _pstr = malloc(size + 1);    // 分配string的内存  
        strcpy(_pstr, p);            // 复制字符串  
        _size = strlen(_pstr);  
    }  
    // 析构函数这里不讨论, 省略...  
};  
  
// 下面是调用:  
CxString string1(24);     // 这样是OK的, 为CxString预分配24字节的大小的内存  
CxString string2 = 10;    // 这样是OK的, 为CxString预分配10字节的大小的内存  
CxString string3;         // 这样是不行的, 因为没有默认构造函数, 错误为: “CxString”: 没有合适的默认构造函数可用  
CxString string4("aaaa"); // 这样是OK的  
CxString string5 = "bbb"; // 这样也是OK的, 调用的是CxString(const char *p)  
CxString string6 = 'c';   // 这样也是OK的, 其实调用的是CxString(int size), 且size等于'c'的ascii码  
string1 = 2;              // 这样也是OK的, 为CxString预分配2字节的大小的内存  
string2 = 3;              // 这样也是OK的, 为CxString预分配3字节的大小的内存  
string3 = string1;        // 这样也是OK的, 至少编译是没问题的, 但是如果析构函数里用free释放_pstr内存指针的时候可能会报错, 完整的代码必须重载运算符"=", 并在其中处理内存释放
```

对`CxString string1(24)`这句，没有什么问题，调用`CxString(int size)`构造函数；但对于`CxString string2 = 10`这句为什么也不报错呢？这时因为当构造函数的参数是一个时，在编译的时候就会有一个自动的转换操作:将该构造函数对应数据类型的数据转换为该类对象. 也就是说 "CxString string2 = 10;" 这段代码, 编译器自动将整型转换为CxString类对象, 实际上等同于下面的操作:

```c++
CxString string2(10);  
或  
CxString temp(10);  
CxString string2 = temp; 
```

但是`10`指的是`_size`的大小，这里当成`CxString`对象，有点不伦不类。同样，`CxString string6 = 'c'`也存在同样的问题

那我们加上`explicit`声明：

```c++
class CxString  // 使用关键字explicit的类声明, 显示转换  
{  
public:  
    char *_pstr;  
    int _size;  
    explicit CxString(int size)  
    {  
        _size = size;  
        // 代码同上, 省略...  
    }  
    CxString(const char *p)  
    {  
        // 代码同上, 省略...  
    }  
};  
  
// 下面是调用:  
CxString string1(24);     // 这样是OK的  
CxString string2 = 10;    // 这样是不行的, 因为explicit关键字取消了隐式转换  
CxString string3;         // 这样是不行的, 因为没有默认构造函数  
CxString string4("aaaa"); // 这样是OK的  
CxString string5 = "bbb"; // 这样也是OK的, 调用的是CxString(const char *p)  
CxString string6 = 'c';   // 这样是不行的, 其实调用的是CxString(int size), 且size等于'c'的ascii码, 但explicit关键字取消了隐式转换  
string1 = 2;              // 这样也是不行的, 因为取消了隐式转换  
string2 = 3;              // 这样也是不行的, 因为取消了隐式转换  
string3 = string1;        // 这样也是不行的, 因为取消了隐式转换, 除非类实现操作符"="的重载
```

可以看到，因为取消了隐式转换，`CxString string2 = 10`会报错，而对`CxString(const char *p)`没有取消隐式转换，`CxString string5="bbb"`是不会报错的。

**总结**

`explicit`关键字让只有一个参数的构造函数取消隐式转换。（多参数时，也不能使用`=`进行构造，所以声明`explicit`没有意义）

google的c++规范中提到`explicit`的优点是可以避免不合时宜的类型变换，缺点无。所以google约定所有单参数的构造函数都必须是显示的，只有极少数情况下拷贝构造函数可以不声明称explicit。

所以，对单参数的构造函数只要不是特殊情况，应该使用`explicit`关键字。

