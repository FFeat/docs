# 安装redis

由于官方推进安装在Linux系统中，并且Windows无官方支持。*（也没有必要，因为Win10+支持WSL）*
所以最终都是安装在Linux环境中。

## 在Linux上安装

如果是精简的发行版（*如Docke容器*），需要先安装`lsb-release`

``` sudo
sudo apt install lsb-release
```

如果已安装，直接Install就行了 .

将存储库添加到apt索引

``` sudo
curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/redis.list

```

更新包，然后安装

``` sudo
sudo apt-get update
sudo apt-get install redis
```

启动Redis服务器

``` sudo
sudo service redis-server start
```

测试服务是否正在运行

``` cli
redis-cli 
```

## 官方图形操作应用 RedisInsight

> 可视化和优化 Redis 数据

[下载地址](https://redis.com/redis-enterprise/redis-insight/)
[更新日志](https://github.com/RedisInsight/RedisInsight/releases)
