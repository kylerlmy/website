---
title: Ubuntu 部署 ASP.NET Core API 应用
comments: true
categories: Computer 
tags: Configuration
date: 2019-04-17
---
#### 环境
框架：.NET Core 2.2
系统：Ubuntu 18.04-x64
Web 服务器：Nginx

#### 构建并发布应用
##### 安装 .NET Core SDK 与 ASP.NET Core 运行时
1. 注册Microsoft密钥和订阅源
```shell
wget -q https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
```
2. 安装开发包
```shell
sudo add-apt-repository universe
sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install dotnet-sdk-2.2
```

3. 安装 .NET 运行时
```shell
sudo add-apt-repository universe
sudo apt-get install apt-transport-https
sudo apt-get update
sudo apt-get install aspnetcore-runtime-2.2
```

##### 创建 Web API 应用
此处略（可以根据自己的实际需求来创建）

##### 构建与发布应用
+ 进入项目所在目录
+ 执行命令 `dotnet build -c Release` 用于构建应用
+ 执行命令 `dotnet publish -c Release -o ./publish` 来发布应用
*注：如果不指定输出目录，默认在 `bin/Release/aspnetcore2.2/publish` 目录下。*

#### 安装并配置 Nginx
##### 安装Nginx
+ [配置 apt-get 源](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
在 `/etc/apt/sources.list` 文件下添加以下内容：
```shell
## Replace $release with your corresponding Ubuntu release.
deb http://nginx.org/packages/ubuntu/ $release nginx
deb-src http://nginx.org/packages/ubuntu/ $release nginx
```
这里的 `$release` 参数要根据当前安装机器的系统版本来配置。如：Ubuntu 18.04（bionic） 
```shell
deb http://nginx.org/packages/ubuntu/ bionic nginx
deb-src http://nginx.org/packages/ubuntu/ bionic nginx
```
+ 安装
```shell
sudo apt-get update
sudo apt-get install nginx
```
+ 启动 Nginx
`sudo service nginx start`
##### 配置 Nginx
+ 进入配置文件
```shell
sudo  vi /etc/nginx/sites-available/default
```
+ 将配置文件中的第二个 server 节点，修改为：
```shell
server {
        listen 192.168.1.110:8089;
        # listen [::]:8089;
        server_name netkyle.com;
        # root /var/www/example.com;
        # index index.html;

        location / {
        # try_files $uri $uri/ =404;

        proxy_pass http://127.0.0.1:8989;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection keep-alive;
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}    
```
查看一下配置是否正确：`sudo nginx -t`
强制加载修改：`sudo nginx -s reload`
查看一下 Nginx 是否启动 `systemctl status nginx` ，如果显示未启动成功，则执行以下命令 `sudo journalctl -fu nginx.service` 来查看错误日志。也可以访问 `/var/crash` 查看是否有错误日志。

如果都没问题了，测试一下 Web服务器是否能正常使用（这里使用的时默认端口 80，如果是其他端口需要在地址后追加端口，并以 ":" 分割）：
`curl http://localhost`

*注意这里的设置代理的端口不是默认的端口，要注意与 `Program.cs`  文件中设置的保持一致：*
```Java
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://127.0.0.1:8989")
    .UseStartup<Startup>();
```
详细的配置可以参考 [Nginx 官网给出的配置文件](https://www.nginx.com/resources/wiki/start/topics/examples/full/) 和 [微软文档给出的配置](https://docs.microsoft.com/zh-cn/aspnet/core/host-and-deploy/linux-nginx?view=aspnetcore-2.2)
#### 部署应用
##### 创建服务文件
+ 创建一个服务文件
`sudo vi /etc/systemd/system/kestrel-webapi.service`

+ 向创建的配置文件中添加如下内容
```shell
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/webapi
ExecStart=/usr/bin/dotnet /var/www/webapi/RedisWebAPI.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=netkyle
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
[Install]
WantedBy=multi-user.target
```
说明：
1. 如果用户 `netkyle` 不存在，需要提前创建用户。
2. 使用 TimeoutStopSec 配置在收到初始中断信号后等待应用程序关闭的持续时间。 如果应用程序在此时间段内未关闭，则将发出 SIGKILL 以终止该应用程序。
3. 环境变量注意事项
	- Linux 具有区分大小写的文件系统。 将 ASPNETCORE_ENVIRONMENT 设置为“Production”会导致搜索配置文件 `appsettings.Production.json` ，而不是 `appsettings.production.json` 。必须转义某些值,以供配置提供程序读取环境变量。 使用以下命令生成适当的转义值以供在配置文件中使用：
`systemd-escape "<value-to-escape>"`
	- 环境变量名不支持冒号 (:) 分隔符。 使用双下划线 (`__`) 代替冒号。 环境变量读入配置时，环境变量配置提供程序将双下划线转换为冒号。示例：
`Environment=ConnectionStrings__DefaultConnection={Connection String}`


+ 将上面 **构建与发布应用** 中发布的 publish 目录中的文件全部拷贝到目录 `/var/www/webapi/`  目录下面。

+ 修改配置文件和文件夹的权限
`sudo chmod 777  /var/www/webapi/appsetting.json`

+ 启动服务：
进入 `/etc/systemd/system/`  目录后，执行 `sudo systemctl enable kestrel-wepai.service`

如果启动失败使用以下命令查看日志：
`sudo journalctl -fu kestrel-webapi.service`

*注：如果开启防火墙记得让 8089通过防火墙*

+ 测试发布的应用是否可用
`curl http://192.168.1.110:8089`