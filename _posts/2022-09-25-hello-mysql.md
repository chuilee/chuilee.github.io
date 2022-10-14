---
layout: post
title: "你好 mysql"
author: "Chuilee"
tags: 数据库
excerpt_separator: <!--more-->


---

> mysql数据库
> <!--more-->

#### 修改mysql数据库远程访问权限

```mssql
use mysql;
select host from user where user='root';  // 查看用户表信息
update user set host = '%' where user ='root'; // 更新用户表信息
flush privileges; // 刷新配置信息
```

### MySQL错误：ERROR 1175: You are using safe update mode 解决方法

```sql
show variables like 'sql_safe%'; // SQL_SAFE_UPDATES有两个取值0和1， 或ON 和OFF;
```

![image-20221010114604408](/Users/chuilee/Library/Application Support/typora-user-images/image-20221010114604408.png)

```sql
set sql_safe_updates=0;
set sql_safe_updates=off;
```

**更改只在当前生效，退出mysql，再次登录后恢复为默认。**

