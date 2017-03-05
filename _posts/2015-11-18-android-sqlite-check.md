---
layout: post
title: Android 查看应用内数据库相关问题
date: 2015-11-18 15:51
categories: Android
tags: [Android,数据库,Sqlite]
---
今天准备学习一下和数据库相关的操作,没有想到在查看APP生成的数据库文件的时候遇到了莫大的麻烦.　　
1. **查看测试APP的本地数据文件夹**  
首先打开Android Studio菜单栏的小机器人的图标打开**DDMS**工具  
![](http://olwt21mf4.bkt.clouddn.com/17-3-3/79749653-file_1488507741431_10341.png)  
然后找到data文件夹，所有APP的本地默认文件夹都在这里  
![](http://olwt21mf4.bkt.clouddn.com/17-3-3/54832184-file_1488507741560_970d.png)  
然后。。。我们会发现。。。打！不！开！  
卧槽！！当场就把我吓到了，然后搜索了一下发现是权限不够，也就是说这是个系统文件夹，一般的进程没有查看系统文件夹的权限，所以接下来就是提权，获得root权限。  
    - **打开adb调试工具**  
打开cmd控制台，然后输入adb  shell就可以进入adb调试台模式，如果没有配置好环境变量，就会出错，这个时候可以直接cd进入Android SDK的安装目录下，在\android-sdk\platform-tools目录下可以找到adb.exe，然后再输入adb shell可以打开工具  
![](http://olwt21mf4.bkt.clouddn.com/17-3-3/6498061-file_1488507741702_f4a9.png)  
    - **输入su命令获取手机的root权限**
![](http://olwt21mf4.bkt.clouddn.com/17-3-3/68076519-file_1488507837196_c60e.png)  
然后输入  
```chmod 777 /data/data/文件夹名 /data/data/文件夹名/*```
改变系统文件夹的挂载模式，然后APP生成的数据库文件就可以查看了.
![](http://olwt21mf4.bkt.clouddn.com/17-3-3/78015318-file_1488507741838_1e49.png)  
2. **打开数据库文件**  
在\android-sdk\platform-tools下还提供有sqlite数据库的访问工具  
![](http://olwt21mf4.bkt.clouddn.com/17-3-3/74448529-file_1488507741964_1113e.png)  
在cmd中可以直接使用该工具打开从手机导出的数据库文件  
![](http://olwt21mf4.bkt.clouddn.com/17-3-3/86706107-file_1488507742084_100ba.png)