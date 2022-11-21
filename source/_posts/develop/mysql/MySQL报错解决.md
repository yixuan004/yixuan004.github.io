---
title: MySQL报错解决
date: 2022-11-20 22:24:11
tags:
    - mysql
categories:
	- develop
---

这里记录MySQL一些报错的解决过程，主要以ERROR CODE作为标识

<!--more-->

# MySQL报错解决

## 1. 报错+状态码解决

### 1.1 [ERROR CODE 2003]

 **2003 - Can't connect to MySQL server on '10.109.246.188' (61 "Connection refused")**

reference: https://blog.csdn.net/kechuno/article/details/107122814

在经过上面的配置后，使用navicat远程连接访问可能会出现这个错误，通过如下方法进行解决

检查mysql是否运行

```shell
bupt@h9:~$ sudo systemctl status mysql.service
● mysql.service - MySQL Community Server
     Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2022-11-20 17:04:54 CST; 4h 53min ago
    Process: 214161 ExecStartPre=/usr/share/mysql/mysql-systemd-start pre (code=exited, status=0/SUCCESS)
   Main PID: 214169 (mysqld)
     Status: "Server is operational"
      Tasks: 39 (limit: 154203)
     Memory: 367.0M
     CGroup: /system.slice/mysql.service
             └─214169 /usr/sbin/mysqld

11月 20 17:04:53 h9 systemd[1]: Starting MySQL Community Server...
11月 20 17:04:54 h9 systemd[1]: Started MySQL Community Server.
```

检查监听端口

```shell
bupt@h9:~$ netstat -ln | grep mysql
unix  2      [ ACC ]     流        LISTENING     58584142 /var/run/mysqld/mysqlx.sock
unix  2      [ ACC ]     流        LISTENING     58584144 /var/run/mysqld/mysqld.sock
```

修改mysql的配置，主要是修改这个bind-address

```shell
bupt@h9:~$ cd /etc/mysql/mysql.conf.d/
bupt@h9:/etc/mysql/mysql.conf.d$ sudo vim mysqld.cnf
修改：bind-address = 0.0.0.0
```

重启mysql

```shell
bupt@h9:~$ service mysql restart
```

### 1.2 [ERROR CODE 1130]

**1130 - Host '10.28.232.177' is not allowed to connect to this MySQL server**

在解决上面2003的问题后，新出现了这个1130的错误，查看博客如下

reference: https://www.php.cn/mysql-tutorials-380131.html

连接服务器后，通过`show databases;`查看库

```shell
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)
```

进入`mysql`数据库，并`show tables;`显示数据库表

```shell
mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+------------------------------------------------------+
| Tables_in_mysql                                      |
+------------------------------------------------------+
| columns_priv                                         |
| component                                            |
| db                                                   |
| default_roles                                        |
| engine_cost                                          |
| func                                                 |
| general_log                                          |
| global_grants                                        |
| gtid_executed                                        |
| help_category                                        |
| help_keyword                                         |
| help_relation                                        |
| help_topic                                           |
| innodb_index_stats                                   |
| innodb_table_stats                                   |
| password_history                                     |
| plugin                                               |
| procs_priv                                           |
| proxies_priv                                         |
| replication_asynchronous_connection_failover         |
| replication_asynchronous_connection_failover_managed |
| replication_group_configuration_version              |
| replication_group_member_actions                     |
| role_edges                                           |
| server_cost                                          |
| servers                                              |
| slave_master_info                                    |
| slave_relay_log_info                                 |
| slave_worker_info                                    |
| slow_log                                             |
| tables_priv                                          |
| time_zone                                            |
| time_zone_leap_second                                |
| time_zone_name                                       |
| time_zone_transition                                 |
| time_zone_transition_type                            |
| user                                                 |
+------------------------------------------------------+
37 rows in set (0.00 sec)
```

查看user表中的数据：

```shell
mysql> select Host, User from user;
+-----------+------------------+
| Host      | User             |
+-----------+------------------+
| localhost | debian-sys-maint |
| localhost | mysql.infoschema |
| localhost | mysql.session    |
| localhost | mysql.sys        |
| localhost | root             |
+-----------+------------------+
5 rows in set (0.00 sec)
```

修改`User='root'`的数据

```shell
mysql> update user set Host='%' where User='root';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
mysql> select Host, User from user;
+-----------+------------------+
| Host      | User             |
+-----------+------------------+
| %         | root             |
| localhost | debian-sys-maint |
| localhost | mysql.infoschema |
| localhost | mysql.session    |
| localhost | mysql.sys        |
+-----------+------------------+
5 rows in set (0.00 sec)
```

最后刷新一下

```shell
mysql> flush privileges;
```

通过navicat测试，这时候可以连接，证明上述操作成功

![image-20221120222628018](http://yixuan004.oss-cn-hangzhou.aliyuncs.com/img/image-20221120222628018.png)
