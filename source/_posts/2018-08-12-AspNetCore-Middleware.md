---
title: Asp.Net Core 之 中间件
comments: true
categories: ASP.NET Core
tags: Middleware
date: 2018-08-12
---


#### 概述
中间件是一种装配到应用管道以处理请求和响应的软件。 每个组件：
+ 选择是否将请求传递到管道中的下一个组件（是否短路）。
+ 可在管道中的下一个组件前后执行工作。

请求委托用于生成请求管道。 请求委托处理每个 HTTP 请求。

#### 使用 IApplicationBuilder 创建中间件
ASP.NET Core 请求管道包含一系列依次调用的请求委托。每个委托均可在下一个委托前后执行操作。 应尽早在管道中调用异常处理委托，这样它们就能捕获在管道的后期阶段发生的异常。

#### 请求管道
`IApplicationBuilder` 是用来构建请求管道的，而所谓请求管道，本质上就是对 HttpContext 的一系列操作，即通过对 Request 的处理，来生成 Reponse。[[1]](https://www.cnblogs.com/RainingNight/p/middleware-in-asp-net-core.html)
对请求管道的注册是通过 `Func<RequestDelegate,RequestDelegate>` 类型的委托（也就是中间件）来实现的。`IApplicationBuilder` 接口中定义的 `Use` 方法的声明如下：
`IApplicationBuilder Use(Func<RequestDelegate, RequestDelegate> middleware);`

其中委托类型 `RequestDelegate` 的声明如下：
`public delegate Task RequestDelegate(HttpContext context); `

#### 引用
每个中间件扩展方法，都通过 `Microsoft.AspNetCore.Builder ` 命名空间在 `IApplicationBuilder` 上公开。

#### 顺序
向 `Startup.Configure` 方法添加中间件组件的顺序定义了针对请求调用这些组件的顺序，以及响应的相反顺序。 此排序对于安全性、性能和功能至关重要。

#### 内置中间件
尽早在管道中调用静态文件中间件(`app.UseStaticFiles()`)，以便它可以处理请求并使请求管道短路，而无需通过剩余其他的中间件。 静态文件中间件不提供授权检查功能。

虽然身份验证中间件对请求进行身份验证，但仅在 MVC 选择特定 的Razor 页或 MVC 控制器，并进行相应的操作后，才发生授权（或拒绝）。

#### Use、Run 和 Map
使用 `Use`、`Run` 和 `Map` 配置 HTTP 管道。
+ 用 `Use` 将多个请求委托链接在一起。next 参数表示管道中的下一个委托。 可通过不调用 next 参数使管道短路。通常需要对请求管道进行短路，因为这样可以避免不必要的工作。 例如，静态文件中间件可以处理对静态文件的请求，并让管道的其余部分短路，从而起到中断中间件的作用。
+ `Run` 是一种约定，它指定了某些可公开在管道末尾运行的中间件方法（`Run[Middleware] `）。
+ `Map` 扩展用作约定来创建管道分支。` Map` 基于给定请求路径的匹配项来创建请求管道分支。 如果请求路径以给定路径开头，则执行分支；使用 `Map` 时，将从 `HttpRequest.Path` 中删除匹配的路径段，并针对每个请求将该路径段追加到 `HttpRequest.PathBase`。
+ `MapWhen` 基于给定谓词的结果创建请求管道分支。

```Java
public void Configure(IApplicationBuilder app)
{
    //自己编写一个中间件
    app.Use(async (context, next) =>
    {
        // Do work that doesn't write to the Response.
        await next.Invoke();
        // Do logging or other work that doesn't write to the Response.
    });
    
    app.Map("/map1", a =>
    {
        a.Run(async context =>
        {
            await context.Response.WriteAsync("Map Test 1");
        });
    });
    
    app.Run(async context =>
    {
        await context.Response.WriteAsync("Hello from 2nd delegate.");
    });
}
```