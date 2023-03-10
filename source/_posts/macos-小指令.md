title: macos 小指令
author: OdinSam
tags:
  - macos
categories:
  - macos
abbrlink: ade6
date: 2022-10-18 15:12:00
---
macos 中的一些小指令

生成markdownde的目录树
find . -print | sed -e ‘s;[^/]*/;|;g;s;|; |;g’