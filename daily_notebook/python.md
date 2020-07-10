# Python系列

[TOC]

### 1. random模块

```python
import random  #导入random模块
 
print(random.random()) #用于生成一个0到1之间的随机浮点数
print(random.uniform(1,3))# 用于生成一个指定范围内的随机浮点数
print(random.uniform(3,1))# 两个参数一个是上限，一个是下限
print(random.randint(1,3)) # 用于生成一个指定范围内的整数
#random.randrange([start],stop[,step]) 从指定范围内，按指定的基数递增的集合中获取一个随机数
print(random.randrange(0,100,2)) # 取 0到100之间的随机偶数
 
# random.choice 从序列中获取一个随机元素。其函数原型为random.choice(sequence)，参数sequence表示
# 一个有序类型。
print(random.choice('改变世界')) # 世
print(random.choice(['sunshine','is','lower'])) #lower
print(random.choice(('sunshine','always','18')))  # 18
 
s = ['改','变','世','界']
random.shuffle(s)
print(s) # ['变', '世', '改', '界']
 
# random.sample(sequence,k) 从指令序列中随机获取指定长度的片段。sample函数不会修改原有的序列。
l = [1,2,3,4,5,6,7,8]
print(random.sample(l,3)) # [7, 3, 5]
```

### 2. *用法

* 单个`*`

  (1) 如：`*parameter`是用来接受任意多个参数并将其放在一个元组中

  ```python
  >>> def demo(*p):
      print(p)
  
  >>> demo(1,2,3)
  (1, 2, 3)
  ```

  (2) 函数在调用多个参数时，在列表、元组、集合、字典及其他可迭代对象作为实参，并在前面加`*`, 如   `*(1,2,3)`，解释器将自动进行解包然后传递给多个单变量参数（参数个数要对应相等）

  ```python
  >>> def d(a,b,c):
      print(a,b,c)
  
  >>> d(1,2,3)
  1 2 3
  
  >>> a=[1,2,3]
  >>> b=[1,2,3]
  >>> c=[1,2,3]
  >>> d(a,b,c)
  [1, 2, 3] [1, 2, 3] [1, 2, 3]
  
  >>> d(*a)
  1 2 3
  ```

* 两个` **`  

  如:    `**parameter`用于接收类似于关键参数一样赋值的形式的多个实参，并放入字典中（即把该函数的参数转换为字典）

  ```python
  >>> def demo(**p):
      for i in p.items():
          print(i)
          
  >>> demo(x=1,y=2)
  ('x', 1)
  ('y', 2)
  ```

### 3. `zip`使用

```python
>>> a = [1,2,3]
>>> b = [4,5,6]
>>> c = [4,5,6,7,8]
>>> zipped = zip(a,b)     # 打包为元组的列表
[(1, 4), (2, 5), (3, 6)]
>>> zip(a,c)              # 元素个数与最短的列表一致
[(1, 4), (2, 5), (3, 6)]
>>> zip(*zipped)          # 与 zip 相反，*zipped 可理解为解压，返回二维矩阵式
[(1, 2, 3), (4, 5, 6)]
```

### 4. `dict.update()`
用法：
```
dict1.update(dict2) # 将dict2中的键值对更新到dict1中
```
示例：
```
a = {"a":1, "b":2}
b = {"C":3, "D":4}

a.update(b)
a
>>> {'a': 1, 'b': 2, 'C': 3, 'D': 4}
```