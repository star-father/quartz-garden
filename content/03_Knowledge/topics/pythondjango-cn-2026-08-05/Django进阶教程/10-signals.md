---
type: topic
title: "Django信号机制及示例"
date: 2026-08-05
category: Django进阶教程
source_url: https://pythondjango.cn/django/advanced/10-signals/
author: 大江狗
tags:
  - Django进阶教程
  - status/done
  - domain/coding
  - type/topic
publish: true
---

# Django信号机制及示例
## 目录
1. [信号的工作机制](#%E4%BF%A1%E5%8F%B7%E7%9A%84%E5%B7%A5%E4%BD%9C%E6%9C%BA%E5%88%B6)
2. [信号的应用场景](#%E4%BF%A1%E5%8F%B7%E7%9A%84%E5%BA%94%E7%94%A8%E5%9C%BA%E6%99%AF)
3. [两个简单例子](#%E4%B8%A4%E4%B8%AA%E7%AE%80%E5%8D%95%E4%BE%8B%E5%AD%90)
4. [Django常用内置信号](#django%E5%B8%B8%E7%94%A8%E5%86%85%E7%BD%AE%E4%BF%A1%E5%8F%B7)
5. [如何放置信号监听函数代码](#%E5%A6%82%E4%BD%95%E6%94%BE%E7%BD%AE%E4%BF%A1%E5%8F%B7%E7%9B%91%E5%90%AC%E5%87%BD%E6%95%B0%E4%BB%A3%E7%A0%81)
6. [自定义信号](#%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BF%A1%E5%8F%B7)
   1. [第一步：自定义信号](#%E7%AC%AC%E4%B8%80%E6%AD%A5%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BF%A1%E5%8F%B7)
   2. [第二步：触发信号](#%E7%AC%AC%E4%BA%8C%E6%AD%A5%E8%A7%A6%E5%8F%91%E4%BF%A1%E5%8F%B7)
   3. [第三步：将监听函数与信号相关联](#%E7%AC%AC%E4%B8%89%E6%AD%A5%E5%B0%86%E7%9B%91%E5%90%AC%E5%87%BD%E6%95%B0%E4%B8%8E%E4%BF%A1%E5%8F%B7%E7%9B%B8%E5%85%B3%E8%81%94)
7. [小结](#%E5%B0%8F%E7%BB%93)
---
Django 框架包含了一个信号机制，它允许若干个发送者（sender）通知一组接收者（receiver）某些特定操作或事件(events)已经发生了， 接收者收到指令信号(signals)后再去执行特定的操作。本文主要讲解Django信号(signals)的工作机制、应用场景，如何在项目中使用信号以及如何自定义信号。
## 信号的工作机制
Django 中的信号工作机制依赖如下三个主要要素：
* 发送者（sender）：信号的发出方，可以是模型，也可以是视图。当某个操作发生时，发送者会发出信号。
* 信号（signal）：发送的信号本身。Django内置了许多信号，比如模型保存后发出的`post_save`信号。
* 接收者（receiver）：信号的接收者，其本质是一个简单的回调函数。将这个函数注册到信号上，当特定的事件发生时，发送者发送信号，回调函数就会被执行。
## 信号的应用场景
信号主要用于Django项目内不同事件的联动，实现程序的解耦。比如当模型A有变动时，模型B与模型C收到发出的信号后同步更新。又或当一个数据表数据有所改变时，监听这个信号的函数可以及时清除已失效的缓存。另外通知也是一个信号常用的场景，比如有人刚刚回复了你的贴子，可以通过信号进行推送。
**注意**：Django中信号监听函数不是异步执行，而是同步执行，所以需要异步执行耗时的任务时(比如发送邮件或写入文件)，不建议使用Django自带的信号。
## 两个简单例子
假如我们有一个Profile模型，与User模型是一对一的关系。我们希望创建User对象实例时自动创建Profile对象实例，而更新User对象实例时不创建新的Profile对象实例。这时我们就可以自定义 `create_user_profile`和`save_user_profile`两个监听函数，同时监听sender (User模型)发出的`post_save`信号。由于`post_save`可同时用于模型的创建和更新，我们用`if created`这个判断来加以区别。
```
from django.db import models
from django.db.models.signals import post_save
from django.dispatch import receiver
 
class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    birth_date = models.DateField(null=True, blank=True)
# 监听User模型创建    
@receiver(post_save, sender=User)
def create_user_profile(sender, instance, created, **kwargs):
   if created:
       Profile.objects.create(user=instance)
# 监听User模型更新  
@receiver(post_save, sender=User)
def save_user_profile(sender, instance, **kwargs):
    instance.profile.save()
```
我们再来看一个使用信号清除缓存的例子。当模型A被更新或被删除时，会分别发出`post_save`和`post_delete`的信号，监听这两个信号的receivers函数会自动清除缓存里的A对象列表。
```
from django.core.cache import cache
from django.db.models.signals import post_delete, post_save
from django.dispatch import receiver
@receiver(post_save, sender=ModelA)
def cache_post_save_handler(sender, **kwargs):
    cache.delete('cached_a_objects')
    
@receiver(post_delete, sender=ModelA)
def cache_post_delete_handler(sender, **kwargs):
     cache.delete('cached_a_objects')
```
注意：有时为了防止信号多次发送，可以通过`dispatch_uid`给receiver函数提供唯一标识符。
```
@receiver(post_delete, sender=ModelA, dispatch_uid = "unique_identifier")
```
## Django常用内置信号
前面例子我们仅仅使用了`post_save`和`post_delete`信号。Django还内置了其它常用信号：
* pre\_save & post\_save: 在模型调用 save()方法之前或之后发送。
* pre\_init& post\_init: 在模型调用\_init\_方法之前或之后发送。
* pre\_delete & post\_delete: 在模型调用delete()方法或查询集调用delete() 方法之前或之后发送。
* m2m\_changed: 在模型多对多关系改变后发送。
* request\_started & request\_finished: Django建立或关闭HTTP 请求时发送。
这些信号都非常有用。举个例子：使用`pre_save`信号可以在将用户的评论存入数据库前对其进行过滤，或则检测一个模型对象的字段是否发生了变更。
**注意**：监听`pre_save`和`post_save`信号的回调函数不能再调用`save()`方法，否则回出现死循环。另外Django的`update`方法不会发出`pre_save`和`post_save`的信号。
## 如何放置信号监听函数代码
在之前案例中，我们将Django信号的监听函数写在了`models.py`文件里。当一个app的与信号相关的自定义监听函数很多时，此时models.py代码将变得非常臃肿。一个更好的方式把所以自定义的信号监听函数集中放在app对应文件夹下的`signals.py`文件里，便于后期集中维护。
假如我们有个`account`的app，包含了User和Profile模型，我们首先需要在`account`文件夹下新建`signals.py`，如下所示：
```
# account/signals.py
from django.db.models.signals import post_save
from django.dispatch import receiver
from .models import User, Profile
@receiver(post_save, sender=User)
def create_user_profile(sender, instance, created, **kwargs):
  if created:
      Profile.objects.create(user=instance)
@receiver(post_save, sender=User)
def save_user_profile(sender, instance, **kwargs):
    instance.profile.save()
```
接下来我们需要修改`account`文件下`apps.py`和`__init__.py`，以导入创建的信号监听函数。
```
# apps.py
from django.apps import AppConfig
 
class AccountConfig(AppConfig):
    name = 'account'
 
    def ready(self):
        import account.signals
        
# account/__init__.py中增加如下代码：
default_app_config = 'account.apps.AccountConfig'
```
## 自定义信号
Django的内置信号在大多数情况下能满足我们的项目需求，但有时我们还需要使用自定义的信号。在Django项目中使用自定义信号也比较简单，分三步即可完成。
### 第一步：自定义信号
每个自定义的信号，都是Signal类的实例。这里我们首先在app目录下新建一个`signals.py`文件，创建一个名为`my_signal`的信号，它包含有`msg`这个参数，这个参数在信号触发的时候需要传递。当监听函数收到这个信号时，会得到`msg`参数的值。
```
from django.dispatch import Signal
my_signal = Signal(providing_args=['msg'])
```
### 第二步：触发信号
视图中进行某个操作时可以使用`send`方法触发自定义的信号，并设定`msg`的值。
```
from . import signals
# Create your views here.
def index(request):
    signals.my_signal.send(sender=None, msg='Hello world')
    return render(request, template_name='index.html')
```
### 第三步：将监听函数与信号相关联
```
from django.dispatch import Signal, Receiver
my_signal = Signal(providing_args=['msg'])
@receiver(my_signal)
def my_signal_callback(sender, **kwargs):
    print(kwargs['msg']) # 打印Hello world!
```
这样每当用户访问/index/视图时，Django都会发出`my_signal`的信号(包含msg这个参数)。回调函数收到这个信号后就会打印出msg的值来。
## 小结
在本文里我们总结了Django信号(signals)的工作机制及应用场景，介绍了如何在Django项目中使用信号实现事件的联动。最后我们还总结了Django常用内置信号以及如何自定义信号。Django信号还有非常多的应用场景等着你去发现。
原创不易，转载请注明来源。我是大江狗，一名Django技术开发爱好者。您可以通过搜索【[CSDN大江狗](https://blog.csdn.net/weixin_42134789)】、【[知乎大江狗](https://www.zhihu.com/people/shi-yun-bo-53)】和搜索微信公众号【Python Web与Django开发】关注我！
![Python Web与Django开发](../assets/assets/images/django.png)
---
[返回顶部](#top)
Copyright © 2021-2022 Yunbo Shi.
