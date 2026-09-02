---
type: topic
title: "Django 路由配置URLConf"
date: 2026-08-05
category: Django基础教程
source_url: https://pythondjango.cn/django/basics/7-URLconf/
author: 大江狗
tags:
  - Django基础教程
  - status/done
  - type/topic
publish: true
---

# Django 路由配置URLConf
## 目录
1. [URLconf是如何工作的?](#urlconf%E6%98%AF%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C%E7%9A%84)
2. [path和re\_path方法](#path%E5%92%8Cre_path%E6%96%B9%E6%B3%95)
3. [更多URL配置示例](#%E6%9B%B4%E5%A4%9Aurl%E9%85%8D%E7%BD%AE%E7%A4%BA%E4%BE%8B)
4. [URL的命名及reverse()方法](#url%E7%9A%84%E5%91%BD%E5%90%8D%E5%8F%8Areverse%E6%96%B9%E6%B3%95)
   1. [使用命名URL](#%E4%BD%BF%E7%94%A8%E5%91%BD%E5%90%8Durl)
   2. [硬编码URL - 不建议](#%E7%A1%AC%E7%BC%96%E7%A0%81url---%E4%B8%8D%E5%BB%BA%E8%AE%AE)
5. [URL指向基于类的视图(View)](#url%E6%8C%87%E5%90%91%E5%9F%BA%E4%BA%8E%E7%B1%BB%E7%9A%84%E8%A7%86%E5%9B%BEview)
6. [通过URL传递额外的参数](#%E9%80%9A%E8%BF%87url%E4%BC%A0%E9%80%92%E9%A2%9D%E5%A4%96%E7%9A%84%E5%8F%82%E6%95%B0)
7. [小结](#%E5%B0%8F%E7%BB%93)
---
Django的项目文件夹和每个应用(app)目录下都有`urls.py`文件，它们构成了Django的路由配置系统(URLconf)。服务器收到用户请求后，会根据用户请求的url地址和urls.py里配置的url-视图映射关系，去调用执行相应的视图函数或视图类，最后由视图返回给客户端数据。
一个优美的URL不仅层次分明、逻辑清晰，而且便于搜索引擎收录。一个糟糕的URL不仅可读性差，而且易造成程序冲突。本章小编我将给大家详细介绍下如何在Django项目开发中进行路由配置。
## URLconf是如何工作的?
假如我们有一个`blog`的博客应用，你需要编写两个视图函数，一个用于展示文章列表，一个用于展示文章详情，你的`urls.py`和`views.py`正常情况下应如下所示：
```
# blog/urls.py
from django.urls import path
from . import views
 
urlpatterns = [
    path('blog/', views.index),
    path('blog/articles/<int:id>/', views.article_detail),
]
 
# blog/views.py
def index(request):
    # 展示所有文章
   
def article_detail(request, id):
    # 展示某篇具体文章
```
那么上面这段代码是如何工作的呢？
* 当用户在浏览器输入`/blog/`时，URL收到请求后会调用视图`views.py`里的`index`方法，展示所有文章
* 当用户在浏览器输入`/blog/article/<int:id>/`时，URL不仅调用了`views.py`里的`article_detail`方法，而且还把参数文章id通过`<>`括号的形式传递给了视图。int这里代表只传递整数，传递的参数名字是id。
在上述代码中，我们通过`urlpatterns`列表的url-视图映射关系列表起了决定性作用，起到了任务调度的作用。
注意：注意当你配置URL时，别忘了把你的应用(blog)的urls加入到项目的URL配置里(mysite/urls.py), 如下图所示:
```
from django.urls import include, path
urlpatterns = [
    path('', include('blog.urls')),
    ...
]
```
## path和re\_path方法
写个URL很简单，但如何通过URL把参数传递给给视图view是个技术活。Django提供了两种设计URL的方法: `path`和`re_path`，它们均支持向视图函数或类传递参数。`path`是正常参数传递，`re_path`是采用正则表达式regex匹配。`path`和`re_path`传递参数方式如下:
* `path`方法：采用双尖括号`<变量类型:变量名>`或`<变量名>`传递，例如`<int:id>`, `<slug:slug>`或`<username>`。
* `re_path`方法: 采用命名组`(?P<变量名>表达式)`的方式传递参数。
下例中，我们分别以`path`和`re_path` 定以了两个urls，它们是等效的，把文章的id(整数类型)传递给了视图。`re_path`里引号前面的小写r表示引号里为正则表达式, `^`代表开头，`$`代表以结尾，`\d+`代表正整数。
```
# blog/urls.py
from django.urls import path, re_path
from . import views
 
urlpatterns = [
    path('blog/articles/<int:id>/', views.article_detail, name = 'article_detail'),
    re_path(r'^blog/articles/(?P<id>\d+)/$', views.article_detail, name='article_detail'),
]
 
# blog/views.py
def article_detail(request, id):
    # 展示某篇文章
```
在使用`path`和`re_path`方法设计urls需注意：
* url中的参数名要用尖括号，而不是圆括号；
* 匹配模式的最开头不需要添加斜杠`/`，但建议以斜杠结尾;
* 使用`re_path`时不一定总是以`$`结尾，有时不能加。比如下例中把`blog.urls`通过`re_path`加入到项目urls中时就不能以`$`结尾，因为这里的`blog/`并不是完整的url，只是一个开头而已。
```
from django.urls import include, re_path
urlpatterns = [
    re_path(r'^blog/', include('blog.urls')),
    ...
]
```
## 更多URL配置示例
`path`支持匹配的数据类型只有`str`,`int`, `slug`, `uuid`四种。一般来说`re_path`更强大，但写起来更复杂一些，我们来看看更多案例。
```
# 示例一，PATH
from django.urls import path
from . import views
urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    path('articles/<int:year>/', views.year_archive),
    path('articles/<int:year>/<int:month>/', views.month_archive),
    path('articles/<int:year>/<int:month>/<slug:slug>/', views.article_detail),
]
# 示例二：RE_PATH，与上例等同
from django.urls import path, re_path
from . import views
urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    re_path(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),
    re_path(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$', views.month_archive),
    re_path(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/(?P<slug>[\w-]+)/$', views.article_detail),
]
```
同样以博客为例，如果你希望设计不同的urls分别对应负责增删改查操作的视图函数或类，你可以按如下设计：
```
# blog/urls.py
from django.urls import path, re_path
from . import views
# app_name = 'blog' # 命名空间，后面会用到。
urlpatterns = [
    path('blog/articles/', views.article_list, name = 'article_list'),
    path('blog/articles/create/', views.article_create, name = 'article_create'),
    path('blog/articles/<int:id>/', views.article_detail, name = 'article_detail'),
    path('blog/articles/<int:id>/update/', views.article_update, name = 'article_update'),
    path('blog/articles/<int:id>/delete/', views.article_update, name = 'article_delete'),
]
```
## URL的命名及reverse()方法
你注意到没？在上述博客示例中，我们中还给每个URL取了一个名字，比如 `article_list`和`article_create`。这个名字大有用处，相当于给每个URL取了个全局变量的名字。它可以让你能够在Django的任意处，尤其是模板内显式地引用它。假设你需要在模板中通过链接指向一篇具体文章，下面那种方式更好？
### 使用命名URL
```
{% for article in articles %}
    <a href="{% url 'article_detail' article.id %}">{{ article.title }}</a>
{% endfor %}
```
`url`是个模板标签，其作用是对命名的url进行方向解析，动态生成链接。
注意：命名的url里有几个参数，使用`url`模板标签反向生成动态链接时，就需要向它传递几个参数。比如我们的命名url`article_detail`里有整数型`id`这个参数，我们在模板中还需要传递`article.id`。
### 硬编码URL - 不建议
```
{% for article in articles %}
    <a href="blog/articles/{{ article.id }}">{{ article.title }}</a>
{% endfor %}
```
如果你还没意识到方法1的好处，那么想想吧，假设老板让你把全部模板链接由blog/articles/id改为blog/article/id, 那种方法更快？更改所有html文件里的链接，还是只改URL配置里的一个字母?
那么问题来了。假设不同的app（比如news和blog)里都有`article_detail`这个命名URL, 我们怎么避免解析冲突呢？ 这时我们只需要在`blog/urls.py`加上`app_name='blog'`这个命名空间即可，然后在模板中以`blog:article_detail`使用即可。
```
{% for article in articles %}
    <a href="{% url 'blog:article_detail' article.id %}">{{ article.title }}</a>
{% endfor %}
```
可惜的是命名的URL一般只在模板里使用，不能直接在视图里使用。如果我们有了命名的URL，我们如何把它转化成常规的URL在视图里使用呢？
Django提供的`reverse()`方法很容易实现这点。它在视图中可以对命名urls进行反向解析，生成动态链接。
```
from django.urls import reverse
# output blog/articles/id
reverse('blog:article_detail', args=[id])
```
## URL指向基于类的视图(View)
目前`path`和`re_path`都只能指向视图view里的一个函数或方法，而不能直接指向一个基于类的视图(Class based view)。Django提供了一个额外`as_view()`方法，可以将一个类伪装成方法。这点在当你使用Django自带的类视图或自定义的类视图时非常重要。
具体使用方式如下:
```
# blog/urls.py
from django.urls import path, re_path
from . import views
 
urlpatterns = [
    # path('blog/articles/', views.article_list, name = 'article_list'),
    path('blog/articles/', views.ArticleList.as_view(), name='article_list'),
]
 
# View (in blog/views.py)
from django.views.generic import ListView
from .views import Article
 
class ArticleList(ListView):
    queryset = Article.objects.filter(date__lte=timezone.now()).order_by('date')[:5]
    context_object_name = 'article_list‘
    template_name = 'blog/article_list.html'
```
如果你对基于类的视图还比较困惑，没有关系，我们后面会做详细介绍。
## 通过URL传递额外的参数
在你配置URL时，你还可以通过字典的形式传递额外的参数给视图, 而不用把这个参数写在链接里。如下面案例所示:
```
# blog/urls.py
from django.urls import path, re_path
from . import views
 
urlpatterns = [
    path('', views.ArticleList.as_view(), name='article_list', {'blog_id': 3}),
]
```
## 小结
本章我们讲解了如何使用`path`和`re_path`方法进行url配置，并详细介绍了什么命名的urls以及如何使用`url`模板标签和 `reverse`方法对命名urls进行反向解析。下篇文章中我们将正式介绍视图的编写，欢迎阅读。
原创不易，转载请注明来源。我是大江狗，一名Django技术开发爱好者。您可以通过搜索【[CSDN大江狗](https://blog.csdn.net/weixin_42134789)】、【[知乎大江狗](https://www.zhihu.com/people/shi-yun-bo-53)】和搜索微信公众号【Python Web与Django开发】关注我！
![Python Web与Django开发](../assets/assets/images/django.png)
---
[返回顶部](#top)
Copyright © 2021-2022 Yunbo Shi.
