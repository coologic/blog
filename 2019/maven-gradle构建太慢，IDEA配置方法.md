---
title: "maven/gradle构建太慢，IDEA配置方法"
tags:
  - Java
categories:
  - Java
date: 2019-05-03 23:46:08
---

镜像地址改为：http://maven.aliyun.com/nexus/content/groups/public/

## idea中maven

Ctrl+alt+s-bulid tool-maven-use setting file这里选择override，建立对应位置的文件，内容如下：

```
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
    <mirrors>
        <mirror>
            <id>alimaven</id>
            <name>aliyun maven</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
            <mirrorOf>central</mirrorOf>
        </mirror>

        <mirror>
            <id>uk</id>
            <mirrorOf>central</mirrorOf>
            <name>Human Readable Name for this Mirror.</name>
            <url>http://uk.maven.org/maven2/</url>
        </mirror>

        <mirror>
            <id>CN</id>
            <name>OSChina Central</name>
            <url>http://maven.oschina.net/content/groups/public/</url>
            <mirrorOf>central</mirrorOf>
        </mirror>

        <mirror>
            <id>nexus</id>
            <name>internal nexus repository</name>
            <url>http://repo.maven.apache.org/maven2</url>
            <mirrorOf>central</mirrorOf>
        </mirror>

    </mirrors>
</settings>
```

注意上面只在maven项目生效

## gradle

maven修改了对gradle无效，需要在项目的build.gradle文件中修改：

```
repositories {
   maven{ url 'http://maven.aliyun.com/nexus/content/groups/public/'}
}
```

修改所有项目：

在c:/user/XXX/.gradle目录下，新建一个：init.gradle文件：

```
allprojects { repositories { def REPOSITORY_URL = 'http://maven.aliyun.com/nexus/content/groups/public/'
all { ArtifactRepository repo -> if (repo instanceof MavenArtifactRepository && repo.url != null) { def url = repo.url.toString() if (url.startsWith('https://repo1.maven.org/maven2') || url.startsWith('https://jcenter.bintray.com/')) { project.logger.lifecycle "Repository ${repo.url} replaced by $REPOSITORY_URL."
remove repo } } } maven { url REPOSITORY_URL } } }
```