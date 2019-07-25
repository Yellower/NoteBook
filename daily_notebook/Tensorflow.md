# Tensorflow系列

### 1. `tf.nn.sparse_softmax_cross_entropy_with_logits()`

```python
tf.nn.sparse_softmax_cross_entropy_with_logits(
    _sentinel=None,
    labels=None,
    logits=None,
    name=None
)
```

`logits`为神经网络输出层的输出，shape为`[batch_size, num_classes]`；`labels`为标签，shape为`[batch_size]`。

内部原理：（参考[[TensorFlow]关于tf.nn.sparse_softmax_cross_entropy_with_logits()](https://blog.csdn.net/ZJRN1027/article/details/80199248))

1. 对`logits`进行`softmax`操作
2. 对`labels`进行`one-hot`编码
3. 计算cross-entropy

如果`labels`是`one-hot`编码格式，可以使用`tf.nn.softmax_cross_entropy_with_logits()`计算cross-entropy损失。

### 2. `tf.identity()`

```python
tf.identity(
    input,
    name=None
)
```

Return a tensor with the same shape and contents as input.

### 3.`tf.get_local_variable()`

```python
tf.get_local_variable(
    name,
    shape=None,
    dtype=None,
    initializer=None,
    regularizer=None,
    trainable=False,
    collections=None,
    caching_device=None,
    partitioner=None,
    validate_shape=True,
    use_resource=None,
    custom_getter=None,
    constraint=None,
    synchronization=tf.VariableSynchronization.AUTO,
    aggregation=tf.VariableAggregation.NONE
)
```

Behavior is the same as in `get_variable`, except that variables are added to the `LOCAL_VARIABLES` collection and `trainable` is set to `False`. This function prefixes the name with the current variable scope and performs reuse checks.

### 4. `tf.name_scope()`和`tf.variable_scope()`

参考：[tf.name_scope()和tf.variable_scope()](https://blog.csdn.net/weixin_38698649/article/details/80099822)

**区别**

对于`tf.Variable()`方式创建变量，具有相同效果，都会在变量名称前面加上作用域名称

对于`tf.get_variable()`方式创建变量时，只有｀｀`variable_scope()`作用域下的域名会加到变量名称前面

**例子**

```python
#tf.name_scope()
with tf.name_scope("my_name_scope"):
    v1 = tf.get_variable("var1", [1], dtype=tf.float32) 
    v2 = tf.Variable(1, name="var2", dtype=tf.float32)
    a = tf.add(v1, v2)
    print(v1.name)
    print(v2.name) 
    print(a.name)
#输出
var1:0
my_name_scope/var2:0
my_name_scope/Add:0

#tf.variable_scope()
with tf.variable_scope("my_variable_scope"):
    v1 = tf.get_variable("var1", [1], dtype=tf.float32)
    v2 = tf.Variable(1, name="var2", dtype=tf.float32)
    a = tf.add(v1, v2)
    print(v1.name) 
    print(v2.name)
    print(a.name)
#输出
my_variable_scope/var1:0
my_variable_scope/var2:0
my_variable_scope/Add:0
```

### 5. `tf.get_collection()`

```python
tf.get_collection(
    key,
    scope=None
)
```

获得`scope`下的`key`对应的集合中的变量

```python
losses = tf.get_collection(tf.GraphKeys.LOSSES, clone_scope)#clone_scope是一个name_scope作用域
total_loss = tf.add_n(losses)
```
