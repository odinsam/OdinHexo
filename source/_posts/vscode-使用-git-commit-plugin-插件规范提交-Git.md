title: vscode 使用 git-commit-plugin 插件规范提交 Git
author: OdinSam
tags:
  - VsCode
  - Git
  - 插件
categories:
  - Git
abbrlink: 60dd
date: 2021-06-30 12:38:00
---
>在团队协作开发时，每个人提交代码时都会写 commit message。每个人都有自己的书写风格，翻看我们组的git log, 可以说是五花八门，十分不利于阅读和维护。本文将介绍 Git 提交的规范以及如何利用 git-commit-plugin 插件快速提交规范的commit。

<!--more-->

一般来说，大厂都有一套的自己的提交规范，尤其是在一些大型开源项目中，commit message 都是十分一致的。因此，我们需要制定统一标准，促使团队形成一致的代码提交风格，更好的提高工作效率，成为一名有追求的工程师。其中 AngularJS 在 github 上 的提交记录被业内许多人认可，逐渐被大家引用。

#### Commit message 的格式

每次提交，Commit message 都包括三个部分：header，body 和 footer。

```text
type(scope):空格subject
换行
[body]
换行
[footer]
```

##### 1. type 类型

type 是 commit 的类别，只允许如下几种标识：

```text
# 主要type
feat:     增加新功能
fix:      修复bug

# 特殊type
docs:    只改动了文档相关的内容
style:    不影响代码含义的改动，例如去掉空格、改变缩进、增删分号
build:    构造工具的或者外部依赖的改动，例如webpack，npm
refactor:  代码重构时使用
revert:   执行git revert打印的message

# 暂不使用type
test:     添加测试或者修改现有测试
perf:     提高性能的改动
ci:       与CI（持续集成服务）有关的改动
chore:    不修改src或者test的其余修改，例如构建过程或辅助工具的变动
```

##### 2. scope

scope也为必填项，用于描述改动的范围，格式为项目名/模块名，例如：xxxServices 。如果一次commit修改多个模块，建议拆分成多次commit，以便更好追踪和维护。

##### 3. subject

commit 目的的简短描述，不超过50个字符。结尾一般是 #33224 这样的超链接。链接到本次提交的 url 但不强制

##### 4. body

对本次 commit 的详细描述

##### 5. footer

Footer 部分只用于以下两种情况：
	
    5.1. 不兼容变动: 如果当前代码与上一个版本不兼容，则 Footer 部分以BREAKING CHANGE开头，后面是对变动的描述、以及变动理由和迁移方法。例如下边这样：
    
    ```text
    BREAKING CHANGE: isolate scope bindings definition has changed.

    To migrate the code follow the example below:

    Before:

    scope: {
      myAttr: 'attribute',
    }

    After:

    scope: {
      myAttr: '@',
    }

    The removed `inject` wasn't generaly useful for directives so there should be no code using it.
    ```

	5.2. 关闭 Issue: 如果当前 commit 针对某个issue，那么可以在 Footer 部分关闭这个 issue 。
    
完整的提交demo如下：

```text
fix(dev-infra): remove bots from special thanks section

With this change we remove known used bots from special thanks section in the changelog.

PR Close #42697
```

那么每次都这样编写提交的内容，还要注意对应的格式。我们有没有简单方便的办法呢，那就是使用对应工具的插件。git-commit-plugin 插件可以帮助我们快速的边写提交的信息，但是插件本身并不支持格式化。所以，我在该插件的基础上做了二次开发，具体使用如下：

1. 下载安装对应的插件：[git-commit-plugin-1.0.6.vsix](https://github.com/odinsam/git-commit-plugin/releases/download/1.0.6/git-commit-plugin-1.0.6.vsix)

2. 在插件的扩展配置中，进行对应的提交模板配置。可以配置多个，这是因为我公司和我自己的提交格式都不一样。具体可以参见项目的 [readme.md](https://github.com/odinsam/git-commit-plugin/blob/master/README.md)

```text
"GitCommitPlugin.Templates": [
    {
        "templateName": "Angular",
        "templateContent": "<icon><space><type>(<scope>):<space><subject><enter><body><enter><footer>"
    },
    {
        "templateName": "git-cz",
        "templateContent": "<type>(<scope>):<space><icon><space><subject><enter><body><enter><footer>"
    }
]
```

3. 配置插件是否启用图标

```text
"GitCommitPlugin.ShowEmoji": true
```
























