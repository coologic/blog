---
title: " django笔记(7)部分数据库操作\t\t"
tags:
  - django
url: 1188.html
id: 1188
categories:
  - python
  - 后端
date: 2018-05-18 16:58:12
---

介绍
--

包括django-admin.py的一些有关数据库的操作名利，以及models.py文件中对于数据库格式的说明。仅为个人笔记，有不全之处请指正。

django-admin.py关于数据库命令
----------------------

详细信息请见官方说明：[文档](https://docs.djangoproject.com/en/dev/ref/django-admin/) 首先直接输入 python django-admin.py或者在建立好的项目里输入 python manage.py可以看到所有可用指令。其中如下指令与数据有关。 inspectdb：从已有的数据库建立models.py文件，比如有个app叫sourcedb，可以使用python manage.py inspectdb > sourcedb/models.py，此时django会根据数据库已有的所有表的结构生成models.py文件 makemigrations：检查models.py文件，记录下所有改动，包括新建或改变，将变动的信息记录到migrations文件夹，注意此操作不会改变数据库，只是记录 migrate：根据migrations文件夹的内容更新数据库信息

字段类型
----

### 所有字段类型

1、models.AutoField　　自增列 = int(11)
　如果没有的话，默认会生成一个名称为 id 的列，如果要显示的自定义一个自增列，必须将给列设置为主键 primary_key=True。
2、models.CharField　　字符串字段
　必须 max_length 参数
3、models.BooleanField　　布尔类型=tinyint(1)
　不能为空，Blank=True
4、models.ComaSeparatedIntegerField　　用逗号分割的数字=varchar
　继承CharField，所以必须 max_lenght 参数
5、models.DateField　　日期类型 date
　对于参数，auto\_now = True 则每次更新都会更新这个时间；auto\_now_add 则只是第一次创建添加，之后的更新不再改变。
6、models.DateTimeField　　日期类型 datetime
　同DateField的参数
7、models.Decimal　　十进制小数类型 = decimal
　必须指定整数位max\_digits和小数位decimal\_places
8、models.EmailField　　字符串类型（正则表达式邮箱） =varchar
　对字符串进行正则表达式
9、models.FloatField　　浮点类型 = double
10、models.IntegerField　　整形
11、models.BigIntegerField　　长整形
　integer\_field\_ranges = {
　　'SmallIntegerField': (-32768, 32767),
　　'IntegerField': (-2147483648, 2147483647),
　　'BigIntegerField': (-9223372036854775808, 9223372036854775807),
　　'PositiveSmallIntegerField': (0, 32767),
　　'PositiveIntegerField': (0, 2147483647),
　}
12、models.IPAddressField　　字符串类型（ip4正则表达式）(已弃用，用GenericIPAddressField)
13、models.GenericIPAddressField　　字符串类型（ip4和ip6是可选的）
　参数protocol可以是：both、ipv4、ipv6，验证时，会根据设置报错
14、models.NullBooleanField　　允许为空的布尔类型
15、models.PositiveIntegerFiel　　正Integer
16、models.PositiveSmallIntegerField　　正smallInteger
17、models.SlugField　　小标题
　只包含以下符号：减号、下划线、字母、数字
18、models.SmallIntegerField　　数字
　数据库中的字段有：tinyint、smallint、int、bigint
19、models.TextField　　字符串=longtext
20、models.TimeField　　时间 HH:MM\[:ss\[.uuuuuu\]\]
21、models.URLField　　字符串，地址正则表达式
22、models.BinaryField　　二进制
23、models.ImageField 图片
　存的是文件存储路径
24、models.FilePathField 文件
　存的是文件存储路径

### 字段参数

1、null=True
  数据库中字段是否可以为空
2、blank=True
  django的Admin中添加数据时是否可允许空值
3、primary_key =False
  主键，对AutoField设置主键后，就会代替原来的自增 id 列
4、auto\_now 和 auto\_now_add
  auto_now 自动创建---无论添加或修改，都是当前操作的时间
  auto\_now\_add 自动创建---永远是创建时的时间
5、choices
  GENDER_CHOICE =(
    (u'M', u'Male'),
    (u'F', u'Female'),
  )
  gender = models.CharField(max\_length=2,choices = GENDER\_CHOICE)
6、max_length
7、default　　默认值
8、verbose_name　　Admin中字段的显示名称
9、name|db_column　　数据库中的字段名称
10、unique=True　　不允许重复
11、db_index =True　　数据库索引
12、editable=True　　在Admin里是否可编辑
13、error_messages=None　　错误提示
14、auto_created=False　　自动创建
15、help_text　　在Admin中提示帮助信息
16、validators=\[\]
17、upload-to

关系
--

[https://docs.djangoproject.com/en/2.0/topics/db/models/#relationships](https://docs.djangoproject.com/en/2.0/topics/db/models/#relationships)

### 多对一关系

注意相比于2.0以前版本，外键关系使用方法有所改变：

class Manufacturer(models.Model):
    # ...
    pass

class Car(models.Model):
    manufacturer = models.ForeignKey(Manufacturer, on_delete=models.CASCADE)
    # ...

### 多对多

django会自动创建第三个表用户记录两个表之间的关系

class UserGroup(models.Model):
    group\_name = models.CharField(max\_length=16)
 
class User(models.Model):
    name = models.CharField(max_length=16)
    sex = models.CharField(max_length=16)
    email = models.EmailField(max_length=32)
    usergroup_user = models.ManyToManyField('UserGroup')

### 一对一

就是和多对一一样，只不过不允许重复，可以用于表内容的扩充

增删改查
----

### 查

from appname import models
\# 假设此文件中有User这个class（也就是User表）
models.User.objects.all()  # 取出所有
models.User.objects.all().values('user')  # 只取user列
models.User.objects.all().values_list('id','user')  # 取出id和user列，并生成一个列表
models.User.objects.get(id=1)  #取出id=1的项
models.User.objects.get(user='test')  取出user=test的项

上面用的是get方法，还有filter方法，具体区别见后

### 增

\# 1-
models.UserInfo.objects.create(user='test',pwd='123456')
\# 2-
obj = models.UserInfo(user='test',pwd='123456')
obj.save()
\# 3
dic = {'user':'test','pwd':'123456'}
models.UserInfo.objects.create(**dic)

### 删

models.UserInfo.objects.filter(user='test').delete()

### 改

\# 1
models.UserInfo.objects.filter(user='test').update(pwd='520')
\# 2
obj = models.UserInfo.objects.get(user='test')
obj.pwd = '520'
obj.save()

django弱化了update和save的区别，当主键存在时save实际上就是在进行update

### get和filter区别

两者返回的内容都是字典，可通过". _ _ dict _ _"方法来查看字典的内容。如models.User.objects.get(id=1).\_\_dict\_\_ django的get方法是从数据库的取得一个匹配的结果，返回一个对象，如果记录不存在的话，它会报错。如果你用django的get去取得关联表的数据的话，而关键表的数据如果多于2条的话也会报错。 也就是只有在存在且唯一的情况下使用get不会报错 django的filter方法是从数据库的取得匹配的结果，返回一个对象列表，如果记录不存在的话，它会返回\[\]，如果有多个数据也不会报错可以通过first获取第一项或者直接\[0\]获取第一项

其他
--

### 后台注册

若需要在默认的admin中显示自定义的model，需要在对应app中的admin.py文件中添加注册

from appname import models as appnamemodels
admin.site.register(appnamemodels.classname)