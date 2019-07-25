# Linux中`find`命令使用

 `find` 命令用来在指定目录下查找文件或目录

`find`可以根据不同的测试项来查找文件，可用的测试项为：

```shell
-name   filename             #查找名为filename的文件
-perm                        #按执行权限来查找
-user    username             #按文件属主来查找
-group groupname            #按组来查找
-mtime   -n +n                #按文件更改时间来查找文件，-n指n天以内，+n指n天以前
-atime    -n +n               #按文件访问时间来查GIN: 0px">
-ctime    -n +n              #按文件创建时间来查找文件，-n指n天以内，+n指n天以前
-nogroup                     #查无有效属组的文件，即文件的属组在/etc/groups中不存在
-nouser                     #查无有效属主的文件，即文件的属主在/etc/passwd中不存
-newer   f1 !f2              找文件，-n指n天以内，+n指n天以前 
-ctime    -n +n               #按文件创建时间来查找文件，-n指n天以内，+n指n天以前 
-nogroup                     #查无有效属组的文件，即文件的属组在/etc/groups中不存在
-nouser                      #查无有效属主的文件，即文件的属主在/etc/passwd中不存
-newer   f1 !f2               #查更改时间比f1新但比f2旧的文件
-type    b/d/c/p/l/f         #查是块设备、目录、字符设备、管道、符号链接、普通文件
-size      n[c]               #查长度为n块[或n字节]的文件
-depth                       #使查找在进入子目录前先行查找完本目录
-fstype                     #查更改时间比f1新但比f2旧的文件
-type    b/d/c/p/l/f         #查是块设备、目录、字符设备、管道、符号链接、普通文件
-size      n[c]               #查长度为n块[或n字节]的文件
-depth                       #使查找在进入子目录前先行查找完本目录
-fstype                      #查位于某一类型文件系统中的文件，这些文件系统类型通常可 在/etc/fstab中找到
-mount                       #查文件时不跨越文件系统mount点
-follow                      #如果遇到符号链接文件，就跟踪链接所指的文件
-cpio                %;      #查位于某一类型文件系统中的文件，这些文件系统类型通常可 在/etc/fstab中找到
-mount                       #查文件时不跨越文件系统mount点
-follow                      #如果遇到符号链接文件，就跟踪链接所指的文件
-cpio                        #对匹配的文件使用cpio命令，将他们备份到磁带设备中
-prune                       #忽略某个目录
```

`find`命令还可以借助`-exec`参数结合其他shell命令使用，使用时`{}`表示查找到的目标，命令以`\;`结尾。

### 示例

* 查找指定名字的文件

  ```shell
  #查找以0-9开头的文件并打印
  find ./files/* -name "[0-9]*" -exec ls -l {} \;
  
  # `| wc -l`显示总数
  find ./files/* -name "[0-9]*" -exec ls -l {} \; | wc -l
  
  #查找并复制到新目录new_files下面
  find ./files/* -name "[0-9]*" -exec cp -r {} new_files/ \;
  ```

* 查找指定时间的文件

  有三种时间戳：

  访问时间（`-atime`/天，`-amin`/分钟）：用户最近一次访问时间。

  修改时间（`-mtime`/天，`-mmin`/分钟）：文件最后一次修改时间。

  变化时间（`-ctime`/天，`-cmin`/分钟）：文件数据元（例如权限等）最后一次修改时间。

  ```shell
  #查看最近7天内被访问所有文件 -type f:表示普通文件
  find . -type f -atime –7
  
  #搜索恰好在7天前被访问过的所有文件
  find . -type f -atime 7
  
  #搜索访问时间超过7天的所有文件
  find . -type f -atime +7
  ```

  