---
layout: post
title: Git的gitignore与SSH
date: 2017-11-14 16:00
categories: Git
tags: [Git]
---

* content
{:toc} 
对于使用 git 很久的人来说，这两个东西肯定不陌生了。但对于新手来说可能会遇到一些问题。

比如：GitHub或者GitLab总是上传了一些自己不想上传的，或者根本没有必要上传的配置文件或者临时文件等。

其实要解决这个问题很简单，只需要在项目的目录下面配置好 .gitignore 文件就好了。

clone项目的时候会发现有两种方式：https和ssh，使用ssh需要配置 SSH 密钥。

# gitignore

demo

```
.idea
*.eml
*.iml
target/
.settings/
.project
.classpath
.DS_Store
```

说明：

.idea 表示隐藏目录 “.idea” 下面的所有文件

*.eml 表示所有的eml 后缀的文件

target/ 表示 target 目录下面的文件

要理解这个很简单，难的是我们会发现，这东西很难生效。

因为 .gitignore只能忽略那些原来没有被track的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的。那么解决方法就是先把本地缓存删除（改变成未track状态），然后再提交：

```
git rm -r --cached .

git add .

git commit -m 'update .gitignore'
```

# SSH Key

这个主要是为了安全。要理解这个，首先需要知道 ssh 的原理：

> 对称加密和非对称加密：
>
> **对称加密**指的是，将你要传输的内容用一个秘钥加密起来，发送方和接收方都要知道这个秘钥，用它来加密解密。但是使用对称机密需要给对方传这个密钥，这个很容易在传输过程中被人窃取。
>
> **非对称加密**是指生产一堆密钥（k1,k2）。凡是k1加密的数据，k1自身不能解密，需要k2解密；凡是k2加密的数据，k2不能解密，需要k1才能解密。
>
> SSL/TLS协议的做法是将两者结合起来：
>
> 1、加入A、B需要传输信息，那么A可以使用非对称加密生成一对密钥（k1，k2），然后将k1发送给B，k2自己保留；
>
> 2、B收到k1后先自己使用对称加密生成一个key，然后使用k1将这个key加密传给A；
>
> 3、A收到密文后使用k2将其解密，至此，A和B已经完成了key的传输，之后他们就可以使用这个key按对称加密的方式传输信息。
>
> 为什么这样就安全了呢？因为在传输过程中值暴露了k1，要解密需要知道k2，所以就算有人窃取到了传输的信息，也无法解密。

**生成SSH KEY**: 

**ssh-keygen -t rsa -C "your_email@example.com"** , 然后会提示输入公钥的名字， 如果你需要多个SSH-KEY（比如有多个github帐号）就需要在命名的时候区分一下，比如输入"/home/chen/.ssh/xyz_rsa"这样的名字，这样在/home/chen/.ssh/文件夹下生成两个文件: xyz_rsa.pub和xyz_rsa, 分别是你的公钥和私钥.

生成SSH KEY的时候还要求输入**私钥密码**"│Enter passphrase (empty for no passphrase):"， 请记住私钥的密码， 下面导入私钥的时候要用到。

将SSH 私钥增加到ssh-agent: ssh-add ~/.ssh/xyz_rsa， 这里会提示输入一次私钥的密码;

有时候已经有秘钥了，然后加入 git 的时候，要输入的话，记得复制完整。从第一个字复制到最后的邮箱。要填的是公钥，也就是  xyz_rsa.pub 下面的东西

# 参考博客

[Github的SSH KEY配置](http://www.jianshu.com/p/ddd3122cb351)