---
layout: post
title: "Hello Redis!"
author: "Chuilee"
tags: Redis
excerpt_separator: <!--more-->
---

> Redis 与其他 key - value 缓存产品有以下三个特点：
>
> - Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
> - Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
> - Redis支持数据的备份，即master-slave模式的数据备份。

### 安装redis

**方法一**

```sh
sudo yum install -y redis
```

### redis服务后台运行reds-server

```sh
redis-server --daemonize yes
```

### 查看redis是否启动

```sh
ps aux | grep redis-server	
```

结果如下：

![image-20220811200317168](https://chuilee-1257187978.cos.ap-nanjing.myqcloud.com/blog/image-20220811200317168.png)

### redis如何设置密码

**方法一**

1. 找到redis的配置文件—redis.conf文件
2. 修改里面的requirepass，这个本来是注释起来了的，将注释去掉，并将后面对应的字段设置成自己想要的密码，保存退出。
3. 重启redis服务，即可。

**方法二**

连接redis之后，通过命令设置，如下：

```sh
config set requirepass 123456
```

查看密码

```sh
config get requirepass
```

