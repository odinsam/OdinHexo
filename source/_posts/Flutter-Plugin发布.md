title: Flutter - Plugin发布
author: OdinSam
tags:
  - Plugin
  - Flutter
categories:
  - Flutter
abbrlink: c804
date: 2022-12-01 16:56:00
---
>Flutter - Plugin发布

<!--more-->

#### Plugin发布

1. 执行 flutter pub publish --dry-run 检查是否具备发布条件

遇到的问题

It‘s strongly recommended to include a “homepage“ or “repository“ field

解决方案：

在 pubspec.yaml 中配置 主页 homepage 地址 :
homepage: https://github.com/author/gitname.git
curl google.com 检测终端代理是否成功连接google服务器
export http_proxy=http://127.0.0.1:10818
export https_proxy=http://127.0.0.1:10818
仅能保证当前终端一次性连接成功

```text vi ~/.zshrc
# proxy
proxy () {
  export http_proxy="http://127.0.0.1:10818"
  export https_proxy=$http_proxy
  echo "HTTP Proxy on"
}

# noproxy
noproxy () {
  unset http_proxy
  unset https_proxy
  echo "HTTP Proxy off"
}
```
source ~/.zshrc

2. 终端命令
proxy 开启代理
noproxy 关闭代理

3. 发布插件
```cmd 终端执行
flutter packages pub publish --server=https://pub.dartlang.org 
```
