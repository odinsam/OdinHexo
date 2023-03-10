title: .Net Core使用SqlSugar和Cap
author: OdinSam
tags:
  - SqlSugar
  - Cap
categories:
  - .Net Core
abbrlink: ed30
date: 2021-06-16 23:21:00
---
> 近期的一次面试当中聊起了 .Net Core 中的 EF 框架和分布式的事务，因为在项目中也遇到过并发导致 EF Core 性能和报错的各种问题，所以就和面试官吐槽了一下，面试官说他们公司用的 SqlSugar 。这个开源组件库我以前知道的，但很久没有关注。回家看了一下发现这个东西已经非常完善，特此将项目中的 EF Core 变更为了 SqlSugar并且加入了 Cap，顺便记录一下遇到的问题。

<!--more-->

#### 1. SqlSugar简介
	

&ensp;&ensp; SqlSugar是一款 老牌 .NET 开源ORM框架，由果糖大数据科技团队维护和更新 ，Github star数仅次于EF 和 Dapper。优点： 简单易用、功能齐全、高性能、轻量级、服务齐全、有专业技术支持一天18小时服务。支持数据库有 MySql、SqlServer、Sqlite、Oracle 、 postgresql、达梦、人大金仓。我的项目习惯了Code First，第一次使用也不知道是否正确。

**Startup.cs** - SqlSugar 注入代码
```csharp
services.AddTransient<OdinProjectSugarDbContext>();
OdinInjectHelper.ServiceProvider = services.BuildServiceProvider();
var sugarEntity = OdinInjectHelper.GetService<OdinProjectSugarDbContext>();
#region 初始化数据库
//修改cnf.config Host配置的链接字符串  enable修改为true，即可自动化初识数据库
if (_Options.DbEntity.InitDb)
{
	sugarEntity.CreateTable("db_odinCore", false);
}
```

**OdinProjectSugarDbContext.cs** - DbContext定义以及初始化数据库
```csharp
public class OdinProjectSugarDbContext
{
    SqlSugarClient db;
    public OdinProjectSugarDbContext()
    {
        db = DbScoped.Sugar;
    }
    public void CreateTable(string databaseName, bool Backup = false)
    {

        var flag = false;
        try
        {	
            // 判断数据库是否存在，如果不存在这里会有异常
            flag = db.DbMaintenance.GetDataBaseList(db).Contains(databaseName);
        }
        catch
        {
            // 如果不存在 初始化创建数据库
            db.DbMaintenance.CreateDatabase(databaseName);
        }
        finally
        {
            if (!flag)
            {
                Log.Logger.Information($"【 自动创建数据库 】");
                db.DbMaintenance.CreateDatabase(databaseName);
                // 我在所有的表后边都实现了一个自己的接口 IDbTable 
                var dbTable = typeof(IDbTable);
                // 找到所有实现了 IDbTable 的类 就是Mysql中对应的表
                var types = this.GetType().Assembly.GetTypes().Where(t => dbTable.IsAssignableFrom(t));
                // 是否备份表
                if (Backup)
                {
                    foreach (var item in types)
                    {
                        // 判断表是否存在  如果不存在则新建表
                        if (!OdinSugarHelper.CheckTable(item))
                        {
                            DbScoped.Sugar.CodeFirst.BackupTable().InitTables(item);
                            Log.Logger.Information($"创建数据表【 {item.ToString()} 】");
                        }

                    }

                }
                else
                {
                    foreach (var item in types)
                    {
                        if (!OdinSugarHelper.CheckTable(item))
                        {
                            DbScoped.Sugar.CodeFirst.InitTables(item);
                            Log.Logger.Information($"创建数据表【 {item.ToString()} 】");
                        }
                    }

                }
                Log.Logger.Information($"启用【 数据库初始化 】---开始配置");
                SampleData.Init();
            }
        }
    }

    public OdinSugarDbSet<Aop_ApiInvokerCatch_DbModel> ApiInvokerCatchs { get { return new OdinSugarDbSet<Aop_ApiInvokerCatch_DbModel>(db); } }

    public OdinSugarDbSet<Aop_ApiInvokerRecord_DbModel> ApiInvokerRecords { get { return new OdinSugarDbSet<Aop_ApiInvokerRecord_DbModel>(db); } }

    public OdinSugarDbSet<ErrorCode_DbModel> ErrorCodes { get { return new OdinSugarDbSet<ErrorCode_DbModel>(db); } }
}
```

**OdinSugarHelper.cs**
```csharp 
public class OdinSugarHelper
{
    static SqlSugarClient Db = DbScoped.Sugar;
    /// <summary>
    /// 检查表是否存在
    /// </summary>
    /// <param name="type"></param>
    /// <returns></returns>
    public static bool CheckTable(Type type)
    {
        string tableName = Db.EntityMaintenance.GetTableName(type);
        return Db.DbMaintenance.IsAnyTable(tableName, false);
    }

    /// <summary>
    /// 检查表是否存在
    /// </summary>
    /// <typeparam name="T"></typeparam>
    /// <returns></returns>
    public static bool CheckTable<T>()
    {
        string tableName = Db.EntityMaintenance.GetTableName(typeof(T));
        return Db.DbMaintenance.IsAnyTable(tableName, false);
    }

    /// <summary>
    /// 检查表是否存在
    /// </summary>
    /// <param name="TableName"></param>
    /// <returns></returns>
    public static bool CheckTable(string TableName)
    {
        return Db.DbMaintenance.IsAnyTable(TableName, false);
    }
}
```

这样程序在运行的时候就基于DbContext的定义可以在数据库新建表。

#### 2. 基于 SqlSugar 使用 cap

&ensp;&ensp;CAP 是一个在分布式系统中（SOA，MicroService）实现事件总线及最终一致性（分布式事务）的一个开源的 C# 库，她具有轻量级，高性能，易使用等特点。

**Startup.cs** - SqlSugar 注入代码

```csharp
services.AddOdinCapInject(_Options.DbEntity.ConnectionString, _Options.MongoDb.MongoConnection, _Options.RabbitMQ);
```

**AddOdinCapInject** - 方法
```csharp
public static IServiceCollection AddOdinCapInject(this IServiceCollection services, string mysqlConnectionString, string mongoConnectionString, RabbitMQOptions rabbitMQOptions)
{
    services.AddCap(x =>
    {

        //如果你使用的ADO.NET，根据数据库选择进行配置：
        // x.UseSqlServer("数据库连接字符串");
        x.UseMySql(mysqlConnectionString);
        // x.UsePostgreSql("数据库连接字符串");

        //如果你使用的 MongoDB，你可以添加如下配置：
        // x.UseMongoDB(mongoConnectionString);  //注意，仅支持MongoDB 4.0+集群

        //CAP支持 RabbitMQ、Kafka、AzureServiceBus 等作为MQ，根据使用选择配置：
        x.UseRabbitMQ(rb =>
        {
            rb.HostName = rabbitMQOptions.HostNames[0];
            rb.UserName = rabbitMQOptions.Account.UserName;
            rb.Password = rabbitMQOptions.Account.Password;
            rb.VirtualHost = rabbitMQOptions.VirtualHost;
            rb.Port = rabbitMQOptions.Port;

        });
        // x.UseKafka("ConnectionStrings");
        // x.UseAzureServiceBus("ConnectionStrings");
        x.UseDashboard();
    });
    return services;
}
```

**OdinCapHelper.cs** - 封装，注入到service中即可使用
```csharp
public class OdinCapHelper : IOdinCapHelper
{
    public void CapPublish<T>(string publishName, T contentObj, Action action = null)
    {
        var db = DbScoped.Sugar;
        var capBus = OdinInjectHelper.GetService<ICapPublisher>();
        using (var connection = (MySqlConnection)db.Ado.Connection)
        {
            using (var transaction = connection.BeginTransaction(capBus, autoCommit: false))
            {
                if (connection.State != ConnectionState.Open)
                {
                    connection.Open();
                }
                db.Ado.Transaction = (IDbTransaction)transaction.DbTransaction;//这行很重要
                if (action != null) action();
                capBus.Publish<T>(publishName, contentObj);
                transaction.Commit();
            }
        }
    }
}
```
<font color="red">这里需要注意的是，如果和我一样是使用mysql数据库，那么需要讲nuget包由 SqlSugarCore 替换为 SqlSugarCore.MySqlConnector 切记！！！ 切记！！！</font>

** Controller ** - Action方法中发布消息
```csharp
var db = DbScoped.Sugar;
OdinCapHelper.CapPublish("Sample.RabbitMQ.MySql", DateTime.Now, () =>
    {
        System.Console.WriteLine("to do something");
    });
```


**Controller** - Action方法中订阅消费消息
```csharp
[CapSubscribe("Sample.RabbitMQ.MySql")]
public async Task<Task> CheckReceivedMessage(DateTime time)
{
    Console.WriteLine(time);
    return Task.CompletedTask;
}
```
完整代码可以在 [GitHub](https://github.com/odinsam/OdinMA)中找到。

    
	