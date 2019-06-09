---
title: " django-rest-framework教程中文版\t\t"
tags:
  - django
  - rest
url: 1175.html
id: 1175
categories:
  - 后端
date: 2018-04-24 16:19:46
---

以下内容来源：**[Django-REST-Framework-Tutorial_zh-CN](https://github.com/leilux/Django-REST-Framework-Tutorial_zh-CN)**

Tutorial 1: 序列化 Serialization
=============================

[src](http://django-rest-framework.org/tutorial/1-serialization.html)

1\. 设置一个新的环境
------------

在我们开始之前， 我们首先使用[virtualenv](http://www.virtualenv.org/en/latest/index.html)要创建一个新的虚拟环境，以使我们的配置和我们的其他项目配置彻底分开。

    $mkdir ~/env
    $virtualenv  ~/env/tutorial
    $source ~/env/tutorial/bin/avtivate

现在我们处在一个虚拟的环境中，开始安装我们的依赖包

    $pip install django
    $pip install djangorestframework
    $pip install pygments   ////使用这个包，做代码高亮显示

需要退出虚拟环境时，运行`deactivate`。更多信息，[virtualenv document](http://www.virtualenv.org/en/latest/index.html)

2\. 开始
------

环境准备好只好，我们开始创建我们的项目

    $ cd ~
    $ django-admin.py startproject tutorial
    $ cd tutorial

项目创建好后，我们再创建一个简单的app

    $python manage.py startapp snippets

我们使用`sqlite3`来运行我们的项目tutorial，编辑`tutorial/settings.py`, 将数据库的默认引擎`engine`改为`sqlite3`, 数据库的名字`NAME`改为`tmp.db`

    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.sqlite3',
            'NAME': 'tmp.db',
            'USER': '',
            'PASSWORD': '',
            'HOST': '',
            'PORT': '',
        }
    }

同时更改`settings.py`文件中的`INSTALLD_APPS`,添加我们的APP `snippets`和`rest_framework`

    INSTALLED_APPS = (
        ...
        'rest_framework',
        'snippets',
    )

在`tutorial/urls.py`中，将snippets app的url包含进来

    urlpatterns = patterns('',
        url(r'^', include('snippets.urls')),
    )

3\. 创建Model
-----------

这里我们创建一个简单的`snippets` model，目的是用来存储代码片段。

    from django.db import models
    from pygments.lexers import get_all_lexers
    from pygments.styles import get_all_styles
    
    LEXERS = [item for item in get_all_lexers() if item[1]]
    LANGUAGE_CHOICES = sorted([(item[1][0], item[0]) for item in LEXERS])
    STYLE_CHOICES = sorted((item, item) for item in get_all_styles())
    
    class Snippet(models.Model):
        created = models.DateTimeField(auto_now_add=True)
        title = models.CharField(max_length=100, default='')
        code = models.TextField()
        linenos = models.BooleanField(default=False)
        language = models.CharField(choices=LANGUAGE_CHOICES,
                                    default='python',
                                    max_length=100)
        style = models.CharField(choices=STYLE_CHOICES,
                                 default='friendly',
                                 max_length=100)
    
        class Meta:
            ordering = ('created',)

完成model时，记得sync下数据库

    python manage.py syncdb

4\. 创建序列化类
----------

我们要使用我们的web api，要做的第一件事就是序列化和反序列化， 以便snippets实例能转换为可表述的内容，例如`json`. 我们声明一个可有效工作的串行器serializer。在`snippets`目录下面，该串行器与django 的表单形式很类似。创建一个`serializers.py` ，并将下面内容拷贝到文件中。

    from django.forms import widgets
    from rest_framework import serializers
    from snippets.models import Snippet
    
    class SnippetSerializer(serializers.Serializer):
        pk = serializers.Field()  # Note: `Field` is an untyped read-only field.
        title = serializers.CharField(required=False,
                                      max_length=100)
        code = serializers.CharField(widget=widgets.Textarea,
                                     max_length=100000)
        linenos = serializers.BooleanField(required=False)
        language = serializers.ChoiceField(choices=models.LANGUAGE_CHOICES,
                                           default='python')
        style = serializers.ChoiceField(choices=models.STYLE_CHOICES,
                                        default='friendly')
    
        def restore_object(self, attrs, instance=None):
            """
            Create or update a new snippet instance.
            """
            if instance:
                # Update existing instance
                instance.title = attrs['title']
                instance.code = attrs['code']
                instance.linenos = attrs['linenos']
                instance.language = attrs['language']
                instance.style = attrs['style']
                return instance
    
            # Create new instance
            return Snippet(**attrs)

该序列化类的前面部分，定义了要序列化和反序列化的类型，`restore_object` 方法定义了如何通过反序列化数据，生成正确的对象实例。

Notice that we can also use various attributes that would typically be used on form fields, such as `widget=widgets.Textarea`. These can be used to control how the serializer should render when displayed as an HTML form. This is particularly useful for controlling how the browsable API should be displayed, as we'll see later in the tutorial.

我们也可以使用`ModelSerializer`来快速生成，后面我们将节省如何使用它。

5\. 使用 Serializers
------------------

在我们使用我们定义的SnippetsSerializers之前，我们先熟悉下Snippets.

    $python manage.py shell

进入shell终端后，输入以下代码：

    from snippets.models import Snippet
    from snippets.serializers import SnippetSerializer
    from rest_framework.renderers import JSONRenderer
    from rest_framework.parsers import JSONParser
    
    snippet = Snippet(code='print "hello, world"\n')
    snippet.save()

我们现在获得了一个Snippets的实例，现在我们对他进行以下序列化

    serializer = SnippetSerializer(snippet)
    serializer.data
    # {'pk': 1, 'title': u'', 'code': u'print "hello, world"\n', 'linenos': False, 'language': u'python', 'style': u'friendly'}

这时，我们将该实例转成了python原生的数据类型。下面我们将该数据转换成`json`格式，以完成序列化：

    content = JSONRenderer().render(serializer.data)
    content
    # '{"pk": 1, "title": "", "code": "print \\"hello, world\\"\\n", "linenos": false, "language": "python", "style": "friendly"}'

反序列化也很简单，首先我们要将一个输入流（content），转换成python的原生数据类型

    import StringIO
    
    stream = StringIO.StringIO(content)
    data = JSONParser().parse(stream)

然后我们将该原生数据类型，转换成对象实例

    serializer = SnippetSerializer(data=data)
    serializer.is_valid()
    # True
    serializer.object
    # <Snippet: Snippet object>

注意这些API和django表单的相似处。这些相似点， 在我们讲述在view中使用serializers时将更加明显。

We can also serialize querysets instead of model instances. To do so we simply add a `many=True` flag to the serializer arguments.

    serializer = SnippetSerializer(Snippet.objects.all(), many=True)
    serializer.data
    # [{'pk': 1, 'title': u'', 'code': u'foo = "bar"\n', 'linenos': False, 'language': u'python', 'style': u'friendly'}, {'pk': 2, 'title': u'', 'code': u'print "hello, world"\n', 'linenos': False, 'language': u'python', 'style': u'friendly'}]

6\. 使用 ModelSerializers
-----------------------

`SnippetSerializer`使用了许多和`Snippet`中相同的代码。如果我们能把这部分代码去掉，看上去将更佳简洁。

类似与django提供`Form`类和`ModelForm`类，Rest Framework也包含了`Serializer` 类和 `ModelSerializer`类。

打开`snippets/serializers.py` ,修改`SnippetSerializer`类：

    class SnippetSerializer(serializers.ModelSerializer):
        class Meta:
            model = Snippet
            fields = ('id', 'title', 'code', 'linenos', 'language', 'style')

7\. 通过Serializer编写Django View
-----------------------------

让我们来看一下，如何通过我们创建的serializer类编写django view。这里我们不使用rest framework的其他特性，仅编写正常的django view。

我们创建一个HttpResponse 子类，这样我们可以将我们返回的任何数据转换成`json`。

在`snippet/views.py`中添加以下内容：

    from django.http import HttpResponse
    from django.views.decorators.csrf import csrf_exempt
    from rest_framework.renderers import JSONRenderer
    from rest_framework.parsers import JSONParser
    from snippets.models import Snippet
    from snippets.serializers import SnippetSerializer
    
    class JSONResponse(HttpResponse):
        """
        An HttpResponse that renders it's content into JSON.
        """
        def __init__(self, data, **kwargs):
            content = JSONRenderer().render(data)
            kwargs['content_type'] = 'application/json'
            super(JSONResponse, self).__init__(content, **kwargs)

我们API的目的是，可以通过view来列举全部的Snippet的内容，或者创建一个新的snippet

    @csrf_exempt
    def snippet_list(request):
        """
        List all code snippets, or create a new snippet.
        """
        if request.method == 'GET':
            snippets = Snippet.objects.all()
            serializer = SnippetSerializer(snippets)
            return JSONResponse(serializer.data)
    
        elif request.method == 'POST':
            data = JSONParser().parse(request)
            serializer = SnippetSerializer(data=data)
            if serializer.is_valid():
                serializer.save()
                return JSONResponse(serializer.data, status=201)
            else:
                return JSONResponse(serializer.errors, status=400)

注意，因为我们要通过client向该view post一个请求，所以我们要将该view 标注为`csrf_exempt`, 以说明不是一个CSRF事件。

Note that because we want to be able to POST to this view from clients that won't have a CSRF token we need to mark the view as `csrf_exempt`. This isn't something that you'd normally want to do, and REST framework views actually use more sensible behavior than this, but it'll do for our purposes right now.

我们也需要一个view来操作一个单独的Snippet，以便能更新／删除该对象。

    @csrf_exempt
    def snippet_detail(request, pk):
        """
        Retrieve, update or delete a code snippet.
        """
        try:
            snippet = Snippet.objects.get(pk=pk)
        except Snippet.DoesNotExist:
            return HttpResponse(status=404)
    
        if request.method == 'GET':
            serializer = SnippetSerializer(snippet)
            return JSONResponse(serializer.data)
    
        elif request.method == 'PUT':
            data = JSONParser().parse(request)
            serializer = SnippetSerializer(snippet, data=data)
            if serializer.is_valid():
                serializer.save()
                return JSONResponse(serializer.data)
            else:
                return JSONResponse(serializer.errors, status=400)
    
        elif request.method == 'DELETE':
            snippet.delete()
            return HttpResponse(status=204)

将views.py保存，在Snippets目录下面创建`urls.py`,添加以下内容：

    urlpatterns = patterns('snippets.views',
        url(r'^snippets/$', 'snippet_list'),
        url(r'^snippets/(?P<pk>[0-9]+)/$', 'snippet_detail'),
    )

注意我们有些边缘事件没有处理，服务器可能会抛出500异常。

It's worth noting that there are a couple of edge cases we're not dealing with properly at the moment. If we send malformed json, or if a request is made with a method that the view doesn't handle, then we'll end up with a 500 "server error" response. Still, this'll do for now.

8\. 测试
------

现在我们启动server来测试我们的Snippet。

在python [mange.py](http://mange.py) shell终端下执行（如果前面进入还没有退出）

quit()

执行下面的命令， 运行我们的server：

    python manage.py runserver
    
    Validating models...
    
    0 errors found
    Django version 1.4.3, using settings 'tutorial.settings'
    Development server is running at http://127.0.0.1:8000/
    Quit the server with CONTROL-C.

新开一个terminal来测试我们的server

序列化：

    curl http://127.0.0.1:8000/snippets/
    >>[{"id": 1, "title": "", "code": "print \"hello, world\"\n", "linenos": false, "language": "python", "style": "friendly"}]
    
    curl http://127.0.0.1:8000/snippets/1/
    >>{"id": 1, "title": "", "code": "print \"hello, world\"\n", "linenos": false, "language": "python", "style": "friendly"}

Tutorial 2: Requests and Response
=================================

[src](http://django-rest-framework.org/tutorial/2-requests-and-responses.html)

从本节我们开始真正接触rest framework的核心部分。首先我们学习一下一些必备知识。

1\. Request Object ——Request对象
------------------------------

rest framework 引入了一个继承自`HttpRequest`的`Request`对象，该对象提供了对请求的更灵活解析。`request`对象的核心部分是`request.DATA`属性，类似于`request.POST`, 但在使用WEB API时，`request.DATA`更有效。

request.POST # Only handles form data. Only works for 'POST' method. request.DATA # Handles arbitrary data. Works any HTTP request with content.

2\. Response Object ——Response对象
--------------------------------

rest framework引入了一个`Response` 对象，它继承自`TemplateResponse`对象。它获得未渲染的内容并通过内容协商content negotiation 来决定正确的content type返回给client。

    return Response(data)  # Renders to content type as requested by the client.

3\. Status Codes
----------------

在views当中使用数字化的HTTP状态码，会使你的代码不宜阅读，且不容易发现代码中的错误。rest framework为每个状态码提供了更明确的标识。例如`HTTP_400_BAD_REQUEST`在`status` module。相比于使用数字，在整个views中使用这类标识符将更好。

4\. 封装API views
---------------

在编写API views时，REST Framework提供了两种wrappers：

1.  `@api_viwe`decorator for working with _function based_ views.
2.  `APIView` class for working with _class based_ views.

这两种封装器提供了许多功能，例如，确保在view当中能够接收到`Request`实例；往`Response`中增加内容以便内容协商content negotiation 机制能够执行。

封装器也提供一些行为，例如在适当的时候返回`405 Methord Not Allowed`响应；在访问多类型的输入`request.DATA`时，处理任何的`ParseError`异常。

5\. 汇总
------

我们开始用这些新的组件来写一些views。

我们不在需要`JESONResponse` 类（在前一篇中创建），将它删除。删除后我们开始稍微重构下我们的view

    from rest_framework import status
    from rest_framework.decorators import api_view
    from rest_framework.response import Response
    from snippets.models import Snippet
    from snippets.serializers import SnippetSerializer
    
    @api_view(['GET', 'POST'])
    def snippet_list(request):
        """
        List all snippets, or create a new snippet.
        """
        if request.method == 'GET':
            snippets = Snippet.objects.all()
            serializer = SnippetSerializer(snippets)
            return Response(serializer.data)
    
        elif request.method == 'POST':
            serializer = SnippetSerializer(data=request.DATA)
            if serializer.is_valid():
                serializer.save()
                return Response(serializer.data, status=status.HTTP_201_CREATED)
            else:
                return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

上面的代码是对我们之前代码的改进。看上去更简洁，也更类似于django的forms api形式。我们也采用了状态码，使返回值更加明确。 下面是对单个snippet操作的view更新：

    @api_view(['GET', 'PUT', 'DELETE'])
    def snippet_detail(request, pk):
        """
        Retrieve, update or delete a snippet instance.
        """              
        try:
            snippet = Snippet.objects.get(pk=pk)
        except Snippet.DoesNotExist:
            return Response(status=status.HTTP_404_NOT_FOUND)
    
        if request.method == 'GET':
            serializer = SnippetSerializer(snippet)
            return Response(serializer.data)
    
        elif request.method == 'PUT':
            serializer = SnippetSerializer(snippet, data=request.DATA)
            if serializer.is_valid():
                serializer.save()
                return Response(serializer.data)
            else:
                return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
    
        elif request.method == 'DELETE':
            snippet.delete()
            return Response(status=status.HTTP_204_NO_CONTENT)

注意，我们并没有明确的要求requests或者responses给出content type。`request.DATA`可以处理输入的`json`请求，也可以输入`yaml`和其他格式。类似的在response返回数据时，REST Framework返回正确的content type给client。

6\. 给URLs增加可选的格式后缀
------------------

利用在response时不需要指定content type这一事实，我们在API端增加格式的后缀。使用格式后缀，可以明确的指出使用某种格式，意味着我们的API可以处理类似http://example.com/api/items/4.json.的URL。

增加`format`参数在views中，如：

    def snippet_list(request, format=None):

and

    def snippet_detail(request, pk, format=None):

现在稍微改动`urls.py`文件，在现有的URLs中添加一个格式后缀pattterns (`format_suffix_patterns`):

    from django.conf.urls import patterns, url
    from rest_framework.urlpatterns import format_suffix_patterns
    
    urlpatterns = patterns('snippets.views',
        url(r'^snippets/$', 'snippet_list'),
        url(r'^snippets/(?P<pk>[0-9]+)$', 'snippet_detail'),
    )

urlpatterns = format\_suffix\_patterns(urlpatterns) 这些额外的url patterns并不是必须的。

7\. How's it looking?
---------------------

Go ahead and test the API from the command line, as we did in tutorial part 1. Everything is working pretty similarly, although we've got some nicer error handling if we send invalid requests.

We can get a list of all of the snippets, as before.

    curl http://127.0.0.1:8000/snippets/
    [{"id": 1, "title": "", "code": "foo = \"bar\"\n", "linenos": false, "language": "python", "style": "friendly"}, {"id": 2, "title": "", "code": "print \"hello, world\"\n", "linenos": false, "language": "python", "style": "friendly"}]

We can control the format of the response that we get back, either by using the Accept header:

    curl http://127.0.0.1:8000/snippets/ -H 'Accept: application/json'  # Request JSON
    curl http://127.0.0.1:8000/snippets/ -H 'Accept: text/html'         # Request HTML

Or by appending a format suffix:

    curl http://127.0.0.1:8000/snippets/.json  # JSON suffix
    curl http://127.0.0.1:8000/snippets/.api   # Browsable API suffix

Similarly, we can control the format of the request that we send, using the Content-Type header.

    # POST using form data
    curl -X POST http://127.0.0.1:8000/snippets/ -d "code=print 123"
    {"id": 3, "title": "", "code": "123", "linenos": false, "language": "python", "style": "friendly"}
    
    # POST using JSON
    curl -X POST http://127.0.0.1:8000/snippets/ -d '{"code": "print 456"}' -H "Content-Type: application/json"
    {"id": 4, "title": "", "code": "print 456", "linenos": true, "language": "python", "style": "friendly"}

Now go and open the API in a web browser, by visiting [http://127.0.0.1:8000/snippets/](http://127.0.0.1:8000/snippets/).

8\. Browsability
----------------

Because the API chooses the content type of the response based on the client request, it will, by default, return an HTML-formatted representation of the resource when that resource is requested by a web browser. This allows for the API to return a fully web-browsable HTML representation.

Having a web-browsable API is a huge usability win, and makes developing and using your API much easier. It also dramatically lowers the barrier-to-entry for other developers wanting to inspect and work with your API.

See the [browsable api](http://django-rest-framework.org/topics/browsable-api.html) topic for more information about the browsable API feature and how to customize it.

Tutorial 3: Class Based Views
=============================

[src](http://django-rest-framework.org/tutorial/3-class-based-views.html)

在之前基于函数的View之外，我们还可以用基于类的view来实现我们的API view。正如我们即将看到的那样，这样的方式可以让我们重用公用功能，并使我们保持代码DRY。

用基于类的view重写我们的API

1\. 我们要用基于类的view来重写刚才的根view，如下重构所示：
-----------------------------------

    from snippets.models import Snippet
    from snippets.serializers import SnippetSerializer
    from django.http import Http404
    from rest_framework.views import APIView
    from rest_framework.response import Response
    from rest_framework import status
    
    class SnippetList(APIView):
        """
        List all snippets, or create a new snippet.
        """
        def get(self, request, format=None):
            snippets = Snippet.objects.all()
            serializer = SnippetSerializer(snippets, many=True)
            return Response(serializer.data)
    
        def post(self, request, format=None):
            serializer = SnippetSerializer(data=request.DATA)
            if serializer.is_valid():
                serializer.save()
                return Response(serializer.data, status=status.HTTP_201_CREATED)
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

目前看上去不错。它看起来和我们之前写的很相似，但我们在不同的HTTP方法见有了更好的分隔方式，我们还需要把示例的view也重构一下：

    class SnippetDetail(APIView):
        """
        Retrieve, update or delete a snippet instance.
        """
        def get_object(self, pk):
            try:
                return Snippet.objects.get(pk=pk)
            except Snippet.DoesNotExist:
                raise Http404
    
        def get(self, request, pk, format=None):
            snippet = self.get_object(pk)
            serializer = SnippetSerializer(snippet)
            return Response(serializer.data)
    
        def put(self, request, pk, format=None):
            snippet = self.get_object(pk)
            serializer = SnippetSerializer(snippet, data=request.DATA)
            if serializer.is_valid():
                serializer.save()
                return Response(serializer.data)
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
    
        def delete(self, request, pk, format=None):
            snippet = self.get_object(pk)
            snippet.delete()
            return Response(status=status.HTTP_204_NO_CONTENT)

做的不错。它和我们之前写的基于函数的view还是有些相像。

我们还需要对URLconf做一些小小的改动：

    from django.conf.urls import patterns, url
    from rest_framework.urlpatterns import format_suffix_patterns
    from snippets import views
    
    urlpatterns = patterns('',
        url(r'^snippets/$', views.SnippetList.as_view()),
        url(r'^snippets/(?P<pk>[0-9]+)/$', views.SnippetDetail.as_view()),
    )
    
    urlpatterns = format_suffix_patterns(urlpatterns)

到目前为止，已经全部完成。你可以运行开发服务器，一切应该表现如初。

2\. 使用mixins
------------

使用基于类的view的最大好处就是可以让我们方便的组合与重用。

刚才我们的create/retrieve/update/delete等函数实现在模型支撑API view下会很类似。其中的公共行为在REST framework's mixin类中实现了。

我们来看看，我们可以用mixin类来吧我们的view组合起来：

    from snippets.models import Snippet
    from snippets.serializers import SnippetSerializer
    from rest_framework import mixins
    from rest_framework import generics
    
    class SnippetList(mixins.ListModelMixin,
                      mixins.CreateModelMixin,
                      generics.GenericAPIView):
        model = Snippet
        serializer_class = SnippetSerializer
    
        def get(self, request, *args, **kwargs):
            return self.list(request, *args, **kwargs)
    
        def post(self, request, *args, **kwargs):
            return self.create(request, *args, **kwargs)

我们将花点时间来解释下这里到底发生了什么。我们用`GenericAPIView`构建了我们的view, 然后加上了`ListModelMixin`和`CreateModelMixin`.

基类提供了核心功能，mixin类提供了 `.list()` 和 `.create()` 动作。我们然后显式的把 `get` 和 `post` 方法与合适的动作绑定在一起，非常简单。

    class SnippetDetail(mixins.RetrieveModelMixin,
                        mixins.UpdateModelMixin,
                        mixins.DestroyModelMixin,
                        generics.GenericAPIView):
        model = Snippet
        serializer_class = SnippetSerializer
    
        def get(self, request, *args, **kwargs):
            return self.retrieve(request, *args, **kwargs)
    
        def put(self, request, *args, **kwargs):
            return self.update(request, *args, **kwargs)
    
        def delete(self, request, *args, **kwargs):
            return self.destroy(request, *args, **kwargs)

示例部分的实现也非常类似。这次我们用`GenericAPIView`来提供核心功能，然后用mixins来提供`.retrieve()`, `.update()` 和 `.destroy()` actions.

3\. 使用基于泛型类的view
----------------

使用mixin类可以让我们重写view时写更少的代码，但我们还可以更进一步，REST framework提供了一系列已经mixed-in的泛型view供我们使用。

    from snippets.models import Snippet
    from snippets.serializers import SnippetSerializer
    from rest_framework import generics
    
    class SnippetList(generics.ListCreateAPIView):
        model = Snippet
        serializer_class = SnippetSerializer
    
    class SnippetDetail(generics.RetrieveUpdateDestroyAPIView):
        model = Snippet
        serializer_class = SnippetSerializer

Wow, 非常简洁. 我们轻松了不少，而且代码看起来优美，干净和符合Django的习惯。

在第四部分 part 4 of the tutorial, 我们将看看我们的API如何处理认证和权限。

Tutorial 4: Authentication & Permissions
========================================

[src](http://django-rest-framework.org/tutorial/4-authentication-and-permissions.html)

目前为止，我们的API对谁能编辑或删除snippet（代码片段）还没有任何限制。我们将增加一些扩展功能来确保以下：

*   snippets总关联一个创建者；
*   只有认证后的用户才能创建一个snippets.
*   只有创建者才能更新或删除snippet；
*   非认证请求只拥有只读权限。

1\. 为model增加信息
--------------

我们先需要对`Snippet`的model做些修改。首先，增加一些fields. 其中一个用来表示创建者，另一个用来存储代码中的HTML高亮。

在模型中增加这两个字段。

    owner = models.ForeignKey('auth.User', related_name='snippets')
    highlighted = models.TextField()

我们还需要确保model存储时，我们能生成高亮字段内容，这里使用 `pygments` 代码高亮库。

首先需要一些额外的imports:

    from pygments.lexers import get_lexer_by_name
    from pygments.formatters.html import HtmlFormatter
    from pygments import highlight

我们现在为模型增加一个 `.save()` 方法：

    def save(self, *args, **kwargs):
        """
        Use the `pygments` library to create a highlighted HTML
        representation of the code snippet.
        """
        lexer = get_lexer_by_name(self.language)
        linenos = self.linenos and 'table' or False
        options = self.title and {'title': self.title} or {}
        formatter = HtmlFormatter(style=self.style, linenos=linenos,
                                  full=True, **options)
        self.highlighted = highlight(self.code, lexer, formatter)
        super(Snippet, self).save(*args, **kwargs)

这些完成后还需要更新一下数据库里面的表。通常我们要创建一个数据库迁移（database migration）来完成，但教程中，我们就只是删除数据库然后重新创建：

    rm tmp.db
    python ./manage.py syncdb

你可能想创建一些其他用户来测试这些API，最快捷的方式是利用 `createsuperuser` 命令。

    python ./manage.py createsuperuser

2\. 为用户模型增加endpoints
--------------------

现在我们需要一些用户，我们最好把用户呈现也增加到API上，创建一个新的serializer很容易：

    from django.contrib.auth.models import User
    
    class UserSerializer(serializers.ModelSerializer):
        snippets = serializers.PrimaryKeyRelatedField(many=True)
    
        class Meta:
            model = User
            fields = ('id', 'username', 'snippets')

因为 `'snippets'` 和用户model是反向关联（reverse relationship，即多对一），所以在使用 `ModelSerializer`时并不会缺省加入，所以我们需要显式的来实现。

我们还需要创建一些views，对用户呈现而言，我们最好使用只读的view，所以使用 `ListAPIView` 和 `RetrieveAPIView` 泛型类Views。

    class UserList(generics.ListAPIView):
        model = User
        serializer_class = UserSerializer
    
    class UserDetail(generics.RetrieveAPIView):
        model = User
        serializer_class = UserSerializer

最后，我们需要修改URL conf：

    url(r'^users/$', views.UserList.as_view()),
    url(r'^users/(?P<pk>[0-9]+)/$', views.UserDetail.as_view()),

3\. 把Snippets与Users关联
---------------------

现在，如果我们创建一个code snippet，还没有方法指定其创建者。User并没有作为序列化内容的一部分发送，而是作为request的一个属性。

这里的处理方法是重载snippet view中的 `.pre_save()` 方法，它可以让我们处理request中隐式的信息。

在 `SnippetList` 和 `SnippetDetail` 的view类中，都需要添加如下的方法：

    def pre_save(self, obj):
        obj.owner = self.request.user

4\. 更新 serializer
-----------------

现在snippets已经和创建者关联起来了，我们接下来还需要更新`SnippetSerializer`，在其定义中增加一个新的字段：

    owner = serializers.Field(source='owner.username')

Note: 确定你在嵌入类`Meta`的字段列表中也加入了 `'owner'`。

这个字段所做的十分有趣。`source` 参数表明增加一个新字段，可以指定序列化实例任何属性。它可以采用如上的点式表示（dotted notation），这时他可以直接遍历到指定的属性。在Django's template中使用时，也可以采用类似的方式。

我们增加字段是一个无类型 `Field` 类，而我们之前的字段都是有类型的，例如 `CharField`, `BooleanField` etc... 无类型字段总是只读的，它们只用在序列化表示中，而在反序列化时（修改model）不被使用。

5\. 给view增加权限控制
---------------

现在代码片段 snippets 已经关联了用户，我们需要确保只有认证用户才能增、删、改snippets.

REST framework 包括许多权限类可用于view的控制。这里我们使用 `IsAuthenticatedOrReadOnly`, 它可确保认证的request获取read-write权限，而非认证的request只有read-only 权限.

现需要在views模块中增加 import。

    from rest_framework import permissions

然后需要在 `SnippetList` 和 `SnippetDetail` view类中都增加如下属性：

    permission_classes = (permissions.IsAuthenticatedOrReadOnly,)

6\. 为可浏览API（Browseable API）增加login
----------------------------------

如果你打开浏览器，访问可浏览API，你会发现只有登录后才能创建新的snippet了。

我们可以编辑URLconf来增加一个登录view。首先增加新的import：

    from django.conf.urls import include

然后，在文件末尾增加一个pattern来为browsable API增加 login 和 logout views.

    urlpatterns += patterns('',
        url(r'^api-auth/', include('rest_framework.urls',
                                   namespace='rest_framework')),
    )

具体的， `r'^api-auth/'` 部分可以用任何你想用的URL来替代。这里唯一的限制就是 urls 必须使用`'rest_framework'` 命名空间。

现在如果你打开浏览器，刷新页面会看到页面右上方的 'Login' 链接。如果你用之前的用户登录后，你就又可以创建 snippets了。

一旦你创建了一些snippets，当导航至'/users/'时，你会看到在每个user的snippets字段都包含了一系列snippet的pk。

7\. 对象级别的权限
-----------

我们希望任何人都可以浏览snippets，但只有创建snippet的用户才能编辑或删除它。

为了实现这个需求，我们需要创建定制的权限（custom permission）。

在 snippets 应用中，创建一个新文件： `permissions.py`

    from rest_framework import permissions
    
    class IsOwnerOrReadOnly(permissions.BasePermission):
        """
        Custom permission to only allow owners of an object to edit it.
        """
    
        def has_object_permission(self, request, view, obj):
            # Read permissions are allowed to any request,
            # so we'll always allow GET, HEAD or OPTIONS requests.
            if request.method in permissions.SAFE_METHODS:            
                return True
    
            # Write permissions are only allowed to the owner of the snippet
            return obj.owner == request.user

现在我们可以为snippet实例增加定制权限了，需要编辑 `SnippetDetail` 类的 `permission_classes` 属性：

    permission_classes = (permissions.IsAuthenticatedOrReadOnly,
                          IsOwnerOrReadOnly,)

别忘了import 这个`IsOwnerOrReadOnly` 类。

    from snippets.permissions importIsOwnerOrReadOnly

现在打开浏览器，你可以看见 'DELETE' 和 'PUT' 动作只会出现在那些你的登录用户创建的snippet页面上了.

8\. 通过API认证
-----------

我们已经有了一系列的权限，如果我们需要编辑任何snippet，我们需要认证我们的request。因为我们还没有建立任何 [authentication classes](http://django-rest-framework.org/api-guide/authentication.html), 所以目前是默认的`SessionAuthentication` 和`BasicAuthentication`在起作用。

当我们通过Web浏览器与API互动时，我们登录后、然后浏览器session可以为所有的request提供所需的验证。

如果我们使用程序访问这些API，我们则需要显式的为每个request提供认证凭证（authentication credentials）。

如果我们试图未认证就创建一个snippet，将得到错误如下：

    curl -i -X POST http://127.0.0.1:8000/snippets/ -d "code=print 123"
    {"detail": "Authentication credentials were not provided."}

如果我们带着用户名和密码来请求时则可以成功创建：

    curl -X POST http://127.0.0.1:8000/snippets/ -d "code=print 789" -u tom:password
    {"id": 5, "owner": "tom", "title": "foo", "code": "print 789", "linenos": false, "language": "python", "style": "friendly"}

9\. 小结
------

我们已经为我们的Web API创建了相当细粒度的权限控制和相应的系统用户。

在教程第5部分 part 5 ，我们将把所有的内容串联起来，为我们的高亮代码片段创建HTML节点，并利用系统内的超链接关联来提升API的一致性表现。

Tutorial 5: Relationships & Hyperlinked APIs
============================================

[src](http://django-rest-framework.org/tutorial/5-relationships-and-hyperlinked-apis.html)

到目前为止，在我们的API中关系（relationship）还是通过主键来表示的。在这部分的教程中，我们将用超链接方式来表示关系，从而提升API的统一性和可发现性。

1\. 为API根创建一个endpoint
---------------------

到目前为止，我们已经有了'snippets'和'users'的endpoint, 但是我们还没有为我们的API单独创立一个端点入口。我们可以用常规的基于函数的view和之前介绍的 `@api_view` 修饰符来创建。

    from rest_framework import renderers
    from rest_framework.decorators import api_view
    from rest_framework.response import Response
    from rest_framework.reverse import reverse
    
    @api_view(('GET',))
    def api_root(request, format=None):
        return Response({
            'users': reverse('user-list', request=request, format=format),
            'snippets': reverse('snippet-list', request=request, format=format)
        })

请注意，我们用 REST framework 的 `reverse` 函数来返回完全合规的URLs.

2\. 为高亮的Snippet创建一个endpoint
---------------------------

我们目前还没有为支持代码高亮的Snippet创建一个endpoints.

与之前的API endpoints不同, 我们将直接使用HTML呈现，而非JSON。在 REST framework中有两种风格的HTML render, 一种使用模板来处理HTML，一种则使用预先处理的方式。在这里我们使用后者。

另一个需要我们考虑的是，对于高亮代码的view并没有具体的泛型view可以直接利用。我们将只返回实例的一个属性而不是对象实例本身。

没有具体泛型view的支持，我们使用基类来表示实例，并创建我们自己的 `.get()` 方法。在你的 snippets.views 中增加：

    from rest_framework import renderers
    from rest_framework.response import Response
    
    class SnippetHighlight(generics.SingleObjectAPIView):
        model = Snippet
        renderer_classes = (renderers.StaticHTMLRenderer,)
    
        def get(self, request, *args, **kwargs):
            snippet = self.get_object()
            return Response(snippet.highlighted)

和以往一样，我们需要为新的view增加新的URLconf，如下增加urlpatterns:

    url(r'^$','api_root'),

还需要为代码高亮增加一个urlpatterns：

    url(r'^snippets/(?P<pk>[0-9]+)/highlight/$', views.SnippetHighlight.as_view()),

3\. API超链接化
-----------

在Web API设计中，处理实体间关系是一个有挑战性的工作。我们有许多方式来表示关系：

*   使用主键；
*   使用超链接；
*   使用相关实体唯一标识的字段；
*   使用相关实体的默认字符串表示；
*   在父级表示中嵌入子级实体；
*   其他自定义的表示。

REST framework支持所有这些方式，包括正向或者反向的关系，或者将其应用到自定义的管理类中，例如泛型外键。

在这部分，我们使用超链接方式。为了做到这一点，我们需要在序列化器中用 `HyperlinkedModelSerializer` 来替代之前的 `ModelSerializer`.

`HyperlinkedModelSerializer` 与 `ModelSerializer` 有如下的区别:

*   缺省状态下不包含 `pk` 字段；
*   具有一个 `url` 字段，即`HyperlinkedIdentityField`类型.
*   用`HyperlinkedRelatedField`表示关系，而非`PrimaryKeyRelatedField`.
*   我们可以很方便的改写现有代码来使用超连接方式： class SnippetSerializer(serializers.HyperlinkedModelSerializer): owner = serializers.Field(source='owner.username') highlight = serializers.HyperlinkedIdentityField(view_name='snippet-highlight', format='html')
    
        class Meta:
              model = models.Snippet
              fields = ('url', 'highlight', 'owner',
                        'title', 'code', 'linenos', 'language', 'style')
    
    class UserSerializer(serializers.HyperlinkedModelSerializer): snippets = serializers.HyperlinkedRelatedField(many=True, view_name='snippet-detail')
    
        class Meta:
              model = User
              fields = ('url', 'username', 'snippets')
    

注意：我们也增加了一个新的 `'highlight'` 字段。该字段与 `url` 字段相同类型。不过它指向了 `'snippet-highlight'`的 url pattern, 而非`'snippet-detail'`的url pattern.

因为我们已经有一个 `'.json'`的后缀，为了更好的表明`highlight`字段链接的区别，使用一个 `'.html'` 的后缀。

4\. 正确使用URL patterns
--------------------

如果要使用超链接API，就必须确保正确的命名和使用 URL patterns. 我们来看看我们需要命名的 URL patterns：

*   指向 `'user-list'` 和 `'snippet-list'` 的API根.
*   snippet的序列化器，包括一个 `'snippet-highlight'`字段.
*   user序列化器，包含一个 `'snippet-detail'`字段.
*   snippet 和user的序列化器，包含 `'url'` 字段（会缺省指向`'snippet-detail'` 和 `'user-detail'`.

一番工作之后，最终的 `'urls.py'` 文件应该如下所示：

    # API endpoints
    urlpatterns = format_suffix_patterns(patterns('snippets.views',
        url(r'^$', 'api_root'),
        url(r'^snippets/$',
            views.SnippetList.as_view(),
            name='snippet-list'),
        url(r'^snippets/(?P<pk>[0-9]+)/$',
            views.SnippetDetail.as_view(),
            name='snippet-detail'),
        url(r'^snippets/(?P<pk>[0-9]+)/highlight/$',
            views.SnippetHighlight.as_view(),
            name='snippet-highlight'),
        url(r'^users/$',
            views.UserList.as_view(),
            name='user-list'),
        url(r'^users/(?P<pk>[0-9]+)/$',
            views.UserDetail.as_view(),
            name='user-detail')
    ))
    
    # Login and logout views for the browsable API
    urlpatterns += patterns('',    
        url(r'^api-auth/', include('rest_framework.urls',
                                   namespace='rest_framework')),
    )

5\. 添加分页
--------

列表view有时会返回大量的实例结果，所以我们应该把结果分页显示，以便用户使用。

通过在 `settings.py` 中添加如下配置，我们就能在结果列表中增加分页的功能:

    REST_FRAMEWORK ={'PAGINATE_BY':10}

请注意REST framework的所有配置信息都是存放在一个叫做 'REST_FRAMEWORK'的dictionary中，以便于其他配置区分。

如有必要，你也可以自定义分页的方式，这里不再赘述。

6\. Browsing the API
--------------------

If we open a browser and navigate to the browsable API, you'll find that you can now work your way around the API simply by following links.

You'll also be able to see the 'highlight' links on the snippet instances, that will take you to the highlighted code HTML representations.

In part 6 of the tutorial we'll look at how we can use ViewSets and Routers to reduce the amount of code we need to build our API.

Tutorial 6:ViewSets & Routers
=============================

REST framework包含一个抽象概念来处理`ViewSets`，它使得开发者可以集中精力对 API的state和interactions建模，留下URL构造被自动处理，基于共同约定。

`ViewSet`类几乎和`View`类一样，除了它们提供像`read`或`update`操作，但没有处理`get`和`put`的方法。

一个`ViewSet`类只是绑定到一组方法处理程序在最后一刻，在它被实例化到一组视图的时候，通常是使用一个`Router`类——为你处理定义URL conf的复杂性。

使用ViewSets来重构
-------------

让我们取出当前集合的views，使用view sets将它们重构。

首先让我们重构我们的`UserListView`和`UserDetailView`views成一个`UserViewSet`。我们可以移除两个views，用一个类来替换它们。

    class UserViewSet(viewsets.ReadOnlyModelViewSet):
        """
        This viewset automatically provides `list` and `detail` actions.
        """
        queryset = User.objects.all()
        serializer_class = UserSerializer

在这里我们将使用`ReadOnlyModelViewSet类`来自动地提供默认的'read-only'操作。正如我们在使用常规的views做的，我们还是会设置`queryset`和`serializer_class`属性，但我们不再需要提供相同的信息给两个独立的类。

下一步我们将替换`SnippetList`,`SnippetDetial`和`SnippetHighlight`view类。我们可以移除这三个views，再次用一个类来替换它们。

    from rest_framework import viewsets
    from rest_framework.decorators import link
    
    class SnippetViewSet(viewsets.ModelViewSet):
        """
        This viewset automatically provides `list`, `create`, `retrieve`,
        `update` and `destroy` actions.
    
        Additionally we also provide an extra `highlight` action. 
        """
        queryset = Snippet.objects.all()
        serializer_class = SnippetSerializer
        permission_classes = (permissions.IsAuthenticatedOrReadOnly,
                              IsOwnerOrReadOnly,)
    
        @link(renderer_classes=[renderers.StaticHTMLRenderer])
        def highlight(self, request, *args, **kwargs):
            snippet = self.get_object()
            return Response(snippet.highlighted)
    
        def pre_save(self, obj):
            obj.owner = self.request.user

这次我们将使用`ModelViewSet`类为了得到默认read和write操作的完整集合。

注意我们还使用`@link`修饰符来创建一个自定义动作名为`highlight`。这个修饰符可以用来添加任何自定义endpoints，不用符合标准的`create`/`update`/`delete`样式。

用`@link`修饰符创建的自定义动作将会对`GET`请亲做出响应。我们也可以使用`@action`修饰符代替如果我们想要一个对`POST`请求做出响应的动作。

明确地绑定ViewSets到URLs
------------------

handler method仅仅在我们定义URLConf的时候绑定到动作(actions)上。去看看盖子下发生了什么首先从我们的ViewSets明确地创建一个views集合。

在urls.py文件中我们绑定了我们的ViewSet类到一个具体views的集合。

    from snippets.views import SnippetViewSet, UserViewSet
    
    snippet_list = SnippetViewSet.as_view({
        'get': 'list',
        'post': 'create'
    })
    snippet_detail = SnippetViewSet.as_view({
        'get': 'retrieve',
        'put': 'update',
        'patch': 'partial_update',
        'delete': 'destroy'
    })
    snippet_highlight = SnippetViewSet.as_view({
        'get': 'highlight'
    })
    user_list = UserViewSet.as_view({
        'get': 'list'
    })
    user_detail = UserViewSet.as_view({
        'get': 'retrieve'
    })

注意我们怎么样从每个ViewSet类创建多样的views，通过绑定http methods到每个view所需的动作(by binding the http methods to the required aciotn for each view)

现在我们已经绑定我们的资源到具体的views，我们可以像往常一样注册views和URL conf。

    urlpatterns = format_suffix_patterns(patterns('snippets.views',
        url(r'^$', 'api_root'),
        url(r'^snippets/$', snippet_list, name='snippet-list'),
        url(r'^snippets/(?P<pk>[0-9]+)/$', snippet_detail, name='snippet-detail'),
        url(r'^snippets/(?P<pk>[0-9]+)/highlight/$', snippet_highlight, name='snippet-highlight'),
        url(r'^users/$', user_list, name='user-list'),
        url(r'^users/(?P<pk>[0-9]+)/$', user_detail, name='user-detail')
    ))

使用Routers
---------

因为我们使用`ViewSet`类而不是`View`类，我们实际上不用自己设计URL conf。连接resources到views和urls的约定可以使用`Router`类自动处理。我们要做的仅仅是用一个router注册适当的view集合，and let it do the rest

这里我们重连接`urls.py`文件

    from snippets import views
    from rest_framework.routers import DefaultRouter
    
    # Create a router and register our viewsets with it.
    router = DefaultRouter()
    router.register(r'snippets', views.SnippetViewSet)
    router.register(r'users', views.UserViewSet)
    
    # The API URLs are now determined automatically by the router.
    # Additionally, we include the login URLs for the browseable API.
    urlpatterns = patterns('',
        url(r'^', include(router.urls)),
        url(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework'))
    )

用router注册viewsets和提供一个urlpattern很像。我们有两个参数——给views的URL前缀和viewset自身。

`DefaultRouter`类自动为我们创建API root vies，所以我们现在可以从我们的`views`方法中删除`api_root`方法。

权衡views VS viewsets
-------------------

使用viewset可以是一个真正有用的抽象。它帮助确保URL约定可以对你的API始终如一，使你需要编写的代码数量最小化，使得你可以集中精力在你API的交互和表现上，而不是URL conf的细节上。

这并不意味着它总是正确的方法。有一个类似的权衡在使用class-based views代替function-based views上。单独构建views比使用viewsets更加清楚。

回顾我们的工作 如果我们打开浏览器来看看之前的API，会发现它们可以用链接的方式工作了。

当你查看snippet实例的 'highlight' 链接时，你会直接看到代码高亮的HTML呈现。

我们目前已经完成了全套的Web API。可以浏览，支持认证，对象粒度的权限，以及多种返回格式。

我们已经完成了流程中的所有步骤，了解了如何从基本的Django View出发，根据需求逐步定义我们的工作方式。

你能从GitHub上得到教程的最终代码： tutorial code ，或者直接访问在线示例： the sandbox.

总结与后续工作
-------

我们的教程已经到此结束，如果你还想更多的了解 REST framework，可以从下面这些地方开始：

为 GitHub 做贡献，审查、提交内容或提出新要求。 参加讨论组 REST framework discussion group, 帮助创建更好的线上社区. Follow the author 作者的 Twitter，打打招呼。 让我们去大展身手吧.