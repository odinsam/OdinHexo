title: Flutter - 基于reflectable反射以及调用
author: OdinSam
tags:
  - Flutter
  - reflectable
categories:
  - Flutter
abbrlink: '4998'
date: 2022-11-28 16:49:00
---
>Flutter默认不允许反射。可以利用基于reflectable反射以及调用。

<!--more-->

1. 引入类库
```yaml
dependencies:
  reflectable: ^3.0.10
```
2. 反射帮助类
```dart 
import 'package:reflectable/reflectable.dart';
class Reflector extends Reflectable {
  const Reflector()
      : super(
      invokingCapability,
      declarationsCapability,
      typeRelationsCapability,
      libraryCapability); // Request the capability to invoke methods.
}
```

3. 反射注解类

```dart
import 'package:odindio/reflector.dart';
import 'package:reflectable/mirrors.dart';
const odinReflectable = Reflector();
class OdinReflect {
  static T reflectInstanceFromMap<T>(Map<String, dynamic> map,
      {methodName = "fromJson"}) {
    //反射T类型
    ClassMirror classMirror = odinReflectable.reflectType(T) as ClassMirror;
    // 调用工厂构造函数
    var instance = classMirror.newInstance(methodName, [map]) as T;
    return instance;
  }
}
```

4. model类

```dart
import 'package:json_annotation/json_annotation.dart';
import 'package:odindio/odin_reflect.dart';
part 'post_model.g.dart';
//反射注解
@odinReflectable
@JsonSerializable()
class PostModel {
  final int? code;
  final String? message;
  final List<Data>? data;

  PostModel({
    this.code,
    this.message,
    this.data,
  });

  factory PostModel.fromJson(Map<String, dynamic> json) =>
      _$PostModelFromJson(json);

  @override
  Map<String, dynamic> toJson() => _$PostModelToJson(this);

  @override
  fromJson(Map<String, dynamic> json) {
    // TODO: implement fromJson
    throw UnimplementedError();
  }
}

@JsonSerializable()
class Data {
  final int? id;
  final String? stuName;
  final int? age;

  Data({
    this.id,
    this.stuName,
    this.age,
  });

  factory Data.fromJson(Map<String, dynamic> json) =>
      _$DataFromJson(json);

  Map<String, dynamic> toJson() => _$DataToJson(this);
}
```

5. 具体使用:
```dart 
import 'odindio_test.reflectable.dart';
void main(){
  initializeReflectable();
}
```

```dart
T dioResultToModel<T>(dynamic data){
  var value = OdinReflect.reflectInstanceFromMap<T>(data);
  return value;
}
```
```cmd 终端执行 生成反射辅助类
flutter pub run build_runner clean && flutter pub run build_runner build --delete-conflicting-outputs 
```