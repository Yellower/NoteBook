# Linux安装Opencv

安装教程参考：1.[官方教程](https://docs.opencv.org/3.4.10/d7/d9f/tutorial_linux_install.html) 2.[OpenCV在Linux上的安装与使用](https://blog.csdn.net/u014587123/article/details/88430503)

### 1.安装

（1）官网下载需要版本的源码（这里下载的是opencv-3.4.10）

文件名：`opencv-3.4.10.zip`

（2）解压，进入opencv的目录后新建`build`目录，并进入

```shell
unzip opencv-3.4.10.zip
cd opencv-3.4.10
mkdir build
cd build
```

（3）cmake

```shell
cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local ..
```

安装路径`/usr/local`可以自行修改

（4）编译&&安装

```sh
make -j7 # runs 7 jobs in parallel
# 编译完成后
sudo make install
```

### 2.使用

创建测试程序`main.cpp`：

```c++
#include <iostream>
#include <opencv2/opencv.hpp>

using namespace std;
using namespace cv;

int main()
{
    Mat srcImage = imread("lena.jpg");
    imshow("源图像",srcImage);
 
    waitKey(0);
 
    return 0;
}
```

编译程序，并运行：

```shell
g++ -o main main.cpp `pkg-config opencv --cflags --libs`
#运行
./main
```

可能的错误：

（1）错误一

`./warp.out: error while loading shared libraries: libopencv_dnn.so.3.4: cannot open shared object file`

解决办法：将安装后opencv的`lib或lib64`路径添加到环境变量`LD_LIBRARY_PATH`中

```shell
vim ~/.bashrc

# 在文件中添加
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/data/laifeng/lib64/

# 运行配置
source ~/.bashrc
```

（2）错误二

`pkg-config opencv --cflags --libs`找不到opencv的配置

解决办法：

查看`pkg-config`的配置是否正确：

```shell
# 命令行输入
pkg-config opencv --cflags --libs
```

查看路径是否正确

如果不正确，将`opencv.pc`所在目录（一般在opencv目录的`/lib64/pkgconfig`中）添加到环境变量`PKG_CONFIG_PATH`中

```
vim ~/.bashrc

# 在文件中添加
export PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/data/laifeng/lib64/pkgconfig

# 运行配置
source ~/.bashrc
```

### 3.安装记录

（1）由于服务器环境没有`cmake`没有办法对编译进行配置，可以使用Anaconda中的`cmake`

```shell
conda install cmake
```

（2）错误一

`/usr/bin/ld: warning: **libzstd.so.1.3.7**, needed by //home/user/anaconda3/lib/libtiff.so.5,

**not found** (try using -rpath or -rpath-link) `

解决办法：

在cmake时增加如下参数（原本的参数也要设置）：

```shell
cmake -D BUILD_TIFF=ON -D ENABLE_PRECOMPILED_HEADERS=OFF ..
```

（3）错误二

`/home/laifeng/anaconda/lib/libpng16.so.16：对‘inflateValidate@ZLIB_1.2.9’未定义的引用`

解决办法：

将`/home/laifeng/anaconda3/lib`添加到环境变量`LD_LIBRARY_PATH`中

