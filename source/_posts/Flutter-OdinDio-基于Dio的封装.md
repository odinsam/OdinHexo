title: Flutter - OdinDio 基于Dio的封装
author: OdinSam
tags:
  - Dio
  - Flutter
categories:
  - Flutter
abbrlink: b092
date: 2022-11-28 16:52:00
---
>Dio是Flutter中较为流行的http操作类。 OdinDio基于Dio封装,完整代码可以在 [GitHub](https://github.com/odinsam/OdinPlugin/tree/feature/1.0.0/odindio) 。

<!--more-->

#### OdinDio
OdinDio单例模式，基于dio封装   
![plateform](https://img.shields.io/badge/Plateform-ios%7Candroid%7Cwindws%7Clinux-greenyellow) ![version](https://img.shields.io/badge/pub.dev-1.0.1-greenyellow)

```yaml
pubspec.yaml
  dependencies:
    json_annotation: ^4.4.0
    odindio: ^1.0.1
  dev_dependencies:
    json_serializable: ^6.2.0
    build_runner: any
```

```dart
import 'dart:convert';
import 'package:dio/dio.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:odindio/odin_dio_interceptor.dart';
import 'package:odindio/odindio.dart';
import 'package:odindio/odindio_platform_interface.dart';
import 'package:odindio/odindio_method_channel.dart';
import 'package:plugin_platform_interface/plugin_platform_interface.dart';
import 'models/post_model.dart';
import 'models/student_model.dart';
import 'models/ten_model.dart';
import 'models/test_model.dart';
import 'package:path/path.dart' as path;
import 'dart:io';
import 'odindio_test.reflectable.dart';

class MockOdindioPlatform
    with MockPlatformInterfaceMixin
    implements OdindioPlatform {
  @override
  Future<String?> getPlatformVersion() => Future.value('42');
}

/// 多请求
class MultiRequest {
  static Future<Response<dynamic>> req1<T>() async {
    OdinDio odindioPlugin = OdinDio();
    odindioPlugin.setBaseUrl("http://httpbin.org");
    return await odindioPlugin.get('/json');
  }

  static Future<Response<dynamic>> req2<T>() async {
    var odindioPlugin = OdinDio();
    odindioPlugin.setBaseUrl("http://tenapi.cn");
    return await odindioPlugin.get('/acg');
  }
}

void main() {
  initializeReflectable();
  final OdindioPlatform initialPlatform = OdindioPlatform.instance;

  test('$MethodChannelOdindio is the default instance', () {
    expect(initialPlatform, isInstanceOf<MethodChannelOdindio>());
  });
  
  /// get request return Model 
  test('getJson-1', () async {
    // getJson no param
    OdinDio odindioPlugin = OdinDio();
    odindioPlugin.setBaseUrl("http://httpbin.org");
    odindioPlugin.removeDioLog();
    //定义拦截器
    var odi1 = OdinDioInterceptor(onRequestHandler: (request, handler) {
      print("onRequestHandler1");
    }, onResponseHandler: (response, handler) {
      print("onResponseHandler1");
    }, onErrorHandler: (err, handler) {
      print("err1");
    });
    var odi2 = OdinDioInterceptor(onRequestHandler: (request, handler) {
      print("onRequestHandler2");
    }, onResponseHandler: (response, handler) {
      print("onResponseHandler2");
    }, onErrorHandler: (err, handler) {
      print("err2");
    });
    //添加拦截器
    odindioPlugin.interceptors().addAll([odi1, odi2]);
    //移除拦截器
    odindioPlugin.interceptors().remove(odi1);
    var model = await odindioPlugin.getModel<TestModel>('/json');
    print(json.encode(model));
    // expect(model.slideshow!.title, 'Sample Slide Show');
  });
	
  /// get request return Model List
  test('getJson-2', () async {
    // getJson no param
    OdinDio odindioPlugin = OdinDio();
    odindioPlugin.removeDioLog();
    odindioPlugin.addDioLog();
    odindioPlugin.setBaseUrl("http://192.168.1.145:8899");
    var models = await odindioPlugin.getModels<StudentModel>('/stus');
    print(json.encode(models));
    expect(models.length, greaterThan(0));
  });
	
  /// get request by queryParameters return Model List
  test('getJson-3', () async {
    // getJson with params
    var odinDioPlugin = OdinDio();
    odinDioPlugin.setBaseUrl("https://tenapi.cn");
    var model = await odinDioPlugin.getModel<TenModel>("/qqname",
        queryParameters: {"qq": "123456"},
        options: Options(responseType: ResponseType.json));
    print(json.encode(model));
    expect(model.name, '腾讯视频');
  });

  /// get request by Stream
  test('getStream', () async {
    // get stream
    var odinDioPlugin = OdinDio();
    odinDioPlugin.setBaseUrl("http://192.168.1.145:8899");
    var stream = await odinDioPlugin.getStream('/stream');
    var lst = <int>[];
    var bytes = await stream.first;
    lst = List.from(bytes.toList());
    print(utf8.decode(lst));
    expect(utf8.decode(lst).length, greaterThan(0));
  });

  /// get request return Bytes(List<int>)
  test('getBytes', () async {
    // get Bytes
    var odinDioPlugin = OdinDio();
    odinDioPlugin.setBaseUrl("http://192.168.1.145:8899");
    var lst = await odinDioPlugin.getBytes('/stream');
    print(utf8.decode(lst));
    expect(utf8.decode(lst).length, greaterThan(0));
  });

  /// post request return Model
  test('postStu', () async {
    // post with param and return Json
    var odinDioPlugin = OdinDio();
    odinDioPlugin.setBaseUrl("http://192.168.1.145:8899");
    odinDioPlugin.removeDioLog();
    var data = odinDioPlugin.dataToFormData({"name": "bakabaka"});
    var response5 = await odinDioPlugin.post('/postStu',
        data: data, options: Options(headers: {"customNewPost": "newPost"}));
    var model = odinDioPlugin.dioResultToModel<PostModel>(response5.data);
    print("model: ${json.encode(model)}");
    expect(model.code, 0);
  });

  /// multirequest
  test('multirequest', () async {
    var odinDioPlugin = OdinDio();
    var response6 = await odinDioPlugin
        .multiRequest([MultiRequest.req1(), MultiRequest.req2()]);
    var rep1 = response6[0];
    var rep1model = odinDioPlugin.dioResultToModel<TestModel>(rep1.data);
    print(json.encode(rep1model));
    expect(rep1model.slideshow!.author, "Yours Truly");
    var rep2 = response6[1];
    print(json.encode(rep2.data.length));
    expect(rep2.data!.length, greaterThan(0));
  });

  /// downloadFile
  test('downloadFile', () async {
    double currentProgress = 0.0;
    var odinDioPlugin = OdinDio();
    odinDioPlugin.setBaseUrl("http://192.168.1.145:8899");
    final cur = path.dirname(Platform.script.path).substring(1);
    var savePath = path.join(cur, 'download.txt');
    var response7 = await odinDioPlugin.downloadFile(
        '/static/output.txt', savePath, onReceiveProgress: (received, total) {
      if (total != -1) {
        ///当前下载的百分比例
        var downloadRatio = (received / total * 100).toStringAsFixed(0);
        currentProgress = received / total;
      }
    });
    var f = File(savePath);
    expect(f.readAsStringSync(), equals('I am a text file'));
    f.deleteSync(recursive: false);
  });

  /// upload file
  test('uploadFile', () async {
    var odinDioPlugin = OdinDio();
    final cur = path.dirname(Platform.script.path).substring(1);
    var uploadFile1 = path.join(cur, 'upload1.txt');
    var uploadFile2 = path.join(cur, 'upload2.txt');
    var uploadFile3 = path.join(cur, 'upload3.txt');
    var formData = odinDioPlugin.dataToFormData({
      'name': 'wendux',
      'age': 25,
      'file':
          await MultipartFile.fromFile(uploadFile1, filename: 'upload1.txt'),
      'files': [
        await MultipartFile.fromFile(uploadFile2, filename: 'upload2.txt'),
        await MultipartFile.fromFile(uploadFile3, filename: 'upload3.txt'),
      ]
    });
    odinDioPlugin.setBaseUrl("http://192.168.1.145:8899");
    var response6 = await odinDioPlugin.post('/upload', data: formData);
    print(response6.data);
  });

  /// post stream data
  test('post stream data', () async {
    var odinDioPlugin = OdinDio();
    var formData = {'name': 'wendux', 'age': 25};
    var lst = utf8.encode(formData.toString());
    odinDioPlugin.setBaseUrl("http://192.168.1.145:8899");
    var response6 = await odinDioPlugin.postDataByStream('/stu',
        data: odinDioPlugin.listIntToStreamFormData(lst),
        options: Options(headers: {
          Headers.contentLengthHeader: lst.length, // 设置content-length
        }));
    print(response6.data);
  });

  /// 完成和终止请求/响应
  test('resolve', () async {
    // getJson no param
    var odinDioPlugin = OdinDio();
    odinDioPlugin.setBaseUrl("http://httpbin.org");
    //定义拦截器
    var odi1 = OdinDioInterceptor(onRequestHandler: (request, handler) {
      print("onRequestHandler1");
      return handler
          .resolve(Response(requestOptions: request, data: 'fake data'));
    }, onResponseHandler: (response, handler) {
      print("onResponseHandler1");
    }, onErrorHandler: (err, handler) {
      print("err1");
    });
    odinDioPlugin.interceptors().add(odi1);
    try {
      var model = await odinDioPlugin.get('/json');
    } on DioError catch (e) {
      if (e.response != null) {
        print(e.response!.data);
        print(e.response!.headers);
        print(e.response!.requestOptions);
      } else {
        // Something happened in setting up or sending the request that triggered an Error
        print(e.requestOptions);
        print(e.message);
      }
    }
    // expect(model.slideshow!.title, 'Sample Slide Show');
  });
}
```

pub.dev发布地址：[odinSam-Flutter - OdinDio封装](https://pub.dev/packages/odindio)
