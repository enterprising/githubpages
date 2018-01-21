---
layout: post
title: Python web框架flask的URL路由
date: 2018-1-21 12:00
categories: Python
tags: [Python, flask]
---

* content
{:toc} 
关于flask的详细介绍网上有很多教程，这里不详细描述。

先说背景：这两周负责的一个东西，需要以Python脚本的文件运行，然后经过考虑，决定把那个脚本放入到我们组的一个GUI项目下面。以URL的形式作为脚本的触发/关闭条件。

这里只介绍flask框架的基本路由使用姿势

# 概述

这里描述的是如何在框架中改动代码，从而使得 浏览器中输入 127.0.0.1:\<port\>/start 之后，web上输出hello world的过程。

demo:

![](https://ws4.sinaimg.cn/large/006tKfTcly1fno33cbasqj30by03k0sv.jpg)

1. 建个python文件，先不写任何东西

2. 修改\_\_init\_\_.py

   ```python
   from flask import Blueprint

   multi_ezone = Blueprint('multi_ezone', __name__)

   from . import multi_ezone_filter
   ```

3. 配置根目录下面的 app.py 文件

   ```python
   app = Flask(__name__)
   from .multi_ezone import multi_ezone as multi_ezone_blueprint
   app.register_blueprint(multi_ezone_blueprint)
   ```

4. 配置route （也就是修改第一步创建的那个文件）

   ```python
   @multi_ezone.route('/start', methods=['GET'])
   def multi_ezone_filter_start():
     return 'hello world'
   ```

这样就可以了

# 路由传参

有两种姿势，一个是在URL中直接指定 \<xxx\> ，最后在route方法的参数里直接带上xxx，就可以直接用了。

还一种是 在URL后面带上 ?env=xxx&time=xxxx的形式

这里主要介绍下面那种 RESTful 形式。

URL的拼接没什么好说的，就是上面说的带问号那种。

这里直接介绍取参的方法：

```python
env = request.args.get('env', type=str)
time = request.args.get('time', type=str)
```

type可以指定类型。