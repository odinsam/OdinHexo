title: vscode无法使用nuget的小问题
author: odinsam
tags:
  - VsCode
  - ''
  - Nuget
  - ''
categories:
  - VsCode
  - ''
abbrlink: 80b6
date: 2021-05-29 05:29:00
---
> 在使用vscode的过程使用NuGet Package Manager插件安装Package包的时候会出现 <font color="red">"Versioning information could not be retrieved from the NuGet package repository. "</font> 的错误导致无法安装Package包，可以修改fetchPackageVersions.js解决问题。

<!-- more -->

问题：

> "Versioning information could not be retrieved from the NuGet package repository. Please try again later."

解决方式：

> 打开 /Users/用户名/.vscode/extensions/jmrog.vscode-nuget-package-manager-1.1.6/out/src/actions/add-methods/fetchPackageVersions.js 修改后的代码 加上 <font color=#FF0000 >.toLowerCase()</font>
	
   ```js
    ...node_fetch_1.default(`${versionsUrl}${selectedPackageName.toLowerCase()}/index.json`, utils_1.getFetchOptions(vscode.workspace.getConfiguration('http')))
   ```