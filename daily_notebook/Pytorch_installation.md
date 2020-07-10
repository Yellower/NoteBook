### 1.安装
参照官网的安装方式：
```
conda install pytorch torchvision cudatoolkit=10.1 -c pytorch
```
一般官网下载速度慢，可以配置国内源来解决
```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
```
配置后，按如下使用
```
# cudatoolkit版本可以自行选择，会自动安装对应版本的pytorch
conda install pytorch torchvision cudatoolkit=10.1
```
### 2.测试
测试是否安装成功，并可以使用GPU
```
import torch
torch.cuda.is_available() # 返回True，则说明可以使用GPU
```
其他相关操作：
```
#返回当前设备索引
torch.cuda.current_device()

#返回GPU的数量
torch.cuda.device_count()

#返回gpu名字，设备索引默认从0开始
torch.cuda.get_device_name(0)
```
