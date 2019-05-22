---
title: docker实验室（一）--MySql8.0安装与配置centOS环境
date: 2019-04-02 01:02:12
tags: 
    - docker
    - mysql
categories: 
    - 教程
    - docker
---

# 1.准备环境
初次安装，准备一台干净的Linux。可以自建虚拟机，或者去阿里云等各大云计算服务提供商购买，选择按量付费，只需要一个鸡腿钱即可学会安装配置mysql。
# 2.下载
俗话说的好，泡最辣的妞，挨最毒的打，学最新的技术。我们下载最新的mysql8.0进行安装。使用wget下载

    wget https://repo.mysql.com/mysql80-community-release-el7-1.noarch.rpm

> [root@izj6chbxeno02a18c5vbmuz ~]# wget https://repo.mysql.com/mysql80-community-release-el7-1.noarch.rpm
> 
>--2018-12-07 09:21:59--  https://repo.mysql.com/mysql80-community-release-el7-1.noarch.rpm

> Resolving repo.mysql.com (repo.mysql.com)... 23.198.138.106
> Connecting to repo.mysql.com (repo.mysql.com)|23.198.138.106|:443... connected.
> 
> HTTP request sent, awaiting response... 200 OK
> Length: 25820 (25K) [application/x-redhat-package-manager]
> Saving to: ‘mysql80-community-release-el7-1.noarch.rpm’
> 
> 100%[===========================================================================================>] 25,820      --.-K/s   in 0s      
> 
> 2018-12-07 09:21:59 (197 MB/s) - ‘mysql80-community-release-el7-1.noarch.rpm’ saved [25820/25820]
> 
> [root@izj6chbxeno02a18c5vbmuz ~]# 
 
# 3.rpm安装

    rpm -ivh mysql80-community-release-el7-1.noarch.rpm

> [root@izj6chbxeno02a18c5vbmuz ~]# rpm -ivh mysql80-community-release-el7-1.noarch.rpm
> 
> warning: mysql80-community-release-el7-1.noarch.rpm: Header V3 DSA/SHA1 Signature, key ID 5072e1f5: NOKEY
> 
> Preparing...                          ################################# [100%]
> 
> Updating / installing...
>    1:mysql80-community-release-el7-1  ################################# [100%]
>    
> [root@izj6chbxeno02a18c5vbmuz ~]#

# 4.云更新与安装

    yum update
    yum install mysql-server

等待大概2~3分钟，安装好后先检测mysql安装是否有问题

    mysqladmin --version

> [root@izj6chbxeno02a18c5vbmuz ~]# mysqladmin --version
> 
> mysqladmin  Ver 8.0.13 for Linux on x86_64 (MySQL Community Server - GPL)
> 
> [root@izj6chbxeno02a18c5vbmuz ~]# 

# 5.启动服务
    systemctl start mysqld

查看服务状态， Active: active (running)表示成功启动
> [root@izj6chbxeno02a18c5vbmuz ~]# systemctl status mysqld
> 
> ● mysqld.service - MySQL Server
> 
>    Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
>    
>    Active: active (running) since Fri 2018-12-07 09:33:29 CST; 21s ago
>    
>      Docs: man:mysqld(8)
>      
>            http://dev.mysql.com/doc/refman/en/using-systemd.html
>            
>   Process: 2889 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
>   
>  Main PID: 2958 (mysqld)
>  
>    Status: "SERVER_OPERATING"
>    
>    CGroup: /system.slice/mysqld.service
>            └─2958 /usr/sbin/mysqld
> 
> Dec 07 09:33:21 izj6chbxeno02a18c5vbmuz systemd[1]: Starting MySQL Server...
> 
> Dec 07 09:33:29 izj6chbxeno02a18c5vbmuz systemd[1]: Started MySQL Server.
> 
> [root@izj6chbxeno02a18c5vbmuz ~]# 

# 6.登录mysql
尝试直接运行mysql登录

    mysql
显示报错
> [root@izj6chbxeno02a18c5vbmuz ~]# mysql
> 
> ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)

由于错误信息太常见了，只能查看log日志信息
> [root@izj6chbxeno02a18c5vbmuz ~]# cat /var/log/mysqld.log 
> 
> 2018-12-07T01:33:21.838041Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.13) initializing of server in progress as process 2910
> 
> 2018-12-07T01:33:24.879919Z 5 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: .aYpme(rj5c7
> 
> 2018-12-07T01:33:26.290412Z 0 [System] [MY-013170] [Server] /usr/sbin/mysqld (mysqld 8.0.13) initializing of server has completed
> 
> 2018-12-07T01:33:28.893966Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.13) starting as process 2958
> 
> 2018-12-07T01:33:29.438704Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
> 
> 2018-12-07T01:33:29.459354Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.13'  socket: '/var/lib/mysql/mysql.sock'  port: 3306  MySQL Community Server - GPL.
> 
> 2018-12-07T01:33:29.546100Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: '/var/run/mysqld/mysqlx.sock' bind-address: '::' port: 33060
> 
> [root@izj6chbxeno02a18c5vbmuz ~]#

日志没有Error错误日志，说明没有错误信息产生，仔细看第二行Note日志 **A temporary password is generated for root@localhost: .aYpme(rj5c7**倒是发现mysql的root用户临时密码一枚，**.aYpme(rj5c7**

尝试用临时密码登录root用户

    mysql -u root -p
此处需要键入临时密码
> [root@izj6chbxeno02a18c5vbmuz ~]# mysql -u root -p
> 
> Enter password: 
> 
> Welcome to the MySQL monitor.  Commands end with ; or \g.
> 
> Your MySQL connection id is 11
> 
> Server version: 8.0.13
> 
> Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
> 
> Oracle is a registered trademark of Oracle Corporation and/or its
> affiliates. Other names may be trademarks of their respective
> owners.
> 
> Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
> 
> mysql> 

可以登录成功，接下来修改root用户密码，以便下次登录使用。

    SET PASSWORD FOR 'root'@'localhost' = PASSWORD('root');

又有报错，让我们检查mysql版本，说sql语法错。
> mysql> SET PASSWORD FOR 'root'@'localhost' = PASSWORD('root');
> 
> ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'PASSWORD('root')' at line 1
> 
> mysql> 

不要慌，刷新一下再继续

> mysql> FLUSH PRIVILEGES;
> 
> ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.

很明显mysql太不友好了，报错提示一个接一个的来，我们只能按照提示用ALTER USER重置密码。

    ALTER USER USER() IDENTIFIED BY 'root';

继续报错
> mysql> ALTER USER USER() IDENTIFIED BY 'root';
> 
> ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
> 
> mysql> 

错误信息提示我们，密码不符合当前策略，我们改用复杂密码吧
尝试一次
> mysql> ALTER USER USER() IDENTIFIED BY 'root1234';
> 
> ERROR 1819 (HY000): Your password does not satisfy the current policy requirements

尝试二次
> mysql> ALTER USER USER() IDENTIFIED BY 'root1234!';
> 
> ERROR 1819 (HY000): Your password does not satisfy the current policy requirements

尝试三次
> mysql> ALTER USER USER() IDENTIFIED BY 'abc123456!';
> 
> ERROR 1819 (HY000): Your password does not satisfy the current policy requirements

好吧，我已经疯了
> mysql> ALTER USER USER() IDENTIFIED BY 'Difficult_password_123';
> 
> Query OK, 0 rows affected (0.11 sec)
> 
> mysql> FLUSH PRIVILEGES;

密码终于成功了，别忘了刷新用户权限FLUSH PRIVILEGES。
# 7.使用
用新密码登录

    mysql -u root -p
键入新密码登录后，创建数据库，创建数据表，插入数据，查询数据等操作都试试。
> mysql> create database testDB ;
> 
> Query OK, 1 row affected (0.04 sec)
> 
> mysql> create table t ( a int, b varchar(30) );
> 
> Query OK, 0 rows affected (0.05 sec)
> 
> mysql> insert into t values (1, '小王');
> 
> Query OK, 1 row affected (0.02 sec)
> 
> mysql> select * from t;
> 
> +------+--------+
> 
> | a    | b      |
> 
> +------+--------+
> 
> |    1 | 小王   |
> 
> +------+--------+
> 
> 1 row in set (0.00 sec)
> 
> mysql> 

使用起来还是挺方便的，不用配置my.cnf的datadir等参数，也不用担心字库的问题，妥妥的。mysql8.0确实做了许多优化与简化，此次安装mysql是较为顺利的一回。

安装完成后，试试各种功能，然后别忘记了释放实例哦，不然每天都会少个鸡腿钱的啦。

关于MySql及其相关技术文章会持续更新，这是第一篇，欢迎大家关注，点赞，分享，收藏，评论拍砖也可以。

----------------------
# 2019-04-06 更新 mysql登录权限问题
[root@VM_0_4_centos ~]# mysql -h 118.126.96.x -uroot -p
mysql: [Warning] Using a password on the command line interface can be insecure.
ERROR 1045 (28000): Access denied for user 'root'@'193.112.190.175' (using password: YES)

异地登录mysql报错，需要生成新用户并设置登录权限。

    mysql> CREATE USER 'test'@'%' IDENTIFIED BY     'Difficult_password1234';
    Query OK, 0 rows affected (0.03 sec)
    
    mysql> GRANT ALL ON *.* TO 'test'@'%' WITH GRANT OPTION;
    Query OK, 0 rows affected (0.02 sec)