---
type: topic
title: "Django模板语言、常用标签与过滤器"
date: 2026-08-05
category: Django基础教程
source_url: https://pythondjango.cn/django/basics/9-templates-filters-tags/
author: 大江狗
tags:
  - Django基础教程
  - status/done
  - type/topic
publish: true
---

# Django模板语言、常用标签与过滤器
## 目录
1. [变量(variables)](#%E5%8F%98%E9%87%8Fvariables)
2. [标签 (tags)](#%E6%A0%87%E7%AD%BE-tags)
3. [过滤器(filters)](#%E8%BF%87%E6%BB%A4%E5%99%A8filters)
4. [模板文件的放置路径](#%E6%A8%A1%E6%9D%BF%E6%96%87%E4%BB%B6%E7%9A%84%E6%94%BE%E7%BD%AE%E8%B7%AF%E5%BE%84)
   1. [项目模板](#%E9%A1%B9%E7%9B%AE%E6%A8%A1%E6%9D%BF)
   2. [应用模板](#%E5%BA%94%E7%94%A8%E6%A8%A1%E6%9D%BF)
5. [模板的继承](#%E6%A8%A1%E6%9D%BF%E7%9A%84%E7%BB%A7%E6%89%BF)
6. [模板文件中加载静态文件](#%E6%A8%A1%E6%9D%BF%E6%96%87%E4%BB%B6%E4%B8%AD%E5%8A%A0%E8%BD%BD%E9%9D%99%E6%80%81%E6%96%87%E4%BB%B6)
7. [小结](#%E5%B0%8F%E7%BB%93)
---
Django的模板是静态的html文件，它决定了一个页面的样式或外观。Django把视图(View)传递过来的数据与模板相结合动态地渲染出一个完整的html页面。这样做的好处是实现了数据和样式的分离。本章将介绍Django的模板语言, 常用模板标签及过滤器以及如何正确地配置模板和静态文件的存放路径。
## 变量(variables)
模板中的变量一般使用双括号``包围。在模板中你可以使用`.`获取一个变量(字典、对象和列表)的属性，如：
```
    {{ my_dict.key }}  
    {{ my_object.attribute }}
    {{ my_list.0 }}
```
## 标签 (tags)
Django的标签(tags)用双%括号包裹，常用Django标签包括：
```
# 内容块
{% block content %} 代码块 {% endblock %}
# 防csrf攻击
{% csrf_token %} 表单专用
# for循环
<ul> 
    {% for athlete in athlete_list %}   
    <li>{{ forloop.counter }} - {{ athlete.name }}</li> 
    {% empty %}   
    <li>Sorry, no athletes。</li> 
    {% endfor %} 
</ul>
# if判断
{% if title == "python" %} 
   Say python. 
{% elif title == "django"}
   Say django.
{% elif title == "django"}
   Say django.
{% else %}
   Say java.
{% endif %} 
# url反向解析
{% url 'blog:article_detail' article.id %}
# with
{% with total=business.employees.count %}   
    {{ total }} employee{{ total|pluralize }} 
{% endwith %}
# 载入模板标签过滤器
{% load sometags_library %}
# 模板包含
{% include "header.html" %}
# 模板继承
{% include "base.html" %}
# 获取当前时间now
{% now "jS F Y H:i" %}
```
## 过滤器(filters)
在模板中你可以使用过滤器(filter)来改变变量在模板中的显示形式。比如`{{ article.title | lower }}`中lower过滤器可以让文章的标题转化为小写。Django的模板提供了许多内置过滤器，你可以直接使用，非常方便。
| 过滤器 | 例子 |
| --- | --- |
| lower, upper | {{ article.title | lower }} 大小写 |
| length | {{ name | length }} 长度 |
| default | {{ value | default: “0” }} 默认值 |
| date | {{ picture.date | date:”Y-m-j “ }} 日期格式 |
| dicsort | {{ value | dicsort: “name” }} 字典排序 |
| escape | {{ title | escape }} 转义 |
| filesizeformat | {{ file | filesizeformat }} 文件大小 |
| first, last | {{ list | first }} 首或尾 |
| floatformat | {{ value | floatformat }} 浮点格式 |
| get\_digit | {{ value | get\_digit }} 位数 |
| join | {{ list | join: “,” }} 字符连接 |
| make\_list | {{ value | make\_list }} 转字符串 |
| pluralize | {{ number | pluralize }} 复数 |
| random | {{ list | random }} 随机 |
| slice | {{ list | slice: “:2” }} 切 |
| slugify | {{ title | slugify }} 转为slug |
| striptags | {{ body | striptags }} 去除tags |
| time | {{ value | time: “H:i” }} 时间格式 |
| timesince | {{ pub\_date | timesince: given\_date }} |
| truncatechars | {{ title | truncatechars: 10 }} |
| truncatewords | {{ title | truncatewords: 2 }} |
| truncatechars\_html | {{ title | truncatechars\_html: 2 }} |
| urlencode | {{ path | urlencode }} URL转义 |
| wordcount | {{ body | wordcount }} 单词字数 |
## 模板文件的放置路径
模板文件有两种, 一种是属于整个项目(project)的模板,一种是属于某个应用(app)的模板。模板文件的放置路径必需正确, 否则Django找不到模板容易出现`TemplateDoesNotExist`的错误。
### 项目模板
属于项目的模板文件路径一般是项目根目录下的`templates`文件夹。除此以外, 你还需要在`settings.py`种显示地将模板目录设置为`BASE_DIR`目录下的`templates`文件夹。
```
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')], # 设置模板目录
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```
### 应用模板
属于单个应用的模板文件路径一般是`app`目录下的`app/templates/app`文件夹, 这样做的好处是可以避免模板命名冲突。
下图以博客项目为例展示了项目模板文件和应用模板文件正确的存放路径。
```
myproject/ # 项目名
    manage.py
    myproject/
        __init__.py
        urls.py
        wsgi.py
        settings.py
    blog/ # 应用名
        __init__.py
        models.py
        managers.py
        views.py
        urls.py
        templates/ # 应用模板文件
            blog/
                base.html
                list.html
                detail.html
     templates/ # 项目模板文件
         base.html
         index.html
     requirements/
         base.txt
         dev.txt
         test.txt
         prod.txt
```
对于上面这个项目布局，在使用`render`方法指定渲染模板时，无需给出完整的路径，只给出相对于`templates`的路径即可，比如：
```
# 指定项目模板
return render(request, "index.html", { "msg": "hello world!",})
# 指定应用模板
return render(request, "blog/list.html", { "articles": articles,})
```
## 模板的继承
Django支持模板的继承。你需要使用`extends`标签。在下面经典模板继承案例中，`index.html`继承了`base.html`的布局,如sidebar和footer,但是content模块会替换掉`base.html`中的content模块。
```
# base.html
{% block sidebar %}
{% endblock %}
{% block content %}
{% endblock %}
{% block footer %}
{% endblock %}
# index.html
{% extends "base.html" %}
{% block content %}
     {{ some code }}
{% endblock }
```
`extends`标签支持相对路径，这就意味着当文件夹结构如下所示时:
```
dir1/
    index.html
    base2.html
    my/
        base3.html
base1.html
```
模板`index.html`使用以下继承方式都是可以的。`.`号代表当前目录, `..`代表上层目录.
```
{% extends "./base2.html" %}
{% extends "../base1.html" %}
{% extends "./my/base3.html" %}
```
## 模板文件中加载静态文件
在模板文件我们要经常需要加载静态文件如css文件和js文件，操作方式如下所示:
第一步: 在`myproject/settings.py`设置静态文件目录`STATIC_URL`, 默认为`static`, 其作用是告诉Django静态文件会存放在各app下的static目录里。同时你还需确保`django.contrib.staticfiles`已经加入到`INSTALLED_APPS`里去了.
```
STATIC_URL = '/static/'
```
第二步: 先在你的app目录下新建一个`static`文件夹，然后再建一个app子目录，放入你需要的静态文件, 此时静态文件路径变为`app/static/app/custom.css`或则`app/static/app/main.js`
如果你需要使用的静态文件不属于某个app，而属于整个项目project，那么你还可以通过`STATICFILES_DIRS`定义静态文件文件夹列表。假设属于整个项目的静态文件放在根目录下的`static`文件夹和`/var/www/static/`里，那么`myproject/settings.py`可以加入下面两行代码:
```
STATICFILES_DIRS = [
    BASE_DIR / "static",
    '/var/www/static/',
]
```
第三步：在你的html模板里使用`static`标签，首先得载入这个标签, 如下所示:
```
{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
<title>{% block title %} Django Web Applications {% endblock %} </title>
    <link rel="stylesheet" href="{% static 'app/custom.css' %}">
    <script type='text/javascript' src="{% static 'app/main.js' %}"></script>  
</head>
```
注意：`load static`需要放在html的头部位置。如果`extends`标签和`load`同时存在，`extends`需要放在最上面，然后再放`load`等标签。
## 小结
本章总结了Django的模板语言以及模板标签和过滤器, 并对模板文件和静态文件的存放路径做了详细介绍。在Django进阶教程部分我们还将介绍如何自定义模板标签和过滤器。下章我们将介绍Django的表单(forms), 如何自定义表单类, 如何渲染表单, 如果自定义表单验证以及如何处理验证过的数据。
原创不易，转载请注明来源。我是大江狗，一名Django技术开发爱好者。您可以通过搜索【[CSDN大江狗](https://blog.csdn.net/weixin_42134789)】、【[知乎大江狗](https://www.zhihu.com/people/shi-yun-bo-53)】和搜索微信公众号【Python Web与Django开发】关注我！
![Python Web与Django开发](../assets/assets/images/django.png)
---
[返回顶部](#top)
Copyright © 2021-2022 Yunbo Shi.
