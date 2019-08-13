---
title: Asp.Net Core 之 Options
comments: true
categories: ASP.NET Core
tags: Configuration
date: 2018-08-30
---
#### Options 模式
Options 模式使用类来代表一组关联的设置。当配置设置由于使用场景而被隔离到单独的类时，应用遵循两个重要软件工程原则：
+ 接口隔离原则（ISP）
依赖于配置设置的类，仅仅只依赖于他们用到的配置设置。
+ 关注点分离原则
应用的不同部分的设置不彼此依赖或相互耦合。

Options 也提供了[验证配置数据的机制](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-2.2#options-validation)。

#### 引用
`Microsoft.Extensions.Options.ConfigurationExtensions`

#### Options 接口
`IOptionsMonitor<TOptions>`  用于检索 Options 并管理 TOptions 实例的选项通知。`IOptionsMonitor<TOptions>` 支持以下场景:
+ 修改通知
+ Options 命名
+ 可重载配置
+ 选择性 Options 失效 `IOptionsMonitorCache<TOptions>`

#### 常规选项设置
配置文件：
```json
{
  "option1": "value1_from_json",
  "option2": -1,
  "subsection": {
    "suboption1": "subvalue1_from_json",
    "suboption2": 200
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

实体类：
```Java
//配置实体类
public class MyOptions
{
    public MyOptions()
    {
        // Set default value.
        Option1 = "value1_from_ctor";
    }
    
    public string Option1 { get; set; }
    public int Option2 { get; set; } = 5;
}

//子选项配置实体类
public class MySubOptions
{
    public MySubOptions()
    {
        // Set default values.
        SubOption1 = "value1_from_ctor";
        SubOption2 = 5;
    }
    
    public string SubOption1 { get; set; }
    public int SubOption2 { get; set; }
}
```
在 Startup 类的 ConfigureServices 方法中，将 MyOptions 添加到服务容器，并绑定到配置

```java
// Example #1: General configuration
// Register the Configuration instance which MyOptions binds against.
services.Configure<MyOptions>(Configuration);

// Example #2: Suboptions
// Bind options using a sub-section of the appsettings.json file.
services.Configure<MySubOptions>.(Configuration.GetSection("subsection"));

// Example #3: Snapshot option
//不需要添加额外配置，使用 Example #1 的配置。
```
在需要使用该配置的地方使用它：
```java
//字段
private readonly MyOptions _options;
private readonly MySubOptions _subOptions;//子选项配置
private readonly MyOptions _snapshotOptions;//可重载配置

//构造函数
public IndexModel(
    IOptionsMonitor<MyOptions> optionsAccessor, 
    IOptionsSnapshot<MyOptions> snapshotOptionsAccessor,
    OptionsMonitor<MySubOptions> subOptionsAccessor)
{
    _options = optionsAccessor.CurrentValue;
 _subOptions = subOptionsAccessor.CurrentValue;
    _snapshotOptions = snapshotOptionsAccessor.Value;
 }
 
 //使用
 
 // Example #1: Simple options
var option1 = _options.Option1;
var option2 = _options.Option2;

// Example #2: Suboptions
var subOption1 = _subOptions.SubOption1;
var subOption2 = _subOptions.SubOption2;

// Example #3: Snapshot options
var snapshotOption1 = _snapshotOptions.Option1;
var snapshotOption2 = _snapshotOptions.Option2;
```