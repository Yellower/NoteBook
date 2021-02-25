# sed指令

### 1.sed基本指令

sed会逐行扫描输入的数据，并将数据复制到缓冲区，我们称之为**模式空间**，然后将模式空间中的数据与给定的条件进行匹配，如果匹配成功则执行特定的sed指令，否则sed会跳过输入的数据行，继续读取后续的数据。默认情况下sed会把匹配的数据显示在屏幕上。

`sed`语法格式：

```
sed [选项] '匹配条件和操作指令' 输入文件...
```

* 常用的命令选项

  ```bash
  -n  : 屏蔽默认的输出功能
  -i[SUFFIX]  : 直接修改原文件，如果设置了SUFFIX后缀名，sed会备份数据
  -r  : 支持扩展正则
  -f  : 指定执行的sed脚本文件
  ```

* sed基本的操作指令

  ```bash
  p  : 打印当前匹配的数据行
  l   : 同 p，但会显示控制字符、回车符等
  =  : 打印当前行号
  a text : 在匹配的数据行后面追加文本内容
  i text: 在匹配的数据行前面插入文本内容
  d : 删除整行匹配的数据行
  c text  : 将匹配的数据行替换为指定文本内容
  w filename : 将匹配的数据写入特定的文件中
  s/regexp/replace/ : 使用正则匹配，将匹配的数据替换为特定的内容
  ```


* sed支持的数据定位方法

  ```bash 
  number  : 直接根据行号匹配数据
  $  : 匹配最后一行
  /regexp/  : 使用正则表达式匹配数据行
  addr1, addr2  : 使用行号定位，匹配从addr1到addr2的所有行
  addr1, +N  : 使用行号定位，匹配从addr1开始的N行
  ```

```bash
# test.txt
1 hello the world.
2 go spurs go.
3 123 456 789.
4 hello the beijing.
5 I am Jacob.

(1) 示例：
sed 'p' test.txt
>>
1 hello the world.
1 hello the world.
2 go spurs go.
2 go spurs go.
3 123 456 789.
3 123 456 789.
4 hello the beijing.
4 hello the beijing.
5 I am Jacob.
5 I am Jacob.

# -n 屏蔽默认输出
sed -n 'p' test.txt
>>
1 hello the world.
2 go spurs go.
3 123 456 789.
4 hello the beijing.
5 I am Jacob.

sed -n '1,3p' test.txt
>>
1 hello the world.
2 go spurs go.
3 123 456 789.

# 多条指令用分号隔开
sed -n '1p;2p;3p' test.txt
>>
1 hello the world.
2 go spurs go.
3 123 456 789.

sed -n '/hello/p' test.txt
>>
1 hello the world.
4 hello the beijing.

# sed会将原文件备份为.bak文件，在修改原文件
sed -i.bak '2d' /tmp/hosts

# 前面插入
sed '1i add new line' test.txt
>>
add new line
1 hello the world.
2 go spurs go.
3 123 456 789.
4 hello the beijing.
5 I am Jacob.

# 后面插入
sed '1a add new line' test.txt
>>
add new line
1 hello the world.
2 go spurs go.
3 123 456 789.
4 hello the beijing.
5 I am Jacob.

# 替换整行
sed '1c new line' test.txt
>>
new line
2 go spurs go.
3 123 456 789.
4 hello the beijing.
5 I am Jacob.

# 删除行
sed '/hello/d' test.txt
>>
2 go spurs go.
3 123 456 789.
5 I am Jacob.

# 删除以 # 开头的行
sed '/^#/d' test. txt  

# 删除空行
sed '/^$/d' test.txt

# 正则替换（常用）
sed 's/hello/nihao/' test.txt
>>
1 nihao the world.
2 go spurs go.
3 123 456 789.
4 nihao the beijing.
5 I am Jacob.

# 默认时，每行只替换匹配到的第一个内容，使用 g 表示所有；或 number 表示替换第几个
sed 's/o/O/g' test.txt
>>
1 hellO the wOrld.
2 gO spurs gO.
3 123 456 789.
4 hellO the beijing.
5 I am JacOb.

# i 表示忽略大小写
sed 's/jacob/vicky/i' test.txt
>>
1 hello the world.
2 go spurs go.
3 123 456 789.
4 hello the beijing.
5 I am Vicky.

# 默认使用 / 作为分隔符，如果文本内容带 / 会比较麻烦，可以使用其他符号作为分隔符
sed 's#/bin/bash#/bin/sh#' /tmp/passwd

# 使用 ; 编写多条指令，使用{}进行分组
# 第二条指令对所有数据行操作
sed '/world/s/hello/hi/;s/the//' test.txt
>>
1 hi  world.
2 go spurs go.
3 123 456 789.
4 hello  beijing.
5 I am Jacob.

# 两条指令都只对包含 world 的行操作
sed '/world/{s/hello/hi; s/the//}' test.txt
>>
1 hi  world.
2 go spurs go.
3 123 456 789.
4 hello the beijing.
5 I am Jacob.
```

当需要执行的指令比较多时，可以将指令写到脚本中在执行

```bash
# script.sed
1c hello world
/beijing/{
	s/h/H/
	s/beijing/china/
}

sed -f script.sed test.txt
>>
hello world
2 go spurs go.
3 123 456 789.
4 Hello the china.
5 I am Jacob.
```

