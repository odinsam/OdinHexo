title: Serilog封装扩展并写入Mysql
author: odinsam
tags:
  - .Net Core
  - Serilog
  - Log
categories:
  - .Net Core
abbrlink: b919
date: 2021-06-07 04:45:00
---
> Serilog是 .NET 中最著名的结构化日志类库。大多数情况下，中小型项目会将日志直接记录在一个对应的文件夹中比如Logs文件夹，并且可以按照日志的等级创建子文件夹比如errror、debug等等，再按照日期创建子文件最后按照日志文件大小上限做日志文件的划分。在一些大型项目中需要将日志写入数据库，文章讲述如何使用Serilog日志类库在mysql数据库中自动创建logs表并将日志写入表中。

<!-- more --> 
 
 1. 封装模型：

	LogWriteFileModel.cs
   
   ```csharp
   public class LogWriteFileModel
   {
       public string FileName { get; set; }
       public int FileSizeLimitBytes { get; set; } = 1000000;
       public bool RollOnFileSizeLimit { get; set; } = true;
       public bool Shared { get; set; } = true;
       public TimeSpan FlushToDiskInterval { get; set; } = TimeSpan.FromSeconds(1);

   }
   ```
   
   LogWriteToConsoleModel.cs
   
   ```csharp
   public class LogWriteToConsoleModel
   {
       public string OutputTemplate { get; set; }
       public SystemConsoleTheme ConsoleTheme { get; set; } = SystemConsoleTheme.Colored;
   }
   ```
   
   LogWriteMySqlModel.cs
   
   ```csharp
   public class LogWriteMySqlModel
   {
       public string ConnectionString { get; set; }
   }
   ```

2. 扩展类

	安装package Serilog.Sinks.MySQL, Version=4.0.0.0，LoggerConfigurationExtends.cs
   
   ```csharp
   public static class LoggerConfigurationExtends
    {
        public static LoggerConfiguration OdinWriteLog(this LoggerConfiguration loggerConfiguration, LogWriteFileModel logWriteFileModel, LogWriteToConsoleModel logWriteToConsole, LogWriteMySqlModel logWriteMySqlModel)
        {
            return loggerConfiguration
                .WriteTo.OdinWrite(
                    LogEventLevel.Debug, logWriteFileModel, logWriteToConsole, logWriteMySqlModel
                )
                .WriteTo.OdinWrite(
                    LogEventLevel.Error, logWriteFileModel, logWriteToConsole, logWriteMySqlModel
                )
                .WriteTo.OdinWrite(
                    LogEventLevel.Fatal, logWriteFileModel, logWriteToConsole, logWriteMySqlModel
                )
                .WriteTo.OdinWrite(
                    LogEventLevel.Information, logWriteFileModel, logWriteToConsole, logWriteMySqlModel
                )
                .WriteTo.OdinWrite(
                    LogEventLevel.Warning, logWriteFileModel, logWriteToConsole, logWriteMySqlModel
                );
        }
        public static LoggerConfiguration OdinWrite(this LoggerSinkConfiguration loggerSinkConfiguration, LogEventLevel logLevel, LogWriteFileModel logWriteFileModel, LogWriteToConsoleModel logWriteToConsole, LogWriteMySqlModel logWriteMySqlModel)
        {
            return loggerSinkConfiguration.Logger(fileLogger =>
            {
                var config = SerilogHelper.OdinWriteToFile(fileLogger, logLevel, logWriteFileModel);
                if (logWriteToConsole != null)
                {
                    config.WriteTo.Console(
                        outputTemplate:
                            string.IsNullOrEmpty(logWriteToConsole.OutputTemplate)
                            ?
                            "{Timestamp:yyyy-MM-dd HH:mm:ss} [{Level}] {Message}{NewLine}{Exception}"
                            :
                            logWriteToConsole.OutputTemplate,
                        theme: logWriteToConsole.ConsoleTheme
                    );
                }
                if (logWriteMySqlModel != null)
                    config.WriteTo.MySQL(connectionString: logWriteMySqlModel.ConnectionString);
            });
        }
    }
   ```

3. 封装类:  SerilogHelper.cs

    ```csharp
    public class SerilogHelper
    {
        public static LoggerConfiguration OdinWriteToFile(LoggerConfiguration fileLogger, LogEventLevel logLevel, LogWriteFileModel logWriteModel)
        {

            return fileLogger.Filter
                   .ByIncludingOnly(p => p.Level.Equals(logLevel))
                   .WriteTo.File(
                       path:
                        string.IsNullOrEmpty(logWriteModel.FileName) ?
                            $"logs/{DateTime.Now.ToString("yyyyMMdd")}/log-{DateTime.Now.ToString("yyyyMMdd")}-{logLevel.ToString()}.txt"
                            :
                            logWriteModel.FileName,
                       fileSizeLimitBytes: logWriteModel.FileSizeLimitBytes,
                       rollOnFileSizeLimit: logWriteModel.RollOnFileSizeLimit,
                       shared: logWriteModel.Shared,
                       flushToDiskInterval: logWriteModel.FlushToDiskInterval
                   );
        }
    }
    ```

4. 使用

    ```csharp
    #region Log设置
    Log.Logger = new LoggerConfiguration()
        // 最小的日志输出级别
        .MinimumLevel.Information()
        //.MinimumLevel.Information ()
        // 日志调用类命名空间如果以 System 开头，覆盖日志输出最小级别为 Information
        .MinimumLevel.Override("System", LogEventLevel.Information)
        // 日志调用类命名空间如果以 Microsoft 开头，覆盖日志输出最小级别为 Information
        .MinimumLevel.Override("Microsoft", LogEventLevel.Information)
        .OdinWriteLog(
            new LogWriteFileModel { }, new LogWriteToConsoleModel { }, new LogWriteMySqlModel { ConnectionString = Configuration.GetSection("ProjectConfigOptions:DbEntity:ConnectionString").Value }
        )
        .CreateLogger();
    #endregion
    ```