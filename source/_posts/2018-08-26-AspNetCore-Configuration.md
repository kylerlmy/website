---
title: Asp.Net Core 之 Configuration
comments: true
categories: ASP.NET Core
tags: Configuration
date: 2018-08-26
---

#### 命名空间
`using Microsoft.Extensions.Configuration` 

#### Host 配置 与 应用配置
在应用被配置和运行之前，Host 优先进行配置并启动。Host 的职责时启动应用，以及应用的生命周期管理。Host 配置的键值对将成为应用全局配置的一部分。

#### 默认配置
+ Host 配置，有以下方式提供：
    - [以 `ASPNETCORE_` 为前缀的环境变量](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2#environment-variables-configuration-provider)
    - [命令行配置](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2#command-line-configuration-provider)
    
+ 应用配置由以下方式提供：
    - [使用配置文件提供程序 appsettings.json 文件](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2#file-configuration-provider)
    - 使用配置文件提供程序的 appsettings.{Environment}.json 文件
    - 应用的入口程序集运行在 Development 环境时，使用的 [Secret Manager](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-2.2&tabs=windows)
    - 使用环境变量配置提供程序的环境变量
    - 使用命令行配置程序的命令行参数

#### 分层配置数据
将文件读入配置时，将创建唯一键以保持配置源的原始分层数据结构。 使用冒号 (:) 来展开节和键以保持原始结构。

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```
访问：`section0:key0`

在配置数据中可以使用 `GetSection`  和 `GetChildren`  来隔离一个 sections 和 sections 的子元素。

#### 约定
在应用启动时，将安装配置提供程序的先后顺序从相应的配置提供程序中读取配置。
实现了配置更改检测的配置提供程序（如，`AddJsonFile`  中指定 `reloadOnChange` 参数）  ，能够在配置修改后重新加载配置（配置热更新）.。
应用的 DI 容器中提供了 `IConfiguration`。`IConfiguration` 可以注入到任何需要它的地方。
配置提供程序不能使用 DI ，因为 Host 在设置这些配置提供时，DI 还不可用。（即 DI 在配置文件配置完成后运行）

配置 Key 时采用以下约定：
+ Key 不区分大小写
+ 由不同或相同的配置提供程序来配置相同的 Key 时，按照配置的先后顺序，最后一个配置的生效。
+ 分层 Key：冒号分隔符 (:) 适用于所有平台。在环境变量中，冒号分隔符可能无法适用于所有平台
+ `ConfigurationBinder` 支持使用配置文件中 Key 的索引数组与对象进行绑定。

配置 Value 时采用的约定：
+ Value 为字符串
+ NULL 不能作为 Value的值，也不能将其绑定到对象。

#### ConfigureAppConfiguration
在构建 Host 时调用 `ConfigureAppConfiguration` 来指定应用的配置提供程序，以及 `CreateDefaultBuilder` 自动添加的配置。
在应用启动期间，可以使用 ConfigureAppConfiguration 中提供给应用的配置，也包括 Startup.ConfigureServices 中指定的配置。

#### GetValue
`ConfigurationBinder.GetValue<T>` 从具有指定键的配置中提取一个值，并将其转换为指定类型。 如果未找到该键，会使用调用该方法时指定的默认值。
如：
`_config.GetValue<int>("NumberKey", 99);` 
`_config 为 通过 DI 获取的一个 IConfiguration 的一个对象`

#### GetSection、GetChildren 和 Exists
```Java
//GetSection
var configSection = _config.GetSection("section1");
var configSection = _config.GetSection("section2:subsection0");

//GetChildren
var configSection = _config.GetSection("section2"); 
var children = configSection.GetChildren();

//Exists
var sectionExists =_config.GetSection("section2:subsection2").Exists();
```

#### 绑定到类
```java
var starship = new Starship();
_config.GetSection("starship").Bind(starship);
Starship = starship;
```

`ConfigurationBinder.Get<T>`  绑定并返回指定类型。 Get<T> 比使用 Bind 更方便。

`var starship = _config.GetSection("starship").Get<Starship>();`

