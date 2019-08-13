---
title: Asp.Net Core 之 Host
comments: true
categories: ASP.NET Core
tags: Host
date: 2018-08-22
---
### Host 概述
ASP.NET Core 应用在启动时构建主机 。 主机是封装所有应用资源的对象。其中包括：
+ HTTP 服务器实现（ Kestrel ）
+ 中间件组件
+ 日志记录
+ DI
+ 配置

将应用程序所有依赖包含在一个对象中的主要原因是为了进行生命周期管理：控制应用的启动和正常关闭。
.NET Core 提供了两个主机：Web 主机和泛型主机。 在 ASP.NET Core 2.x 上，泛型主机仅用于非 Web 方案。

#### Web主机的默认配置(IWebHost,IWebHostBuilder)
`CreateDefaultBuilder` 方法使用常用选项来配置主机，常用选项如下：
+ 将 Kestrel 用作 Web 服务器并启用 IIS 集成。
+ 从 appsettings.json、appsettings.[EnvironmentName].json、环境变量、命令行参数和其他配置源中加载配置 。
+ 将日志记录输出发送到控制台并调试提供程序。

#### 非 Web 方案(IHost,IHostBuilder)
泛型主机允许其他类型的应用程序使用跨领域的框架扩展：如日志记录、依赖项注入 (DI)、配置和应用生命周期管理。

### Web Host
ASP.NET Core 应用配置和启动“主机” 。 主机负责应用程序启动和生存期管理。主机至少配置服务器和请求处理管道。主机也可以设置日志，DI，和配置。

#### 设置主机
使用 `IWebHostBuilder` 的实例来创建主机，这通常在Main方法中（应用程序的入口点）执行。以下是个典型的示例，使用 `CreateDefaultBuilder`   来开始设置 Host。
```Java
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```
`CreateDefaultBuilder`  方法由 `CreateWebHostBuilder` 方法调用，用于构建 WebHost，它与启动 WebHost 的代码分离。这种分离在使用 Entity Framework Core 工具时，是必须的。这些工具期望找到 `CreateWebHostBuilder`  方法，这样它们就可以在设计时，在不启动应用的情况下来配置主机。另一种方法是实现 `IDesignTimeDbContextFactory`。

`CreateDefaultBuilder `  方法执行下列任务：
+ 使用应用的托管配置提供程序将 Kestrel 服务器配置为 Web 服务器。[Kestrel默认设置](https://docs.microsoft.com/zh-cn/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2#kestrel-options)。 
+ 将内容根设置为由 Directory.GetCurrentDirectory 返回的路径。(使用 `UseContentRoot`  方法)
+ 通过以下对象加载**主机**配置：
    - 前缀为 ASPNETCORE_ 的环境变量（例如，ASPNETCORE_ENVIRONMENT）。
    - 命令行参数。
+ 按以下顺序加载**应用**配置：
    - appsettings.json 。
    - appsettings.{Environment}.json 。
    - 应用在使用入口程序集的 Development 环境中运行时的机密管理器。
    - 环境变量。
    - 命令行参数。
+ 配置控制台和调试输出的日志记录。 日志记录的配置包含 appsettings.json 或 appsettings.{Environment}.json 文件中，日志记录配置部分指定的日志筛选规则 。
+ 使用 ASP.NET Core 模块在 IIS 后面运行时，CreateDefaultBuilder 会启用 IIS 集成，这会配置应用的基址和端口。 IIS 集成还配置应用以捕获启动错误。 
+ 如果应用环境为“开发”，请将 ServiceProviderOptions.ValidateScopes 设为 true，来[设置作用域验证](https://docs.microsoft.com/zh-cn/aspnet/core/fundamentals/host/web-host?view=aspnetcore-2.2#scope-validation)

#### 增强配置
`CreateDefaultBuilder` 指定的配置可以通过下面的方法进行覆盖：
+ `ConfigureAppConfiguration`
用于指定**应用（而非主机）**的其他 IConfiguration。~~也可以通过 `ConfigurationBuilder` 来构建一个 IConfiguration ，然后，在 `CreateDefaultBuilder`  后调用 `UseConfiguration`  来指定这个配置。~~（这种方式时用于设置主机配置的）
+ `ConfigureLogging`
重写 `CreateDefaultBuilder` 在 appsettings.Development.json 和 appsettings.Production.json 中配置的设置
+ ` IWebHostBuilder` 的其他方法和扩展方法可重写和增强 CreateDefaultBuilder 定义的配置。
用` ConfigureKestrel` 来重写 `CreateDefaultBuilder` 对 Kestrel 的配置  

### 主机配置值（注意不是应用配置）
`WebHostBuilder` 依赖下面的方法来设置 Host 配置值：
+ 应用程序名称: `UseSetting`  ，默认值：包含应用入口点的程序集的名称。
+ 捕获启动错误：`CaptureStartupErrors` ，此设置控制启动错误的捕获。
+ 内容根：`UseContentRoot`，此设置确定 ASP.NET Core 从什么地方开始搜索内容文件，如 MVC 视图等。默认为应用程序集所在的文件夹。
+ 详细错误：`UseSetting` ，确定是否应捕获详细错误。
+ 环境：`UseEnvironment` ，设置应用的环境。
+ 承载启动程序集：`UseSetting` ，设置应用的承载启动程序集。
+ HTTPS 端口：`UseSetting` ，设置 HTTPS 重定向端口。 用于强制实施 HTTPS。
+ 首选承载 URL：`PreferHostingUrls` ，指示主机是否应该侦听使用 WebHostBuilder 配置的 URL，而不是使用 IServer 实现配置的 URL。
+ 阻止承载启动：`UseSetting` ，阻止承载启动程序集自动加载，包括应用的程序集所配置的承载启动程序集。
+ 服务器 URL：`UseUrls` ，指示 IP 地址或主机地址，其中包含服务器应针对请求侦听的端口和协议。
+ 关闭超时：`UseSetting` ，指定等待 Web 主机关闭的时长。
+ 启动程序集：`UseStartup` ，确定要在其中搜索 Startup 类的程序集。
+ Web 根路径：`UseWebRoot` ，设置应用的静态资产的相对路径

#### 重写配置
可以使用  `UseConfiguration`  来覆盖 Host 的默认的配置，`IWebHostBuilder`  中的配置也会添加到**应用**配置中。但是对应用的配置不会影响到主机的配置；如，**`ConfigureAppConfiguration` 不影响 `IWebHostBuilder` 的配置。**
```java
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)//命令行会重写配置
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)//应用上面创建的配置
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```
以上两个配置方式的代码实现：
```Java
//WebHostBuilderExtensions:
public static IWebHostBuilder ConfigureAppConfiguration(this IWebHostBuilder hostBuilder, Action<IConfigurationBuilder> configureDelegate)
{
    return hostBuilder.ConfigureAppConfiguration((context, builder) => configureDelegate(builder));
}

//WebHostBuilder:
public IWebHostBuilder ConfigureAppConfiguration(Action<WebHostBuilderContext, IConfigurationBuilder> configureDelegate)
{
    _configureAppConfigurationBuilder += configureDelegate;
    return this;
}
//HostingAbstractionsWebHostBuilderExtensions：
//在 Web Host 上应用给定的配置
public static IWebHostBuilder UseConfiguration(this IWebHostBuilder hostBuilder, IConfiguration configuration)
{
    foreach (var setting in configuration.AsEnumerable(makePathsRelative: true))
    {
        hostBuilder.UseSetting(setting.Key, setting.Value);
    }
    return hostBuilder;
}
```

#### 管理主机
调用方法：
+  `Start`
+  `Start(RequestDelegate app)`
+  `Start(string url, RequestDelegate app)`
+  `Start(Action<IRouteBuilder> routeBuilder)`
+  `Start(string url, Action<IRouteBuilder> routeBuilder)`
+  `StartWith(Action<IApplicationBuilder> app)`
+  `StartWith(string url, Action<IApplicationBuilder> app)`

#### IHostingEnvironment
该接口提供有关应用的 Web 承载环境的信息。 使用构造函数注入获取 IHostingEnvironment 以使用其属性和扩展方法。

#### IApplicationLifetime 接口
该接口用于在启动后、关闭时和关闭后指定执行相应的操作。接口上的三个属性是用于注册 Action 方法。

#### 作用域验证
若将 ValidateScopes 设为 true，默认服务提供程序会执行检查来验证以下内容：
+ 不直接从 root 服务提供商解析有作用域的服务
+ 有作用域的服务不直接后间接的注册为单例模式

调用 BuildServiceProvider 时，会创建根服务提供程序。 在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。
有作用域的服务由创建它们的容器释放。 如果作用域创建于根容器，则该服务的生命周期会提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。 
