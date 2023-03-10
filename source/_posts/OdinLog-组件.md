title: OdinLog 组件
author: OdinSam
tags:
  - .Net Core
  - Log
categories:
  - .Net Core
abbrlink: 86b2
date: 2022-07-03 13:39:00
---
>自定义简单的一个日志组件。可以存储本地文件，也可以存储到数据库(目前仅支持 mysql 和 sqlServer 数据库)。后期扩展和ELK结合。具体源代码在github可以查看。

<!--more-->

#### 1. 简介

组件可以生成对应日志文件( bin 目录下)，可用于winform、webapi项目。如果存储在本地，以日志级别 Info、Debug、Error生成文件夹，内部以 yyyy-MM-dd 格式生成文件夹。日志文件以数字标识，如果单个日志文件大小超过5M则另生成日志文件。

#### 2. 组件使用

可以使用依赖注入，也可以在配置Config后直接使用

```csharp 依赖注入
builder.Services.AddOdinSingletonOdinLogs(opt=>
  opt.Config=new LogConfig {
      LogSaveType=new EnumLogSaveType[]{EnumLogSaveType.All},
      ConnectionString = "server=xxxx;Database=xxxx;Uid=xxx;Pwd=xxx;"});
```

```csharp 直接配置
OdinLog.Core.OdinLogs = new OdinLogs(new LogConfig {
       LogSaveType=new EnumLogSaveType[]{EnumLogSaveType.All},
       ConnectionString = "server=xxxx;Database=xxxx;Uid=xxx;Pwd=xxx;"});)
```

```csharp 调用
OdinLogs.Info(new LogInfo(){LogContent = "log info test",LogMark="log mark", });
     OdinLogs.Error(
         new ExceptionLog(){
             LogContent = "log exception test",
             LogMark="log mark",
             LogException = new Exception("custom exceptioni")});
```

#### 3. 存储表结构

[https://github.com/odinsam/OdinLog/tree/master/OdinLog/doc/DDL/scripts](https://github.com/odinsam/OdinLog/tree/master/OdinLog/doc/DDL/scripts)

#### 4. 文件夹结构、文件内容格式如下：

```text
-- logs
	--Info
      -- 2022-06-01
			0.txt
			1.txt
	--Debug
      -- 2022-06-01
			0.txt
	--Error
      -- 2022-06-01
			0.txt
```

Info、Debug 文件内容格式如下:

```text
【 LogId 】: 766c769d349d494daf82fca503666d5d 
【 Log Level 】: Info 
【 LogTime 】: 2022-07-03 17:59:44 
【 LogContent 】:
log info test
****************************************************************************************************
```

Error 文件内容格式如下:

```text
【 LogId 】: 57c2978db92a44959e613f7a1e733d8b 
【 Log Level 】: Error 
【 LogTime 】: 2022-07-03 18:10:51 
【 Exception Message 】: custom exceptioni
【 Exception Info 】: 
{
    "ClassName": "System.Exception",
    "Message": "custom exceptioni",
    "Data": null,
    "InnerException": null,
    "HelpURL": null,
    "StackTraceString": null,
    "RemoteStackTraceString": null,
    "RemoteStackIndex": 0,
    "ExceptionMethod": null,
    "HResult": -2146233088,
    "Source": null,
    "WatsonBuckets": null
}
****************************************************************************************************
```


具体的代码在 [GitHub](https://github.com/odinsam/OdinLog) [![nuget](https://img.shields.io/nuget/v/OdinLog)](https://www.nuget.org/packages/OdinLog)