---
layout: post
title: Git的使用-筛选/删除不必要的代码
date: 2017-8-30 16:00
categories: Git
tags: [Git,版本控制]
---

* content
{:toc} 
关于一些Git的简单指令以及分支切换、pull request那些没什么好介绍的。关于这个东西是我上上个月踩的一个坑，无意间想起来，所以整理一下。

这里只简单说一下GitHub的一个坑。简单的说就是 .gitignore 文件，这个当时没怎么注意，其实用处非常大。主要用于筛选部分不必要提交的代码，比如一些工具的配置文件、不必要的程序字节码等。

# gitignore文件

demo：

```java
.gitignore
.settings/
.project
.classpath
.DS_Store
target/
tmp/
项目名/logs
项目名/test-output/
项目名/jmp.log*
项目名/conf/git.properties

# idea
.idea
*.eml
*.iml
testscript/__pycache__/
```

根据实际情况自己修改。有了这个文件之后，Git端的代码就会简洁很多。

但如果你已经把那些乱七八糟的东西提交上去了呢？怎么删？

# 文件删除

这里主要是用一些简单的Linux指令。

> git rm -r -n --cached  */src/\*      //-n：加上这个参数，执行命令时，是不会删除任何文件，而是展示此命令要删除的文件列表预览。
>
> git rm -r --cached  */src/\*      //最终执行命令.
>
> git commit -m"移除src目录下所有文件的版本控制"    //提交
>
> git push origin master   //提交到远程服务器

这样就能把Git端不必要的文件或者文件夹删除了，而且不会影响到本地。这时候再加上 .gitignore 文件，就美滋滋了，不然你每次都要手动筛选那些不必要提交的代码。