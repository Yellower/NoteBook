# Python的*args和**kwargs

args是arguments的缩写，而kwargs是keyword arguments的缩写，所以args表示的是位置参数，而kwargs表示的关键字参数

### 1.*args

将传入的任意数量参数打包成元组

```python
def function(*args):
    print(args, type(args))

function(1)
>>> (1,) <class 'tuple'>
```

```python
def function(x, y, *args):
    print(x, y, args)

function(1, 2, 3, 4, 5)
>>> 1 2 (3, 4, 5)
```

### 2.**kwargs

将参数打包成dict形式

```python
def function(**kwargs):
    print(kwargs)

function(a=1, b=2, c=3)
>>> {'a':1, 'b':2, 'c':3}
```

### 3.示例

```python
# clockdeco.py
import time
def clock(func):
    def clocked(*args):  #*args传递任意数量的位置参数，并打包成元组形式
        t0 = time.perf_counter()
        result = func(*args) 
        elapsed = time.perf_counter() - t0
        name = func.__name__
        arg_str = ', '.join(repr(arg) for arg in args)
        print('[%0.8fs] %s(%s) -> %r' % (elapsed, name, arg_str, result))
        return result
    return clocked # 返回内部函数，替换被装饰的函数
```

```python
# clockdeco_demo.py
import time
from clockdeco import clock

@clock
def snooze(seconds):
	time.sleep(seconds)

@clock
def factorial(n):
	return 1 if n < 2 else n*factorial(n-1)

if __name__=='__main__':
    print('*' * 40, 'Calling snooze(.123)')
    snooze(.123) #实际上调用的是clocked(.123)
    print('*' * 40, 'Calling factorial(6)')
    print('6! =', factorial(6))
```

对于被装饰的函数，因为函数的参数数量不固定，所以使用`*args`表示任意数量参数

