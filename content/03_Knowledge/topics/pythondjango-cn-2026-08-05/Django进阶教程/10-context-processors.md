---
type: topic
title: "Django全局上下文处理器"
date: 2026-08-05
category: Django进阶教程
source_url: https://pythondjango.cn/django/advanced/10-context-processors/
author: 大江狗
tags:
  - Django进阶教程
  - status/done
  - type/topic
publish: true
---

# Django全局上下文处理器
## 目录
1. [全局上下文处理器(Context Processors)应用场景](#%E5%85%A8%E5%B1%80%E4%B8%8A%E4%B8%8B%E6%96%87%E5%A4%84%E7%90%86%E5%99%A8context-processors%E5%BA%94%E7%94%A8%E5%9C%BA%E6%99%AF)
2. [Django内置的全局上下文处理器](#django%E5%86%85%E7%BD%AE%E7%9A%84%E5%85%A8%E5%B1%80%E4%B8%8A%E4%B8%8B%E6%96%87%E5%A4%84%E7%90%86%E5%99%A8)
3. [如何自定义全局上下文处理器](#%E5%A6%82%E4%BD%95%E8%87%AA%E5%AE%9A%E4%B9%89%E5%85%A8%E5%B1%80%E4%B8%8A%E4%B8%8B%E6%96%87%E5%A4%84%E7%90%86%E5%99%A8)
4. [全局变量与本地变量的优先级](#%E5%85%A8%E5%B1%80%E5%8F%98%E9%87%8F%E4%B8%8E%E6%9C%AC%E5%9C%B0%E5%8F%98%E9%87%8F%E7%9A%84%E4%BC%98%E5%85%88%E7%BA%A7)
5. [小结](#%E5%B0%8F%E7%BB%93)
---
Django的全局上下文处理器(Context Processors)的作用就是向模板传递需要全局使用的变量。今天小编就来带大家一起来看看这把利器，并教你如何自定义全局上下文处理器(Context Processors)。
## 全局上下文处理器(Context Processors)应用场景
当你需要向所有模板传递一个可以被全局使用的变量时。在编写Django视图函数时，我们一般会在视图函数中以Python字典(dict)形式向模板中传递需要被调用或使用的变量并指定渲染模板。通常情况下，我们向模板的传递的字典变量与模板是一一对应的关系。有时我们还需要向模板传递全局变量，即每个模板都需要使用到的变量(比如站点名称, 博客的最新文章列表)。
如果每个视图函数分别去查询数据库，然后向每个模板传递这些变量，不仅造成代码冗余，而且会造成对数据库的重复查询。一个更好的解决方案就是使用自定义的上下文处理器(Context Processors)给模板传递全局变量，一次查询全局使用，完美解决了这些问题。
## Django内置的全局上下文处理器
你或许没有自定义过自己的全局上下文处理器(Context Processors)，但你一定使用过Django内置的全局上下文处理器(Context Processors)。举个例子，虽然你没有向某个模板中传递过权限perms对象，你却可以在所有模板中随时调用它（如下所示)。同样可以在模板中全局使用的变量还有request和user对象。
为什么？因为Django的`settings.py`里已经包含了`django.template.context_processors.request`和`django.contrib.auth.context_processors.auth`这两个全局上下文处理器。如果你把他们移除， 看看还能不能在模板中调用 `user`和`perms`?
```
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            os.path.join(BASE_DIR, 'templates')
        ],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [ # 以下包含了4个默认的全局上下文处理器
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
                'myapp.custom_context_processors.xxx',  # 自定义全局上下文处理器
            ],
        },
    },
]
```
Django一般包含了上述4个默认全局上下文处理器，它们作用如下所示：
* django.template.context\_processors.debug：在模板里面可以直接使用settings的DEBUG参数以及强大的sql\_queries:它本身是一个字典，其中包括当前页面执行SQL查询所需的时间
* django.template.context\_processors.request：在模板中可以直接使用request对象
* django.contrib.auth.context\_processors.auth：在模板里面可以直接使用user，perms对象。
* django.contrib.messages.context\_processors.messages：在模板里面可以直接使用message对象。
另外Django还提供了几个全局上下文处理器：
* django.template.context\_processors.i18n：在模板里面可以直接使用settings的LANGUAGES和LANGUAGE\_CODE
* django.template.context\_processors.media：可以在模板里面使用settings的MEDIA\_URL参数
* django.template.context\_processors.csrf : 给模板标签 csrf\_token提供值
* django.template.context\_processors.tz: 可以在模板里面使用 TIME\_ZONE参数。
## 如何自定义全局上下文处理器
自定义的全局上下文处理器本质上是个函数，使用它必须满足3个条件：
1. 传入参数必须有`request`对象
2. 返回值必须是个字典
3. 使用前需要在settings的`context_processors`里申明。
我们通常会把自定义的上下文处理器函数放在单独命名的`context_processors.py`里，这个python文件可以放在project目录下，也可以放在某个app的目录下。
接下来我们来看一个具体例子。我们需要向所有模板传递一个叫`site_name`的全局变量以便在所有模板中直接使用 `site_name`输出站点名称，我们可以在blog(应用名)的目录下新建`context_processors.py`，新增如下代码：
```
# blog/context_processors.py
from django.conf import settings
def global_site_name(request):
    return {'site_name': settings.SITE_NAME,}
```
然后可以在settings.py里声明：
```
'context_processors': [ # 以下包含了4个默认的全局上下文处理器
    'django.template.context_processors.debug',
    'django.template.context_processors.request',
    'django.contrib.auth.context_processors.auth',
    'django.contrib.messages.context_processors.messages',
    'blog.context_processors.global_site_name',  # 自定义全局上下文处理器
]
```
## 全局变量与本地变量的优先级
全局上下文处理器提供的变量优先级高于单个视图函数给单个模板传递的变量。这意味着全局上下文处理器提供的变量可能会覆盖你视图函数中自定义的本地变量，因此请注意避免本地变量名与全局上下文处理器提供的变量名称重复。这些变量名包括perms, user和debug等等。
如果你希望单个视图函数定义的变量名覆盖全局变量，请使用以下强制模式：
```
from django.template import RequestContext
high_priority_context = RequestContext(request)
high_priority_context.push({"my_name": "Adrian"})
```
## 小结
本文总结了什么是Django的全局上下文处理器(Context Processors)，它的应用场景及如何自定义使用自己的全局上下文处理器，希望大家喜欢。
原创不易，转载请注明来源。我是大江狗，一名Django技术开发爱好者。您可以通过搜索【[CSDN大江狗](https://blog.csdn.net/weixin_42134789)】、【[知乎大江狗](https://www.zhihu.com/people/shi-yun-bo-53)】和搜索微信公众号【Python Web与Django开发】关注我！
![Python Web与Django开发](../assets/assets/images/django.png)
---
[返回顶部](#top)
Copyright © 2021-2022 Yunbo Shi.
