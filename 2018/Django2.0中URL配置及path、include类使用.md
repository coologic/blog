---
title: " Django2.0中URL配置及path/include类使用\t\t"
tags:
  - django
url: 1218.html
id: 1218
categories:
  - python
date: 2018-06-22 13:30:50
---

实例
--

先看一个例子：

from django.urls import path
from . import views

urlpatterns = \[
path('articles/2003/', views.special\_case\_2003),
path('articles//', views.year_archive),
path('articles///', views.month_archive),
path('articles////', views.article_detail),
\]

注意： 要捕获一段url中的值，需要使用尖括号，而不是之前的圆括号； 可以转换捕获到的值为指定类型，比如例子中的int。默认情况下，捕获到的结果保存为字符串类型，不包含/这个特殊字符； 匹配模式的最开头不需要添加/，因为默认情况下，每个url都带一个最前面的/，既然大家都有的部分，就不用浪费时间特别写一个了。 匹配例子：

/articles/2005/03/ 将匹配第三条，并调用views.month_archive(request, year=2005, month=3)；
/articles/2003/匹配第一条，并调用views.special\_case\_2003(request)；
/articles/2003将一条都匹配不上，因为它最后少了一个斜杠，而列表中的所有模式中都以斜杠结尾；
/articles/2003/03/building-a-django-site/ 将匹配最后一个，并调用views.article_detail(request, year=2003, month=3, slug="building-a-django-site"

path转换器
-------

默认情况下，Django内置下面的路径转换器： str：匹配任何非空字符串，但不含斜杠/，如果你没有专门指定转换器，那么这个是默认使用的； int：匹配0和正整数，返回一个int类型 slug：可理解为注释、后缀、附属等概念，是url拖在最后的一部分解释性字符。该转换器匹配任何ASCII字符以及连接符和下划线，比如’ building-your-1st-django-site‘； uuid：匹配一个uuid格式的对象。为了防止冲突，规定必须使用破折号，所有字母必须小写，例如’075194d3-6885-417e-a8a8-6c931e272f00‘ 。返回一个UUID对象； path：匹配任何非空字符串，重点是可以包含路径分隔符’/‘。这个转换器可以帮助你匹配整个url而不是一段一段的url字符串。

自定义path转换器
----------

其实就是写一个类，并包含下面的成员和属性： 类属性regex：一个字符串形式的正则表达式属性； to\_python(self, value) 方法：一个用来将匹配到的字符串转换为你想要的那个数据类型，并传递给视图函数。如果转换失败，它必须弹出ValueError异常； to\_url(self, value)方法：将Python数据类型转换为一段url的方法，上面方法的反向操作。 例如，新建一个converters.py文件，与urlconf同目录，写个下面的类：

class FourDigitYearConverter:
regex = '\[0-9\]{4}'

def to_python(self, value):
return int(value)

def to_url(self, value):
return '%04d' % value

写完类后，在URLconf 中注册，并使用它，如下所示，注册了一个yyyy：

from django.urls import register_converter, path

from . import converters, views

register_converter(converters.FourDigitYearConverter, 'yyyy')

urlpatterns = \[
path('articles/2003/', views.special\_case\_2003),
path('articles//', views.year_archive),
...
\]

使用正则表达式
-------

Django2.0的url虽然改‘配置’了，但它依然向老版本兼容。而这个兼容的办法，就是用re\_path()方法代替path()方法。re\_path()方法在骨子里，根本就是以前的url()方法，只不过导入的位置变了。下面是一个例子，对比一下Django1.11时代的语法，有什么太大的差别？

from django.urls import path, re_path

from . import views

urlpatterns = \[
path('articles/2003/', views.special\_case\_2003),
re\_path('articles/(?P\[0-9\]{4})/', views.year\_archive),
re\_path('articles/(?P\[0-9\]{4})/(?P\[0-9\]{2})/', views.month\_archive),
re\_path('articles/(?P\[0-9\]{4})/(?P\[0-9\]{2})/(?P\[\\w-\_\]+)/', views.article_detail),
\]

与path()方法不同的在于两点： year中匹配不到10000等非四位数字，这是正则表达式决定的 传递给视图的所有参数都是字符串类型。而不像path()方法中可以指定转换成某种类型。在视图中接收参数时一定要小心。

Import变动
--------

`django.urls.path`?可以看成是?`django.conf.urls.url`?的增强形式。 为了方便，其引用路径也有所变化。

1.X

2.0

备注

-

django.urls.path

新增，url的增强版

django.conf.urls.include

django.urls.include

路径变更

django.conf.urls.url

django.urls.re_path

异名同功能，url不会立即废弃

包含外部urls文件
----------

At any point, your `urlpatterns` can “include” other URLconf modules. This essentially “roots” a set of URLs below other ones. For example, here’s an excerpt of the URLconf for the [Django website](https://www.djangoproject.com/) itself. It includes a number of other URLconfs:

from django.urls import include, path

urlpatterns = \[
    \# ... snip ...
    path('community/', include('aggregator.urls')),
    path('contact/', include('contact.urls')),
    \# ... snip ...
\]

Whenever Django encounters [`include()`](https://docs.djangoproject.com/en/2.0/ref/urls/#django.urls.include "django.urls.include"), it chops off whatever part of the URL matched up to that point and sends the remaining string to the included URLconf for further processing. Another possibility is to include additional URL patterns by using a list of [`path()`](https://docs.djangoproject.com/en/2.0/ref/urls/#django.urls.path "django.urls.path") instances. For example, consider this URLconf:

from django.urls import include, path

from apps.main import views as main_views
from credit import views as credit_views

extra_patterns = \[
    path('reports/', credit_views.report),
    path('reports/<int:id>/', credit_views.report),
    path('charge/', credit_views.charge),
\]

urlpatterns = \[
    path('', main_views.homepage),
    path('help/', include('apps.help.urls')),
    path('credit/', include(extra_patterns)),
\]

In this example, the `/credit/reports/` URL will be handled by the `credit_views.report()` Django view. This can be used to remove redundancy from URLconfs where a single pattern prefix is used repeatedly. For example, consider this URLconf:

from django.urls import path
from . import views

urlpatterns = \[
    path('<page\_slug>-<page\_id>/history/', views.history),
    path('<page\_slug>-<page\_id>/edit/', views.edit),
    path('<page\_slug>-<page\_id>/discuss/', views.discuss),
    path('<page\_slug>-<page\_id>/permissions/', views.permissions),
\]

We can improve this by stating the common path prefix only once and grouping the suffixes that differ:

from django.urls import include, path
from . import views

urlpatterns = \[
    path('<page\_slug>-<page\_id>/', include(\[
        path('history/', views.history),
        path('edit/', views.edit),
        path('discuss/', views.discuss),
        path('permissions/', views.permissions),
    \])),
\]

 

### 参数捕获

An included URLconf receives any captured parameters from parent URLconfs, so the following example is valid:

\# In settings/urls/main.py
from django.urls import include, path

urlpatterns = \[
    path('<username>/blog/', include('foo.urls.blog')),
\]

\# In foo/urls/blog.py
from django.urls import path
from . import views

urlpatterns = \[
    path('', views.blog.index),
    path('archive/', views.blog.archive),
\]

在上面的示例中，捕获的“username”变量被传递到所包含的URLCONF，如预期的那样。

注意
--

运行以后如果无法访问，请按照如下顺序检查：

1.  开启admin，访问admin，如果可以访问，则自建的url有误
2.  检查进程是否正在运行django服务
3.  使用traceroute（linux）、tracert（win）查看路由，是否正确的到达了本机的地址，若错误考虑vpn等未关闭情况

参考
--

https://docs.djangoproject.com/en/2.0/topics/http/urls/ https://www.cnblogs.com/feixuelove1009/p/8399338.html