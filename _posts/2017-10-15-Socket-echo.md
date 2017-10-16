---
layout: post
title: Socket编程-实现echo服务(单线程，多进程，多线程三个版本)
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

# 单线程版

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
//
// 单线程版 echo 服务
// Created by peng.tan on 17/10/15.
//
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <stdio.h>
#include <unistd.h>
#include <string.h>
#include <stdlib.h>
#define BUFFER_SIZE 1024 //缓冲区长度
#define backlog 5  // 排队等待Socket连接的最大数目
/**
 * 读取客户端Socket发来的信息，并处理、最后返回
 * @param client
 */
void echo(int client_sockfd) {
    char senBuff[BUFFER_SIZE];
    char receiveBuff[BUFFER_SIZE];
    memset(senBuff, 0, sizeof(senBuff));
    memset(receiveBuff, 0, sizeof(receiveBuff));
    while (recv(client_sockfd, receiveBuff, sizeof(receiveBuff), 0) > 0) {
        printf("received: %s", receiveBuff);
        strcpy(senBuff, receiveBuff);
        send(client_sockfd, senBuff, strlen(senBuff), 0);
    }
    close(client_sockfd);
}
int main(int argc, char *argv[]) {
    int sockfd = 0;
    int client_sockfd = 0;
    int ref = 0;
    int port = 1234;
    struct sockaddr_in serverAddress;
    sockfd = socket(AF_INET, SOCK_STREAM, 0);
    if (sockfd < 0) {
        perror("socket() error!");
        exit(-1);
    }
    if (argv[1] != NULL) {
        port = atoi(argv[1]);
    }
    memset(&serverAddress, 0, sizeof(serverAddress));
    serverAddress.sin_family = AF_INET;
    serverAddress.sin_addr.s_addr = htonl(INADDR_ANY);
    serverAddress.sin_port = htons(port);
    ref = bind(sockfd, (struct sockaddr *) &serverAddress, sizeof(serverAddress));
    if (ref < 0) {
        perror("bind() error!");
        exit(-1);
    }
    ref = listen(sockfd, backlog);
    if (ref < 0) {
        perror("listen() error!");
        exit(-1);
    }
    struct sockaddr_in clientAddress;
    memset(&clientAddress, '0', sizeof(clientAddress));
    socklen_t cli_len;
    cli_len = sizeof(clientAddress);
    while (1) {
        client_sockfd = accept(sockfd, (struct sockaddr *) &clientAddress, &cli_len);
        if (client_sockfd < 0) {
            perror("accept() error!");
            exit(-1);
        }
        printf("client id:%s, port:%d\n", inet_ntoa(clientAddress.sin_addr), ntohs(clientAddress.sin_port));
        echo(client_sockfd);
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
//
// 多进程版 echo 服务
// Created by peng.tan on 17/10/15.
//
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <stdio.h>
#include <unistd.h>
#include <string.h>
#include <stdlib.h>

#define BUFFER_SIZE 1024 //缓冲区长度
#define backlog 5  // 排队等待Socket连接的最大数目

/**
 * 读取客户端Socket发来的信息，并处理、最后返回
 * @param client
 */
void echo(int client_sockfd) {
    char receiveBuff[BUFFER_SIZE];
    memset(receiveBuff, 0, sizeof(receiveBuff));
    while (recv(client_sockfd, receiveBuff, sizeof(receiveBuff), 0) > 0) {
        printf("received: %s", receiveBuff);
        send(client_sockfd, receiveBuff, strlen(receiveBuff), 0);
        memset(receiveBuff, 0, sizeof(receiveBuff));
    }
    close(client_sockfd);
}
int main(int argc, char *argv[]) {
    int port = 1234;
    int sockfd, client_sockfd;
    int ret = 0;
    struct sockaddr_in serv_addr;
    if (argv[1] != NULL)
        port = atoi(argv[1]);
    pid_t child_pid;
    printf("starting server..\n");
    sockfd = socket(AF_INET, SOCK_STREAM, 0);
    if (sockfd < 0) {
        perror("socket() error!");
        exit(-1);
    }
    memset(&serv_addr, 0, sizeof(serv_addr));

    serv_addr.sin_family = AF_INET;
    serv_addr.sin_addr.s_addr = htonl(INADDR_ANY);
    serv_addr.sin_port = htons(port);

    ret = bind(sockfd, (struct sockaddr *) &serv_addr, sizeof(serv_addr));
    if (ret < 0) {
        perror("bind socket error!");
        exit(-1);
    }
    ret = listen(sockfd, backlog);
    if (ret < 0) {
        perror("listen socket error");
        exit(-1);
    }
    struct sockaddr_in clientAddress; //保存用户Socket信息
    memset(&clientAddress, '0', sizeof(clientAddress));
    socklen_t cli_len;
    cli_len = sizeof(clientAddress);
    while (1) {
        client_sockfd = accept(sockfd, (struct sockaddr *) &clientAddress, &cli_len);
        if (client_sockfd < 0) {
            perror("accept error!");
            exit(-1);
        }
        child_pid = fork();  // fork()出一个子进程
        printf("child_pid = %d\n", child_pid);
        if (child_pid < 0) {
            perror("fork progress error!");
            exit(-1);
        }
        // 这里表示，父进程 继续监听连接。continue后面的由子进程处理
        if (child_pid != 0) {
            close(client_sockfd);
            continue;
        }
        printf("client id:%s, port:%d\n", inet_ntoa(clientAddress.sin_addr), ntohs(clientAddress.sin_port));
        echo(client_sockfd);
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
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <stdio.h>
#include <unistd.h>
#include <string.h>
#include <stdlib.h>
#include <pthread.h>
#define backlog 5

void *echo(void *argv) {
    //获取传进来的参数
    int *clientID_Temp = (int *) argv;
    int clientID = *clientID_Temp;
    char buf[1024];
    bzero(buf, 1024);
    while (recv(clientID, buf, sizeof(buf), 0) > 0) {
        printf("receive: %s", buf);
        send(clientID, buf, strlen(buf), 0);
        bzero(buf, 1024);
    }
    close(clientID);
    return NULL;
}
int main(int argc, char *argv[]) {
    int port = 1234;  //默认端口
    int sockfd;
    struct sockaddr_in servAddr;
    int ref = 0;
    sockfd = socket(AF_INET, SOCK_STREAM, 0);
    if (sockfd < 0) {
        perror("socket error!");
        exit(-1);
    }
    if (argv[1] != NULL)
        port = atoi(argv[1]);
    servAddr.sin_family = AF_INET;
    servAddr.sin_port = htons(port);  //将本地的无符号短整型变为 网络字节序
    //INADDR_ANY就是指定地址为0.0.0.0的地址，这个地址事实上表示不确定地址
    servAddr.sin_addr.s_addr = htonl(INADDR_ANY); //将本地的无符号长整型变为 网络字节序；
    ref = bind(sockfd, (struct sockaddr *) &servAddr, sizeof(servAddr));
    if (ref < 0) {
        perror("bind socket error!");
        exit(-1);
    }
    ref = listen(sockfd, backlog);
    if (ref < 0) {
        perror("listen socket error!");
        exit(-1);
    }
    while (1) {
        int clientID = accept(sockfd, NULL, NULL);
        if (clientID < 0) {
            perror("accept error");
            exit(-1);
        }
        pthread_t clientThread;
        pthread_create(&clientThread, NULL, &echo, &clientID);
        if (clientThread < 0) {
            perror("create thread error!");
            exit(-1);
        }
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