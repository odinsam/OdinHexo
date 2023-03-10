title: 基于git-commit-plugin插件的扩展
author: OdinSam
tags:
  - Git
  - VsCode
  - 插件
  - ''
  - ''
categories:
  - VsCode插件开发
abbrlink: 9b56
date: 2021-06-29 23:57:00
---
> Git 每次提交代码，都要写 Commit message（提交说明），否则就不允许提交。但是，一般来说，commit message 应该清晰明了，说明本次提交的目的。因为我使用vscode开发工具，所以就找到了一款叫做 git-commit-plugin 的插件。

<!--more-->

目前，社区有多种 Commit message 的写法规范。本文介绍Angular 规范是目前使用最广的写法，比较合理和系统化，并且有配套的工具。前前端框架Angular.js采用的就是该规范。

> Commit message 的格式

	每次提交，Commit message 都包括三个部分：header，body 和 footer。

```text
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```
但是每个公司或者个人的提交格式可能都不一致，而 git-commit-plugin 插件没有模板配置功能。所以在作者的基础上新增了一个提交模板配置功能。

> 配置格式

```json
{
    "templateName": "Angular",
    "templateContent": "<icon><space><type>(<scope>):<space><subject><enter><body><enter><footer>"
},
{
    "templateName": "git-cz",
    "templateContent": "<type>(<scope>):<space><icon><space><subject><enter><body><enter><footer>"
}
```

其中 &lt;space&gt; 代表空格,&lt;enter&gt; 代表换行,并且可以调整icon的位置。

