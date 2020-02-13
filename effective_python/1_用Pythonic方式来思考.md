# 第一章 用Pythonic方式来思考

python开发人员在编程时逐渐形成的编程规范，叫做pythonic风格

## 第1条 确认自己使用的python版本 

```bash
#版本查看 命令行
python --version
```

开发后续项目时，优先考虑python3版本

## 第2条 遵循PEP 8风格

* 编写python程序时要遵守PEP 8标准
* 可以使项目更利于多人合作
* 可以令后续的修改工作变得更加容易

PEP 8（[《Python Enhancement Proposal #8》](https://www.python.org/dev/peps/pep-0008/)）是针对python编程制订的风格指南

pylint分析工具可以自动检查代码是否符合PEP 8网络指南

![](F:\NoteBook\.gitbook\assets\PEP8_1.png)

![PEP8_2](F:\NoteBook\.gitbook\assets\PEP8_2.png)

![PEP8_3](F:\NoteBook\.gitbook\assets\PEP8_3.png)

## 第3条 了解bytes、str和Unicode的区别

python3中有两种表示字符序列的类型：bytes和str，bytes包含原始的8位值（二进制数据），而str包含Unicode字符。

Unicode字符转成二进制数据，使用encode()；二进制数据转成Unicode字符，使用decode()

编程时，编码和解码操作就放在程序的最外围，在核心部分使用Unicode字符

打开文件时，使用'wb'和'rb'来写入或读取二进制数据

## 第4条 用辅助函数来取代复杂的表达式

对于结构复杂的表达式或者需要重复使用的表达式，使用辅助函数来代替

## 第5条 了解切割序列的办法

* 当start索引为0或end索引为列表长度时，应该将其忽略

  ```python
  a = ['a', 'b', 'c', 'd', 'e', 'f']
  a[0:3] #不好，使用a[:3]
  a[2:len(a)] #不好，使用a[2:]
  ```

* 对列表切割后，会产生新列表（浅拷贝），对新列表赋值不影响原始列表

  ```python
  a = ['a', 'b', 'c', 'd', 'e', 'f']
  b = a[:3] # ['a', 'b', 'c', 'd']
  b[0] = 100 # b:[100, 'b', 'c', 'd'] a:['a', 'b', 'c', 'd', 'e', 'f']
  ```

* 没有start和end时

  ```python
  a = ['a', 'b', 'c', 'd', 'e', 'f']
  b = a[:] #拷贝a 
  b = a #不拷贝a，b和a同时指向同一个列表
  ```

## 第6条 在单次切片操作内，不要同时指定start、end和stride

既有start和end，又有stride的切割操作，可能令人费解

尽量使用stride为正数，且不带start和end作为索引的切割操作

```python
a = ['red', 'orange', 'yellow', 'green', 'blue']
odd = [::2]
```

尽量不使用负数的stride，`[::-1]`表示倒序

如果确实需要使用start、end和strid时，将代码拆解成两条赋值语句：

```python
a = ['a', 'b', 'c', 'd', 'e', 'f']
#不建议使用
b = a[1:-1:2]
#建议使用
b = a[::2]
c = b[1:-1]
```

## 第7条 用列表推导来取代map和filter

## 第8条 不要使用含有两条以上表达式的列表推导

## 第9条 用生成器表达式来改写数据量较大的列表推导

列表推导的缺点：在推导过程中，对于输入序列中的每个值，可能都要创建仅含一项元素的新列表。当输入序列的数据非常多时，会消耗大量内存。

使用生成器表达式替代列表推导，生成器表达式会产生一个迭代器，通过迭代器产生一项新数据

```python
# 使用列表推导
value = [len(x) for x in open('my_file.txt')]
print(value)
>>> [100, 57, 15, 2, 4, 3]

#使用圆括号即为生成器表达式
it = (len(x) for x in open('my_file.txt'))
print(it)
>>> <generator object <genexpr> at 0x0000016666467AF0>

#通过next()生成一项数据 
print(next(it))
>>> 100
```

生成器表达式可以相互结合

```python
roots = ((x, x**0.5) for x in it)
print(next(roots))
>> (15, 3.872993346207)
```

**生成器**

在 Python 中，使用了 yield 的函数被称为生成器（generator）。

跟普通函数不同的是，生成器是一个返回迭代器的函数，只能用于迭代操作，更简单点理解生成器就是一个迭代器。

在调用生成器运行的过程中，每次遇到 yield 时函数会暂停并保存当前所有的运行信息，返回 yield 的值, 并在下一次执行 next() 方法时从当前位置继续运行。

调用一个生成器函数，返回的是一个迭代器对象。

```python
import sys
 
def fibonacci(n): # 生成器函数 - 斐波那契
    a, b, counter = 0, 1, 0
    while True:
        if (counter > n): 
            return
        yield a
        a, b = b, a + b
        counter += 1
f = fibonacci(10) # f 是一个迭代器，由生成器返回生成
 
while True:
    try:
        print (next(f), end=" ")
    except StopIteration:
        sys.exit()
#输出结果：0 1 1 2 3 5 8 13 21 34 55
```

## 第10 条 尽量用enumerate取代range

enumerate()函数同时返回下标和元素

```python
flavor_list = ['vanilla', 'chocolate', 'pecan', 'strawberry']
for i, flavor in enumerate(flavor_list):
    print('%d: %s' % (i+1, flavor))
>>>
1: vanilla
2: chocolate
3: pecan
4: strawberry
    
#可以指定起始下标
flavor_list = ['vanilla', 'chocolate', 'pecan', 'strawberry']
for i, flavor in enumerate(flavor_list, 1):
    print('%d: %s' % (i, flavor))
>>>
1: vanilla
2: chocolate
3: pecan
4: strawberry
```

## 第11条 用zip函数同时遍历两个迭代器

zip函数可以把两个或两个以上的迭代器封装为生成器，每次会从迭代器中获取下一个值，将所有值汇聚成元组

当需要同时遍历多个相关联的列表时，很有用

```python
flavor_list = ['vanilla', 'chocolate', 'pecan', 'strawberry']
nums = [len(x) for x in flavor_list]
for x in zip(flavor_list, num):
    print(x)
>>>
('vanilla', 7)
('chocolate', 9)
('pecan', 5)
('strawberry', 10)
```

## 第12条 不要在for和while循环后面写else块

## 第13条 合理利用try/except/else/finally结构中的每个代码块









