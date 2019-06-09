---
title: "Git Commitizen"
tags:
  - 其他
categories:
  - 其他
date: 2019-05-04 23:46:08
---
转载，非原创

## Git Commit内容格式规范

引自：<http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html>

```
<type>(<scope>): <subject>
// 空一行
<body>
// 空一行
<footer>
```

其中，Header 是必需的，Body 和 Footer 可以省略。``

不管是哪一个部分，任何一行都不得超过72个字符（或100个字符）。这是为了避免自动换行影响美观。` `

- `type`用于说明 `commit` 的类别，只允许使用下面7个标识

```
feat：新功能（feature）
fix：修补bug
docs：文档（documentation）
style： 格式（不影响代码运行的变动）
refactor：重构（即不是新增功能，也不是修改bug的代码变动）
test：增加测试
chore：构建过程或辅助工具的变动
```

- `scope` 用来说明本次Commit影响的范围，即简要说明修改会涉及的部分,比如数据层、控制层、视图层等,

- ```
  subject
  ```

  是 commit 目的的简短描述，不超过50个字符。

  > - 以动词开头，使用第一人称现在时，比如`change`，而不是`changed`或`changes`
  > - 第一个字母小写
  > - 结尾不加句号（`.`）

### Body 是对本次 commit 的详细描述，可以分成多行

### Footer 部分只用于两种情况

- 不兼容变动

> 如果当前代码与上一个版本不兼容，则 `Footer` 部分以`BREAKING CHANGE`开头，后面是对变动的描述、以及变动理由和迁移方法

- 关闭 Issue

> 如果当前 commit 针对某个issue，那么可以在 Footer 部分关闭这个 issue (可依次关闭过个issue`Closes #123, #245, #992`)

Revert

还有一种特殊情况，如果当前 commit 用于撤销以前的 commit，则必须以revert:开头，后面跟着被撤销 Commit 的 Header

```
revert: type(scope):  some comment
This reverts commit bfe307ce57d87677c6c473c228e6c2ed8b81dcec.
```

Body部分的格式是固定的，必须写成`This reverts commit &lt;hash>.`，其中的hash是被撤销 commit 的 `HSHA` 标识符。
 如果当前 commit 与被撤销的 commit，在同一个发布（release）里面，那么它们都不会出现在 Change log 里面。如果两者在不同的发布，那么当前 commit，会出现在 Change log 的Reverts小标题下面

## Commitizen插件

[Commitizen](https://github.com/commitizen/cz-cli)是一个撰写合格 Commit message 的工具。

## Interllij Idea插件

idea自带的git version功能不支持上面的风格，需要安装插件：[git commit template](https://plugins.jetbrains.com/plugin/9861-git-commit-template)

下载后在setting-plugins-install from disk找到文件安装

安装后，在启动没有git的项目时会提示：The directory <Project> is registered as a Git root, but no Git repositories were found there.

新版本可以在：setting-plugins-marketplace中搜索commit找到gitcommit template

使用：在commit change界面的commit message编辑框右上角多一个新的按钮，点击后打开a