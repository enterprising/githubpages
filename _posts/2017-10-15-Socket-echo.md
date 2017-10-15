---
layout: post
title: Socket编程-实现echo服务(单进程，多进程，多线程三个版本)
date: 2017-10-15 12:00
categories: 计算机网络
tags: [计算机网络]
---

* content
{:toc} 
# 说明

这个是网络编程的入门。。

所谓的echo服务就是： 实现这么一种服务，服务端客户端之间建立起Socket连接。然后服务端监听，当有连接进入时，就Accept它。无需做任何处理，只要把收到的内容照样返回过去就行。要求用 C语言实现，不能使用高级的协议。

主要目的是为了熟悉中间的过程和原理。

# 单进程版

## 原理说明

这中间的过程其实并不复杂。

简单的来说。

服务端：

1. 创建socket；
2. bind()，绑定服务器地址和端口等信息；
3. listen()，开始监听这个端口上的所有信息；
4. 如果监听到了，然后： accept() 连接，这时候会得到一个clientSocketID；
5. 通过 clientSocketID，进程recv()、send()、read()、write()等操作

客户端：

1. 创建socket；
2. connet()；连接指定计算机端口；
3. send()、recv() 等操作；

## 代码

```c
#include <stdlib.h>
#include <sys/socket.h>
#include <stdio.h>
#include <netinet/in.h>
#include <strings.h>
#include <zconf.h>
int main(int argc, char *argv[]) {
    int port = atoi(argv[1]);
    int socketId, clientSocktId;
    char buf[1024];
    struct sockaddr_in sockAddr;
    socketId = socket(AF_INET, SOCK_STREAM, 0);
    if (socketId < 0) {
        perror("socket()");
        exit(-1);
    }
    // 帮地址三元组赋值
    sockAddr.sin_port = htons(port);
    sockAddr.sin_family = AF_INET;
    sockAddr.sin_addr.s_addr = htonl(INADDR_ANY);
    int b = bind(socketId, (struct sockaddr *) &sockAddr, sizeof(sockAddr));
    if (b < 0) {
        perror("bind()");
        exit(-1);
    }
    int l = listen(socketId, 6);
    if (l < 0) {
        perror("listen()");
        exit(-1);
    }
    while (1) {
        clientSocktId = accept(socketId, NULL, NULL);
        memset(buf, 0, sizeof(buf));
        while (recv(clientSocktId, buf, sizeof(buf), 0) > 0) {
            printf("receive: %s", buf);
            send(clientSocktId, buf, strlen(buf), 0);
            memset(buf, 0, sizeof(buf));
        }
        close(clientSocktId);
        exit(0);
    }
}
```

# 多进程版

## 原理说明

简单的多进程版，其实并不复杂。

这里面最关键的一句就是：fork() 操作，通过fork()这一系统调用，会创建出来一个新的进程。

我们监听到一个Connection只后，就fork出一个新的进程来处理这一连接。然后主进程继续返回监听。

## 关于fork()的说明

一、作用

fork() 函数通过系统调用，创建一个与原来进程完全相同的进程。也就是两个进程可以做完全相同的事，当然，参数不同，也可以做不同的事。

一个进程调用 fork() 之后，系统会先给新的进程分配资源。然后把老进程里面的值复制到新进程里面去。相当于克隆了一个自己。

二、父子关系

在一个进程里fork一个进程，老的就是父进程。新的叫子进程。

这里有个非常重要的点，就是：

fpid = fork(); 

这个函数，在父进程里面，返回的是子进程的进程ID，但是子进程那边这个值是 0 。这就是实现 echo 多进程的关键。

## 代码

```c
#include <stdlib.h>
#include <sys/socket.h>
#include <stdio.h>
#include <netinet/in.h>
#include <strings.h>
#include <zconf.h>
int main(int argc, char *argv[]) {
    int port = atoi(argv[1]);
    int socketId, clientSocktId;
    pid_t childId;
    char buf[1024];
    struct sockaddr_in sockAddr;
    socketId = socket(AF_INET, SOCK_STREAM, 0);
    if (socketId < 0) {
        perror("socket()");
        exit(-1);
    }
    // 帮地址三元组赋值
    sockAddr.sin_port = htons(port);
    sockAddr.sin_family = AF_INET;
    sockAddr.sin_addr.s_addr = htonl(INADDR_ANY);
    int b = bind(socketId, (struct sockaddr *) &sockAddr, sizeof(sockAddr));
    if (b < 0) {
        perror("bind()");
        exit(-1);
    }
    int l = listen(socketId, 6);
    if (l < 0) {
        perror("listen()");
        exit(-1);
    }

    while (1) {
        clientSocktId = accept(socketId, NULL, NULL);
        if (clientSocktId != 0) {
            //创建子进程来处理这个连接上的信息
            childId = fork();
        }
        if (childId != 0) { //对于父进程来说，这个值是0.
            close(clientSocktId); //关闭父进程上的这个客户端Socket，因为已经交给子进程去处理了
            continue;
        }
        memset(buf, 0, sizeof(buf));
        while (recv(clientSocktId, buf, sizeof(buf), 0) > 0) {
            printf("receive: %s", buf);
            send(clientSocktId, buf, strlen(buf), 0);
            memset(buf, 0, sizeof(buf));
        }
        close(clientSocktId);
        exit(0);
    }
}
```

# 多线程版

多线程版和多进程版，都是在单进程版的基础上进行优化修改的。

## 原理说明

像多进程版的 fork() 一样，在多线程版里面最关键的也是一个系统调用。**pthread_create()**。

1. pthread_t

   说明：这是一个线程的标识符，类似于 Socket标识符socketID、文件标识符fd那种。

2. pthread_create()

   原型是：

   > int pthread_create(pthread_t * __restrict, const pthread_attr_t * __restrict,      void *(*)(void *), void * __restrict);

   简单说明一下它的四个参数：

   - 第一个参数是指向线程标识符的指针；
   - 第二个参数是线程的属性（pthread_attr_t）;
   - 第三个参数是线程运行函数的起始地址；
   - 第四个参数是线程运行函数的参数，没有的时候设置NULL；

   使用demo：

   > pthread_t clientThread;
   > pthread_create(&clientThread, NULL, &echo, &clientID);

   echo函数的定义：

   > void *echo(void *argv) {}

   echo函数里对参数的获取：

   > int *temp = (int *) argv;
   >
   > int id = *temp;

## 代码

```c
//
// 多线程版 echo 服务
// Created by peng.tan on 17/10/15.
//
#include <cstdlib>
#include <netinet/in.h>
#include <pthread.h>
#include <strings.h>
#include <stdio.h>
#include <zconf.h>

void *echo(void *argv) {
    //获取传进来的参数
    int *clientID_Temp = (int *) argv;
    int clientID = *clientID_Temp;
    char buf[1024];
    bzero(buf, 1024);
    while (recv(clientID, buf, sizeof(buf), 0) > 0) {
        printf("receive: %s", buf);
        send(clientID, buf, strlen(buf), 0);
    }
    close(clientID);
    return NULL;
}

int main(int argc, char *argv[]) {

    int prot = atoi(argv[1]);
    int socketID;
    struct sockaddr_in servAddr;

    socketID = socket(AF_INET, SOCK_STREAM, 0);
    servAddr.sin_family = AF_INET;
    servAddr.sin_port = htons(prot);  //将本地的无符号短整型变为 网络字节序

    //INADDR_ANY就是指定地址为0.0.0.0的地址，这个地址事实上表示不确定地址
    servAddr.sin_addr.s_addr = htonl(INADDR_ANY); //将本地的无符号长整型变为 网络字节序；

    bind(socketID, (struct sockaddr *) &servAddr, sizeof(servAddr));
    listen(socketID, 10);

    while (1) {
        int clientID = accept(socketID, NULL, NULL);
        pthread_t clientThread;
        pthread_create(&clientThread, NULL, &echo, &clientID);
    }
}
```

# 如何查看效果

1. 进入shell控制台

2. 进入项目目录下面

   gcc -o xxx(名字) xxx.c(文件名)

3. 之后会生成一个 xxx.out 的文件

   ./xxx 就能运行了，有参数的话，直接在后面加参数

至此，服务端就启起来了。关闭的话，除了代码内关闭逻辑，还可以用 ctrl + c 来关闭。

测试运行效果的话，这里没写客户端。客户端用 telnet 指令代替了。

打开另一个shell控制台，输入 telnet 127.0.0.1 xxx(服务的端口号)

如果服务端没问题，那这时候就连上去了。直接输入内容，然后回车。就触发了send()系统调用，这时候你就能看到服务端返回的是不是你输入的东西了。

因为这里写了三个版本，每个版本的效果不太一样。就不放截图了。