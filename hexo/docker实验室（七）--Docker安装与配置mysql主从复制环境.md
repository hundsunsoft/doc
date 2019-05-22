---
title: docker实验室（七）--Docker安装与配置mysql主从复制环境
date: 2019-04-02 07:02:12
tags: 
    - mysql
categories: 
    - 教程
    - 实战
    - docker
---

# 1.mysql集群，主从复制
基于日志 binlog 的 主从配置

MySQL 同步操作通过 3 个线程实现，其基本步骤如下：

主服务器 将数据的更新记录到 二进制日志（Binary log）中，用于记录二进制日志事件，这一步由 主库线程 完成；

从库 将 主库 的 二进制日志 复制到本地的 中继日志（Relay log），这一步由 从库 I/O 线程 完成；

从库 读取 中继日志 中的 事件，将其重放到数据中，这一步由 从库 SQL 线程 完成。

docker 两种启动容器的方式。
# 2.Dockerfile配置
从docker远程仓库直接获取mysql:8.0的镜像，也可以先pull下来。

    docker pull mysql:8.0

#### my.cnf,配置log-bin 与 server-id，my.cnf配置:**server-id需要保持不一致**

    log-bin         = /var/run/mysqld/mysql-bin
    server-id       = 2
#### 2.DockerFile:

    FROM mysql:8.0
    
    MAINTAINER syLucky <sylucky2004@gmail.com>
    
    WORKDIR /root
    
    COPY config/* /tmp/
    
    RUN mv /tmp/my.cnf /etc/mysql/my.cnf && \
        mv /tmp/my.cnf.master /etc/mysql/my.cnf.master && \
        mv /tmp/my.cnf.slave1 /etc/mysql/my.cnf.slave1 && \
        mv /tmp/my.cnf.slave2 /etc/mysql/my.cnf.slave2 
    
    RUN chmod +x ~/start-mysql.sh && \
        chmod +x ~/stop-mysql.sh
    
    CMD [ "sh", "-c", "service ssh start; bash"]

#### 3.启动mysql主库容器：

    docker run -itd \
    -v "/home/docker/data/dbdata-master":/var/lib/mysql \
    -v "/home/docker/data":/usr/local/data \
    --net=bridge \
    -p 3306:3306 \
    --name mysql-master \
    --hostname mysql-master \
    -e MYSQL_ROOT_PASSWORD=Difficult_password1234 \
    -d mysql:8.0 \
    --character-set-server=utf8mb4 \
    --collation-server=utf8mb4_unicode_ci \
    --restart always

#### 4.从库容器：
**注意改从库的映射端口**

    docker run -itd \
    -v "/home/docker/data/dbdata-slave1":/var/lib/mysql \
    -v "/home/docker/data":/usr/local/data \
    --net=bridge \
    -p 3307:3306 \
    --name mysql-slave1 \
    --hostname mysql-slave1 \
    -e MYSQL_ROOT_PASSWORD=Difficult_password1234 \
    -d mysql:8.0 \
    --character-set-server=utf8mb4 \
    --collation-server=utf8mb4_unicode_ci \
    --restart always

#### 5.改了my.cnf需要重启容器

    docker stop mysql-master
    docker stop mysql-slave1
    docker stop mysql-slave2
    
    docker start mysql-master
    docker start mysql-slave1
    docker start mysql-slave2

#### 6.主库建立复制用户，分配slave权限

    CREATE USER 'repl'@'%' IDENTIFIED BY 'Difficult_password1234';
    GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';

#### 7.锁定主库，取得log-bin与快照

    FLUSH TABLES WITH READ LOCK;
    SHOW MASTER STATUS;

    # 两种方法选一种，一种是覆盖数据文件data，一种是导入模式
    # 选择导入模式，主库备份数据
    docker exec mysql-master mysqldump -u root '-pDifficult_password1234'     --all-databases --master-data > /home/docker/data/dbdump.db
    

#### 8.从库导入数据

    # 从库导入数据
    docker exec mysql-slave1 sh -c "mysql -u root '-pDifficult_password1234' < /usr    /local/data/dbdump.db"
    docker exec mysql-slave2 sh -c "mysql -u root '-pDifficult_password1234' < /usr    /local/data/dbdump.db"

#### 9.检查从库用repl用户登录主库

    mysql -h mysql-master -urepl '-pDifficult_password1234'
    
    root@mysql-slave1:/# mysql -hmysql-master -uroot '-pDifficult_password1234'
    mysql: [Warning] Using a password on the command line interface can be     insecure.
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 63
    Server version: 8.0.15 MySQL Community Server - GPL
    
    Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> 


#### 10.从库change master配置

    CHANGE MASTER TO
    MASTER_HOST='mysql-master',
    MASTER_USER='repl',
    MASTER_PASSWORD='Difficult_password1234',
    MASTER_LOG_FILE='mysql-bin.000003',
    MASTER_LOG_POS=1169;

MASTER_LOG_FILE填写图中File的值
MASTER_LOG_POS填写图中Position的值

#### 11.从库start slave
**需要等待从库成功连接主库**

    START SLAVE;
    SHOW SLAVE STATUS\G

    mysql> show slave status\G
    *************************** 1. row ***************************
                   Slave_IO_State: Waiting for master to send event
                      Master_Host: mysql-master
                      Master_User: repl
                      Master_Port: 3306
                    Connect_Retry: 60
                  Master_Log_File: mysql-bin.000003
              Read_Master_Log_Pos: 1169
                   Relay_Log_File: mysql-slave1-relay-bin.000002
                    Relay_Log_Pos: 322
            Relay_Master_Log_File: mysql-bin.000003
                 Slave_IO_Running: Yes
                Slave_SQL_Running: Yes
                  Replicate_Do_DB: 
              Replicate_Ignore_DB: 
               Replicate_Do_Table: 
           Replicate_Ignore_Table: 
          Replicate_Wild_Do_Table: 
      Replicate_Wild_Ignore_Table: 
                       Last_Errno: 0
                       Last_Error: 
                     Skip_Counter: 0
              Exec_Master_Log_Pos: 1169
                  Relay_Log_Space: 537
                  Until_Condition: None
                   Until_Log_File: 
                    Until_Log_Pos: 0
               Master_SSL_Allowed: No
               Master_SSL_CA_File: 
               Master_SSL_CA_Path: 
                  Master_SSL_Cert: 
                Master_SSL_Cipher: 
                   Master_SSL_Key: 
            Seconds_Behind_Master: 0
    Master_SSL_Verify_Server_Cert: No
                    Last_IO_Errno: 0
                    Last_IO_Error: 
                   Last_SQL_Errno: 0
                   Last_SQL_Error: 
      Replicate_Ignore_Server_Ids: 
                 Master_Server_Id: 1
                      Master_UUID: c7402f5d-3d08-11e9-9a24-0242ac130002
                 Master_Info_File: mysql.slave_master_info
                        SQL_Delay: 0
              SQL_Remaining_Delay: NULL
          Slave_SQL_Running_State: Slave has read all relay log; waiting for more     updates
               Master_Retry_Count: 86400
                      Master_Bind: 
          Last_IO_Error_Timestamp: 
         Last_SQL_Error_Timestamp: 
                   Master_SSL_Crl: 
               Master_SSL_Crlpath: 
               Retrieved_Gtid_Set: 
                Executed_Gtid_Set: 
                    Auto_Position: 0
             Replicate_Rewrite_DB: 
                     Channel_Name: 
               Master_TLS_Version: 
           Master_public_key_path: 
            Get_master_public_key: 0
    1 row in set (0.00 sec)
    
    mysql> 

#### 12.主库写，观察从库是否同步，需检查从库状况为 Waiting for master to send event。
**不要忘记关闭主库锁**

主库写：

    UNLOCK TABLES;
    mysql> create database testdb111;
    Query OK, 1 row affected (0.03 sec)
    
    mysql> show databases;
    +--------------------+
    | Database           |
    +--------------------+
    | information_schema |
    | mysql              |
    | performance_schema |
    | replicas_db        |
    | sys                |
    | testdb             |
    | testdb111          |
    +--------------------+
    7 rows in set (0.00 sec)
    
    mysql> 

从库读：

    mysql> show databases;
    +--------------------+
    | Database           |
    +--------------------+
    | information_schema |
    | mysql              |
    | performance_schema |
    | replicas_db        |
    | sys                |
    | testdb             |
    | testdb111          |
    +--------------------+
    7 rows in set (0.00 sec)
    
    mysql> 

# 3.docker-compose.yml配置
mysql主服务器：

    version: '2.3'
    services:
      mysql-master:
        image: mysql:8.0
      container_name: mysql-master
      environment:
        - "MYSQL_ROOT_PASSWORD=Difficult_password1234"
        - "MYSQL_DATABASE=replicas_db"
      links:
        - mysql-slave1
        - mysql-slave2
      ports:
        - "3306:3306"
      restart: always
      hostname: mysql-master
      volumes:
        - /home/docker/data/dbdata-master:/var/lib/mysql
        - /home/docker/data:/usr/local/data  

mysql从服务器:

    mysql-slave1:
      image: mysql:8.0
      container_name: mysql-slave1
      environment:
        - "MYSQL_ROOT_PASSWORD=Difficult_password1234"
        - "MYSQL_DATABASE=replicas_db"
      ports:
        - "3307:3306"
      restart: always
      hostname: mysql-slave1
      volumes:
        - /home/docker/data/dbdata-slave1:/var/lib/mysql
        - /home/docker/data:/usr/local/data


    mysql-slave2:
      image: mysql:8.0
      container_name: mysql-slave2
      environment:
        - "MYSQL_ROOT_PASSWORD=Difficult_password1234"
        - "MYSQL_DATABASE=replicas_db"
      ports:
        - "3308:3306"
      restart: always
      hostname: mysql-slave2
      volumes:
        - /home/docker/data/dbdata-slave1:/var/lib/mysql
        - /home/docker/data:/usr/local/data  

修改my.cnf和主从复制，同上，接着第5步继续往下做即可。

另外附上docker-compose的官方文档
https://docs.docker.com/compose/compose-file/compose-file-v2/

简书上有篇中文的写的不错
https://www.jianshu.com/p/2217cfed29d7

