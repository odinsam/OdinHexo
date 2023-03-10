title: vscode控制台中文乱码
author: odinsam
tags:
  - 中文乱码
  - VsCode
  - ''
categories:
  - VsCode
  - ''
abbrlink: cda
date: 2021-06-06 11:27:00
---
> vscode是现在较为流行的一款开发工具，他可以按照用户对应需要的语言插件进行自定义安装和配置，在使用vscode的过程中发现终端输出控制台输出稳重会出现中文乱码的情况，网上搜索很多的解决方案都是修改系统的GBK，但是发现在修改了GBK以后可能会造成其他程序出现中文乱码以及其他的一些问题，这里我们使用修改PowerShell的OutputEncoding来解决这个问题。仅win10系统测试有效。

<!-- more -->

正确方法：

> 1.打开 Windows PowerShell (管理员)，执行命令：

    Set-ExecutionPolicy Unrestricted

> 2.新建文档 profile.ps1

> 3.用记事本编辑，粘贴以下代码并保存：

    $OutputEncoding = [console]::InputEncoding = [console]::OutputEncoding = New-Object System.Text.UTF8Encoding

> 4.把 profile.ps1 保存到以下路径：

    C:\Windows\System32\WindowsPowerShell\v1.0

> 5.完成。

> 6.检测是否成功

    打开 PowerShell，执行：chcp 结果为 Active code page: 65001，说明设置成功了