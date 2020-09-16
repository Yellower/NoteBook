# g++使用

### 1.参数说明

`--version`：显示g++版本信息

`-std=`：指定c++标准，如`--std=c++11`

`-O`：编译时对代码的优化等级，可选：`-O0`表示没有优化；`-O1,-O2,-O3`分别表示不同等级

`-w` : 编译时，不显示任何警告消息

`-Wall`：编译时，显示所有警告消息

`-o` : 指定输出结果文件

`-I`: 指定头文件所在的路径

`-L`: 指定库文件所在的路径，通常与`-l`一起使用

`-l`: 指定使用的库名

### 2.基本使用

* 编译并生成可执行文件

```shell
g++ file1.cpp file2.cpp -o file # 注意编译时要包含所有的cpp文件
```

* 仅预处理

```shell
# -E:表示只进行预处理，生成`.i`文件
g++ -E -o hello.i hello.cpp
```

* 只编译

```shell
# -S：只进行预处理和编译，生成汇编代码的文本形式
g++ -S -o hello.s hello.cpp
```

* 不链接

```shell
# -c:表示只进行预处理、编译和汇编操作，不进行链接，生成可链接目标文件
g++ -c -o hello.o hello.cpp
```

* 仅链接

```shell
# -o:也能将可链接文件进行链接，得到可执行目标文件
gcc hello.o -o hello
```

### 3.库文件

库文件分为静态链接库和动态链接库

示例：

```c++
# main.cpp文件
#include"dynamic.h"
void dynamic_a()
{
  cout<<"this is in dynamic_a "<<endl;
}
```

```c++
# dynamic.h文件
#ifndef __DYNAMIC_H_
#define __DYNAMIC_H_
#include <iostream>
void dynamic_a();
void dynamic_b();
void dynamic_c();
#endif
```

```c++
# dynamic_a.cpp
#include"dynamic.h"
void dynamic_a()
{
  cout<<"this is in dynamic_a "<<endl;
}

# dynamic_b.cpp
#include"dynamic.h"
void dynamic_b()
{
  cout<<"this is in dynamic_b "<<endl;
}

# dynamic_c.cpp
#include"dynamic.h"
void dynamic_c()
{
  cout<<"this is in dynamic_c "<<endl;
}
```

#### 3.1静态链接库

静态链接库在程序编译链接时使用，将库文件的代码加入到最后生成的可执行文件中，文件以`.a`后缀表示（windows中是`.lib`文件）

* 创建静态链接库

```shell
# 首先编译程序
g++ -c dynamic_a.cpp dynamic_b.cpp dynamic_c.cpp

# 使用ar命令创建静态链接库
ar cr libstatic.a dynamic_a.o dynamic_b.o dynamic_c.o
```

* 使用静态链接库

```shell
# -static表示使用静态库； -L .表示库文件所在目录为当前目录；-lstatic指定库文件为libstatic.a
g++ main.cpp -lstatic -L . -static -o main
```

#### 3.2动态链接库

动态链接库与静态库不同，它不将代码加入到可执行文件中，而是在程序运行时，链接动态链接库，后缀为`.so`（windows中生成`.lib`和`.DLL`文件，其中`lib`文件在编译和链接时使用，`DLL`文件包含真正的库代码，运行时调用）

* 创建动态链接库

```shell
# -shared:表示生成动态链接库
# -fPIC:表示编译为位置独立的代码，一般都使用
g++ dynamic_a.cpp dynamic_b.cpp dynamic_c.cpp -fPIC -shared -o libdynamic.so
```

* 使用静态链接库

```shell
g++ main.cpp -L. -ldynamic -o main
```

如果运行时报错找不到动态库文件，需要在添加环境变量`LD_LIBRARY_PATH`