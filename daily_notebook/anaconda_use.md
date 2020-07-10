

# Anaconda虚拟环境使用

### 1. 创建虚拟环境

* 直接创建

  ```shell
  conda create -n env_name python==3.6
  ```

* 从导出的配置文件创建

  ```shell
  conda env create -f environment.yaml
  ```

  **导出虚拟环境**

  进入到虚拟环境中，输入：

  ```shell
  conda env export > environment.yaml
  ```

### 2. 激活、退出虚拟环境

* 激活

  ```shell
  conda activate env_name 或 source activate env_name
  ```

* 退出

  ```shell
  conda deactivate 或 source deactivate
  ```

### 3. 删除虚拟环境

**删除前先退出该虚拟环境**

```shell
conda remove -n env_name --all
```

### 4. 其他

* 查看安装的虚拟环境

  ```shell
  conda env list
  ```

* 安装CUDA和cudnn

  如果系统安装了`CUDA`和`cudnn`，默认情况下虚拟环境中自动使用系统的。如果需要的版本与系统的版本不匹配，可以使用下列命令安装

  ```shell
  # 先安装指定版本的cuda，自动安装对应版本的cudann
  conda install cudatoolkit=9.0
  conda install cudnn
  ```

* 配置国内镜像源
```shell
# 查看当前下载源
conda config --show channel

# 方法一：命令行设置
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes

# 方法二：.condrc文件设置
channels:
     - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
     - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
     - defaults
show_channel_urls: true
```

其他源：
```
# 中科大源
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```

  

