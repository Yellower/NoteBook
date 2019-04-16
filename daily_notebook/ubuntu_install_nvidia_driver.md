# Ubuntu命令行安装显卡驱动注意事项

> **注意：**
>
> 1. 保证当前的CUDA版本与待安装的显卡驱动版本对应
> 2. 保证gcc版本符合对应版本驱动的要求

### 1.查看驱动对应的gcc版本

参考[CUDA，NVIDIA Driver，Linux，GCC之间的版本对应关系表格](<https://blog.csdn.net/dudu815110/article/details/87167518>)

### 2.安装新版本gcc

#### 2.1 添加源

```bash
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
```

#### 2.2 安装gcc/g++

Ubuntu系统中默认的是gcc-4.8，通常都需要新的版本，目前gcc-5对应的即是5.3.0版本

```bash
sudo apt-get install gcc-4.8 g++-4.8
sudo apt-get install gcc-4.9 g++-4.9
sudo apt-get install gcc-5 g++-5
sudo apt-get install gcc-6 g++-6
```

###  3. 切换gcc

切换gcc版本有两种方法：

##### 手动建立软链接 

```bash
cd /usr/bin  #gcc指令保存的位置
sudo rm gcc  #删除原始链接
sudo ln -s gcc-5 g++  #新建链接
sudo rm g++
sudo ln -s g++-5 g++
```

#### 通过update-alternatives建立文件关联

利用update-alternatives可以为不同版本的gcc设置优先级，系统默认选择优先级最高的gcc版本。

设置优先级：

```bash
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.8 20
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-5 50
```

查询系统中所有安装的gcc：

```bash
sudo update-alternatives --query gcc
```



