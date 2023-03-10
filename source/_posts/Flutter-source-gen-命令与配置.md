title: Flutter - source-gen 命令与配置
author: OdinSam
tags:
  - source-gen
  - Flutter
categories:
  - Flutter
abbrlink: a7f
date: 2022-11-28 16:46:00
---
>Flutter 通过 source-gen 生成对应的model类

<!--more-->

#### 1. 命令

1. 引入 part ‘xxx.g.dart’; 生成对应 xxx.g.dart 文件

```cmd 终端执行
flutter pub run build_runner clean && flutter pub run build_runner build --delete-conflicting-outputs
```

2. 生成配置
```yaml 根目录下新建文件 build.yaml
targets:
  $default:
    builders:
      reflectable:
        generate_for:
          - test/**.dart
        options:
          formatted: true
```