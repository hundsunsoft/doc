# mysql安装与配置
MySQL 安装
所有平台的 MySQL 下载地址为： [MySQL 下载](https://dev.mysql.com/downloads/mysql/) 。 挑选你需要的 MySQL Community Server 版本及对应的平台。


安装前，我们可以检测系统是否自带安装 MySQL:

    [root@jus-zhan ~]# rpm -qa | grep mysql
    mysql-community-release-el7-5.noarch
    mysql-community-libs-5.6.42-2.el7.x86_64
    mysql-community-server-5.6.42-2.el7.x86_64
    mysql-community-client-5.6.42-2.el7.x86_64
    mysql-community-common-5.6.42-2.el7.x86_64
    [root@jus-zhan ~]# 



如果你系统有安装，那可以选择进行卸载:
> rpm -e mysql　　// 普通删除模式
> 网上有人用以上方法卸载，实际是不可以的。
> yum remove mysql
> yum remove是可以的
> 
我们再检查一下
> [root@jus-zhan ~]# rpm -qa | grep mysql
> mysql-community-release-el7-5.noarch
> mysql-community-libs-5.6.42-2.el7.x86_64
> mysql-community-common-5.6.42-2.el7.x86_64
> [root@jus-zhan ~]# 

发现仍然有顽固分子不肯投降，那么只能用强制命令了
> rpm -e --nodeps mysql　　// 强力删除模式，如果使用上面命令删除时，提示有依赖的其它文件，则用该命令可以对其进行强力删除
> [root@jus-zhan ~]# rpm -e --nodeps mysql-community-release-el7-5.noarch
> [root@jus-zhan ~]# rpm -e --nodeps mysql-community-libs-5.6.42-2.el7.x86_64
> [root@jus-zhan ~]# rpm -e --nodeps mysql-community-common-5.6.42-2.el7.x86_64
> [root@jus-zhan ~]# 
> [root@jus-zhan ~]# rpm -qa|grep mysql
> [root@jus-zhan ~]# 

下载并安装mysql-server，我们wget最新版本 mysql80
> wget https://repo.mysql.com/mysql80-community-release-el7-1.noarch.rpm
> rpm -ivh mysql80-community-release-el7-1.noarch.rpm
> yum update
> yum install mysql-server


> [root@jus-zhan ~]# wget https://repo.mysql.com/mysql80-community-release-el7-1.noarch.rpm
> --2018-12-06 23:36:35--  https://repo.mysql.com/mysql80-community-release-el7-1.noarch.rpm
> Resolving repo.mysql.com (repo.mysql.com)... 104.127.195.16
> Connecting to repo.mysql.com (repo.mysql.com)|104.127.195.16|:443... connected.
> HTTP request sent, awaiting response... 200 OK
> Length: 25820 (25K) [application/x-redhat-package-manager]
> Saving to: ‘mysql80-community-release-el7-1.noarch.rpm’
> 
> 100%[===============================================================================>] 25,820      --.-K/s   in 0.1s    
> 
> 2018-12-06 23:36:36 (173 KB/s) - ‘mysql80-community-release-el7-1.noarch.rpm’ saved [25820/25820]
> 
> [root@jus-zhan ~]# 
[root@jus-zhan ~]# rpm -ivh mysql80-community-release-el7-1.noarch.rpm
Preparing...                          ################################# [100%]
Updating / installing...
   1:mysql80-community-release-el7-1  ################################# [100%]
[root@jus-zhan ~]# 

[root@jus-zhan ~]# yum install mysql-server
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
Resolving Dependencies
--> Running transaction check
---> Package mysql-community-server.x86_64 0:8.0.13-1.el7 will be installed
--> Processing Dependency: mysql-community-common(x86-64) = 8.0.13-1.el7 for package: mysql-community-server-8.0.13-1.el7.x86_64
--> Processing Dependency: mysql-community-client(x86-64) >= 8.0.0 for package: mysql-community-server-8.0.13-1.el7.x86_64
--> Running transaction check
---> Package mysql-community-client.x86_64 0:8.0.13-1.el7 will be installed
--> Processing Dependency: mysql-community-libs(x86-64) >= 8.0.0 for package: mysql-community-client-8.0.13-1.el7.x86_64
---> Package mysql-community-common.x86_64 0:8.0.13-1.el7 will be installed
--> Running transaction check
---> Package mysql-community-libs.x86_64 0:8.0.13-1.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

=========================================================================================================================
 Package                             Arch                Version                    Repository                      Size
=========================================================================================================================
Installing:
 mysql-community-server              x86_64              8.0.13-1.el7               mysql80-community              381 M
Installing for dependencies:
 mysql-community-client              x86_64              8.0.13-1.el7               mysql80-community               26 M
 mysql-community-common              x86_64              8.0.13-1.el7               mysql80-community              554 k
 mysql-community-libs                x86_64              8.0.13-1.el7               mysql80-community              2.3 M

Transaction Summary
=========================================================================================================================
Install  1 Package (+3 Dependent packages)

Total download size: 410 M
Installed size: 1.8 G
Is this ok [y/d/N]:

[root@jus-zhan ~]# mysqladmin --version
mysqladmin  Ver 8.0.13 for Linux on x86_64 (MySQL Community Server - GPL)
[root@jus-zhan ~]# 

vi /etc/my.cnf
datadir=/home/mysql/data
重启mysqld服务
[root@jus-zhan mysql]# service mysqld restart
Redirecting to /bin/systemctl restart mysqld.service
[root@jus-zhan mysql]# 


强制杀进程
[root@jus-zhan mysql]# systemctl stop mysqld
[root@jus-zhan mysql]# ps -ef|grep mysql
zhandd   18728 18573  0 23:05 ?        00:00:00 /bin/sh /usr/bin/mysqld_safe
zhandd   19082 18728  0 23:05 ?        00:00:01 /usr/sbin/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib/mysql/plugin --log-error=/var/log/mysql/error.log --pid-file=/var/run/mysqld/mysqld.pid --socket=/var/run/mysqld/mysqld.sock --port=3306 --log-syslog=1 --log-syslog-facility=daemon --log-syslog-tag=
root     19398 18573  0 23:27 pts/0    00:00:00 su - mysql
root     19983 16361  0 23:52 pts/3    00:00:00 vi mysqld.log
root     20055 11356  0 23:54 pts/0    00:00:00 grep --color=auto mysql
[root@jus-zhan mysql]# kill -918728
-bash: kill: 918728: invalid signal specification
[root@jus-zhan mysql]# kill -9 18728
[root@jus-zhan mysql]# kill -9 19082
[root@jus-zhan mysql]# ps -ef|grep mysql
root     19398 18573  0 23:27 pts/0    00:00:00 su - mysql
root     19983 16361  0 23:52 pts/3    00:00:00 vi mysqld.log
root     20059 11356  0 23:55 pts/0    00:00:00 grep --color=auto mysql
[root@jus-zhan mysql]# 


cd /etc
rm -rf my.cnf

cd /var/lib
rm -rf mysql

yum remove mysql-server
yum install mysql-server

启动mysqld服务
[root@jus-zhan ~]# service mysqld status
Redirecting to /bin/systemctl status mysqld.service
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: inactive (dead)
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
[root@jus-zhan ~]# service mysqld start
Redirecting to /bin/systemctl start mysqld.service
[root@jus-zhan ~]# service mysqld status
Redirecting to /bin/systemctl status mysqld.service
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-12-07 00:13:05 CST; 3s ago
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
  Process: 3375 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
 Main PID: 3448 (mysqld)
   Status: "SERVER_OPERATING"
   CGroup: /system.slice/mysqld.service
           └─3448 /usr/sbin/mysqld

Dec 07 00:12:56 jus-zhan systemd[1]: Starting MySQL Server...
Dec 07 00:13:05 jus-zhan systemd[1]: Started MySQL Server.
[root@jus-zhan ~]# 


错误解决办法：
[root@jus-zhan ~]# mysqladmin version
mysqladmin: connect to server at 'localhost' failed
error: 'Access denied for user 'root'@'localhost' (using password: NO)'
[root@jus-zhan ~]#

mysqld --user=mysql --skip-grant-tables --skip-networking&

mysql就可以登录了

两种办法修改root密码
use mysql
方法1： 用SET PASSWORD命令

　　mysql -u root

　　mysql> SET PASSWORD FOR 'root'@'localhost' = PASSWORD('root');


方法2：用mysqladmin

　　mysqladmin -u root password "newpass"

　　如果root已经设置过密码，采用如下方法

　　mysqladmin -u root password oldpass "newpass"

    没有设置过，则：
    [root@jus-zhan etc]# mysqladmin password
New password: 
Confirm new password: 
Warning: Since password will be sent to server in plain text, use ssl connection to ensure password safety.
mysqladmin: 
You cannot use 'password' command as mysqld runs
 with grant tables disabled (was started with --skip-grant-tables).
Use: "mysqladmin flush-privileges password '*'" instead
[root@jus-zhan etc]# 



方法3： 用UPDATE直接编辑user表

　　mysql -u root

　　mysql> use mysql;

　　mysql> UPDATE user SET Password = PASSWORD('newpass') WHERE user = 'root';

　　mysql> FLUSH PRIVILEGES;



权限设置：
chown mysql:mysql -R /var/lib/mysql
初始化 MySQL：
mysqld --initialize
启动 MySQL：
systemctl start mysqld
查看 MySQL 运行状态：
systemctl status mysqld


验证 MySQL 安装
在成功安装 MySQL 后，一些基础表会表初始化，在服务器启动后，你可以通过简单的测试来验证 MySQL 是否工作正常。
使用 mysqladmin 工具来获取服务器状态：
使用 mysqladmin 命令俩检查服务器的版本, 在 linux 上该二进制文件位于 /usr/bin 目录，在 Windows 上该二进制文件位于C:\mysql\bin 。
[root@host]# mysqladmin --version
linux上该命令将输出以下结果，该结果基于你的系统信息：
mysqladmin  Ver 8.23 Distrib 5.0.9-0, for redhat-linux-gnu on i386
如果以上命令执行后未输入任何信息，说明你的Mysql未安装成功。
使用 MySQL Client(Mysql客户端) 执行简单的SQL命令
你可以在 MySQL Client(Mysql客户端) 使用 mysql 命令连接到 MySQL 服务器上，默认情况下 MySQL 服务器的登录密码为空，所以本实例不需要输入密码。
命令如下：
[root@host]# mysql
以上命令执行后会输出 mysql>提示符，这说明你已经成功连接到Mysql服务器上，你可以在 mysql> 提示符执行SQL命令：
mysql> SHOW DATABASES;
+----------+
| Database |
+----------+
| mysql    |
| test     |
+----------+
2 rows in set (0.13 sec)
Mysql安装后需要做的
Mysql安装成功后，默认的root用户密码为空，你可以使用以下命令来创建root用户的密码：
[root@host]# mysqladmin -u root password "new_password";
现在你可以通过以下命令来连接到Mysql服务器：
[root@host]# mysql -u root -p
Enter password:*******
注意：在输入密码时，密码是不会显示了，你正确输入即可。





































[root@jus-zhan ~]# wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
--2018-12-07 01:25:52--  http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
Resolving repo.mysql.com (repo.mysql.com)... 125.56.213.148
Connecting to repo.mysql.com (repo.mysql.com)|125.56.213.148|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 6140 (6.0K) [application/x-redhat-package-manager]
Saving to: ‘mysql-community-release-el7-5.noarch.rpm’

100%[===============================================================================>] 6,140       --.-K/s   in 0s      

2018-12-07 01:25:53 (376 MB/s) - ‘mysql-community-release-el7-5.noarch.rpm’ saved [6140/6140]

[root@jus-zhan ~]# 
[root@jus-zhan ~]# 
[root@jus-zhan ~]# rpm -ivh mysql-community-release-el7-5.noarch.rpm
Preparing...                          ################################# [100%]
Updating / installing...
   1:mysql-community-release-el7-5    ################################# [100%]
[root@jus-zhan ~]# 
[root@jus-zhan ~]# 
[root@jus-zhan ~]# yum update
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
mysql56-community                                                                                 | 2.5 kB  00:00:00     
No packages marked for update
[root@jus-zhan ~]# 
[root@jus-zhan ~]# yum install mysql-server
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
Resolving Dependencies
--> Running transaction check
---> Package mysql-community-server.x86_64 0:5.6.42-2.el7 will be installed
--> Processing Dependency: mysql-community-common(x86-64) = 5.6.42-2.el7 for package: mysql-community-server-5.6.42-2.el7.x86_64
--> Processing Dependency: mysql-community-client(x86-64) >= 5.6.10 for package: mysql-community-server-5.6.42-2.el7.x86_64
--> Running transaction check
---> Package mysql-community-client.x86_64 0:5.6.42-2.el7 will be installed
--> Processing Dependency: mysql-community-libs(x86-64) >= 5.6.10 for package: mysql-community-client-5.6.42-2.el7.x86_64
---> Package mysql-community-common.x86_64 0:5.6.42-2.el7 will be installed
--> Running transaction check
---> Package mysql-community-libs.x86_64 0:5.6.42-2.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

=========================================================================================================================
 Package                             Arch                Version                    Repository                      Size
=========================================================================================================================
Installing:
 mysql-community-server              x86_64              5.6.42-2.el7               mysql56-community               59 M
Installing for dependencies:
 mysql-community-client              x86_64              5.6.42-2.el7               mysql56-community               20 M
 mysql-community-common              x86_64              5.6.42-2.el7               mysql56-community              257 k
 mysql-community-libs                x86_64              5.6.42-2.el7               mysql56-community              2.0 M

Transaction Summary
=========================================================================================================================
Install  1 Package (+3 Dependent packages)

Total download size: 81 M
Installed size: 352 M
Is this ok [y/d/N]: y
Downloading packages:
(1/4): mysql-community-common-5.6.42-2.el7.x86_64.rpm                                             | 257 kB  00:00:00     
(2/4): mysql-community-libs-5.6.42-2.el7.x86_64.rpm                                               | 2.0 MB  00:00:00     
(3/4): mysql-community-client-5.6.42-2.el7.x86_64.rpm                                             |  20 MB  00:00:02     
(4/4): mysql-community-server-5.6.42-2.el7.x86_64.rpm                                             |  59 MB  00:00:09     
-------------------------------------------------------------------------------------------------------------------------
Total                                                                                    7.9 MB/s |  81 MB  00:00:10     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Warning: RPMDB altered outside of yum.
** Found 2 pre-existing rpmdb problem(s), 'yum check' output follows:
2:postfix-2.10.1-7.el7.x86_64 has missing requires of libmysqlclient.so.18()(64bit)
2:postfix-2.10.1-7.el7.x86_64 has missing requires of libmysqlclient.so.18(libmysqlclient_18)(64bit)
  Installing : mysql-community-common-5.6.42-2.el7.x86_64                                                            1/4 
  Installing : mysql-community-libs-5.6.42-2.el7.x86_64                                                              2/4 
  Installing : mysql-community-client-5.6.42-2.el7.x86_64                                                            3/4 
  Installing : mysql-community-server-5.6.42-2.el7.x86_64                                                            4/4 
  Verifying  : mysql-community-libs-5.6.42-2.el7.x86_64                                                              1/4 
  Verifying  : mysql-community-common-5.6.42-2.el7.x86_64                                                            2/4 
  Verifying  : mysql-community-client-5.6.42-2.el7.x86_64                                                            3/4 
  Verifying  : mysql-community-server-5.6.42-2.el7.x86_64                                                            4/4 

Installed:
  mysql-community-server.x86_64 0:5.6.42-2.el7                                                                           

Dependency Installed:
  mysql-community-client.x86_64 0:5.6.42-2.el7                mysql-community-common.x86_64 0:5.6.42-2.el7               
  mysql-community-libs.x86_64 0:5.6.42-2.el7                 

Complete!
[root@jus-zhan ~]# 


[root@jus-zhan ~]# mysqld --initialize
2018-12-07 01:27:48 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2018-12-07 01:27:48 0 [Note] mysqld (mysqld 5.6.42) starting as process 7027 ...
2018-12-07 01:27:48 7027 [ERROR] Fatal error: Please read "Security" section of the manual to find out how to run mysqld as root!

2018-12-07 01:27:48 7027 [ERROR] Aborting

2018-12-07 01:27:48 7027 [Note] Binlog end
2018-12-07 01:27:48 7027 [Note] mysqld: Shutdown complete

[root@jus-zhan ~]# 


解决办法：user=mysql，解决不能root启动的问题

vi /etc/my.cnf

【mysqld】
user=mysql

[root@jus-zhan ~]# mysqld --initialize
2018-12-07 01:31:18 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2018-12-07 01:31:18 0 [Note] mysqld (mysqld 5.6.42) starting as process 7255 ...
2018-12-07 01:31:18 7255 [Note] Plugin 'FEDERATED' is disabled.
mysqld: Table 'mysql.plugin' doesn't exist
2018-12-07 01:31:18 7255 [ERROR] Can't open the mysql.plugin table. Please run mysql_upgrade to create it.
2018-12-07 01:31:18 7255 [Note] InnoDB: Using atomics to ref count buffer pool pages
2018-12-07 01:31:18 7255 [Note] InnoDB: The InnoDB memory heap is disabled
2018-12-07 01:31:18 7255 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
2018-12-07 01:31:18 7255 [Note] InnoDB: Memory barrier is not used
2018-12-07 01:31:18 7255 [Note] InnoDB: Compressed tables use zlib 1.2.11
2018-12-07 01:31:18 7255 [Note] InnoDB: Using Linux native AIO
2018-12-07 01:31:18 7255 [Note] InnoDB: Using CPU crc32 instructions
2018-12-07 01:31:18 7255 [Note] InnoDB: Initializing buffer pool, size = 128.0M
2018-12-07 01:31:18 7255 [Note] InnoDB: Completed initialization of buffer pool
2018-12-07 01:31:18 7255 [Note] InnoDB: Highest supported file format is Barracuda.
2018-12-07 01:31:18 7255 [Note] InnoDB: 128 rollback segment(s) are active.
2018-12-07 01:31:18 7255 [Note] InnoDB: Waiting for purge to start
2018-12-07 01:31:18 7255 [Note] InnoDB: 5.6.42 started; log sequence number 1600607
2018-12-07 01:31:18 7255 [ERROR] mysqld: unknown option '--initialize'
2018-12-07 01:31:18 7255 [ERROR] Aborting

2018-12-07 01:31:18 7255 [Note] Binlog end
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'partition'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'PERFORMANCE_SCHEMA'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_SYS_DATAFILES'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_SYS_TABLESPACES'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_SYS_FOREIGN_COLS'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_SYS_FOREIGN'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_SYS_FIELDS'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_SYS_COLUMNS'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_SYS_INDEXES'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_SYS_TABLESTATS'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_SYS_TABLES'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_FT_INDEX_TABLE'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_FT_INDEX_CACHE'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_FT_CONFIG'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_FT_BEING_DELETED'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_FT_DELETED'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_FT_DEFAULT_STOPWORD'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_METRICS'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_BUFFER_POOL_STATS'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_BUFFER_PAGE_LRU'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_BUFFER_PAGE'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_CMP_PER_INDEX_RESET'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_CMP_PER_INDEX'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_CMPMEM_RESET'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_CMPMEM'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_CMP_RESET'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_CMP'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_LOCK_WAITS'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_LOCKS'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'INNODB_TRX'
2018-12-07 01:31:18 7255 [Note] Shutting down plugin 'InnoDB'
2018-12-07 01:31:18 7255 [Note] InnoDB: FTS optimize thread exiting.
2018-12-07 01:31:18 7255 [Note] InnoDB: Starting shutdown...
2018-12-07 01:31:20 7255 [Note] InnoDB: Shutdown completed; log sequence number 1600617
2018-12-07 01:31:20 7255 [Note] Shutting down plugin 'BLACKHOLE'
2018-12-07 01:31:20 7255 [Note] Shutting down plugin 'ARCHIVE'
2018-12-07 01:31:20 7255 [Note] Shutting down plugin 'MRG_MYISAM'
2018-12-07 01:31:20 7255 [Note] Shutting down plugin 'MyISAM'
2018-12-07 01:31:20 7255 [Note] Shutting down plugin 'MEMORY'
2018-12-07 01:31:20 7255 [Note] Shutting down plugin 'CSV'
2018-12-07 01:31:20 7255 [Note] Shutting down plugin 'sha256_password'
2018-12-07 01:31:20 7255 [Note] Shutting down plugin 'mysql_old_password'
2018-12-07 01:31:20 7255 [Note] Shutting down plugin 'mysql_native_password'
2018-12-07 01:31:20 7255 [Note] Shutting down plugin 'binlog'
2018-12-07 01:31:20 7255 [Note] mysqld: Shutdown complete

[root@jus-zhan ~]# 


mysql_upgrade
不行


datadir=/home/mysql/data
无法解决，不支持降级。






mysql太傻逼了，离线版安装
下载
mysql-5.7.21-linux-glibc2.12-x86_64.tar.gz

[mysql@jus-zhan ~]$ cd mysql-5.7.21-linux-glibc2.12-x86_64
[mysql@jus-zhan mysql-5.7.21-linux-glibc2.12-x86_64]$ cd bin
[mysql@jus-zhan bin]$ ./mysqld --initialize
2018-12-06T17:49:22.010636Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2018-12-06T17:49:22.010755Z 0 [Warning] 'NO_ZERO_DATE', 'NO_ZERO_IN_DATE' and 'ERROR_FOR_DIVISION_BY_ZERO' sql modes should be used with strict mode. They will be merged with strict mode in a future release.
2018-12-06T17:49:22.010759Z 0 [Warning] 'NO_AUTO_CREATE_USER' sql mode was not set.
2018-12-06T17:49:22.010869Z 0 [ERROR] Can't find error-message file '/usr/local/mysql/share/errmsg.sys'. Check error-message file location and 'lc-messages-dir' configuration directive.
2018-12-06T17:49:23.079002Z 0 [Warning] InnoDB: New log files created, LSN=45790
2018-12-06T17:49:23.203784Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2018-12-06T17:49:23.267575Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 440d2738-f97f-11e8-a017-00163e06537a.
2018-12-06T17:49:23.269862Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2018-12-06T17:49:23.270598Z 1 [Note] A temporary password is generated for root@localhost: A?4nH%onah/r
[mysql@jus-zhan bin]$


./mysqld
不行，杀进程后，重健data文件
[root@jus-zhan bin]# ./mysqld --initialize
2018-12-06T18:11:12.827794Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2018-12-06T18:11:12.827898Z 0 [Warning] 'NO_ZERO_DATE', 'NO_ZERO_IN_DATE' and 'ERROR_FOR_DIVISION_BY_ZERO' sql modes should be used with strict mode. They will be merged with strict mode in a future release.
2018-12-06T18:11:12.827902Z 0 [Warning] 'NO_AUTO_CREATE_USER' sql mode was not set.
2018-12-06T18:11:12.827998Z 0 [ERROR] Can't find error-message file '/usr/local/mysql/share/errmsg.sys'. Check error-message file location and 'lc-messages-dir' configuration directive.
2018-12-06T18:11:13.894953Z 0 [Warning] InnoDB: New log files created, LSN=45790
2018-12-06T18:11:14.022821Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2018-12-06T18:11:14.087732Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 515c6fb7-f982-11e8-8712-00163e06537a.
2018-12-06T18:11:14.089845Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2018-12-06T18:11:14.090506Z 1 [Note] A temporary password is generated for root@localhost: dgY&qMBt7=ei
[root@jus-zhan bin]# 

./mysqld --user=mysql --skip-grant-tables --skip-networking&
mysql 突然可以进了，又试了一遍，上面的命令可以直接进入mysql

1290错要刷新
mysql> select password from user;
ERROR 1054 (42S22): Unknown error 1054
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'root';
ERROR 1290 (HY000): Unknown error 1290
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!'; 
ERROR 1290 (HY000): Unknown error 1290
mysql> 
mysql> 
mysql> set password for 'root'@'localhost'=password('MyNewPass4!')
    -> ;
ERROR 1290 (HY000): Unknown error 1290
mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'root';
Query OK, 0 rows affected (0.00 sec)

mysql> 


flush privileges;

./mysqld_safe 启动起来完事。
mysql -u root -p
root

/etc/rc.d/init.d  是开机启动的
/usr/sbin  放执行码  mysqld  mysqld-debug
/usr/bin  放执行码  mysql





## 先找对版本
重新安装5.7.21
wget http://repo.mysql.com/yum/mysql-5.7-community/el/6/x86_64/mysql-community-client-5.7.21-1.el6.x86_64.rpm
从http://repo.mysql.com/yum/mysql-5.7-community/el/6/x86_64/上面挑选自己想安装的历史版本
wget http://repo.mysql.com/yum/mysql-5.7-community/el/6/x86_64/mysql-community-common-5.7.21-1.el6.x86_64.rpm
wget http://repo.mysql.com/yum/mysql-5.7-community/el/6/x86_64/mysql-community-devel-5.7.21-1.el6.x86_64.rpm
mysql-community-libs-5.7.21-1.el6.x86_64.rpm
mysql-community-server-5.7.21-1.el6.x86_64.rpm


删除历史版本
[root@jus-zhan ~]# yum remove mysql
Loaded plugins: fastestmirror
No Match for argument: mysql
No Packages marked for removal
[root@jus-zhan ~]# rpm -qa|grep mysql
mysql-community-release-el7-5.noarch
mysql-community-libs-5.6.42-2.el7.x86_64
mysql-community-common-5.6.42-2.el7.x86_64
[root@jus-zhan ~]# rpm -e mysql-community-release-el7-5.noarch
[root@jus-zhan ~]# rpm -e mysql-community-libs-5.6.42-2.el7.x86_64 mysql-community-common-5.6.42-2.el7.x86_64
error: Failed dependencies:
    libmysqlclient.so.18()(64bit) is needed by (installed) postfix-2:2.10.1-7.el7.x86_64
    libmysqlclient.so.18(libmysqlclient_18)(64bit) is needed by (installed) postfix-2:2.10.1-7.el7.x86_64
[root@jus-zhan ~]# rpm -e -nodeps mysql-community-libs-5.6.42-2.el7.x86_64 mysql-community-common-5.6.42-2.el7.x86_64
rpm: -nodeps: unknown option
[root@jus-zhan ~]# rpm -e --nodeps mysql-community-libs-5.6.42-2.el7.x86_64 mysql-community-common-5.6.42-2.el7.x86_64
[root@jus-zhan ~]# 



my.cnf
[mysqld]
datadir=/home/mysql/data1
socket=/home/mysql/mysql/mysql.sock

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

# Recommended in standard MySQL setup
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
user=mysql

[mysqld_safe]
log-error=/home/mysql/mysqld.log
pid-file=/home/mysql/mysqld.pid



[root@jus-zhan ~]# ls
get-docker.sh                                   mysql-community-devel-5.7.21-1.el6.x86_64.rpm
mysql-community-client-5.7.21-1.el6.x86_64.rpm  mysql-community-libs-5.7.21-1.el6.x86_64.rpm
mysql-community-common-5.7.21-1.el6.x86_64.rpm  mysql-community-server-5.7.21-1.el6.x86_64.rpm
[root@jus-zhan ~]# rpm -ivh mysql-community*rpm


rpm -ivh mysql57-community-release-el7-7.noarch.rpm

[root@jus-zhan ~]# rpm -qa|grep mysql
[root@jus-zhan ~]# rpm -ivh mysql57-community-release-el7-7.noarch.rpm
Preparing...                          ################################# [100%]
Updating / installing...
   1:mysql57-community-release-el7-7  ################################# [100%]
[root@jus-zhan ~]# yum install mysql-server
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
mysql57-community                                                                                 | 2.5 kB  00:00:00     
mysql57-community/x86_64/primary_db                                                               | 162 kB  00:00:00     
Resolving Dependencies
--> Running transaction check
---> Package mysql-community-server.x86_64 0:5.7.24-1.el7 will be installed
--> Processing Dependency: mysql-community-common(x86-64) = 5.7.24-1.el7 for package: mysql-community-server-5.7.24-1.el7.x86_64
--> Processing Dependency: mysql-community-client(x86-64) >= 5.7.9 for package: mysql-community-server-5.7.24-1.el7.x86_64
--> Running transaction check
---> Package mysql-community-client.x86_64 0:5.7.24-1.el7 will be installed
--> Processing Dependency: mysql-community-libs(x86-64) >= 5.7.9 for package: mysql-community-client-5.7.24-1.el7.x86_64
---> Package mysql-community-common.x86_64 0:5.7.24-1.el7 will be installed
--> Running transaction check
---> Package mysql-community-libs.x86_64 0:5.7.24-1.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

=========================================================================================================================
 Package                             Arch                Version                    Repository                      Size
=========================================================================================================================
Installing:
 mysql-community-server              x86_64              5.7.24-1.el7               mysql57-community              165 M
Installing for dependencies:
 mysql-community-client              x86_64              5.7.24-1.el7               mysql57-community               24 M
 mysql-community-common              x86_64              5.7.24-1.el7               mysql57-community              274 k
 mysql-community-libs                x86_64              5.7.24-1.el7               mysql57-community              2.2 M

Transaction Summary
=========================================================================================================================
Install  1 Package (+3 Dependent packages)

Total download size: 192 M
Installed size: 863 M
Is this ok [y/d/N]: y
Downloading packages:
(1/4): mysql-community-common-5.7.24-1.el7.x86_64.rpm                                             | 274 kB  00:00:00     
(2/4): mysql-community-libs-5.7.24-1.el7.x86_64.rpm                                               | 2.2 MB  00:00:00     
(3/4): mysql-community-client-5.7.24-1.el7.x86_64.rpm                                             |  24 MB  00:00:03     
(4/4): mysql-community-server-5.7.24-1.el7.x86_64.rpm                                             | 165 MB  00:00:20     
-------------------------------------------------------------------------------------------------------------------------
Total                                                                                    9.0 MB/s | 192 MB  00:00:21     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Warning: RPMDB altered outside of yum.
** Found 2 pre-existing rpmdb problem(s), 'yum check' output follows:
2:postfix-2.10.1-7.el7.x86_64 has missing requires of libmysqlclient.so.18()(64bit)
2:postfix-2.10.1-7.el7.x86_64 has missing requires of libmysqlclient.so.18(libmysqlclient_18)(64bit)
  Installing : mysql-community-common-5.7.24-1.el7.x86_64                                                            1/4 
  Installing : mysql-community-libs-5.7.24-1.el7.x86_64                                                              2/4 
  Installing : mysql-community-client-5.7.24-1.el7.x86_64                                                            3/4 
  Installing : mysql-community-server-5.7.24-1.el7.x86_64                                                            4/4 
  Verifying  : mysql-community-server-5.7.24-1.el7.x86_64                                                            1/4 
  Verifying  : mysql-community-libs-5.7.24-1.el7.x86_64                                                              2/4 
  Verifying  : mysql-community-common-5.7.24-1.el7.x86_64                                                            3/4 
  Verifying  : mysql-community-client-5.7.24-1.el7.x86_64                                                            4/4 

Installed:
  mysql-community-server.x86_64 0:5.7.24-1.el7                                                                           

Dependency Installed:
  mysql-community-client.x86_64 0:5.7.24-1.el7                mysql-community-common.x86_64 0:5.7.24-1.el7               
  mysql-community-libs.x86_64 0:5.7.24-1.el7                 

Complete!
[root@jus-zhan ~]#

[mysql@jus-zhan ~]$ mysqld --initialize
2018-12-06T23:47:16.578098Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2018-12-06T23:47:16.578203Z 0 [Warning] 'NO_ZERO_DATE', 'NO_ZERO_IN_DATE' and 'ERROR_FOR_DIVISION_BY_ZERO' sql modes should be used with strict mode. They will be merged with strict mode in a future release.
2018-12-06T23:47:16.578208Z 0 [Warning] 'NO_AUTO_CREATE_USER' sql mode was not set.
2018-12-06T23:47:17.610695Z 0 [Warning] InnoDB: New log files created, LSN=45790
2018-12-06T23:47:17.780074Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2018-12-06T23:47:17.844125Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 43e57d99-f9b1-11e8-bbf9-00163e06537a.
2018-12-06T23:47:17.846473Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2018-12-06T23:47:17.847121Z 1 [Note] A temporary password is generated for root@localhost: pSTq9byJGo;w
[mysql@jus-zhan ~]$ mysql -u root -p

systemctl start mysqld

[mysql@jus-zhan ~]$ systemctl status mysqld
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: deactivating (final-sigterm) (Result: exit-code)
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
  Process: 26500 ExecStart=/usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid $MYSQLD_OPTS (code=exited, status=1/FAILURE)
  Process: 26479 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
    Tasks: 14
   Memory: 184.5M
   CGroup: /system.slice/mysqld.service
           └─26504 /usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid


[mysql@jus-zhan ~]$ systemctl status mysqld
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: activating (start) since Fri 2018-12-07 07:51:02 CST; 853ms ago
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
  Process: 28100 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
  Control: 28121 (mysqld)
    Tasks: 2
   Memory: 18.4M
   CGroup: /system.slice/mysqld.service
           ├─28121 /usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid
           └─28124 /usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid
[mysql@jus-zhan ~]$

发现原来的mysqld没停止
停止服务
systemctl stop mysqld
再初始化
[root@jus-zhan ~]# mysqld --initialize
2018-12-06T23:55:04.972933Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2018-12-06T23:55:04.973023Z 0 [Warning] 'NO_ZERO_DATE', 'NO_ZERO_IN_DATE' and 'ERROR_FOR_DIVISION_BY_ZERO' sql modes should be used with strict mode. They will be merged with strict mode in a future release.
2018-12-06T23:55:04.973027Z 0 [Warning] 'NO_AUTO_CREATE_USER' sql mode was not set.
2018-12-06T23:55:06.029830Z 0 [Warning] InnoDB: New log files created, LSN=45790
2018-12-06T23:55:06.159083Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2018-12-06T23:55:06.255058Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 5b1753a4-f9b2-11e8-9d8b-00163e06537a.
2018-12-06T23:55:06.257563Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2018-12-06T23:55:06.258209Z 1 [Note] A temporary password is generated for root@localhost: qZeJnjgeZ2;J
[root@jus-zhan ~]#
[root@jus-zhan ~]# systemctl start mysqld
[root@jus-zhan ~]# 
[root@jus-zhan ~]# systemctl status mysqld
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-12-07 07:56:13 CST; 14s ago
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
  Process: 30489 ExecStart=/usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid $MYSQLD_OPTS (code=exited, status=0/SUCCESS)
  Process: 30465 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
 Main PID: 30492 (mysqld)
    Tasks: 27
   Memory: 187.9M
   CGroup: /system.slice/mysqld.service
           └─30492 /usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid

Dec 07 07:56:13 jus-zhan mysqld[30489]: 2018-12-06T23:56:13.599647Z 0 [Note] InnoDB: 5.7.24 started; log sequenc...591440
Dec 07 07:56:13 jus-zhan mysqld[30489]: 2018-12-06T23:56:13.600056Z 0 [Note] Plugin 'FEDERATED' is disabled.
Dec 07 07:56:13 jus-zhan mysqld[30489]: 2018-12-06T23:56:13.608393Z 0 [Note] InnoDB: Loading buffer pool(s) from...r_pool
Dec 07 07:56:13 jus-zhan mysqld[30489]: 2018-12-06T23:56:13.610127Z 0 [Note] InnoDB: Buffer pool(s) load complet...:56:13
Dec 07 07:56:13 jus-zhan mysqld[30489]: 2018-12-06T23:56:13.611514Z 0 [Warning] Failed to set up SSL because of ...te key
Dec 07 07:56:13 jus-zhan mysqld[30489]: 2018-12-06T23:56:13.611721Z 0 [Note] Server hostname (bind-address): '*'...: 3306
Dec 07 07:56:13 jus-zhan mysqld[30489]: 2018-12-06T23:56:13.611777Z 0 [Note] IPv6 is available.
Dec 07 07:56:13 jus-zhan mysqld[30489]: 2018-12-06T23:56:13.611790Z 0 [Note]   - '::' resolves to '::';
Dec 07 07:56:13 jus-zhan mysqld[30489]: 2018-12-06T23:56:13.611814Z 0 [Note] Server socket created on IP: '::'.
Dec 07 07:56:13 jus-zhan systemd[1]: Started MySQL Server.
Hint: Some lines were ellipsized, use -l to show in full.

密码进不去，我们用安全模式
[root@jus-zhan ~]# systemctl stop mysqld
[root@jus-zhan ~]#
./mysqld --user=mysql --skip-grant-tables --skip-networking&