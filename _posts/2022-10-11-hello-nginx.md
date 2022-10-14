---
layout: post
title: "你好 Nginx"
author: "Chuilee"
tags: 前端开发
excerpt_separator: <!--more-->
---

> 
> <!--more-->

1. ### nginx: [emerg] getgrnam("root") failed in /usr/local/nginx/./conf/nginx.conf:3

在nginx 文档中提到：
如果group 省略，就认为组名和用户名一样；

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190728105102135.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p0MTU3MzI2MjU4Nzg=,size_16,color_FFFFFF,t_70)

 在终端输入命令：

```sh
dscacheutil -q group | grep root
或者是 id root
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190728105425473.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p0MTU3MzI2MjU4Nzg=,size_16,color_FFFFFF,t_70)

 从查询结果可以看到root 用户属于 admin组，那我的写法就不对了，它找不到root组，所以需要改下配置文件：

```sh
user root admin;
```

  重启nginx ，问题解决！

  另： 在网上找到一种解决方案：将admin 换成owner ，问题也得到解决！
经测试，写对root 用户所属的组（比如 everyone 、wheel、staff）再启动也是正常的!
