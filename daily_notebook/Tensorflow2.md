# Tensorflow2常用张量操作

张量的二元操作可以进行广播，与numpy的广播规则一样

## 一、tf.math

### 1.abs

返回张量的绝对值，输出类型、形状与输入一致

```python
tf.math.abs(
    x,
    name=None
)
```

```python
a = tf.constant([[1, 2, -1], [4, -3, 2]], dtype=tf.float32)
tf.abs(a)
>>>
<tf.Tensor: id=4, shape=(2, 3), dtype=float32, numpy=
array([[1., 2., 1.],
       [4., 3., 2.]], dtype=float32)>
```

### 2.accumulate_n

同`add_n()`，对多个张量对应位置求和

```python
a = tf.constant([[1, 2], [3, 4]])
b = tf.constant([[5, 0], [0, 6]])
tf.math.accumulate_n([a, b, a])  # [[7, 4], [6, 14]]

# Explicitly pass shape and type
tf.math.accumulate_n([a, b, a], shape=[2, 2], tensor_dtype=tf.int32) # [[7,  4],[6, 14]]
```

### 3.add

x和y对应元素相加，相当于`+`

```python
tf.math.add(
    x,
    y,
    name=None
)
```

### 4.add_n

同`accumulate_n()`

### 5.argmax

返回张量沿某一轴的最大值的下标

```python
tf.math.argmax(
    input,
    axis=None,
    output_type=tf.dtypes.int64,
    name=None
)
```

```python
A=tf.constant([2,20,30,3,6]) # Constant 1-D Tensor
tf.math.argmax(A) # output 2 as index 2 (A[2]) is maximum in tensor A
B=tf.constant([[2,20,30,3,6],[3,11,16,1,8],[14,45,23,5,27]])
tf.math.argmax(B,0) # [2, 2, 0, 2, 2]
tf.math.argmax(B,1) # [2, 2, 1]
```

**axis原则**

设axis=i ,则numpy沿着第i个下标变化的方向进行操作；所以，axis=0时，按列操作

### 6.argmin

与`argmax()`类似

### 7.bincount

### 8.ceil

对每个元素向上取整

输入类型必须是float

```python
tf.math.ceil(
    x,
    name=None
)
```

### 9.floor和round

floor: 向下取整

round: 最近取整

### 10.confusion_matrix

计算标签和输出的混淆矩阵

```python
tf.math.confusion_matrix(
    labels,
    predictions,
    num_classes=None,
    weights=None,
    dtype=tf.dtypes.int32,
    name=None
)
```

```python
# num_class自动计算为4+1，标签自动假定为[0, 1, 2, 3, 4]
tf.math.confusion_matrix([1, 2, 4], [2, 2, 4]) ==>
      [[0 0 0 0 0]
       [0 0 1 0 0]
       [0 0 1 0 0]
       [0 0 0 0 0]
       [0 0 0 0 1]]
```

### 11. equal

```python
x = tf.constant([2, 4]) 
y = tf.constant([2, 4]) 
tf.math.equal(x, y) 
>>>
<tf.Tensor: id=96, shape=(2,), dtype=bool, numpy=array([ True,  True])>
```

### 12. 逻辑操作

`logical_and`、`logical_or`、`logical_not`、`logical_xor`

```python
x = tf.constant([False, False, True, True], dtype = tf.bool)
y = tf.constant([False, True, False, True], dtype = tf.bool)
z = tf.logical_xor(x, y, name="LogicalXor")
#  here z = [False  True  True False]
```

### 13. maximum和minimum

返回元素级 x > y ? x : y 结果

```python
tf.math.maximum(
    x,
    y,
    name=None
)
```

```python
x = tf.constant([[1, 3], [3, 2]], dtype=tf.float32)
y = tf.constant([[2, 3], [0, 4]], dtype=tf.float32)
tf.maximum(x, y)
>>>
<tf.Tensor: id=106, shape=(2, 2), dtype=float32, numpy=
array([[2., 3.],
       [3., 4.]], dtype=float32)>
```

### 14.pow

指数运算，相当于 x**y

```python
tf.math.pow(
    x,
    y,
    name=None
)
```

```python
x = tf.constant([[2, 2], [3, 3]])
y = tf.constant([[8, 16], [2, 3]])
tf.pow(x, y)  # [[256, 65536], [9, 27]]
```

### 15.reduce_max和reduce_min

张量沿某一轴的最大或最小值

设置keepdims=True，保持维度

```python
tf.math.reduce_max(
    input_tensor,
    axis=None,
    keepdims=False,
    name=None
)
```

```python
x = tf.constant([[1, 3], [5, 2]], dtype=tf.float32)
tf.reduce_max(x, axis=0)
>>>
<tf.Tensor: id=137, shape=(2,), dtype=float32, numpy=array([5., 3.], dtype=float32)>

tf.reduce_max(x, axis=0, keepdims=True)
>>>
<tf.Tensor: id=143, shape=(1, 2), dtype=float32, numpy=array([[5., 3.]], dtype=float32)>
```

### 16.reduce_mean和reduce_sum

沿张量某一轴求平均值/和

### 17.top_k

返回最后一维上的前K大的值和下标

```python
tf.math.top_k(
    input,
    k=1,
    sorted=True,
    name=None
)
```

```python
x = tf.constant([[1, 3], [5, 2]], dtype=tf.float32)
v, i =tf.math.top_k(x, 1)
print(v)
print(i)
>>>
tf.Tensor(
[[3.]
 [5.]], shape=(2, 1), dtype=float32) tf.Tensor(
[[1]
 [0]], shape=(2, 1), dtype=int32)
```

## 二、tf

### 1.argsort

沿某一轴排序后的元素下标

direction='ASCENDIENG'，升序；direction='DESCENDING'，降序

```python
tf.argsort(
    values,
    axis=-1,
    direction='ASCENDING',
    stable=False,
    name=None
)
```

```python
import tensorflow as tf
a = [1, 10, 26.9, 2.8, 166.32, 62.3]
b = tf.argsort(a,axis=-1,direction='ASCENDING',stable=False,name=None) # [0 3 1 2 5 4]
```

### 2.boolean_mask

根据boolean的mask来取出张量元素

```python
tf.boolean_mask(
    tensor,
    mask,
    axis=None,
    name='boolean_mask'
)
```

```python
# 1-D example
tensor = [0, 1, 2, 3]
mask = np.array([True, False, True, False])
boolean_mask(tensor, mask)  # [0, 2]

# 2-D example
tensor = [[1, 2], [3, 4], [5, 6]]
mask = np.array([True, False, True])
boolean_mask(tensor, mask)  # [[1, 2], [5, 6]]
```

### 3.cast

转换张量的类型

```python
x = tf.constant([1.8, 2.2], dtype=tf.float32)
tf.cast(x, tf.int32)  # [1, 2], dtype=tf.int32
```

### 4.clip_by_value

限制张量的值在指定的区间内

```python
A = tf.constant([[1, 20, 13], [3, 21, 13]])
B = tf.clip_by_value(A, clip_value_min=0, clip_value_max=3) # [[1, 3, 3],[3, 3, 3]]
C = tf.clip_by_value(A, clip_value_min=0., clip_value_max=3.) # throws `TypeError` as input and clip_values are of different dtype
```

### 5.concat

沿某一轴联接多个张量

假设某个张量的形状:[D0, D1, ..., Dn]，则联接后的形状为：[D0, D1, ... D_axis, ..., Dn]，D_axis的值等于张量的数量乘以该维度的形状

```python
t1 = tf.constant([[1, 2, 3], [4, 5, 6]]) 
t2 = tf.constant([[7, 8, 9], [10, 11, 12]])
tf.concat([t1, t2], 0)
>>>
<tf.Tensor: id=134, shape=(4, 3), dtype=int32, numpy=
array([[ 1,  2,  3],
       [ 4,  5,  6],
       [ 7,  8,  9],
       [10, 11, 12]])>

tf.concat([t1, t2], 1)
>>>
<tf.Tensor: id=136, shape=(2, 6), dtype=int32, numpy=
array([[ 1,  2,  3,  7,  8,  9],
       [ 4,  5,  6, 10, 11, 12]])>
```

### 6.convert_to_tensor

转换成张量，包括张量、numpy array、python list、python标量

```python
value_3 = tf.convert_to_tensor(np.array([[1.0, 2.0], [3.0, 4.0]], dtype=np.float32))
```

### 7.executing_eagerly

查看当前是否是热运行

通常情况下，都是热运行，除了：

* 在`tf.function`内执行时，并且没有提前使用`tf.config.experimental_run_functions_eagerly()`
* 在`tf.dataset`的变换函数中
* 使用`tf.compat.v1.disable_eager_execution()`禁止了热运行

```python
print(tf.executing_eagerly()) 
>>> True
```

### 8.expand_dims

在`axis`指定的位置增加一维

```python
t = tf.constant([[1, 2, 3],[4, 5, 6]]) # shape [2, 3]
tf.expand_dims(t, axis=1)
>>>
<tf.Tensor: id=2, shape=(2, 1, 3), dtype=int32, numpy=
array([[[1, 2, 3]],

       [[4, 5, 6]]])>
```

### 9.fill

创建一个填满某个值的张量

```python
# Output tensor has shape [2, 3].
tf.fill([2, 3], -1)
>>>
<tf.Tensor: id=11, shape=(2, 3), dtype=int32, numpy=
array([[-1, -1, -1],
       [-1, -1, -1]])>
```

### 10.gather

```python
tf.gather(
    params,
    indices,
    validate_indices=None,
    axis=None,
    batch_dims=0,
    name=None
)
```

### 11.gather_nd

```python
tf.gather_nd(
    params,
    indices,
    batch_dims=0,
    name=None
)
```

与`gather`的区别：

`gather`提取某一指定维上，indices指定位置的元素

`gather_nd`提取indices指定位置上的元素，类似numpy数组取值

```python
indices = [[0, 0], [1, 1]]
params = [['a', 'b'], ['c', 'd']]
output = ['a', 'd']
```

### 12.linespace

按间隔生成元素

```python
tf.linspace(
    start,
    stop,
    num,
    name=None
)
```

```python
tf.linspace(10.0, 12.0, 3, name="linspace") => [ 10.0  11.0  12.0]
```

### 13.meshgrid

生成网格坐标，类似numpy.meshgrid()

```python
x = [1, 2, 3]
y = [4, 5, 6]
X, Y = tf.meshgrid(x, y)
>>>
X = [[1, 2, 3],
    [1, 2, 3],
     [1, 2, 3]]
Y = [[4, 4, 4],
     [5, 5, 5],
     [6, 6, 6]]
```

### 14.ones和ones_like

`tf.ones()`:生成给定开关，值全为1的张量

```python
tf.ones(
    shape,
    dtype=tf.dtypes.float32,
    name=None
)
```

`tf.ones_like()`: 生成与给定张量开关一样，值全为1的张量

```python
tf.ones_like(
    input,
    dtype=None,
    name=None
)
```

### 15.one_hot

根据给的下标生成one-hot张量，`indices`指定的位置的值为`on_value`，其他位置为`off_value`

```python
tf.one_hot(
    indices,
    depth,
    on_value=None,
    off_value=None,
    axis=None,
    dtype=None,
    name=None
)
```

### 16.pad

填充张量

padding.shape = [n, 2]

对维度为D的输入张量，`padding[D, 0]`表示在输入张量的D维的前面填充的数量，padding[D, 1]`表示在输入张量的D维的后面填充的数量，

```python
tf.pad(
    tensor,
    paddings,
    mode='CONSTANT',
    constant_values=0,
    name=None
)
```

```
t = tf.constant([[1, 2, 3], [4, 5, 6]])
paddings = tf.constant([[1, 1,], [2, 2]])
# 'constant_values' is 0.
# rank of 't' is 2.
tf.pad(t, paddings, "CONSTANT")  # [[0, 0, 0, 0, 0, 0, 0],
                                 #  [0, 0, 1, 2, 3, 0, 0],
                                 #  [0, 0, 4, 5, 6, 0, 0],
                                 #  [0, 0, 0, 0, 0, 0, 0]]
```

### 17.py_function

把一个python函数包装成tensorflow op

```python
tf.py_function(
    func,
    inp,
    Tout,
    name=None
)
```

### 18.range

生成数字序列

```python
start = 3
limit = 18
delta = 3
tf.range(start, limit, delta)  # [3, 6, 9, 12, 15]

start = 3
limit = 1
delta = -0.5
tf.range(start, limit, delta)  # [3, 2.5, 2, 1.5]

limit = 5
tf.range(limit)  # [0, 1, 2, 3, 4]
```

### 19.repeat

重复inputs中的元素

### 20.reshape

```python
a = tf.constant([[1, 2, 3], [4, 5, 6]])
tf.reshape(a, [6])
>>>
<tf.Tensor: id=2, shape=(6,), dtype=int32, numpy=array([1, 2, 3, 4, 5, 6])>
```

### 21.shape

返回张量的形状，返回值是一个张量

```python
a = tf.constant([[1, 2, 3], [4, 5, 6]])
tf.shape(a)
>>>
<tf.Tensor: id=3, shape=(2,), dtype=int32, numpy=array([2, 3])>
 
# tensor.shape tensor.get_shape的区别
a.shape
a.get_shape  #输出：TensorShape([2, 3]),可以用as_list()转化成list类型
```

### 22.squeeze

将大小为1的维度移除

```python
# 't' is a tensor of shape [1, 2, 1, 3, 1, 1]
tf.shape(tf.squeeze(t))  # [2, 3]

# 't' is a tensor of shape [1, 2, 1, 3, 1, 1]
tf.shape(tf.squeeze(t, axis=[2, 4]))  # [1, 2, 3, 1]
```

### 23.stack

将多个张量进行堆叠，张量的秩加1

`concat`不改变张量的秩

```python
tf.stack(
    values,
    axis=0,
    name='stack'
)
```

```python
x = tf.constant([1, 4]) 
y = tf.constant([2, 5]) 
z = tf.constant([3, 6]) 
tf.stack([x, y, z]) 
>>>
<tf.Tensor: id=7, shape=(3, 2), dtype=int32, numpy=
array([[1, 4],
       [2, 5],
       [3, 6]])>
```

### 23.tile

输出张量的第i维的大小 = 输入第i维的大小 * multiples[i]

```python
tf.tile(
    input,
    multiples,
    name=None
)
```

```python
a = tf.constant([[1,2,3],[4,5,6]], tf.int32) 
b = tf.constant([1,2], tf.int32) 
tf.tile(a, b)
>>>
<tf.Tensor: id=10, shape=(2, 6), dtype=int32, numpy=
array([[1, 2, 3, 1, 2, 3],
       [4, 5, 6, 4, 5, 6]])>
       
c = tf.constant([2,1], tf.int32) 
tf.tile(a, c) 
>>>
<tf.Tensor: id=12, shape=(4, 3), dtype=int32, numpy=
array([[1, 2, 3],
       [4, 5, 6],
       [1, 2, 3],
       [4, 5, 6]])>
```

### 24.where

```
tf.where(
    condition,
    x=None,
    y=None,
    name=None
)
```

根据条件返回`x`或`y`。如果`x`和`y`都没有提供，则返回条件为true的元素的坐标

### 25.zeros和zeros_like

同ones和ones_like

