---
title: centos安装mysql@5.7.40
date: 2022-12-29 15:14:51
tags: mysql
featured_image: https://www.bythewayer.com/img/mysql1.webp
---

> 根据网上的[一篇文章](https://juejin.cn/post/6845166890755555341#heading-12)修改而来，检验过了，安装过程丝滑顺利

### 1.下载 MySql yum 包

```bash
wget http://repo.mysql.com/mysql57-community-release-el7-10.noarch.rpm
```

### 2.安装 MySQL 源

```bash
rpm -Uvh mysql57-community-release-el7-10.noarch.rpm
```

### 3.安装 MySQL 服务端

![mysql-v](https://www.bythewayer.com/img/mysql-v.webp)

```bash
yum -y install mysql-community-server --nogpgcheck
```

### 4.启动 mySql

```bash
systemctl start mysqld.service
```

### 5.检查是否启动成功

```bash
systemctl status mysqld.service
```

### 6.获取临时密码

![mysql_password](https://www.bythewayer.com/img/mysql_password.webp)

```bash
grep 'temporary password' /var/log/mysqld.log
```

### 7.通过临时密码登录 MySQL，进行修改密码操作

![mysql_login](https://www.bythewayer.com/img/mysql_login.webp)

```bash
mysql -uroot -p
```

输入密码的时候 把临时密码输进入，即可成功登录 mysql

> 设置完密码之后后续阔以 mysql -uroot -p123456，直接把密码带在-p 后面，这样就可以一行实现登陆

### 8.全局修改 mysql 密码

```bash
mysql> set global validate_password_policy=0;

mysql> set global validate_password_length=1;

# 设置你想要的密码 记得存储好
ALTER USER 'root'@'localhost' IDENTIFIED BY 'yourpassword';
```

### 9.授权其他机器远程登录

```bash
mysql> GRANT ALL PRIVILEGES ON . TO 'root'@'%' IDENTIFIED BY 'yourpassword' WITH GRANT OPTION;

mysql> FLUSH PRIVILEGES;
```

### 10.开启开机自启动

需要先退出当前 mysql,在执行以下命令

```bash
systemctl enable mysqld

systemctl daemon-reload
```

### 11.设置 MySQL 的字符集为 UTF-8，令其支持中文

```bash
[mysql]
default-character-set=utf8

[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
default-storage-engine=INNODB
character_set_server=utf8

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

### 12.重启一下 MySQL,令配置生效

```bash
service mysqld restart
```

### 13.防火墙开放 3306 端口

```bash
# mysql开放3306端口
firewall-cmd --zone=public --add-port=3306/tcp --permanent
```

### 14.mysql 常用命令

```bash
查看mysql是否启动：service mysqld status

启动mysql：service mysqld start

停止mysql：service mysqld stop

重启mysql：service mysqld restart
```

### 15.mysql 的其他常用命令

- 首先，如果我们想断开客户端与服务器的连接并且关闭客户端的话，可以在 mysql>提示符后输入如下任意一个命令

```bash
quit
exit
\q
```

大多数情况下我们执行的是一些普通 SQL，这些 SQL 往往需要以**;**分号结束，比如我查询当前的时间：

```bash
>mysql: select now();
```

mysql 基本命令[mySql](https://bythewayer.com/learn/server/DataBase/1)
