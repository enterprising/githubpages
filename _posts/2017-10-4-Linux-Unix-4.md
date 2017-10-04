---
layout: post
title: 《Linux/Unix系统编程手册》-第四章 文件I/O
date: 2017-10-1 9:00
categories: Core
tags: [Core]
---

* content
{:toc} 
# 概述

所有执行IO操作的系统调用都以文件描述符来指代打开的文件。

这里的文件包括：管道、FIFO、Socket、终端、设备和普通文件。

标准的文件描述符：

| 文件描述符 | 用途   | Posix名称       | stdio流 |
| ----- | ---- | ------------- | ------ |
| 0     | 标准输入 | STDIN_FILENO  | stdin  |
| 1     | 标准输出 | STDOUT_FILENO | stdout |
| 2     | 标准错误 | STDERR_FILENO | stderr |

四个主要的系统调用：

- fd = open(pathname,flags,mode); flags参数主要是指代的打开方式（只读或可写）、mode参数指代了由open()调用创建文件的访问权限，如果已存在不需要创建，那么这个参数可以忽略；
- numread = read(fd, buffer, count); 调用从fd指代的文件中读取至多count字节的数据，并存储到buffer中。返回值为实际读取到的字节数，如果再无字节可读，则返回0；
- numwritten = write(fd, buffer, count); 调用从buffer中读取多达count字节的数据，然后写入由fd指代的文件中。返回实际写入文件中的字节数；
- status = close(fd); 释放文件描述符 fd 以及与之相关的内核资源。

# 通用 I/O

所谓的通用，并不是指跨系统通用。。这个比较的维度在于，上面四个系统调用，在Unix下，对所有类型的文件都可以执行IO操作。这叫做通用IO。

一旦应用程序需要访问文件系统或设备的专有功能时，可以选择瑞士军刀般的 ioctl()系统调用，该调用为通用IO模型之外的专有特性提供了访问接口。

# 打开一个文件：open()

> int open(const char *pathname,int flags,node_t mode); 

这个系统调用，返回文件描述符：fd。

位掩码参数指定了文件的访问权限（flags）：

| 访问模式     | 描述         |
| -------- | ---------- |
| O_RDONLY | 以只读的方式打开文件 |
| O_WRONLY | 以只写的方式打开文件 |
| O_RDWR   | 以读写的方式打开文件 |

## open()调用中的 flags 参数

就是上面这张表的扩展，包含 O_CREAT, O_TRUNC, O_APPEND等。

大概可以分成三组：

- 文件访问模式标志：O_RDONLY, O_WRONLY, O_RDWR 等，调用open() 时，这三者不能同时使用，只能指定一个；
- 文件创建标志：这些标志不能检索，也无法修改。O_CREAT等；
- 已打开文件的状态标志；

具体某一项的意思，用到的时候再查表吧。

## open()函数的错误

open() 返回-1，错误号 errno 标识错误原因：

- EACCES：权限问题
- EISDIR：所指定的文件属于目录
- EMFILE：进程已打开的文件描述符数量达到了进程资源限制所设定的上线
- ENFILE：文件打开数量已经达到系统允许的上限
- ENOENT：文件不存在且未指定 O_CREAT标识（不存在则创建），或者指定了O_CREAT标志，但文件路径目录不存在。。或者符号链接为空链接
- EROFS：所指定的文件隶属于只读文件系统，而调用者企图以写的方式打开文件
- ETXTBSY：所指定的文件为可执行文件，且正常运行。系统不允许修改正在运行的程序

## creat()系统调用

在早期的Unix实现中，open只有两个参数，无法创建新的文件，而是使用create()系统调用来创建并打开一个新文件。

如果已存在，那么会打开文件，并清空文件的内容。

# 读取文件内容：read()

从文件描述符 fd 所指代的打开文件中读取数据。

> ssize_t read(in fd,void *buffer,size_t count); 

count 参数指定最多能读取的字节数，buffer参数提供用来存放输入数据的内存缓冲区地址。缓冲区至少有count个字节。

如果read调用成功，则返回实际读取到的字节数，如果遇到文件结束（EOF）则返回0，如果出现错误则返回 -1。

# 数据写入文件：write()

将输入写入一个已打开的文件中。

> ssize_t write(int fd, void * buffer, size_t count);

参数的含义类似read。

如果调用成功，将返回实际写入文件的字节数，该返回值可能小于count参数值。

对磁盘文件执行 IO 操作时，write() 调用成功并不能保证数据已经写入磁盘。因为为了减少磁盘活动量和加快 write() 系统调用，内核会缓存磁盘的 IO 操作。

# 关闭文件：close()

关闭一个打开的文件描述符，并将其释放回调用进程，供该进程继续使用。

当一进程终止时，将自动关闭其已打开的所有文件描述符。

> int close(int fd);

# 改变文件偏移量：lseek()

对于每个打开的文件，系统内核会记录其文件偏移量，有时也将文件偏移量称为读写偏移量或指针。

所谓的文件偏移量，其实指的是 执行下一个 read()或 write()操作的文件起始位置，会以相对于文件头部起始点的文件当前位置来表示。

文件的第一个字节的偏移量是 0。

文件打开后，每次read()或 write() 调用将自动对其进行调整。指向已读或已写数据后的下一字节。

> off_t  lseek(int fd, off_t  offset, int whence);

offset 参数指定了一个以字节为单位的数值；

whence 参数则表明应参照哪个基点来解释 offset 参数。

whence包含了以下几种类型：

- SEEK_SET：将文件偏移量设置为从文件头部起始点开始的offset个字节；
- SEEK_CUR：相对于当前文件偏移量，将文件偏移量调整 offset个字节；
- SEEK_END：将文件偏移量设置为其实于文件尾部的offset 个字节。也就是说，offset参数应该从文件最后一个字节的下一个字节算起。

图解：

![](https://ws1.sinaimg.cn/large/006tNc79ly1fk6di3qwh5j30su0a6adp.jpg)

lseek() 调用成功，会返回新的文件偏移量。

相遇于文件头部的绝对偏移量 = 当前文件偏移量+offset。

当然，lseek() 调用只是调整内核中与文件描述符相关的文件偏移量记录，并没有引起对任何物理设备的访问。

**文件空洞：**

如果程序的文件偏移量已经跨越了文件结尾，然后再执行 IO 操作。。read()调用将返回0，表示文件结尾。但write()函数可以在文件结尾后的任意位置写入数据。。

那段不应该存在的空间，就叫做文件空洞。

seek demo：

```c
#include <sys/stat.h>
#include <fcntl.h>
#include <ctype.h>
#include "tlpi_hdr.h"

int
main(int argc, char *argv[])
{
    size_t len;
    off_t offset;
    int fd, ap, j;
    char *buf;
    ssize_t numRead, numWritten;

    if (argc < 3 || strcmp(argv[1], "--help") == 0)
        usageErr("%s file {r<length>|R<length>|w<string>|s<offset>}...\n",
                 argv[0]);

    fd = open(argv[1], O_RDWR | O_CREAT,
                S_IRUSR | S_IWUSR | S_IRGRP | S_IWGRP |
                S_IROTH | S_IWOTH);                     /* rw-rw-rw- */
    if (fd == -1)
        errExit("open");

    for (ap = 2; ap < argc; ap++) {
        switch (argv[ap][0]) {
        case 'r':   /* Display bytes at current offset, as text */
        case 'R':   /* Display bytes at current offset, in hex */
            len = getLong(&argv[ap][1], GN_ANY_BASE, argv[ap]);

            buf = malloc(len);
            if (buf == NULL)
                errExit("malloc");

            numRead = read(fd, buf, len);
            if (numRead == -1)
                errExit("read");

            if (numRead == 0) {
                printf("%s: end-of-file\n", argv[ap]);
            } else {
                printf("%s: ", argv[ap]);
                for (j = 0; j < numRead; j++) {
                    if (argv[ap][0] == 'r')
                        printf("%c", isprint((unsigned char) buf[j]) ?
                                                buf[j] : '?');
                    else
                        printf("%02x ", (unsigned int) buf[j]);
                }
                printf("\n");
            }

            free(buf);
            break;

        case 'w':   /* Write string at current offset */
            numWritten = write(fd, &argv[ap][1], strlen(&argv[ap][1]));
            if (numWritten == -1)
                errExit("write");
            printf("%s: wrote %ld bytes\n", argv[ap], (long) numWritten);
            break;

        case 's':   /* Change file offset */
            offset = getLong(&argv[ap][1], GN_ANY_BASE, argv[ap]);
            if (lseek(fd, offset, SEEK_SET) == -1)
                errExit("lseek");
            printf("%s: seek succeeded\n", argv[ap]);
            break;

        default:
            cmdLineErr("Argument must start with [rRws]: %s\n", argv[ap]);
        }
    }

    if (close(fd) == -1)
        errExit("close");

    exit(EXIT_SUCCESS);
}

```

# 通用 I/O 模型以外的操作：ioctl()

ioctl()系统调用为执行文件和设备操作提供了一种多用途机制。

> int ioctl(int fd, int request, ….);

fd，为文件描述符；

request参数指定了将在 fd 上的控制操作；

第三个参数，可以是任意数据类型，ioctl()根据 request 的参数值来确定 argp 所期望的类型。通常情况下是指向整数或结构的指针。

一般用途的话，官方定义为：针对流设备的控制操作。

# 总结

为了对普通文件执行 IO 操作，首先必须调用 open() 以获得一个文件描述符，然后根据这个，再使用read() 和 write()。

对于那两个系统调用，最核心的其实是参数的把握。

对于已打开的每个文件，内核都维护有一个文件偏移量，**这决定了下一次读或写操作的起始位置。**

对于未纳入标准IO模型的所有设备和文件操作而言，ioctl()系统调用是一个“百宝箱”。

# 练习

先将上面东西消化一下，晚点更。



