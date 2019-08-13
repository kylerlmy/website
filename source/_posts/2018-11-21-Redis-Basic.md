---
title: Redis 基础配置
comments: true
categories: ASP.NET Core
tags: Redis
date: 2018-11-21
---

[Redis](https://en.wikipedia.org/wiki/Redis)

### What Redis
Redis（Remote Dictionary Server）实现分布式的内存数据结构项目，
具有可选耐久性的内存键值数据库。Redis最初是在开发一个实时的 web 日志分析器的时候，为了解决可扩展性的问题而被创建的。在使用传统数据库系统扩展某些类型的工作负载遇到重大问题的背景下 Salvaore 开发了第一个Redis版本。

### 与其他数据库系统的不同
Redis普遍认为系统可以同时考虑存储和缓存，使用一种设计，这种设计可以使得数据可以一直保存在计算机内存中，以被读取和修改，但是也能以一种不适合随机访问数据，但仅在系统重新启动后重新构建数据到内存中的格式保存到磁盘中。
与关系数据库管理系统（RDBMS）相比，Redis提供的数据模型非常不寻常，因为用户命令不描述数据库引擎要执行的查询，而是对给定的抽象数据执行的特定操作 因此，数据必须以稍后适合快速检索的方式存储，而无需来自数据库系统的辅助索引，聚合或传统RDBMS的其他常见特征的帮助。
Redis实现大量使用Fork（系统调用）来复制保存数据的进程，以便父进程继续为客户端提供服务，而子进程在磁盘上创建数据的副本。

### 数据类型
+ 字符串列表
+ 字符串集合（未重复，未排序的元素的集合）
+ 有序的字符串集合（通过一个称为分数的浮点数进行排序的非重复集合）
+ 哈希表，键和值都为字符串
+ 超重对数（HyperLogLogs）用于近似集基数大小估计
+ 消费者群体的条目流，允许您使用自动存储多个字段和字符串值，每个键是一个基于时间的序列。
+ 通过实现geohash技术的地理空间数据。

值的类型决定了可以对值应用哪些操作。Redis 支持高等级的、原子的、服务端的操作，例如相交、合并、比较集合和有序列表的不同，以及集合与有序集合的不同。
基于Redis Modules API支持更多数据类型，Redis 的模块 RedisJson 实现了将 Json 作为原生数据类型。

### 持久化
Redis通常将整个数据集保存在内存中。2.4 以上的版本可以配置为使用它们所称的虚拟内存，其中一些数据集存储在磁盘上，但不推荐使用此功能。在Redis 中可以同通过两种途径实现持久化。第一种是通过快照的方式，这种方式将数据集作为二进制转存定期的从内存异步传输到磁盘上，使用 Redis 的RDB转存文件格式。或者通过日记功能，将修改数据集的每个操作的记录添加到后台进程中的仅附加文件（AOF）中。Redis可以在后台重写仅附加的文件，以避免日志无限期增长。日记在1.1版中引入，通常被认为是更安全的方法。
默认情况下，Redis至少每2秒将数据写入文件系统，如果需要，可以使用更多或更少的健壮选项。如果在默认设置下一个完整系统出现故障，则只会丢失几秒钟的数据。

### 复制
Redis 支持主副本复制。来自任何 Redis 服务器的数据都可以复制到任意数量的副本。一个副本可能是另一个副本的主副本。这允许 Redis 实现单根复制树。Redis 副本可以配置为接受写入，允许实例之间有意和无意的不一致。发布/订阅功能已完全实现，因此副本的客户端可以订阅通道，并在复制树的任何位置接收发布到主服务器的完整消息。复制对于读取（但不是写入）可伸缩性或数据冗余非常有用。

### 性能
当不需要数据的持久性时，与考虑提交事务之前将每个更改写入磁盘的数据库系统相比，Redis的内存中性质使其能够表现良好。Redis 作为单个进程运行，并且在重写 AOF（仅附加文件）时是单线程或双线程的。因此，单个Redis实例不能使用诸如存储过程之类的任务的并行执行。

### redis Docker 启动
```shell
docker run -p 6379:6379 -v /home/kyle/myredis/conf/redis.conf:/usr/local/etc/redis/redis.conf --name redis01 -d  redis redis-server /usr/local/etc/redis/redis.conf

docker run 
-p 6379:6379
-v /home/kyle/myredis/conf/redis.conf:/usr/local/etc/redis/redis.conf 
--name redis01
-d  
redis redis-server /usr/local/etc/redis/redis.conf
```

### redis 命令行程序
Redis CLI 作为命令行程序的方式非常有利于脚本和某些类型的测试，但是大多人将会花费大部分时间在命令行的交互模式。

#### 修改主机名
+ `-h` 指定不同的主机名和IP地址
`root@334dc022c633:/data# redis-cli -h 192.168.1.110 -p 6379 ping PONG`

#### 修改密码
+ `-a` 修改密码
`root@334dc022c633:/data# redis-cli -a Netkyle ping PONG`

#### 向指定数据库进行操作
指定向第几个数据库进行操作
`root@334dc022c633:/data# redis-cli -n 5 set newkey 'kyle' ` 

#### 从其他程序中获取输入
```shell
root@334dc022c633:/usr/local/etc/redis# cat redis.conf 
#config
root@334dc022c633:/usr/local/etc/redis# redis-cli -x set foo < /usr/local/etc/redis/redis.conf 
OK
root@334dc022c633:/usr/local/etc/redis# redis-cli getrange foo 0 10
"#config\n"
root@334dc022c633:/usr/local/etc/redis# 
```
另一种方式：
```shell

$ cat /tmp/commands.txt
set foo 100
incr foo
append foo xxx
get foo
$ cat /tmp/commands.txt | redis-cli
OK
(integer) 101
(integer) 6
"101xxx"
```

#### 连续不断执行相同的命令
该功能由两个参数控制：`-r (count)` 和  `-i (delay)`，第一个参数指定执行多少次该命令，第二个参数配置在每个命令执行之间的时间间隔是多少，单位为秒，在使用`-r` 指定次数时，也可以将值指定为 -1，表示一直执行。

`root@334dc022c633:/data# redis-cli -n 6 -r 5 -i 5 INFO | grep rss_human`

`-n` 表示对 第几个数据库操作
`-r` 表示执行多少次
`-i` 表示等待多久执行下一条命令

#### 使用redis-cli大量插入数据
https://redis.io/topics/mass-insert

#### 数据输出
用时可能需要使用 redis-cli 快速导出数据到一个外部程序中，使用逗号分隔值（CSV）可以完成这个功能：
```shell
root@334dc022c633:/data# redis-cli -n 9 lpush mylist a b c d
(integer) 4
root@334dc022c633:/data# redis-cli -n 9 --csv lrange mylist 0 -1
"d","c","b","a"
```
目前，不可能像这样导出整个数据库，而只能用CSV输出运行单个命令。

#### 运行 Lua 脚本
redis-cli广泛支持使用Lua脚本编写新的 Lua 调试工具，但是，即使不使用调试器，与以交互方式将脚本键入shell或作为参数相比，您可以使用redis-cli以更舒适的方式运行文件中的脚本。
```shell
$ cat /tmp/script.lua
return redis.call('set',KEYS[1],ARGV[1])
$ redis-cli --eval /tmp/script.lua foo , bar
OK
```
https://redis.io/topics/ldb


### 交互模式
在交互模式下，用户在提示符下键入Redis命令。命令被发生到服务端、执行并，返回的回复被解析，并以更容易的方式呈现出来。
在运行CLI的交互模式时，不需要指定任何特殊的参数，仅仅在不提供任何的参数的加载就可以

#### 进入交互模式的方式
```shell
root@334dc022c633:/data# redis-cli 
127.0.0.1:6379> ping PONG
"PONG"
127.0.0.1:6379> select 8
OK
127.0.0.1:6379[8]> dbsize
(integer) 0
127.0.0.1:6379[8]> select 9
OK
127.0.0.1:6379[9]> dbsize
(integer) 1
```

#### 处理连接和重新连接
使用 connect 命令 ，通过指定你想要连接的 hostname 和 port来连接到不同的实例。
```shell
127.0.0.1:6379[9]> connect metal 6379
Could not connect to Redis at metal:6379: Name or service not known
```
一般情况下，当一个断开的连接被检测到，CLI  通常尝试重新连接，如果尝试连接失败，它将显示错误信息并进入断开连接的状态。

如下：
```shell
127.0.0.1:6379> debug restart
Could not connect to Redis at 127.0.0.1:6379: Connection refused
not connected> ping
PONG
127.0.0.1:6379> (now we are connected again)
```
当重新连接被执行，redis-cli 自动重新选择最后一个被选择的数据库。然而，所有关于连接的其他状态将丢失，例如处于事务的中间状态。
```shell
$ redis-cli
127.0.0.1:6379> multi
OK
127.0.0.1:6379> ping
QUEUED

( here the server is manually restarted )

127.0.0.1:6379> exec
(error) ERR EXEC without MULTI
```

#### 编辑、历史和结束
由于 redis-cli 使用linenoise线编辑库，它一直由编辑能力，没有依赖 libreadline 或者其他可选的类库。
为了避免一次又一次的重新输入，你可以访问命令执行的历史，通过使用上方向键和下方向键来查看。在重新启动CLI之间保留历史记录，这些内容被保存在用户 home目录下的 .rediscli_history 目录下，由HOME环境变量指定。同时 CLI 也支持 TAB 键。

#### 多次运行同一个命令
通过在要执行的命令前加上一个数字前缀，来实现多次运行同一个命令。
```shell
127.0.0.1:6379> 5 incr counter
(integer) 5
(integer) 6
(integer) 7
(integer) 8
(integer) 9
```
#### 获取 Redis 命令行的帮助
+ `help @<category>` 显示给定分类的命令。这些分类有：
@generic, @list, @set, @sorted_set, @hash, @pubsub, @transactions, @connection, @server, @scripting, @hyperloglog.
`127.0.0.1:6379[5]> help @list`

+ `help @<commandname>` 显示指定命令的帮助信息
`127.0.0.1:6379[5]> help set`

### 特殊操作模式
CLI会执行与Redis相关的其他辅助任务
+ 展示Redis Server运行状态的监控工具
+ 扫描Redis数据库以获取非常大的Key。
+ 具有模式匹配的关键空间扫描仪
+ 作为 Pub/Sub 客户端订阅通道
+ 监控 Redis 实例的命令执行
+ 以不同的方式检查 Redis Server 的延迟
+ 检查本地计算机的调度程序延迟
+ 在本地获取远程 Redis Server中的 RDB备份
+ 为了展示从站接受的数据，表现为为一个 Redis 从站
+ 模拟LRU工作负载以显示有关键命中的统计信息。
+ Lua调试器的客户端。

#### 持续状态模式
`root@334dc022c633:/data# redis-cli --stat`


[命令行参考](https://redis.io/topics/rediscli)


### Redis 配置
#### 配置命令格式
redis.conf文件包含许多指令，指令的格式非常简单：如下

#### 命令行配置
`keyword argument1 argument2 ... argumentN`
可以通过命令行传递参数，这种方式非常适合用于测试的目的。所使用的格式除了要在关键字前添加 `--` 前缀外，其他都与在 redis.conf 中使用的方式相同。
如：
`./redis-server --port 6380 --slaveof 127.0.0.1 6379`

#### 运行时配置
在 Redis 运行时，可以在不重启Redis 服务的情况下，对Redis进行重新的配置。也可以以编程方式查询当前的Redis 服务配置。
config set ：添加配置
config get：查询配置

#### 将 Redis 配置为一个缓存（cache）
如果仅仅打算使用 Redis 作为一个缓存，缓存中的每个 key 都有一个过期集间。可以使用以下配置
```shell
maxmemory 2mb
maxmemory-policy allkeys-lru
```
在上面的配置中，没必要在应用中再使用 EXPIRE 命令设置 Key的生存时间，因为只要达到2兆字节的内存限制，所有Key都将使用近似 LRU 算法逐出。基本上在这种配置中，Redis的行为与memcached类似。
[使用Redis作为 LRU缓存](https://redis.io/topics/lru-cache)

### Redis 持久化
有两种方式:
+ AOF (Append-only file)
启用该模式后，它会把每个操作都记录到系统文集里，如果Redistribution服务重启了，它会根据AOF这个文件重建整个数据集。这个文件会很快就变得很大，但是Redis 很聪明，它会使用里面最新版本的数据，并压缩文件到可控大小。**默认不开启**，通过修改 APPEND ONLU MODE 模块下的 appendonly 命令来修改。
+ RDB (Redis database file)
Redis 默认模式，它有点像数据库快照，创建一些时间点的数据，如果发生灾难，你可以从这些数据李进行恢复。当达到一些条件的时候，例如数据集里面再某段时间内新添加一定条目的数据，Redis 就会把数据做个快照，并写入RDB文件。
最佳实践时两者都使用，AOF 能够提升速度可可用性，RDB可以做灾难恢复。

### Master-Slave Replication 主从复制
1. 建立docker 网络
```shell
kyle@kyle-ubuntu:~$ docker network create --driver bridge redis-net
c6b8e810a4faadab8c2c8fe4c47bbd96e11fd31b667c8c92c210dde11f7b95d3
kyle@kyle-ubuntu:~$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
02ef9e8e0178        bridge              bridge              local
e02188c9f117        elastic             bridge              local
1ac6654efefb        host                host                local
d47e05c5fe88        none                null                local
c6b8e810a4fa        redis-net           bridge              local
kyle@kyle-ubuntu:~$ 
```
2. 修改 redis.conf 中绑定的IP地址：
将 bind 127.0.0.1 修改为 bind 0.0.0.0
3. 创建 master 容器
```shell
$ docker run -p 6380:6379 -v /home/kyle/master-redis/conf/redis.conf:/usr/local/etc/redis/redis.conf --name redis-master --network redis-net --rm redis redis-server /usr/local/etc/redis/redis.conf
```
其中 `home/kyle/master-redis/conf/redis.conf` 为本地创建的配置文件。
4. 参看 创建的网络 redis-net 的IP地址：
`docker network inspect redis-net`
```Json
 "Containers": {
  "32048c1124d9485d50dd795766492b210ff2374a9db3b9e2b0b0ecc5ff08d4a0": {
                "Name": "redis-master",
                "EndpointID": "3dc4868930deaae8206a3359064ecd8a5e50e29ef93069970723407bc3ba90e5",
                "MacAddress": "02:42:ac:13:00:02",
                "IPv4Address": "172.19.0.2/16",
                "IPv6Address": ""
            }
        }
```
5. 复制 redis.conf （/home/kyle/master-redis/conf/redis.conf），将文件名命名为 redis2.conf,并修改redis2.conf 的 Replication 部分里的 replicaof 命名
`# slaveof <masterip> <masterport>`
~~`slaveof 172.19.0.2 6380`~~
`slaveof 172.19.0.2 6379`
这里的IP地址要与master-redis的IP保持一致。
注意这里端口映射的时候 容器中的端口要使用 6379 ，我将端口设置为6380时，启动 从数据库 的容器时，抛出 *Error condition on socket for SYNC: Connection refused* 的错误
6. 运行 redis 的slave容器
```shell
kyle@kyle-ubuntu:~/master-redis/conf$ docker run -v /home/kyle/master-redis/conf/redis2.conf:/usr/local/etc/redis/redis2.conf --name slave-redis --network redis-net --rm redis redis-server /usr/local/etc/redis/redis2.conf
```
7. 测试主从是否工作正常
```shell
kyle@kyle-ubuntu:~/master-redis/conf$ docker exec -it redis-master redis-cli
127.0.0.1:6379> set name kyle
OK
127.0.0.1:6379> set age 25
OK
```
进入命令行，执行一些操作，查看主备机是否都执行了这些操作。

#### 设置密码
1. 主从复制的master通常需要设置密码，修改redis.conf文件，找到requirepass这部分，取消注释并设置密码：
2. 在slave的redis2.conf里，找到masterauth这部分，填写master的密码：
[参考文档-持久化](https://redis.io/topics/persistence)
[参考文档-主从复杂](https://redis.io/topics/replication)
[参考文档-应用](https://www.cnblogs.com/cgzl/p/10294175.html)

### Redis 命令
#### String 类型的命令操作
+ Set 和 Get
+ getset：设置后，将之前设置的值返回
+ incr,incrby 和 decr ,decrby：incr 自增，incrby 自增，可以指定增量；decr 自减，decrby ，自减，可以指定增量。
+ mset 和 mget：批量设置值与批量读取值
+ exists：判断 key 是否存在
+ del：删除 Key
+ type 检查类型
+ expire，ex，ttl：expire 设置 key的有效期，ex 在设置 key 的时候直接设置有效值；ttl 查看 key 还能存活多久。

### ASP.NET Core 中使用 Redis
[Redis 缓存](https://docs.microsoft.com/en-us/aspnet/core/performance/caching/distributed?view=aspnetcore-2.2#distributed-redis-cache)
[2.2版本及以上](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.caching.stackexchangeredis.rediscache?view=aspnetcore-2.2)
[2.2版本以以下](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.caching.redis.rediscache?view=aspnetcore-2.2)