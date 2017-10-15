---
layout: post
title: Socket编程-实现echo服务
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

更加高深的东西，等我再研究一会儿。