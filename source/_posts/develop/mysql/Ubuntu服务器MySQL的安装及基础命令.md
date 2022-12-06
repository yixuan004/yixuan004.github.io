---
title: Ubuntu服务器MySQL的安装及基础命令
date: 2022-11-20 20:24:07
tags:
    - mysql
categories:
	- develop
	- mysql
---

背景：之前只基本在服务器上使用mysql，也曾经尝试过安装但是没有整理笔记，这次顺着项目部署，整理一下笔记

<!--more-->

# Ubuntu服务器MySQL的安装&基础命令&BUG

## 1. 操作

### 1.1 安装mysql

进入root用户， 执行如下命令

```shell
sudo apt-get install mysql-server mysql-client
```

成功安装后，系统会有如下输出

```shell
done!
update-alternatives: 使用 /var/lib/mecab/dic/ipadic-utf8 来在自动模式中提供 /var/lib/mecab/dic/debian (mecab-dictionary)
正在设置 mysql-server-8.0 (8.0.31-0ubuntu0.20.04.1) ...
update-alternatives: 使用 /etc/mysql/mysql.cnf 来在自动模式中提供 /etc/mysql/my.cnf (my.cnf)
Renaming removed key_buffer and myisam-recover options (if present)
mysqld will log errors to /var/log/mysql/error.log
mysqld is running as pid 190567
Created symlink /etc/systemd/system/multi-user.target.wants/mysql.service → /lib/systemd/system/mysql.service.
```

这里不会有密码的显示，直接使用`sudo mysql`命令进入`mysql>`命令行管理界面，再之后，通过一系列的命令修改mysql root的密码，修改完成后不要忘记restart

```shell
bupt@h9:~$ sudo mysql
# 选择管理user表的数据库
mysql> use mysql;

# 将authentication_string 置空
mysql> update user set authentication_string='' where user='root';

# 将plugin改为以前版本的密码认证方式
mysql> update user set plugin='mysql_native_password' where user='root';

# 刷新
mysql> FLUSH PRIVILEGES;

# 修改密码
mysql> alter user 'root'@'localhost' identified by 'newpassword';

# 重启MySQL
bupt@h9:~$ service mysql restart
```

在之后，即可使用`mysql -u root -p`命令登录管理界面，注意这里使用其他用户也是可以登录的

```shell
(base) lyx@h9:~$ mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 15
Server version: 8.0.31-0ubuntu0.20.04.1 (Ubuntu)

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```

### 1.2 查看mysql的端口

```shell
mysql> show global variables like 'port';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| port          | 3306  |
+---------------+-------+
1 row in set (0.00 sec)
```

## 2. 使用Navicat连接数据库

这里需要解决一些报错码的配置，详见另外一篇博客：**MySQL报错解决**

如下配置，即可使用Navicat连接数据库，主要注意是选择**常规**这个tab

![image-20221120223114869](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221120223114869.png)



