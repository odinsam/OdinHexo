title: 利用BackgroundService封装后台服务
author: OdinSam
tags:
  - .Net Core
  - BackgroundService
  - 后台服务
  - 微服务
categories:
  - .Net Core
abbrlink: c075
date: 2021-07-22 13:20:00
---
>在之前的文章 [使用 BackgroundService 类在微服务中实现后台任务](/articles/2893.html) 中有介绍到如何利用 BackgroundService 来实现后台服务，这里我们依旧利用 BackgroundService 来进行类似 hangfire 的封装。

<!--more-->

#### OdinPlugs.OdinHostedService 使用方法

##### 1 后台任务 - 普通任务，立即执行，只执行一次

```csharp
services.AddOdinBgServiceNomalJob(opt =>
{
    opt.ActionJob = () =>
    {
#if DEBUG
        Log.Information($"Service:【 BgService - Nomal - Job - Running 】\tTime:【 {DateTime.Now.ToString("yyyy-dd-MM hh:mm:ss")} 】");
#endif
    };
});
```

##### 2 后台任务 - 延迟调用，只执行一次

```csharp
services.AddOdinBgServiceScheduleJob(opt =>
{
    opt.DueTime = 5000;
    opt.ActionJob = () =>
    {
#if DEBUG
        Log.Information($"Service:【 BgService - ScheduleJob - Running 】\tTime:【 {DateTime.Now.ToString("yyyy-dd-MM hh:mm:ss")} 】");
#endif
    };
});
```

##### 3 后台任务 - 循环任务执行：重复执行的任务，使用常见的时间循环模式

```csharp
services.AddOdinBgServiceScheduleJob(opt =>
{
    opt.DueTime = 5000;
    opt.ActionJob = () =>
    {
#if DEBUG
        Log.Information($"Service:【 BgService - ScheduleJob - Running 】\tTime:【 {DateTime.Now.ToString("yyyy-dd-MM hh:mm:ss")} 】");
#endif
    };
});
```

##### 4 后台任务 - 循环任务执行：重复执行的任务(任务执行完后继续自动执行)

```csharp
services.AddOdinBgServiceLoopJob(opt =>
{
    opt.ActionJob = () =>
    {
#if DEBUG
        Log.Information($"Service:【 BgService - LoopJob - Running 】\tTime:【 {DateTime.Now.ToString("yyyy-dd-MM hh:mm:ss")} 】");
#endif
        Thread.Sleep(1000);
    };
});
```

##### 5 后台任务 - 自定义任务

```csharp
services.AddOdinBgServiceJob(opt =>
{
    Timer timer = null;
    void worker(object state)
    {
#if DEBUG
        Log.Information($"Service:【 BgService - Running 】\tTime:【 {DateTime.Now.ToString("yyyy-dd-MM hh:mm:ss")} 】");
#endif
    }
    opt.StartAsyncAction = () =>
    {
        timer = new Timer(worker, null, 0, 2000);
    };
    opt.ExecuteAsyncAction = () =>
    {

    };
    opt.StopAsyncAction = () =>
    {
        timer?.Change(Timeout.Infinite, 0);
    };
    opt.DisposeAction = () =>
    {
        timer?.Dispose();
    };
});
```

##### 6 后台任务 - 多任务执行

```csharp
services
    .AddOdinBgServiceJob(opt =>
    {
        Timer timer = null;
        void worker(object state)
        {
#if DEBUG
            Log.Information($"Service:【 BgService - Running 】\tTime:【 {DateTime.Now.ToString("yyyy-dd-MM hh:mm:ss")} 】");
#endif
        }
        opt.StartAsyncAction = () =>
        {
            timer = new Timer(worker, null, 0, 2000);
        };
        opt.ExecuteAsyncAction = () =>
        {

        };
        opt.StopAsyncAction = () =>
        {
            timer?.Change(Timeout.Infinite, 0);
        };
        opt.DisposeAction = () =>
        {
            timer?.Dispose();
        };
    })
    .AddOdinBgServiceLoopJob(opt =>
    {
        opt.ActionJob = () =>
        {
            // new ReceiveRabbitMQHelper().ReceiveMQ(_Options);
#if DEBUG
            Log.Information($"Service:【 BgService - LoopJob - Running 】\tTime:【 {DateTime.Now.ToString("yyyy-dd-MM hh:mm:ss")} 】");
#endif
            Thread.Sleep(1000);
        };
    })
    .AddOdinBgServiceRecurringJob(opt =>
    {
        opt.Period = TimeSpan.FromSeconds(1);
        opt.ActionJob = () =>
        {
            // new ReceiveRabbitMQHelper().ReceiveMQ(_Options);
#if DEBUG
            Log.Information($"Service:【 BgService - RecurringJob - Running 】\tTime:【 {DateTime.Now.ToString("yyyy-dd-MM hh:mm:ss")} 】");
#endif
        };
    })
    .AddOdinBgServiceNomalJob(opt =>
    {
        opt.ActionJob = () =>
        {
#if DEBUG
            Log.Information($"Service:【 BgService - Nomal- Job - Running 】\tTime:【 {DateTime.Now.ToString("yyyy-dd-MM hh:mm:ss")} 】");
#endif
        };
    })
    .AddOdinBgServiceScheduleJob(opt =>
    {
        opt.DueTime = 5000;
        opt.ActionJob = () =>
        {
#if DEBUG
            Log.Information($"Service:【 BgService - ScheduleJob - Running 】\tTime:【 {DateTime.Now.ToString("yyyy-dd-MM hh:mm:ss")} 】");
#endif
        };
    });
```

具体的代码在 [GitHub](https://github.com/odinsam/OdinPlugs.OdinHostedService) [![nuget](https://img.shields.io/nuget/v/OdinPlugs.OdinHostedService)](https://www.nuget.org/packages/OdinPlugs.OdinHostedService)