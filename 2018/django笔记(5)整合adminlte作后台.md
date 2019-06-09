---
title: " django笔记(5)整合adminlte作后台\t\t"
tags:
  - 后台
url: 1159.html
id: 1159
categories:
  - 后端
date: 2018-04-23 22:09:05
---
介绍

adminlte是一个成熟的基于bootstrap的后台模板：网址

django是python的web框架，其帮助文档：django-docs，当前2.X版本，给的就是这个网址了。

admin：django自带一套admin后台模板，反正我感觉界面很不好看，帮助文档：admin帮助文档，当然这是以app方式存在的，这也是一个app。

django-adminlte2：这也是一个app，是将adminlte的显示风格替换掉admin默认的风格。django-adminlte2帮助文档

下面说一下django-adminlte2的具体使用方式。
django-adminlte2使用方法
安装

pip install django-adminlte2，只需一行搞定安装，安装完成后实际上是安装了两个python包，这两个包分别是两个django的app：django-adminlte2、django-adminlte-theme（详细说明请见最后）

然后再settings.py中设置

INSTALLED_APPS = [
    # #####################自定义内容
    # 自定义 adminlte 的 app 用于重写adminlte 第三方库
    'zadminlte',
    # #####################第三方插件/库
    # adminlte 第三方库 需要pip install django-adminlte
    'django_adminlte',
    # adminlte-theme 第三方风格库 pip install django-adminlte 时自动安装
    'django_adminlte_theme',
    # #####################django内容
    ……
]

    新加的内容后面别忘了“,”

    只需要输入名字即可，前面不用include

    django_adminlte，而不是django-adminlte

    最前面的是一个针对django-adminlte二次开发app，这个就是自己建立的app了，可以python manage.py startapp zadminlte建立

    通过输入“python manage.py”可以查看到 django-admin的所有指令

然后python manage.py runserver就可以看到改变后的界面效果了，一定要注意 app中第三方app如果要覆盖django的内容一定要在django的前面，也就是谁是最终的效果就在最前面
进阶
通过模板代码块自定义页面

部分人只想实现效果，所以这里只给出具体例子，而原因请见下面的“其他”

上面已经建立了zadminlte app了，在这个app里面建立templates模板目录，在目录下建立adminlte目录，让这个目录与adminlte2 app一致，如果想要自定义那个页面内容，就在下面建立对应的文件即可。要保证目录和文件名完全一致。下面是一个例子的目录结构和文件内容：

--zadminlte
----templates
------adminlte
--------lib
----------_main_header.html
--------components
--------pages
--------base.html
--------index.html
--------login.html

只是app名字不一样，下面的目录结构应该完全一样，我对_main_header.html进行自定义，这个文件是头部内容，自定义内容如下：
```
{% extends 'adminlte/lib/_main_header.html' %}

{% block logo_text_small %}
    <b>Tlcms</b>
{% endblock logo_text_small %}


{% block logo_text %}
    <b>Techie亮CMS</b>
{% endblock logo_text %}
```

这样实现了头部显示内容从原始文件Adminlte变为了Techie亮CMS，而缩小后原始显示的是Alte改为了Tlcms
利用sites框架自动填充site类型变量

adminlte2 app中有一些{{site.name}}一类的变量名，这些是直接调用的的django sites站点框架，文档。可以通过设置sites，实现这些变量内容对站点的自适应。如copyright等。

    settings.py的app中添加’django.contrib.sites’
    python manage.py migrate执行以后数据库中会新增一个django_site表，只有三个字段id、domain、name
    settings.py添加SITE_ID = 1 这个id填写具体的id，在最外层添加
    后面就可以在四处使用了，上面文档给了各种地方使用的方法

问题-使用sites框架后adminlte框架仍为完全引用

这样配置完成以后，自己写的代码调用的sites框架都没有问题，adminlte2 2.0.4版本中，登陆进去以后首页下面的一直显示“Copyright ? 2018. All rights reserved. ”而按照adminlte的代码(_main_footer.html)：

```
<footer class="main-footer">
    <div class="pull-right hidden-xs">
        {% block footer_right %}
            <b>Version</b> {% block version %}0.1{% endblock %}
        {% endblock %}
    </div>

    {% block footer_left %}
    {% block legal %}
    <strong>Copyright &copy; {% now "Y" %}{% if not site %}.{% endif %}
        {% if site %}
            <a href="http://{{ site.domain }}">{{ site.name }}</a>
        {% endif %}
    </strong> All rights
    reserved.
    {% endblock %}
    {% endblock %}
</footer>
```

按照正常来说，应该是会自动变的。而后来发现在注销后（注销后还在adminlte主页面，只不过提示重新登录，点击以后才会到登录页面）底部就显示出了正确的内容。

此问题未解决，直接重写了legal块
sites框架其他应用

可以做为一个django多个站点的搭建，实现多站点共用一个数据库，部分数据共用且各个站点也有自己独有的数据。
登陆界面修改

登录界面显示的是“django管理”，想要修改成自己的内容，这时候在adminlte2中就找不到对应的代码块了。实际上这个在theme应用中。在theme/templates/admin/lib/login.html和base_login.html
实际上是在base_login.html

相应代码：

```
<div class="login-logo">
    <a href="{% url 'admin:index' %}">{{ site_header|default:_('Django administration') }}</a>
</div>
```

这里使用了一个变量：site_header，而这个变量实际上是由admin应用传入的(后面描述了原因)，所以只需要修改admin即可，肯定不能改官方的模块，应该改自建的zadminlte应用的admin.py这个文件，只需要在这里增加上一行`admin.site.site_header = “XXXX”`就完成了更改。
其他

下面的内容是我个人的理解，可能不正确
关于adminlte2和adminlte-theme两个app

其中django-adminlte2是通过django模板将adminlte封装了，也就是html随处可见的`"{ }"`、`"{ % % }"`这样的标记符，这就是模板的直观体现吧，反正我是这么认为的。
而theme是利用django模板的继承特性将admin这个app模板中的各种代码块继承以后重写了，而重写的代码引用的就是django-adminlte2的了。

首先adminlte2这个app的目录结构：

```
--django_adminlte
----templates
------adminlte
--------lib
--------components
--------pages
--------base.html
--------index.html
--------login.html
```

而admin这个官方app的目录结构

```
--admin 
----templates 
------admin 
--------base.html 
--------index.html 
--------login.html
```

再看theme这个app的目录

```
--django-adminlte-theme
----templates 
------admin 
--------base.html 
--------index.html 
--------login.html
```

通过上面可以发现，theme与admin具有相同的目录结构，这样在文件上是重复的，而根据django模板文件加载的顺序(见后面一节)，因为在app设置中把theme放在了admin前面，所以theme的模板文件优先被找到，进而实现了对原有admin文件的替换

而theme中文件是这么写的，比如base.html