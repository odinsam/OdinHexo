title: vscode控制台中文乱码
author: odinsam
tags:
  - vscode
  - 中文乱码
categories:
  - vscode
date: 2021-06-06 11:25:00
---
改 GBK 还是有其他的一些问题

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