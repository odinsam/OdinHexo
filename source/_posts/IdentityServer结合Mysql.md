title: IdentityServer结合Mysql
author: odinsam
tags:
  - IdentityServer4
  - Mysql
  - .Net Core
categories:
  - .Net Core
abbrlink: '7094'
date: 2021-06-06 11:23:00
---
> IdentityServer4 是为 Asp.Net Core 2.0+ 系列量身打造的一款基于 OpenID Connect 和 OAuth 2.0 认证框架，官网提供了对应持久化到SQL Server数据库的方法。但是在持久化到Mysql数据库时，会出现 <font color="red">Specified key was too long</font> 的错误。我们可以通过重写 OnModelCreating 方法的方式解决问题。

<!-- more -->

1. 使用 MySql.EntityFrameworkCore

2. ConfigureServices 添加代码如下:

   ```csharp
    const string connectionString = @"Server=ip;database=databasename;uid=userid;pwd=password;";
    var migrationsAssembly = typeof(Startup).GetTypeInfo().Assembly.GetName().Name;
    var mysqlVersion = new MySqlServerVersion(new Version(8, 0, 21));
    services.AddIdentityServer()
                    .AddDeveloperSigningCredential()
                    // 客户端和资源的数据库存储
                    // ConfigurationDbContext
                    // dotnet ef migrations add ConfigDbContext -c ConfigurationDbContext -o Data/Migrations/IdentityServer/ConfiguragtionDb
                    // dotnet ef database update -c ConfigurationDbContext
                    .AddConfigurationStore(opt =>
                    {
                        opt.ConfigureDbContext = context =>
                        {
                            context.UseMySQL(_Options.DbEntity.ConnectionString, sql => sql.MigrationsAssembly(migrationsAssembly));
                        };
                    })
                    // 令牌和授权码的数据库存储
                    // PersistedGrantDbContext
                    // dotnet ef migrations add OperationContext -c PersistedGrantDbContext  -o Data/Migrations/IdentityServer/OperationDb
                    // dotnet ef database update -c PersistedGrantDbContext
                    .AddOperationalStore(opt =>
                    {
                        opt.ConfigureDbContext = context =>
                            context.UseMySQL(_Options.DbEntity.ConnectionString, sql => sql.MigrationsAssembly(migrationsAssembly));
                        opt.EnableTokenCleanup = true;
                        opt.TokenCleanupInterval = 30;
                    });

                services.AddIdentityServerDbContext<ConfigurationDbContext>(options =>
                        {
                            options.ConfigureDbContext = builder => builder.UseMySQL(_Options.DbEntity.ConnectionString, db => db.MigrationsAssembly(migrationsAssembly));
                        })
                        .AddIdentityServerDbContext<PersistedGrantDbContext>(options =>
                        {
                            options.ConfigureDbContext = builder => builder.UseMySQL(_Options.DbEntity.ConnectionString, db => db.MigrationsAssembly(migrationsAssembly));
                        });

                // 更改Identity中关于用户和角色的处理到Entityframework
                // dotnet ef migrations add UserStoreContext -c OdinIdentityEntities -o Data/Migrations/IdentityServer/UserDb
                // dotnet ef database update -c OdinIdentityEntities
            }
   ```

3. 安装包如下

   IdentityServer4<br />
   IdentityServer4.EntityFramework<br />
   Microsoft.EntityFrameworkCore.Tools<br />
   Microsoft.AspNet.Identity.EntityFramework<br />
   Microsoft.EntityFrameworkCore<br />
   IdentityServer4.AspNetIdentity<br />

4. 添加 ApplicationDbContext.cs、ApplicationUser.cs 和 ApplicationRole.cs

```csharp
public class ApplicationDbContext : Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext<ApplicationUser, ApplicationRole, Guid>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options)
    {
    }
    public DbSet<IdUser> IdentityUsers { get; set; }
    public DbSet<IdUser> IdentityRoles { get; set; }
    public DbSet<IdentityUserClaim> IdentityUserClaim { get; set; }
    // 其他表
    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
        // mysql 修改索引长度 解决  Specified key was too long; max key length is 3072 bytes
        builder.Entity("Microsoft.AspNetCore.Identity.IdentityRole", b =>
                  {
                      b.Property<string>("Id")
                          .HasColumnType("varchar(256)");
                  });
       	builder.Entity("Microsoft.AspNetCore.Identity.IdentityUser", b =>
              {
                  b.Property<string>("Id")
                      .HasColumnType("varchar(256)");
              });
       builder.Entity("Microsoft.AspNetCore.Identity.IdentityUserLogin<string>", b =>
              {
                  b.Property<string>("LoginProvider")
                          .HasColumnType("varchar(256)");

                  b.Property<string>("ProviderKey")
                      .HasColumnType("varchar(256)");
              });
       builder.Entity("Microsoft.AspNetCore.Identity.IdentityUserToken<string>", b =>
                  {

                      b.Property<string>("LoginProvider")
                          .HasColumnType("varchar(256)");

                      b.Property<string>("Name")
                          .HasColumnType("varchar(256)");
                  });
       builder.Entity("OdinOIS.Models.DbModels.IdentityUserStore.IdentityUserClaim", b =>
              {
                  b.Property<string>("ClaimId")
                      .HasColumnType("varchar(256)");

              });
    }
}
```

```csharp
public class ApplicationUser : Microsoft.AspNetCore.Identity.IdentityUser<Guid>
{
    //可以在这里扩展
}
```

```csharp
public class ApplicationRole : Microsoft.AspNetCore.Identity.IdentityRole<Guid>
{

}
```

5. 控制台输入

   dotnet ef migrations add ConfigDbContext -c ConfigurationDbContext -o Date\Migrations\IdentityServer\ConfiguragtionDb<br />
   dotnet ef database update ConfigDbContext -c ConfigurationDbContext<br />
   dotnet ef migrations add ConfigDbContext -c PersistedGrantDbContext -o Date\Migrations\IdentityServer\PersistedGrantDb<br />
   dotnet ef database update ConfigDbContext -c PersistedGrantDbContext<br />
   dotnet ef migrations add UserStoreContext -c OdinIdentityEntities -o Data/Migrations/IdentityServer/UserDb<br />
   dotnet ef database update -c OdinIdentityEntities