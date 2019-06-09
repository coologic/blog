---
title: " django复杂查询\t\t"
tags:
  - django
url: 1213.html
id: 1213
categories:
  - 后端
date: 2018-06-18 13:27:09
---

介绍
--

django模型的objects对象中有filter、get等方法，其中可以“字段=XXX”的方式填入参数，最终构成AND关系的SQL Where子句，若需要OR或多种混合的复杂搜索需要使用Q对象。 除此以为，django还支持利用“__”方式实现查找条件关键字、__方式的多表连接查询。 所有查询相关的官方文档地址：[查询简介](https://docs.djangoproject.com/en/2.0/topics/db/queries/)、[查询集完整API](https://docs.djangoproject.com/en/2.0/ref/models/querysets/)

查找关键字
-----

__exact 精确等于 like ‘aaa’
__iexact 精确等于 忽略大小写 ilike ‘aaa’
__contains 包含 like ‘%aaa%’
__icontains 包含 忽略大小写 ilike ‘%aaa%’，但是对于sqlite来说，contains的作用效果等同于icontains。
__gt 大于
__gte 大于等于
__lt 小于
__lte 小于等于
__in 存在于一个list范围内
__startswith 以…开头
__istartswith 以…开头 忽略大小写
__endswith 以…结尾
__iendswith 以…结尾，忽略大小写
__range 在…范围内
__year 日期字段的年份
__month 日期字段的月份
__day 日期字段的日
__isnull=True/False

范例：filter(name__contains='techie')等效于WHERE name like '%techie%'

连表查询
----

class A(models.Model):
    name = models.CharField()
class B(models.Model):
    aa = models.ForeignKey(A)
B.objects.filter(aa\_\_name\_\_contains='test')

通过B的外键找到A中name字段包含test内容的数据

class A(models.Model):
    name = models.CharField()
class B(models.Model):
    aa = models.ForeignKey(A,related_name="FAN")
    bb = models.CharField()
A.objects.filter(FAN__bb='XXXX')

反向查询，查询出所有(B.aa=A且B.bb=XXXX)的A实例

Q对象使用
-----

官方文档：[https://docs.djangoproject.com/en/2.0/topics/db/queries/#complex-lookups-with-q-objects](https://docs.djangoproject.com/en/2.0/topics/db/queries/#complex-lookups-with-q-objects) 引用django.db.models.Q Q对象与SQL语句对应关系如下：

Q(question='Who') | Q(question='What')
WHERE question = 'Who' OR question = 'What'

Model.objects.get(
    Q(question='Who'),
    Q(pub\_date=date(2005, 5, 2)) | Q(pub\_date=date(2005, 5, 6))
)
SELECT * from model WHERE question = 'Who' AND (pub\_date = '2005-05-02' OR pub\_date = '2005-05-06')

如果有比较复杂的关系，可以像使用容器一样构造Q对象： 单一类型条件查询：

q1 = Q()
q1.connector = 'OR'
q1.children.append(('id', 1))
q1.children.append(('id', 2))
q1.children.append(('id', 3))
    
models.Tb1.objects.filter(q1)

符合类型条件查询：

con = Q()

q1 = Q()
q1.connector = 'OR'
q1.children.append(('id', 1))
q1.children.append(('id', 2))
q1.children.append(('id', 3))

q2 = Q()
q2.connector = 'OR'
q2.children.append(('status', '在线'))

con.add(q1, 'AND')
con.add(q2, 'AND')

models.Tb1.objects.filter(con)

### Q对象官方使用范例

[https://github.com/django/django/blob/master/tests/or_lookups/tests.py](https://github.com/django/django/blob/master/tests/or_lookups/tests.py)

from datetime import datetime
from operator import attrgetter

from django.db.models import Q
from django.test import TestCase

from .models import Article


class OrLookupsTests(TestCase):

    def setUp(self):
        self.a1 = Article.objects.create(
            headline='Hello', pub_date=datetime(2005, 11, 27)
        ).pk
        self.a2 = Article.objects.create(
            headline='Goodbye', pub_date=datetime(2005, 11, 28)
        ).pk
        self.a3 = Article.objects.create(
            headline='Hello and goodbye', pub_date=datetime(2005, 11, 29)
        ).pk

    def test\_filter\_or(self):
        self.assertQuerysetEqual(
            (
                Article.objects.filter(headline__startswith='Hello') |
                Article.objects.filter(headline__startswith='Goodbye')
            ), \[
                'Hello',
                'Goodbye',
                'Hello and goodbye'
            \],
            attrgetter("headline")
        )

        self.assertQuerysetEqual(
            Article.objects.filter(headline\_\_contains='Hello') | Article.objects.filter(headline\_\_contains='bye'), \[
                'Hello',
                'Goodbye',
                'Hello and goodbye'
            \],
            attrgetter("headline")
        )

        self.assertQuerysetEqual(
            Article.objects.filter(headline\_\_iexact='Hello') | Article.objects.filter(headline\_\_contains='ood'), \[
                'Hello',
                'Goodbye',
                'Hello and goodbye'
            \],
            attrgetter("headline")
        )

        self.assertQuerysetEqual(
            Article.objects.filter(Q(headline\_\_startswith='Hello') | Q(headline\_\_startswith='Goodbye')), \[
                'Hello',
                'Goodbye',
                'Hello and goodbye'
            \],
            attrgetter("headline")
        )

    def test_stages(self):
        # You can shorten this syntax with code like the following,  which is
        # especially useful if building the query in stages:
        articles = Article.objects.all()
        self.assertQuerysetEqual(
            articles.filter(headline\_\_startswith='Hello') & articles.filter(headline\_\_startswith='Goodbye'),
            \[\]
        )
        self.assertQuerysetEqual(
            articles.filter(headline\_\_startswith='Hello') & articles.filter(headline\_\_contains='bye'), \[
                'Hello and goodbye'
            \],
            attrgetter("headline")
        )

    def test\_pk\_q(self):
        self.assertQuerysetEqual(
            Article.objects.filter(Q(pk=self.a1) | Q(pk=self.a2)), \[
                'Hello',
                'Goodbye'
            \],
            attrgetter("headline")
        )

        self.assertQuerysetEqual(
            Article.objects.filter(Q(pk=self.a1) | Q(pk=self.a2) | Q(pk=self.a3)), \[
                'Hello',
                'Goodbye',
                'Hello and goodbye'
            \],
            attrgetter("headline"),
        )

    def test\_pk\_in(self):
        self.assertQuerysetEqual(
            Article.objects.filter(pk__in=\[self.a1, self.a2, self.a3\]), \[
                'Hello',
                'Goodbye',
                'Hello and goodbye'
            \],
            attrgetter("headline"),
        )

        self.assertQuerysetEqual(
            Article.objects.filter(pk__in=(self.a1, self.a2, self.a3)), \[
                'Hello',
                'Goodbye',
                'Hello and goodbye'
            \],
            attrgetter("headline"),
        )

        self.assertQuerysetEqual(
            Article.objects.filter(pk__in=\[self.a1, self.a2, self.a3, 40000\]), \[
                'Hello',
                'Goodbye',
                'Hello and goodbye'
            \],
            attrgetter("headline"),
        )

    def test\_q\_repr(self):
        or_expr = Q(baz=Article(headline="Fo?"))
        self.assertEqual(repr(or_expr), "<Q: (AND: ('baz', <Article: Fo?>))>")
        negated_or = ~Q(baz=Article(headline="Fo?"))
        self.assertEqual(repr(negated_or), "<Q: (NOT (AND: ('baz', <Article: Fo?>)))>")

    def test\_q\_negated(self):
        # Q objects can be negated
        self.assertQuerysetEqual(
            Article.objects.filter(Q(pk=self.a1) | ~Q(pk=self.a2)), \[
                'Hello',
                'Hello and goodbye'
            \],
            attrgetter("headline")
        )

        self.assertQuerysetEqual(
            Article.objects.filter(~Q(pk=self.a1) & ~Q(pk=self.a2)), \[
                'Hello and goodbye'
            \],
            attrgetter("headline"),
        )
        # This allows for more complex queries than filter() and exclude()
        # alone would allow
        self.assertQuerysetEqual(
            Article.objects.filter(Q(pk=self.a1) & (~Q(pk=self.a2) | Q(pk=self.a3))), \[
                'Hello'
            \],
            attrgetter("headline"),
        )

    def test\_complex\_filter(self):
        # The 'complex_filter' method supports framework features such as
        # 'limit\_choices\_to' which normally take a single dictionary of lookup
        # arguments but need to support arbitrary queries via Q objects too.
        self.assertQuerysetEqual(
            Article.objects.complex_filter({'pk': self.a1}), \[
                'Hello'
            \],
            attrgetter("headline"),
        )

        self.assertQuerysetEqual(
            Article.objects.complex_filter(Q(pk=self.a1) | Q(pk=self.a2)), \[
                'Hello',
                'Goodbye'
            \],
            attrgetter("headline"),
        )

    def test\_empty\_in(self):
        # Passing "in" an empty list returns no results ...
        self.assertQuerysetEqual(
            Article.objects.filter(pk__in=\[\]),
            \[\]
        )
        # ... but can return results if we OR it with another query.
        self.assertQuerysetEqual(
            Article.objects.filter(Q(pk\_\_in=\[\]) | Q(headline\_\_icontains='goodbye')), \[
                'Goodbye',
                'Hello and goodbye'
            \],
            attrgetter("headline"),
        )

    def test\_q\_and(self):
        # Q arg objects are ANDed
        self.assertQuerysetEqual(
            Article.objects.filter(Q(headline\_\_startswith='Hello'), Q(headline\_\_contains='bye')), \[
                'Hello and goodbye'
            \],
            attrgetter("headline")
        )
        # Q arg AND order is irrelevant
        self.assertQuerysetEqual(
            Article.objects.filter(Q(headline\_\_contains='bye'), headline\_\_startswith='Hello'), \[
                'Hello and goodbye'
            \],
            attrgetter("headline"),
        )

        self.assertQuerysetEqual(
            Article.objects.filter(Q(headline\_\_startswith='Hello') & Q(headline\_\_startswith='Goodbye')),
            \[\]
        )

    def test\_q\_exclude(self):
        self.assertQuerysetEqual(
            Article.objects.exclude(Q(headline__startswith='Hello')), \[
                'Goodbye'
            \],
            attrgetter("headline")
        )

    def test\_other\_arg_queries(self):
        # Try some arg queries with operations other than filter.
        self.assertEqual(
            Article.objects.get(Q(headline\_\_startswith='Hello'), Q(headline\_\_contains='bye')).headline,
            'Hello and goodbye'
        )

        self.assertEqual(
            Article.objects.filter(Q(headline\_\_startswith='Hello') | Q(headline\_\_contains='bye')).count(),
            3
        )

        self.assertSequenceEqual(
            Article.objects.filter(Q(headline\_\_startswith='Hello'), Q(headline\_\_contains='bye')).values(), \[
                {"headline": "Hello and goodbye", "id": self.a3, "pub_date": datetime(2005, 11, 29)},
            \],
        )

        self.assertEqual(
            Article.objects.filter(Q(headline\_\_startswith='Hello')).in\_bulk(\[self.a1, self.a2\]),
            {self.a1: Article.objects.get(pk=self.a1)}
)

 

F对象使用
-----

官方文档：[https://docs.djangoproject.com/en/2.0/ref/models/expressions/#f-expressions](https://docs.djangoproject.com/en/2.0/ref/models/expressions/#f-expressions) 有关聚合、分组、F查询可看博客：[django-聚合、分组、F查询和Q查询、总结](https://blog.csdn.net/u013210620/article/details/79182038)