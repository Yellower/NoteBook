# Python中的sys.stdout.write

参考：[python print 和 sys.stdout.write()](https://blog.csdn.net/u011244839/article/details/79932148)

当我们使用print(obj)在console上打印对象的时候，实质上调用的是sys.stdout.write(obj+'\n')，print在打印时会自动加个换行符，以下两行等价：

```python
sys.stdout.write('hello'+'\n')
print('hello')
```

### 从控制台重定向到文件

原始的 sys.stdout 指向控制台，如果把文件的对象的引用赋给 sys.stdout，那么 print 调用的就是文件对象的 write 方法，将对象写入文件中：

```python
f_handler=open('out.log', 'w')
sys.stdout=f_handler
print('hello')
# this hello can't be viewed on concole
# this hello is in file out.log
```

### 利用 sys.stdout.write和\r实现在同一行打印

| 转义字符 |        输出        |
| :------: | :----------------: |
|    \n    | 回车，光标在下一行 |
|    \r    | 换行，光标在上一行 |
|    \b    |        退格        |

```python
#利用\r可以实现在同一行打印，即覆盖之前打印的内容，让输出看起来是变化的
import sys
import time

def bar():
    i = 100
    li = []
    while i < 200:
        i += 1
        li.append(i)
        time.sleep(1)
        #sys.stdout.write(str(i) + '\r') #打印变化的数字
        sys.stdout.write(str(li) + '\r') #打印变化列表

bar()
```



