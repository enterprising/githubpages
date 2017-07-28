---
layout: post
title: Linux基础指令
date: 2017-6-27 20:50
categories: Linux
tags: [Linux]
top: true
---

* content
{:toc}
# 基础命令

## echo

```$ echo Hello World```

用于输出信息

## cat

```$ cat file1 file2…```

用于显示一个或多个文件的内容

标准输入输出，使用 流 

## ls

```$ ls -l```

ls，显示指定目录的内容。

ls -l 显示详细的列表

ls -f 显示文件类型信息

## cp

```$ cp file1 file2```

用于复制文件，上面这条指令是指将文件file1复制到文件file2

```$ cp file1..fileN dir```

将多个文件复制到dir目录下

## mv

类似cp，用于重命名文件

```$ mv file1 file2```

将文件名从file1更变为file2

也可以使用mv将多个文件移动到某个目录：

```$ mv file1 … file2 dir```

## touch

用于创建文件。如果文件已经存在，则该命令会更新文件的时间戳。

```$ touch file```

## rm

用于删除文件，文件一旦被删除通常无法恢复

```$ rm file```

rm -rf的作用：删除目录下面的所有文件。

-r 代表向下递归，不管有多少级目录，一并删除

-f 代表直接强行删除，不做任何提示的意思

## echo

输出

## who

查看目前有哪些用户在线

> [peng.tan@xxx机器名~]$ who  
> shaoyang.qi pts/0        2017-06-22 13:15 (10.12.67.43)  
> peng.tan pts/1        2017-07-12 10:58 (10.12.35.180) 

## telnet

是进行远程登录的标准协议和主要方式。可以用telnet命令来测试端口号是否正常打开还是关闭状态。

今天玩netty的时候，看官方文档的时候用到过。

![屏幕快照 2017-07-24 下午9.35.54](https://ws3.sinaimg.cn/large/006tNc79ly1fhvax2c1qxj30ek05u3yy.jpg)

<br/>

# 浏览目录

## cd

用于设置当前工作目录

```$ cd dir```

如果不带参数，cd命令将会返回你的个人主目录，即你登录系统后进入的目录。

## mkdir

用于创建新目录

```$mkdir dir```

## rmdir

删除目录

```$ rmdir dir```

如果要删除的目录里面有内容，上面的命令就会执行失败。只能删除空目录。

你可以使用：```rm -rf```来删除一个目录已经该目录下面的所有内容。但这条指令十分危险。

## 通配符

主要就是 . 和 *

<br/>

# 中间命令

## grep

用于显示文件和输入流中和参数匹配的行。可以用于文件内的字符串查找。

如：```$ grep root /etc/passwd``` 用于显示文件/etc/passwd中包含文本root的所有行。

在对文件进行批量操作的时候，grep命令会非常好用。因为他显示文件名和匹配的内容。

如果你想查看目录 /etc下面所有包含root的文件，可以执行以下命令：

```$ grep root /etc/*```

grep还有两个很重要的选项，一个是 ： -i (不区分大小写)，一个是 -v(反转匹配，就是显示所有不匹配的行)

grep还能识别正则表达式

## less

用于将内容分屏显示，按空格键可以查看下一屏，B键可以查看上一屏，Q键退出。

```$ top | less```

这个非常非常常用

## pwd

仅用于输出当前的工作目录名。看上去没啥用，其实很有用。主要有以下两个用处：1、并不是所有的提示符都显示当前目录名，甚至有时候你需要摆脱它，因为它占用很大空间，这时候就需要pwd来解决。其次，使用符号链接的时候通常很难获知当前目录信息，这是我们可以使用 pwd -p 来查看

## diff

这个很重要。用于查看两个文件之间的不同。在git里经常有用到。

```$ diff file1 file2```

该命令有几个选项可以让你设置输出结果的格式，如 -u，但是其实不常用。

## find和locate

在目录中寻找文件

```$ find dir -name file -print```

locate与find的区别在于：locate在系统创建的文件索引中查找文件，这个索引由操作系统进行更新，查找速度比find要快。但是要找新创建的文件的时候可能会找不到。

## head和tail

head命令用于显示文件的前10行内容

tail命令用于显示文件的最后10行内容

也可以在后面加上  -n 来指定显示的行数

## sort

用于将文件内的所有行按照字母顺序快速排序。你可以使用 -n 选项按照数字顺序排序那些以数字开头的行，使用 -r 选项反向排序。

<br/>

# 查看Linux服务器性能的指令

对系统的资源情况有大致的了解：

- uptime
- dmesg | tail
- vmstat 1
- mpstat -p ALL 1
- pidstat 1
- iostat -xz 1
- free -m
- sar -n DEV 1
- sar -n TCP,ETCP 1
- top

## uptime

输出平均负载

![1](https://ws4.sinaimg.cn/large/006tNc79ly1fhut4ds19jj30pi01wdga.jpg)

说明：信息输出依次是，系统当前时间、系统开机到现在运行了多长时间、系统当前有多少个登录用户、系统在1分钟 5分钟 10分钟内的平均负载。

注意：如果load average值长期大于系统CPU的个数则说明CPU很繁忙，负载很高，可能会影响系统性能，导致系统卡顿等。

## dmesg

dmesg | tail  输出系统日志的最后10行

## vmstat

vmstat 1  输出系统核心的一些指标    （退出：control + c）

![2](https://ws3.sinaimg.cn/large/006tNc79ly1fhut94vlecj30vo0jswis.jpg)

说明：输出依次是：

  procs：

-  r：在等待CPU资源的进程数。这个数据比平均负载更能体现CPU的负载情况。数据中不包含等待IO的进程。如果这个数值大于机器CPU核数，那么机器的CPU资源以及饱和。
- b：表示在等待资源的进程数，比如正在等待IO或者内存交换等。

  memory：

- swpd：表示切换到内存交换区的内存大小（单位KB）。通俗的讲就是虚拟内存的大小。
- free：表示当前空闲的物理内存（单位KB）
- buff：表示buffers cached 内存大小，也就是缓冲区的大小。
- cache：表示page cached 的内存大小，也就是缓存大小。

  swap：

- si：表示由磁盘调入内存，也就是内存进入内存交换区的内存大小
- so：表示由内存进入磁盘，也就是由内存交换区进入内存的内存大小

  io：

- bi：表示由块设备读入数据的总量 kb/s
- bo：写，kb/s

  system：

- in：表示某一时间间隔内观测到的每一秒设备中断的次数
- cs：表示每秒产生的上下文切换次数

  cpu：

- us：用户进程消耗的CPU时间百分比
- sy：系统内核进程消耗的CPU时间百分比
- id：CPU处于空闲状态下的百分比
- wa：IP等待所占的CPU时间百分比。值越高，说明IO等待越严重。一般正常是 20%

## top

![3](https://ws3.sinaimg.cn/large/006tNc79ly1fhutbmmviej30yg0m8agv.jpg)

说明：能够实时监控系统的运行状态，并且可以按照CPU、内存和执行时间进行排序。

排序说明：

Cpu ： 在top交互界面按shift+p。

Mem ：在top交互界面按shift+m。

Time ：在top交互界面按shift+t。

## ps

列出系统中当前运行的进程。就是process status的缩写。

![屏幕快照 2017-07-24 上午11.29.22](https://ws1.sinaimg.cn/large/006tNc79ly1fhute2mk2ej30vg08cjtc.jpg)

<br/>

# 复杂命令

## 统计代码的行数

``find . -name "*.java" | xargs wc -l``

这里统计的是所有后缀为Java的文件的函数，也可以换成其他的。

``find . -name ".m" -or -name ".h" -or -name ".xib" -or -name ".c" |xargs grep -v "^$"|wc -l ``

统计.m .h .xib .c 文件内容总行数