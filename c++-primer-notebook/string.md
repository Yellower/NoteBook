# 额外的String操作

### 1.构造String的其他方法

* substr

  `s.substr(pos, n)`返回从pos开始的n个字符的拷贝

  ```c++
  string s("hello world.");
  string s2 = s.substr(0,5); //s2 = hello
  ```

* 顺序容器构造方法

  ```c++
  //从vector<char>初始化string
  string s(vec.cbegin(),vec.cend());
  ```

### 2.改变String的其他方法

* s.insert

  s.insert可以将其他string插入到当前string中

  ```c++
  string s = "some string", s2 = "other string";
  s.insert(0, s2); //在s的位置0之前插入s2
  ```

* s.replace

  ```c++
  string s("C++ Primer 4th Ed.");
  s.replace(11, 3, "5th"); //从位置11开始删除3个字符并插入“5th"，删除字符数量可以与插入数量不等
  s.replace(11, 3, "Fifth");
  ```

### 3. String的搜索操作

搜索操作返回一个string::size_type值，表示匹配发生位置的下标；搜索失败时，返回一个名为string::npos的成员，值为-1。

```c++
//string搜索操作
s.find(args)
s.rfind(args) //反向查找
s.find_first_of(args)
s.find_last_of(args)
s.find_first_not_of(args)
s.find_last_not_of(args)
//args可能的形式
c,pos      //从位置pos查找字符c, pos默认为0
s2,pos     //查找字符串s2
```

#### find

查找指定的字符串，返回第一个匹配位置的下标。查找时区分大小写，可以对每个字符使用`tolower(c)`转化成小写后再查找。

```c++
string name("AnnaBelle");
auto pos = name.find("Anna"); //pos=0
```

#### find_first_of

查找与给定字符串中任何一个字符匹配的位置。

```c++
//定位第一个数字
string numbers("0123456789"), name(r2d2);
auto pos = name.find_first_of(numbers) //pos=1
```

#### find_first_not_of

与find_first_of相反，返回第一个不在给定字符串中的位置。

#### 通过指定查找位置实现查找所有出现位置

```c++
string::size_type pos=0;
while((pos = name.find_first_of(numbers, pos)) != string::npos){
    cout<<name[pos]<<endl;
    ++pos;
}
```

### 4.数值转换

```c++
to_string(val) //将数值转化成string
stoi(s,p,b) //转化成int;b表示转化所用的基数默认为10，p保存第一个非数值字符的下标，默认为0
stof(s, p) //转化成float
stod(s, p) //转化成double
```

```c++
//找到第一个数值
string s2 = "pi = 3.14";
d = stod(s2.substr(s2.find_first_of("+-.0123456789")));
```

