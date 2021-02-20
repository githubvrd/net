## Linux

### Linux文件属性

![1612164002701](C:\Users\ZS\AppData\Roaming\Typora\typora-user-images\1612164002701.png)

```shell
ls：查看文件名称
ls -l：查看文件详细信息
```

实例中，文件的第一个属性用 **d** 表示，**d** 在 Linux 中代表该文件是一个目录文件。

在 Linux 中第一个字符代表这个文件是目录、文件或链接文件等等。

- 当为 **d** 则是目录
- 当为 **-** 则是文件；
- 若是 **l** 则表示为链接文档(link file)；
- 若是 **b** 则表示为装置文件里面的可供储存的接口设备(可随机存取装置)；
- 若是 **c** 则表示为装置文件里面的串行端口设备，例如键盘、鼠标(一次性读取装置)。

接下来的字符中，以三个为一组，且均为 **rwx** 的三个参数的组合。其中， **r** 代表可读(read)、 **w** 代表可写(write)、 **x** 代表可执行(execute)。 要注意的是，这三个权限的位置不会改变，如果没有权限，就会出现减号 **-** 而已。每个文件的属性由左边第一部分的 10 个字符来确定（如下图）。

![363003_1227493859FdXT](https://www.runoob.com/wp-content/uploads/2014/06/363003_1227493859FdXT.png)

第 **0** 位确定文件类型，第 **1-3** 位确定属主（该文件的所有者）拥有该文件的权限。第4-6位确定属组（所有者的同组用户）拥有该文件的权限，第7-9位确定其他用户拥有该文件的权限。

第 **1、4、7** 位表示读权限，如果用 **r** 字符表示，则有读权限，如果用 **-** 字符表示，则没有读权限；

第 **2、5、8** 位表示写权限，如果用 **w** 字符表示，则有写权限，如果用 **-** 字符表示没有写权限；

第 **3、6、9** 位表示可执行权限，如果用 **x** 字符表示，则有执行权限，如果用 **-** 字符表示，则没有执行权限。

### Linux文件目录管理

**绝对路径：**由根目录 **/** 写起，例如： /usr/share/doc 这个目录

**相对路径：**例如由 /usr/share/doc 要到 /usr/share/man 底下，可以写成： **cd ../man** 

```
ls         #（list files）列出目录和文件名
cd         #（change directory）切换目录
pwd        #（print work directory）显示当前目录
mkdir      #（make directory）创建目录
rmdir      #（remove directory）删除目录
cp         #（copy file）复制文件目录
rm         #（remove）删除文件目录
mv         #（move file）移动文件目录，或修改文件目录名称
```

1、ls

```
ls -a
```

选项与参数：

-a ：全部的文件，连同隐藏文件一起列出来

-d ：列出目录本身，而不是列出目录内的文件数据

-l ：长数据串列出，包含文件的属性与权限等等数据

2、cd

```
cd ..    # 返回上级目录
cd usr   # 切换到usr文件
cd /     # 返回根目录
```

3、pwd

```
pwd -P   # 显示当前目录
```

4、mkdir

参数

-m ：配置文件的权限喔！直接配置，不需要看默认权限 (umask) 的脸色～

-p ：帮助你直接将所需要的目录(包含上一级目录)递归创建起来！

实例：

```
mkdir test <==创建test目录

mkdir test1/test2/test3/test4
cannot create directory `test1/test2/test3/test4': No such file or directory <== 无法直接创建此目录

mkdir -p test1/test2/test3/test4
```

实例：创建权限为 **rwx--x--x** 的目录。

```
[root@www tmp]# mkdir -m 711 test2
[root@www tmp]# ls -l
drwxr-xr-x  3 root  root 4096 Jul 18 12:50 test
drwxr-xr-x  3 root  root 4096 Jul 18 12:53 test1
drwx--x--x  2 root  root 4096 Jul 18 12:54 test2
```

使用 -m ，如上例我们 -m 711 给予新的目录 drwx--x--x 的权限

5、rmdir

```
rmdir test   <==可直接删除掉，没问题
rmdir test1  <==因为尚有内容，所以无法删除！
rmdir -p test1/test2/test3/test4
```

6、cp

```
cp [-adfilprsu] 来源档(source) 目标档(destination)
cp [options] source1 source2 source3 .... directory
```

7、rm

参数：

-f ：就是 force 的意思，忽略不存在的文件，不会出现警告信息；

-i ：互动模式，在删除前会询问使用者是否动作

-r ：递归删除啊！最常用在目录的删除了！这是非常危险的选项！

8、mv

参数：

-f ：force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖；

-i ：若目标文件 (destination) 已经存在时，就会询问是否覆盖！

-u ：若目标文件已经存在，且 source 比较新，才会升级 (update)

### Linux文件内容查看

cat 由第一行开始显示文件内容

tac 从最后一行开始显示，可以看出 tac 是 cat 的倒着写！

nl  显示的时候，顺道输出行号！

more 一页一页的显示文件内容

less 与 more 类似，但是比 more 更好的是，他可以往前翻页！

head 只看头几行

tail 只看尾巴几行

