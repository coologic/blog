---
title: " django笔记(6)基于rest-framework的token认证\t\t"
tags:
  - django
  - rest
url: 1179.html
id: 1179
categories:
  - 后端
date: 2018-04-27 10:58:10
---

介绍
--

django-rest-framework是django的一个框架，内涵多个app，而authtoken是针对django-auth的一个应用，可以在增加一个django-token表的基础上实现用于基于token的登陆认证。而原始的django-auth认证只支持用户名-密码的方式。 注意：rest-framework-authtoken只支持一个token存储，相关文档请见：[TokenAuthentication](http://www.django-rest-framework.org/api-guide/authentication/#tokenauthentication)

> **Note:** If you use `TokenAuthentication` in production you must ensure that your API is only available over `https`.

安装及配置
-----

pip install djangorestframework 在settings.py中添加如下配置

INSTALLED_APPS = \[
    ......
    ......
    'rest_framework.authtoken',
    ......        
\]
REST_FRAMEWORK = {
    'DEFAULT\_AUTHENTICATION\_CLASSES': (
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.TokenAuthentication',
    )
}

范例
--

下面是一个很简单的例子，可能并不符合REST_FRAMEWORK 的最佳使用方案，具体使用请见上述地址的教程。

from django.http import HttpResponse
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework.authtoken.models import Token
from django.contrib.auth.models import User
from rest_framework import permissions
import json
from django.contrib.auth import authenticate, login

from django.views.decorators.csrf import csrf_exempt
@csrf_exempt
def user_login(request):
    username = request.POST.get('username', '')
    password = request.POST.get('password', '')
    user = authenticate(username=username, password=password)
    print(username,password)
    if user is not None and user.is_active:
            login(request, user)
            user\_token = Token.objects.get(user\_id=user.id)
            if user_token is not None:
                user_token.delete()
            token_test = Token.objects.create(user=user)
            return\_text={'token': token\_test.key}
            return HttpResponse(json.dumps(return\_text), content\_type="application/json")
    else:
        return HttpResponse(status=403)
class DoingByToken(APIView):
    permission_classes = (permissions.IsAuthenticated,)
    def get(self,request):
       return Response({'user':str(request.user)})

from django.conf.urls import url
from django.urls import path
from rest\_framework.urlpatterns import format\_suffix_patterns
from snippets import views


urlpatterns = \[
    path('test/',views.DoingByToken.as_view()),
    path('login/',views.user_login)
\]

注意：上面的登陆使用了csrf\_exempt，表示被装饰方法不进行跨站请求限制 上面并非最优的例子，比如在进行登陆时，我选择了每次登陆都删除旧的token后创建。若不需要这么做可以直接使用Token.objects.get\_or_create()，并且对于登陆的view并没有使用rest框架。下面官方给的例子可能更合适：

views.py
from rest_framework.authtoken.views import ObtainAuthToken
from rest_framework.authtoken.models import Token
from rest_framework.response import Response

class CustomAuthToken(ObtainAuthToken):

    def post(self, request, \*args, \*\*kwargs):
        serializer = self.serializer_class(data=request.data,
                                           context={'request': request})
        serializer.is\_valid(raise\_exception=True)
        user = serializer.validated_data\['user'\]
        token, created = Token.objects.get\_or\_create(user=user)
        return Response({
            'token': token.key,
            'user_id': user.pk,
            'email': user.email
        })
urls.py
urlpatterns += \[
    url(r'^api-token-auth/', CustomAuthToken.as_view())
\]

部分api
-----

from rest_framework.authtoken.models import Token
from django.contrib.auth.models import User
from rest_framework import permissions
token = Token.objects.create(user=...)# 创建一个用户的token


\# 下面的视图类使用APIView模板，只需要增加permissions.IsAuthenticated作为限制即可实现对token的认证
\# 需要在调用时在header中写入“Authorization”：“Token XXXXXXXXXXXXXXXXXXXXX”
class DoingByTokenView(views.APIView):
    permission_classes = (permissions.IsAuthenticated,)
    def get(self,request):
       # 对应的操作