### Linux常用命令
1. head
`head`命令用于输出文件的开头部分，标准输入也是文件，所以也可以作为`head`的参数
```
# 参数
-n 指定显示的行数
-c 指定显示的字节数
-v 显示文件标题名
```
**示例**
```
# 显示前5行
head -n 5 test.txt
# 显示前10个字节，并显示文件名 
head -c 10 -v test.txt
# 结合ls，显示ls输出结果的前20行
ls | head -n 20
```
2. tail
`tail`命令类似于`head`，返回文件的尾部，参数与`head`相同
3. xargs
`xargs`的作用是将标准输入转化为命令行参数，当某个命令不能以标准输入作为参数时很有用
`|`命令可以将标准输出(stdout)转化为标准输入(stdin)，接受标准输入作为参数的命令，可以直接接在管道命令之后
`xargs`一般接在`|`后面，再接其他命令
```
# 参数
-i 以{}占据参数的位置，xargs的输出将会替换{}
-I 自定义占位符
```
**示例**
```
ls *.jpg | xargs -i cp {} ./target_dir
ls *.jpg | xargs -I aa cp aa ./target_dir
```
4. man
`man`命令可以用来查看命令的文档
**使用**
```
空格  向后翻一页
b     向前翻页
回车  向后翻一行
k     向前翻一行
查找  与vim相同
退出   q
```
5. shuf
在标准输出中打印输入行的随机排列，即打乱输入行
```
# 参数
-n 最多输出n行
-r 输出可以重复
-o 将结果写入指定文件
```
**示例**
```
# 从目录中随机拷贝300张图片到目标目录
ls *.jpg | shuf -n 300 | xargs -i cp {} ./target_dir
```
6. nohup
`nohup`
一般配合`&`使用
**示例**
```
nohup python train.py > train.log 2>&1 &
# > train.log ：将标准输出重定向到train.log文件
# 2>&1 : 将标准错误重定向到标准输出，也就会重定向到train.log文件
# & : 表示后台运行

# 也可以不保存日志
nohup python train.py > /dev/null 2>&1 &
或
nohup python train.py > /dev/null 2>error.log & #保存错误日志
```

7.cat

`cat`有三种用法：

* 1.一次性显示整个文件 

  `cat filename`

  示例：

  ````bash
  # 查看前10行
  cat filename | head -n 10
  ````

* 2.创建文件 

  ```shell
  cat > test.txt
  hello cat!! #Ctrl+D完成输入
  
  # 追加内容到原有文件
  cat >> test.txt
  ```

* 3.合并几个文件

  ```shell
  cat file1 file2 > new_file
  ```

8.暂停、继续进程

```bash
# 暂停
kill -STOP pid
# 继续
kill -CONT pid
```

9.awk

`awk`是文本分析工具，把文件读入，依次对每一行处理（默认分隔符为空格）

```bash
awk '{pattern + action}' filenames
```

假设`test.txt`的内容如下：

```txt
12,34,56,78
12,34,56,78
12,34,56,78
12,34,56,78
```

* 内置变量

  ```
  $n: 当前的第n个字段，比如$1表示第一个字段
  $0: 完整的行
  FS: 分隔的符号
  NF: 一行记录中的分段数量
  NR: 已经读出的行数，递增的
  ```

  示例：

  ```bash
  awk -F "," '{print $0, $1 "\t" $2 "\t" $3 " FS="FS " NF="NF " NR="NR}' test.txt
  >>>
  12,34,56,78 12  34      56 FS=, NF=4 NR=1
  12,34,56,78 12  34      56 FS=, NF=4 NR=2
  12,34,56,78 12  34      56 FS=, NF=4 NR=3
  12,34,56,78 12  34      56 FS=, NF=4 NR=4
  
  # 打印某几行
  awk '{if(NR>=2 && NR<=3) print $1}' test.txt 
  >>>
  12,34,56,78
  12,34,56,78
  ```

* 参数

  ```
  -F: 指定分隔符
  ```

```bash
# 将all_train.txt的第一列中的文件移动到./train/gt
awk -F "\t" '{print $2}' all_train.txt | xargs -i mv {} ./train/gt/
```

#### 10.查看系统信息

* 查看操作系统版本

  ```shell
  cat /proc/version
  ```

* 查看内核信息

  ```bash
  uname -a
  ```

* 查看当前操作系统发行版本信息

  ```shell
  cat /etc/centos-release
  ```

  