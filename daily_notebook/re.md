# python中re模块的使用

参考：[Python 正则式学习笔记](https://blog.csdn.net/whycadi/article/details/2011046)

###  1.`|`或的使用

将两个规则并列起来，以`|`连接，表示只要满足其中之一就可以匹配。比如

`[a-zA-Z]|[0-9] `表示满足数字或字母就可以匹配，这个规则等价于` [a-zA-Z0-9]`

**注意：关于`'|'`要注意两点：** 

* 第一，它在`[]`之中不再表示或，而表示他本身的字符。如果要在`[]`外面表示一个`|`字符，必须用反斜杠引导，即 `\|` ;

* 第二，它的有效范围是它两边的整条规则。

  比如`dog|cat` 匹配的是`dog`和`cat`，而不是`g`和`c`。如果想限定它的有效范围，必需使用一个无捕获组 `(?: )`包起来。比如要匹配 `I have a dog`或`I have a cat`，需要写成`r'I have a (?:dog|cat)'` ，而不能写成 `r'I have a dog|cat'`

**示例**

```python
s = 'I have a dog , I have a cat'
re.findall( r'I have a (?:dog|cat)' , s )
#结果：
['I have a dog', 'I have a cat']     #正如我们所要的
```

下面再看看不用无捕获组会是什么后果：

```python
re.findall( r’I have a dog|cat’ , s )

['I have a dog', 'cat']      #它将’I have a dog’ 和’cat’当成两个规则了
```

### 2. `(?:)` 无捕获组

当你要将一部分规则作为一个整体对它进行某些操作，比如指定其重复次数时，你需要将这部分规则用`(?:’ ‘)`把它包围起来，而不能仅仅只用一对括号，那样将得到绝对出人意料的结果。

例：匹配字符串中重复的`ab`

```python
s=’ababab abbabb aabaab’
re.findall( r’\b(?:ab)+\b’ , s )
#结果：
['ababab']
```

如果仅使用一对括号，看看会是什么结果：

```python
re.findall( r’b\(ab)+\b’ , s )
#结果：
['ab']
```

这是因为如果只使用一对括号，那么这就成为了一个组(group)。组的使用比较复杂，将在后面详细讲解。