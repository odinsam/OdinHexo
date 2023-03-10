title: 关于net core动态加载配置文件的小问题
author: odinsam
tags:
  - .Net Core
  - ''
categories:
  - .Net Core
abbrlink: '8950'
date: 2021-05-27 21:57:00
---
> 在我的项目当中配置文件较多，例如包括 <font color="blue">项目自身的配置文件</font>、<font color="blue">consul.config</font>、<font color="blue">redis.cofing</font>、<font color="blue">identityServer.config</font> 等等，要在项目启动的时候动态加载所有的配置文件。在我的项目中有 serverConfig 文件夹中有对应的所有的配置文件需要动态加载所有文件，在加载过程中发现按照文件路径无法加载 win10 环境。最后发现加载时不能使用绝对路径需要使用相对路径。  

<!-- more -->


#### 需求场景:

> serverConfig 中有对应的一系列配置文件。其中 cnf.json 为主配置文件,里边有当前项目的运行环境的配置，还有其他的文件夹以及对应的其他的配置文件。

    现在需要在Config Builder之前递归加载所有的配置文件。

#### 问题:

> 在编码后运行发现，总是找不对对应的配置文件（win 10 环境），代码如下:

    ```csharp
    public void LoadConfigFiles(string currentPath, IConfigurationBuilder config)
    {
        foreach (var item in Directory.GetFiles(currentPath))
        {
            if (Path.GetFileName(item) != "cnf.json")
            {
                if (File.Exists(item))
                    config.Add(new JsonConfigurationSource { Path = item, Optional = false, ReloadOnChange = true });
            }
        }
        var dir = Directory.GetDirectories(currentPath);
        if (dir != null && dir.Length > 0)
        {
            foreach (var dirItem in dir)
            {
                if (!Path.GetDirectoryName(dirItem).EndsWith(Path.Combine(FileHelper.DirectorySeparatorChar, "envConfig")))
                    LoadConfigFiles(dirItem, config);
            }
        }
    }
    ```
    代码总是报错，提示找不对config文件

#### 解决:

> 最后发现 config.add 加载的文件路径需要是相对路径而不能是绝对路径，解决代码如下:

    ```csharp
    public void LoadConfigFiles(string currentPath, IConfigurationBuilder config, string rootPath)
    {
        foreach (var item in Directory.GetFiles(currentPath))
        {
            if (Path.GetFileName(item) != "cnf.json")
            {

                var configPath = item.Replace(rootPath, "");
                if (File.Exists(item))
                    config.Add(new JsonConfigurationSource { Path = configPath, Optional = false, ReloadOnChange = true });     //config.add 加载的文件 路径需要是相对路径 而不能是绝对路径
            }
        }
        var dir = Directory.GetDirectories(currentPath);
        if (dir != null && dir.Length > 0)
        {
            foreach (var dirItem in dir)
            {
                if (!Path.GetDirectoryName(dirItem).EndsWith(Path.Combine(FileHelper.DirectorySeparatorChar, "envConfig")))
                    LoadConfigFiles(dirItem, config, rootPath);
            }
        }
    }
    ```