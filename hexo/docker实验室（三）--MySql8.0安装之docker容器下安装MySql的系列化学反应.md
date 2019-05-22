---
title: docker实验室（三）--MySql8.0安装之docker容器下安装MySql的系列化学反应
date: 2019-04-02 03:02:12
tags: 
    - docker
    - mysql
categories: 
    - 教程
    - docker
---

不知道docker为何物或者不清楚docker安装使用的先去专栏了解
[Docker安装与配置--centOS环境（二）](https://zhuanlan.zhihu.com/p/51889991)

# ubuntu配置
首先，还是利用docker最常用的容器ubuntu

    docker run -i -t ubuntu /bin/bash

进入docker的ubuntu容器后，更新apt-get，安装必要的安装包vim，wget，net-tools。

    apt-get update
    apt-get install vim
    apt-get install wget
    apt-get install net-tools //使用 netstat命令需要添加

新增用户使用adduser，注意ubuntu下使用useradd是没有新增用户目录的

    adduser mysql

ubuntu不支持rpm安装，我们使用alien安装

    apt-get install alien

# mysql安装与启动

    alien -i package.rpm
    apt-get install mysql-server
    service mysql start

然后就可以正常登录了

    mysql
    use mysql;
    show tables;

修改root密码

    mysqladmin password
键入你需要修改的密码

然后用root就可以登录了

    mysql -u root -p

# mysql使用
登录mysql后，还可以新增mysql用户登录，这里我们新增一个用户test，密码test，并给予权限，具体操作如下

    use mysql
    insert into mysql.user(Host,User,Password) values("localhost","test",password("test"));
    create database testDB;
    CREATE USER 'test'@'localhost' IDENTIFIED BY 'test';
    flush privileges;
    grant all privileges on testDB.* to test@localhost identified by 'test';
    exit

使用新建的test用户登录试试，新建库，表等操作

    mysql -u test -p
    use testDB
    create table tt ( a int, b char(8));
    insert into tt values (1, "hello");
    insert into tt values (2, "mysql");
    mysql> select * from tt;
    +------+-------+
    | a    | b     |
    +------+-------+
    |    1 | hello |
    |    2 | mysql |
    +------+-------+
    2 rows in set (0.00 sec)
    
    mysql> 

# 解决中文问题

顺便尝试输入中文，发现中文根本输入不进去。查下系统编码

    show variables like '%character%';
    
    mysql> show variables like '%character%';
    +--------------------------+----------------------------+
    | Variable_name            | Value                      |
    +--------------------------+----------------------------+
    | character_set_client     | latin1                     |
    | character_set_connection | latin1                     |
    | character_set_database   | latin1                     |
    | character_set_filesystem | binary                     |
    | character_set_results    | latin1                     |
    | character_set_server     | latin1                     |
    | character_set_system     | utf8                       |
    | character_sets_dir       | /usr/share/mysql/charsets/ |
    +--------------------------+----------------------------+
    8 rows in set (0.00 sec)

全部改为utf8，注意filesystem不能动，不要问为什么

    set character_set_client=utf8;
    set character_set_connection=utf8;
    set character_set_database=utf8;
    set character_set_results=utf8;
    set character_set_server=utf8;

再次查下系统编码

    mysql> show variables like '%character%';;
    +--------------------------+----------------------------+
    | Variable_name            | Value                      |
    +--------------------------+----------------------------+
    | character_set_client     | utf8                       |
    | character_set_connection | utf8                       |
    | character_set_database   | utf8                       |
    | character_set_filesystem | binary                     |
    | character_set_results    | utf8                       |
    | character_set_server     | utf8                       |
    | character_set_system     | utf8                       |
    | character_sets_dir       | /usr/share/mysql/charsets/ |
    +--------------------------+----------------------------+
    8 rows in set (0.00 sec)

发现还是不行，我们尝试修改数据库编码

    mysql> ALTER DATABASE test CHARACTER SET utf8;
    ERROR 1044 (42000): Access denied for user 'test'@'localhost' to database 'test'
    mysql> exit

不让修改，用root用户试试

    mysql -u root -p
    mysql> ALTER DATABASE test CHARACTER SET utf8;
    Query OK, 1 row affected (0.00 sec)
    
    mysql> 
    

查看表tt的编码

    mysql> show create table tt;
    +-------+-------------------------------------------------------------------------------------------------------------------+
    | Table | Create Table                                                                                                      |
    +-------+-------------------------------------------------------------------------------------------------------------------+
    | tt    | CREATE TABLE `tt` (
      `a` int(11) DEFAULT NULL,
      `b` char(8) DEFAULT NULL
    ) ENGINE=InnoDB DEFAULT CHARSET=latin1 |
    +-------+-------------------------------------------------------------------------------------------------------------------+
    1 row in set (0.01 sec)
    
    mysql> 

有点小小的崩溃，使出失传已久的删库大法吧

    mysql> drop database testDB;
    Query OK, 1 row affected (0.08 sec)
    
    mysql> create database testDB DEFAULT CHARACTER SET utf8;
    Query OK, 1 row affected (0.00 sec)
    
    mysql> 
    mysql> show create database testDB;
    +----------+-----------------------------------------------------------------+
    | Database | Create Database                                                 |
    +----------+-----------------------------------------------------------------+
    | testDB   | CREATE DATABASE `testDB` /*!40100 DEFAULT CHARACTER SET utf8 */ |
    +----------+-----------------------------------------------------------------+
    1 row in set (0.00 sec)
    
    mysql> 

库的编码终于对了，重新建表

    mysql> create table tt ( a int, b varchar(30));
    Query OK, 0 rows affected (0.01 sec)
    
    mysql> 
    
    mysql> show create table tt;
    +-------+---------------------------------------------------------------------------------------------------------------------+
    | Table | Create Table                                                                                                        |
    +-------+---------------------------------------------------------------------------------------------------------------------+
    | tt    | CREATE TABLE `tt` (
      `a` int(11) DEFAULT NULL,
      `b` varchar(30) DEFAULT NULL
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
    +-------+---------------------------------------------------------------------------------------------------------------------+
    1 row in set (0.00 sec)
    
    mysql> 

最后查明，运行容器的时候要带上env编码参数，只能删掉容器

    docker rm mysql_test

以上步骤重新来一遍，运行的时候小心翼翼的带上参数

    docker run -i -t mysql_test env LANG=C.UTF-8 /bin/bash

或者硬核技术控，直接去改文件吧。
切换root用户。

    cd /var/lib/docker/containers

我们发现docker并不支持我们修改文件，很不友好，但是没关系，还记得上张截图的容器ID吗，拷贝出来备用，*号之前的部分替换成自己的容器ID

    ls ebc3d6ca6bd7*
    cd ebc3d6ca6bd7*
    vi config.v2.json

如图所示，两个地方加上env参数

    "Path":"env","Args":["LANG=C.UTF-8","/bin/bash"],
    "Cmd":["env","LANG=C.UTF-8","/bin/bash"],

然后重启容器

    [root@jus-zhan ~]# docker restart mysql_test
    mysql_test
    [root@jus-zhan ~]# 
    [root@jus-zhan ~]# docker attach mysql_test

进入容器，启动mysql

    root@ebc3d6ca6bd7:/# service mysql start
     * Starting MySQL database server mysqld                                                                              [ OK ] 
    root@ebc3d6ca6bd7:/# 

顺便设置mysql在docker容器开机启动

    update-rc.d mysql defaults

再次测试中文效果

    root@ebc3d6ca6bd7:~# mysql -u test -p
    Enter password: 
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 5
    Server version: 5.7.24-0ubuntu0.18.04.1 (Ubuntu)
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> use testDB
    Reading table information for completion of table and column names
    You can turn off this feature to get a quicker startup with -A
    
    Database changed
    mysql> insert into tt values ( 1, "小明");
    Query OK, 1 row affected (0.00 sec)
    
    mysql> 
    
    mysql> select * from tt;
    +------+--------+
    | a    | b      |
    +------+--------+
    |    1 | 小明   |
    +------+--------+
    1 row in set (0.00 sec)
    
    mysql> 

中文问题至此已经搞定

# mysql中文问题深究
但是很有研究精神的我们发现切换用户到mysql不能输入中文

    su - mysql

研究良久发现，应该是docker传给root的环境变量，在其他用户下是不起作用的，即使export LANG=C.UTF-8也不行，尝试用带root环境变量的su mysql解决

    su mysql
    mysql -u test -p
    mysql> insert into tt values (2, '小胖');
    Query OK, 1 row affected (0.00 sec)
    
    mysql> select * from tt;
    +------+--------+
    | a    | b      |
    +------+--------+
    |    1 | 小明   |
    |    2 | 小胖   |
    +------+--------+
    2 rows in set (0.00 sec)

猜想和实际验证结合，得出结论，只有root的环境变量支持输入中文，也算发现docker的一个不大不小的bug，相信用不了多久就可以修复的了。

最后，运行下面命令有惊喜

    docker run -it --name=mysql01 -e MYSQL_ROOT_PASSWORD="1234" -d mysql:8.0

实际运行情况

    [docker@jus-zhan ~]$ docker run -it --name=mysql01 -e MYSQL_ROOT_PASSWORD="1234" -d mysql:8.0
    Unable to find image 'mysql:8.0' locally
    8.0: Pulling from library/mysql
    f17d81b4b692: Already exists 
    c691115e6ae9: Already exists 
    41544cb19235: Already exists 
    254d04f5f66d: Already exists 
    4fe240edfdc9: Already exists 
    0cd4fcc94b67: Already exists 
    8df36ec4b34a: Already exists 
    720bf9851f6a: Already exists 
    e933e0a4fddf: Already exists 
    9ffdbf5f677f: Already exists 
    a403e1df0389: Already exists 
    4669c5f285a6: Already exists 
    Digest: sha256:811483efcd38de17d93193b4b4bc4ba290a931215c4c8512cbff624e5967a7dd
    Status: Downloaded newer image for mysql:8.0
    89c5d854305bb21c07f7ca8ecbc753e0c8e8967fb8a7e89e8d05f2b577630b9e
    [docker@jus-zhan ~]$ 

docker库里的是有mysql容器的，而且有最新的mysql8.0版本，可以直接运行，当docker在本地找不到的时候会去docker库里下载。
但是这个容器，系统自带mysql用户，但是没有目录，所以需要增加mysql目录

    mkdir /home/mysql

使用docker exec运行mysql服务器，并试试好不好用

    [docker@jus-zhan ~]$ docker exec -it mysql01 env LANG=C.UTF-8 /bin/bash
    root@89c5d854305b:/# mysql -u root -p
    Enter password: 
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 8
    Server version: 8.0.13 MySQL Community Server - GPL
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> create database testDB;
    Query OK, 1 row affected (0.10 sec)
    
    mysql> use testDB
    Database changed
    mysql> create table test( a int , b char(20));
    Query OK, 0 rows affected (0.13 sec)
    
    mysql> insert into test values(1,'小明');
    Query OK, 1 row affected (0.06 sec)
    
    mysql> select * from test;
    +------+--------+
    | a    | b      |
    +------+--------+
    |    1 | 小明   |
    +------+--------+
    1 row in set (0.00 sec)
    
    mysql> exit
    Bye
    root@89c5d854305b:/# 

和我们自己装的mysql一样的好用。

# mysql服务容器映射端口
下面用映射端口的办法，实现本地连接docker容器内数据库

    docker run -it --name=mysql02 -p=3306 -e MYSQL_ROOT_PASSWORD="1234" -v /storage/mysql02/datadir:/var/lib/mysql -d mysql:8.0
    docker exec -it mysql02 env LANG=C.UTF-8 /bin/bash
实际操作

    [docker@jus-zhan ~]$ docker run -it --name=mysql02 -p=3306 -e MYSQL_ROOT_PASSWORD="1234" -v /storage/mysql02/datadir:/var/lib/mysql -d mysql:8.0
    3d86c335b4f2638d9c5ff7c28513e3658be73f82f5242e7ebecfdad6dffd56a6
    [docker@jus-zhan ~]$ 
    [docker@jus-zhan ~]$ docker exec  -it mysql02 env LANG=C.UTF-8 /bin/bash
    root@3d86c335b4f2:/# mysql -u root -p
    Enter password: 
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 8
    Server version: 8.0.13 MySQL Community Server - GPL
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> exit
    Bye
    root@3d86c335b4f2:/# read escape sequence
    [docker@jus-zhan ~]$ docker port mysql02
    3306/tcp -> 0.0.0.0:32772
    [docker@jus-zhan ~]$
    [docker@jus-zhan ~]$ mysql -u root -p -h 127.0.0.1 -P 32772
    Enter password: 
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 11
    Server version: 8.0.13 MySQL Community Server - GPL
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> use testDB
    ERROR 1049 (42000): Unknown database 'testDB'
    mysql> 
    mysql> create database testDB
        -> ;
    Query OK, 1 row affected (0.12 sec)
    
    mysql> create table test(a int, b char(20));
    Query OK, 0 rows affected (0.07 sec)
    
    mysql> insert into test values(1,'小明');
    Query OK, 1 row affected (0.12 sec)
    
    mysql> select * from test;
    +------+--------+
    | a    | b      |
    +------+--------+
    |    1 | 小明   |
    +------+--------+
    1 row in set (0.00 sec)
    
    mysql> exit;
    
    [docker@jus-zhan ~]$ 


更新一下用户的密码，刷新权限

    ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
    FLUSH PRIVILEGES;
    
    mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
    Query OK, 0 rows affected (0.10 sec)
    
    mysql> FLUSH PRIVILEGES;
    Query OK, 0 rows affected (0.01 sec)
    
    mysql> 

修改加密规则，更新一下用户的密码，刷新权限

从windows用workbench远程登录也是可以的了。