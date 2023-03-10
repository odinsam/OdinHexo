title: 使用 BackgroundService 类在微服务中实现后台任务
author: OdinSam
tags:
  - .Net Core
  - 微服务
  - BackgroundService
categories:
  - .Net Core
abbrlink: '2893'
date: 2021-06-14 17:34:00
---
> 任何应用程序中都可能需要使用后台任务和计划作业，无论应用程序是否遵循微服务体系结构模式均是如此。 使用微服务体系结构的区别在于，你可以在一个单独的用于托管的进程/容器中实现后台任务。一般在 .NET 中，我们将这些类型的任务称为托管服务，因为它们是托管在主机/应用程序/微服务中的服务/逻辑。 请注意，在这种情况下，托管服务仅表示具有后台任务逻辑的类。

<!--more-->

#### 1. IHostedService介绍

自 .NET Core 2.0 开始，该框架提供名为 IHostedService 的新接口，有助于轻松实现托管服务。 基本理念是，可以注册多个后台任务（托管服务），在 Web 主机或主机运行时在后台运行具体介绍如下图：
   
   ![IHostedService介绍](https://docs.microsoft.com/zh-cn/dotnet/architecture/microservices/multi-container-microservice-net-applications/media/background-tasks-with-ihostedservice/ihosted-service-webhost-vs-host.png) 
   
但是，由于大多数后台任务在取消令牌管理和其他典型操作方面都有类似的需求，因此有一个非常方便且可以从中进行派生的抽象基类，名为 BackgroundService（自 .NET Core 2.1 起提供），该类提供设置后台任务所需的主要工作。从抽象基类派生时，只需在自定义的托管服务类中实现 ExecuteAsync() 方法，结合 [利用Canal集合RabbitMQ实现数据和缓存同步](https://www.odinsam.com/articles/a3b9.html) 这篇文章，就可以搭建Canal + RabbitMQ + CacheManager 的基本架构，从而实现由 Canal 监控和发现 mysql 数据库的增量信息并推送到 RabbitMQ ，而我们使用BackgroundSerivce 搭建的后台托管服务消费 RabbitMQ 信息修改 Redis 中的缓存数据，而 CacheManager 设定 Redis 缓存为缓存挡板，故而内存中的二级缓存也会得到对应修改。

#### 2. 具体实现

这里是以 ErrorCode 错误码为例，实现一系列操作。首先是 实现 BackgroundService 的子类 OdinBackgroundService ：
    
```csharp
public class OdinBackgroundService : BackgroundService
{
    private readonly ProjectExtendsOptions apiOptions;
    private readonly ReceiveRabbitMQHelper receiveRabbitMQHelper;
    private int executionCount = 0;
    private Timer _timer;
    public OdinBackgroundService()
    {
        this.apiOptions = OdinInjectHelper.GetService<IOptionsSnapshot<ProjectExtendsOptions>>().Value;
        this.receiveRabbitMQHelper = new ReceiveRabbitMQHelper();
    }

    private void DoWork(object state)
    {
        receiveRabbitMQHelper.ReceiveMQ(apiOptions);
    }

    public override Task StartAsync(CancellationToken cancellationToken)
    {
        return ExecuteAsync(cancellationToken);
    }
    protected override Task ExecuteAsync(CancellationToken stoppingToken)
    {
        Log.Information($"Service:【 Run 】\tTime:【{DateTime.Now.ToString("yyyy-dd-MM hh:mm:ss")} 】");
        _timer = new Timer(DoWork, null, TimeSpan.Zero, TimeSpan.FromMilliseconds(300));
        return Task.CompletedTask;
    }

    public override Task StopAsync(CancellationToken cancellationToken)
    {
        Log.Information($"Service:【 Stop 】\tTime:【 {DateTime.Now.ToString("yyyy-dd-MM hh:mm:ss")} 】");
        _timer?.Change(Timeout.Infinite, 0);
        return base.StopAsync(cancellationToken);
    }

    public override void Dispose()
    {
        _timer?.Dispose();
    }
}
```

这里采用轮询机制，每300ms消费一次 RabbitMQ 的信息，具体的消费代码 ReceiveRabbitMQHelper 如下:
    
```csharp
public class ReceiveRabbitMQHelper
{
    private readonly IRabbitMQReceiveServer rabbitMQReceiveServer;
    private readonly IOdinCanalHelper canalHelper;
    private readonly IOdinCacheManager cacheManager;

    public ReceiveRabbitMQHelper()
    {
        this.rabbitMQReceiveServer = OdinInjectHelper.GetService<IRabbitMQReceiveServer>();
        this.canalHelper = OdinInjectHelper.GetService<IOdinCanalHelper>();
        this.cacheManager = OdinInjectHelper.GetService<IOdinCacheManager>();
    }

    public void ReceiveMQ(ProjectExtendsOptions apiOptions)
    {
        rabbitMQReceiveServer.ReceiveJsonMessage(
            apiOptions.RabbitMQ,
            new RabbitMQReceivedModel
            {
                ExchangeName = "canal-exchange",
                QueueName = "canal-queues",
                AutoAck = false
            },
            (BasicGetResult result, IModel channel) =>
            {
                var msg = RabbitMQReceiveHandler.ReceiveJsonMessageHandler(result, channel);
                if (!string.IsNullOrEmpty(msg))
                {
                    System.Console.WriteLine($"Canal-WorkService:【 Run 】\tTime:【 {DateTime.Now.ToString("yyyy-dd-MM hh:mm:ss")} 】");
                    System.Console.WriteLine(msg);
                    // 这里用来处理获取到的 RabbitMQ 的增量信息
                    ErrorCodeHelper.ErrorCodeCanalHandler(canalHelper, cacheManager, msg);
                    System.Console.WriteLine("\r\n");
                }
            }
        );
    }
}
```

ErrorCodeCanalHandler用来处理获取到的 RabbitMQ 的增量信息，具体封装如下：

```csharp
public class ErrorCodeHelper
{
    public static void ErrorCodeCanalHandler(IOdinCanalHelper canalHelper, IOdinCacheManager cacheManager, string canalString)
    {
        var obj = canalHelper.GetCanalInfo(canalString);
        var type = obj.type;
        switch (type.ToLower())
        {
            case "insert":
                {
                    var model = ConvertCanalDataToErrorCodeModel(obj);
                    var flag = cacheManager.Add(model.ErrorCode, model);
                    if (flag)
                        System.Console.WriteLine("cacheManager add success");
                    else
                        System.Console.WriteLine("cacheManager add fail");
                }
                break;
            case "update":
                {
                    var model = ConvertCanalDataToErrorCodeModel(obj);
                    cacheManager.Cover<ErrorCode_Model>(model.ErrorCode, model);
                    System.Console.WriteLine("cacheManager Cover success");
                }
                break;
            case "delete":
                {
                    var errorCode = GetErrorCode(obj);
                    bool flag = cacheManager.Delete(errorCode);
                    if (flag)
                        System.Console.WriteLine("cacheManager delete success");
                    else
                        System.Console.WriteLine("cacheManager delete fail");
                }
                break;
            default:
                break;
        }
    }
    private static string GetErrorCode(OdinCanalModel obj)
    {
        var errorCode = obj.data[0].GetValue("ErrorCode").ToString();
        return errorCode;
    }
    private static ErrorCode_Model ConvertCanalDataToErrorCodeModel(OdinCanalModel obj)
    {
        var errorCode = obj.data[0].GetValue("ErrorCode").ToString();
        var codeShowMessage = obj.data[0].GetValue("CodeShowMessage").ToString();
        var codeErrorMessage = obj.data[0].GetValue("CodeErrorMessage").ToString();
        ErrorCode_Model model = new ErrorCode_Model()
        {
            ErrorCode = errorCode,
            ErrorMessage = codeErrorMessage,
            ShowMessage = codeErrorMessage,
        };
        return model;
    }
}
```

这样基本的搭建就完成了，完整代码可以在 [GitHub](https://github.com/odinsam/OdinMA) 。