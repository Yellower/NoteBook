### 1.pip导出所有依赖

进行需要导出的环境，输入：

```shell
pip freeze > requirement.txt
```

### 2. Pip配置国内源
方法一：（推荐）
```
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/
```
可用的源：
```
# 阿里云    
https://mirrors.aliyun.com/pypi/simple/

# 清华大学(可能没用了)
https://pypi.tuna.tsinghua.edu.cn/simple/

# 豆瓣
https://pypi.doubanio.com/simple/
```
方法二：
找到pip的配置文件，修改配置文件，配置文件的位置通过方法一配置时会显示，Linux默认在`~/.pip/pip.conf`
没有配置文件时自己创建一个即可
```
[global]
timeout = 10
index-url = http://mirrors.aliyun.com/pypi/simple/
[install]
trusted-host = mirrors.aliyun.com
```