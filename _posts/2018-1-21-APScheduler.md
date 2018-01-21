---
layout: post
title: Python任务调度APScheduler
date: 2018-1-21 12:10
categories: Python
tags: [Python,任务调度]
---

* content
{:toc} 


不管在哪种语言下面，任务调度似乎总是很重要的一种东西。本篇文章介绍一下Python中用得比较多的一种任务调度框架： APScheduler。

它下面有几个很重要的概念，一个是scheduler，一个是job。

只介绍我用到的东西，APScheduler里面还很多细节可以慢慢挖掘，可以多看看官方文档。

# 主要的几种Scheduler

- [`BlockingScheduler`](http://apscheduler.readthedocs.io/en/latest/modules/schedulers/blocking.html#apscheduler.schedulers.blocking.BlockingScheduler): use when the scheduler is the only thing running in your process
- [`BackgroundScheduler`](http://apscheduler.readthedocs.io/en/latest/modules/schedulers/background.html#apscheduler.schedulers.background.BackgroundScheduler): use when you’re not using any of the frameworks below, and want the scheduler to run in the background inside your application
- [`AsyncIOScheduler`](http://apscheduler.readthedocs.io/en/latest/modules/schedulers/asyncio.html#apscheduler.schedulers.asyncio.AsyncIOScheduler): use if your application uses the asyncio module
- [`GeventScheduler`](http://apscheduler.readthedocs.io/en/latest/modules/schedulers/gevent.html#apscheduler.schedulers.gevent.GeventScheduler): use if your application uses gevent
- [`TornadoScheduler`](http://apscheduler.readthedocs.io/en/latest/modules/schedulers/tornado.html#apscheduler.schedulers.tornado.TornadoScheduler): use if you’re building a Tornado application
- [`TwistedScheduler`](http://apscheduler.readthedocs.io/en/latest/modules/schedulers/twisted.html#apscheduler.schedulers.twisted.TwistedScheduler): use if you’re building a Twisted application
- `QtScheduler`: use if you’re building a Qt application

这里用得比较多的应该是第一个和第二个。我在项目里面用的是第二个。因为不需要多线程，不需要异步IO，所以没用第三个。我的应用场景只是把一个job扔到后台就行了，然后让它定时执行。

# 三种job的介绍

- [`date`](http://apscheduler.readthedocs.io/en/latest/modules/triggers/date.html#module-apscheduler.triggers.date): use when you want to run the job just once at a certain point of time
- [`interval`](http://apscheduler.readthedocs.io/en/latest/modules/triggers/interval.html#module-apscheduler.triggers.interval): use when you want to run the job at fixed intervals of time
- [`cron`](http://apscheduler.readthedocs.io/en/latest/modules/triggers/cron.html#module-apscheduler.triggers.cron): use when you want to run the job periodically at certain time(s) of day

date是最简单的，表示在某个时间点运行。interval表示定时执行。cron：表示每天定时执行。

# 使用姿势

```python
def my_job():
    print(time.strftime("%Y-%m-%d %H:%M:%S", time.localtime()) + 'hello world')

    
sched = BackgroundScheduler()

# sched.add_job(my_job, 'interval', seconds=10, start_date='{}'.format(execute_time))
sched.add_job(my_job, 'cron', hour='15', minute='*', second='10,20,30,40,50,0')
```

参数的配置很简单，后面那个10，20，30... 表示，在10秒、20秒、30秒的时候执行。当然，你也可以不配逗号。

一般一天执行一次差不多了，看需求吧。

# 新增/删除job

新增：

```python
job = sched.add_job(multi_ezone_filter, 'cron', hour=hour, minute=minute, second=second,
                                args=(env_value,))
```

删除

```python
job.remove()
```



# 参考资料

1. [APscheduler官方文档](http://apscheduler.readthedocs.io/en/latest/userguide.html)