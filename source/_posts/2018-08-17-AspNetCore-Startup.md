---
title: Asp.Net Core 之 Startup
comments: true
categories: ASP.NET Core
tags: Configuration
date: 2018-08-17
---
#### Startup 概述
Startup 类配置服务和应用的请求管道。
ASP.NET Core 应用使用 Startup 类，按照约定命名为 Startup。
Startup类包含：
+ `ConfigureServices`  方法，用于配置应用的服务，每个服务是提供了该应用所需功能的可重用组件，通过在 `ConfigureServices` 中配置服务（也称为服务被注册到 `ConfigureServices` 中），通过依赖注入（DI）或者`IApplicationBuilder.ApplicationServices ` 使得服务能够在整个应用中使用。
+ `Configure` 方法，用于创建应用的请求处理管道。

>`IApplicationBuilder.ApplicationServices` 的类型为 `IServiceProvider`
>`IServiceProvider` 定义了一种机制用于查找一个服务对象；也就是说，它是一个为其他对象提供自定义支持的对象。这个接口由为其他对象提供服务的类或值类型来实现。`IServiceProvider`  只有 `GetService`  这一个方法，该方法用于获取提供服务的对象实例。`System.Web.HttpContext`  （只能在 .NET Framework 中使用）也实现了这个接口。

当应用程序启动时，`ConfigureServices`  和 `Configure`  被 ASP.NET Core 运行时调用。Startup 类在应用的 Host 被构建时指定，一般通过调用 Host的构建器（`IWebHostBuilder` ）`WebHostBuilderExtensions.UseStartup<TStartup>`  方法来实现。

Host 提供了 Startup 构造器可用的一些服务。应用可以通 `ConfigureServices`  方法来添加可选的服务。然后，主机和应用服务都可以在 Configure 和整个应用中使用。 一般使用依赖注入向 Startup 类中注入：
+ `IHostingEnvironment`  用于通过环境配置服务
+ `IConfiguration`  用于读取配置
+ `ILoggerFactory` 用于在`ConfigureServices` 创建一个 Logger。

另一种注入` IHostingEnvironment`  的方法是，使用基于约定的方式。
当应用针对不同的环境定义单独的 Startup 类时，（例如在开发环境中定义的 StartupDevelopment 类），特定的 Startup 类在运行时被选定，类名称的后缀与当前环境匹配的 Startup 将被优先选择。当在开发环境中同时存在 Startup 类 和 StartupDevelopment 类，那么 StartupDevelopment 将会被选择。

#### ConfigureServices 方法
+ 可选
+ 在 `Configure` 方法配置应用的服务之前，由 Host 调用。
+ 配置选项按照阅读设置

在调用 Startup 的方法之前，Host 会配置一些服务，如：
+ HTTP 服务器实现（ Kestrel ）
+ 中间件组件
+ 日志记录
+ DI
+ 配置

对于希望设置更多的功能,可以调用 `IServiceCollection` 的`Add{Service}`扩展方法。添加服务到服务容器，使得它在整个应用程序中和 Configure 方法内可用。服务通过依赖关系注入或 `ApplicationServices` 进行解析。

#### Configure 方法
 `Configure` 方法用于指定应用如何相应 HTTP 请求。可以通过添加中间件到 `IApplicationBuilder`  的实例来配置请求管道。 `IApplicationBuilder` 在 `Configure`  方法中可用，但它没有被注册到服务容器（`IServiceCollection` ）中，Host 创建了一个  `IApplicationBuilder`  并且直接传递给 `Configure`  方法。
 
ASP.NET Core 模板配置的管道支持：
+ 开发人员异常页异常处理程序
+ HTTP 严格传输安全性 (HSTS)
+ HTTPS 重定向
+ 静态文件
+ 一般数据保护条例 (GDPR)
+ ASP.NET Core MVC 和 Razor Pages

每个 `Use` 扩展方法将一个或多个中间件组件添加到请求管道。 

#### 简便方式
要配置服务和请求处理管道，而不使用 Startup 类，可以在在主机生成器（ `IWebHostBuilder` ）上调用 `ConfigureServices` 和 `Configure` 便捷方法。 多次调用 `ConfigureServices` 方法，前一个将追加到后一个上。 如果存在多个 `Configure` 方法调用，则使用最后一个 `Configure` 调用。
