# Ubuntu命令行安装显卡驱动注意事项

> **注意：**
>
> 1. 保证当前的CUDA版本与待安装的显卡驱动版本对应
> 2. 保证gcc版本符合对应版本驱动的要求

### 1.查看驱动对应的gcc版本

参考[CUDA，NVIDIA Driver，Linux，GCC之间的版本对应关系表格](<https://blog.csdn.net/dudu815110/article/details/87167518)

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

# 4.安装

主要参考：[Ubuntu16.04服务器版安装NVIDIA显卡驱动](<https://blog.csdn.net/qq_30163461/article/details/80314630)

## 4.1下载官方驱动程序

官方下载地址：[http://www.geforce.cn/drivers](http://www.geforce.cn/drivers)

选择对应显卡型号的驱动程序，其中操作系统选择Linux 64-bit即可。

## 4.2禁止集成的nouveau驱动

查看属性：

```shell
sudo ls -lh /etc/modprobe.d/blacklist.conf
```

修改属性：

```shell
#修改文件的权限
sudo chmod 666 /etc/modprobe.d/blacklist.conf
#用vim打开
sudo chmod 666 /etc/modprobe.d/blacklist.conf
```

在打开的文件后添加：

```
blacklist vga16fb
blacklist nouveau
blacklist rivafb
blacklist rivatv
blacklist nvidiafb
```

并执行：

```shell
sudo update-initramfs -u
```

重启后执行：

```shell
lsmod | grep nouveau
```

如果没有输出即表明禁止成功。

## 4.3开始安装

对于桌面版系统需要用`sudo service lightdm stop`关闭当前图形界面，按`Alt+Ctrl+F3`进行终端执行安装。

若之前安装过显卡驱动，需要先卸载掉。**建议装驱动前都执行以下该指令**，有益无害。

```shell
sudo apt-get --purge remove nvidia-*
```

该指令能卸载驱动并不保留配置文件。

**安装指令**如下：

```shell
sudo chmod a+x NVIDIA-Linux-x86_64-384.130.run
sudo ./NVIDIA-Linux-x86_64-384.130.run -no-x-check -no-nouveau-check -no-opengl-files
```

在上述指令中，`–no-opengl-files`表示只安装驱动文件，不安装OpenGL文件，这个参数最重要。`–no-x-check` 安装驱动时不检查X服务。`–no-nouveau-check` 安装驱动时不检查nouveau（注：这个选项和4.2禁止集成的nouveau驱动组成双保险，其实一项操作就可以了）。