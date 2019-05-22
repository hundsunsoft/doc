---
title: docker实验室（四）--Docker安装与配置hadoop--centOS镜像
date: 2019-04-02 04:02:12
tags: 
    - docker
    - hadoop
categories: 
    - 教程
    - docker
---

# 1.下载centos镜像

可以直接运行centos，会自行下载一个centos镜像

    docker run --privileged -d -ti --name myhadoop -e     "container=docker"  -v /sys/fs/cgroup:/sys/fs/cgroup  centos  /usr/sbin/init

# 2.centos容器内安装必要软件

进入容器

    docker exec -it myhadoop /bin/bash

但是用privileged参数运行hadoop程序会造成宿主机器CPU被占用100%

所以在运行容器时，可以不用--privileged参数的尽量不用，用--cap-add参数替代。如果必须使用--privileged=true参数的，可以通过在宿主机和容器中执行以下命令将agetty关闭

    systemctl stop getty@tty1.service
    systemctl mask getty@tty1.service

更换启动参数

    docker run -tid --name myhadoop11 --cap-add NET_ADMIN centos /usr/sbin/init

更新yum，安装必要的软件

    yum update
    yum install net-tools
    yum install openssh-server.x86_64
    yum install openssh-clients.x86_64
    yum install which

# 3.设置sshd服务

## 配置与登录
passwd设置密码

    [root@526634ec2393 ~]# passwd
    Changing password for user root.
    New password: 
    BAD PASSWORD: The password is shorter than 8 characters
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    [root@526634ec2393 ~]# 

启动sshd服务

    [root@526634ec2393 ~]# systemctl start sshd
    [root@526634ec2393 ~]# ps -ef
    UID        PID  PPID  C STIME TTY          TIME CMD
    root         1     0  0 15:06 ?        00:00:02 /usr/lib/systemd/systemd     --system --deserialize 18
    root        22     1  0 15:06 ?        00:00:00 /usr/lib/systemd/    systemd-journald
    dbus        46     1  0 15:06 ?        00:00:00 /usr/bin/dbus-daemon --system     --address=systemd: --nofork --nopidfile --s
    root        55     1  0 15:06 ?        00:00:00 /usr/lib/systemd/systemd-logind
    root        58     1  0 15:06 console  00:00:00 /sbin/agetty --noclear     --keep-baud console 115200 38400 9600 vt220
    root       181     0  0 15:13 pts/1    00:00:00 /bin/bash
    root       267     1  0 15:18 ?        00:00:00 /usr/lib/systemd/systemd-udevd
    root       342     0  0 15:26 pts/2    00:00:00 /bin/bash
    root       469     1  0 15:37 ?        00:00:00 /usr/sbin/sshd -D
    root       470   181  0 15:37 pts/1    00:00:00 ps -ef
    [root@526634ec2393 ~]# 

ssh登录试试

    [root@526634ec2393 ~]# ssh root@localhost
    The authenticity of host 'localhost (127.0.0.1)' can't be established.
    ECDSA key fingerprint is SHA256:z7EXnUBWlbEWKcgdUULdlXtq0WJDEuqDIRYg8rKnBGI.
    ECDSA key fingerprint is MD5:a2:fa:0b:0d:03:e8:b0:dc:23:a6:db:45:5a:72:7a:c8.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
    root@localhost's password: 
    [root@526634ec2393 ~]#

另外，如果没用privileged参数，systemctl是不能使用的，还需要重新生成ssh主机密钥，然后用/usr/sbin/sshd启动sshd服务

    ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
    ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
    ssh-keygen -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key 
    ssh-keygen -t dsa -f /etc/ssh/ssh_host_ed25519_key

启动sshd服务

    /usr/sbin/sshd

试试登录效果

    [root@75e5348189a2 ~]# ssh root@localhost
    The authenticity of host 'localhost (127.0.0.1)' can't be established.
    ECDSA key fingerprint is SHA256:zMRc1D1B9VLpC5BbXwHZ6lXoJ2gHsQoEHr/NPegu1ok.
    ECDSA key fingerprint is MD5:56:0f:34:eb:56:09:a0:a8:f3:aa:7f:f4:72:b9:fa:df.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
    root@localhost's password: 
    [root@75e5348189a2 ~]#

## 免密登录

sshd启动后，设置免密码登录，还是需要先生成主机密钥

    ssh-keygen -t rsa

一路回车下去，然后拷贝公钥，需要输入一次密码

    [root@526634ec2393 ~]# ssh-copy-id -i .ssh/id_rsa.pub root@localhost
    /usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: ".ssh/id_rsa.pub"
    /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to     filter out any that are already installed
    /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are     prompted now it is to install the new keys
    root@localhost's password: 
    
    Number of key(s) added: 1
    
    Now try logging into the machine, with:   "ssh 'root@localhost'"
    and check to make sure that only the key(s) you wanted were added.
    
    [root@526634ec2393 ~]# 

试试免密码登录效果

    [root@526634ec2393 ~]# ssh root@localhost
    Last login: Sat Dec 15 15:45:17 2018 from localhost
    [root@526634ec2393 ~]# 

# 4.配置hadoop环境与jdk环境

安装软件hadoop与jdk，退出容器回到docker宿主机

    docker cp hadoop.tar.gz myhadoop:/root/

进入容器

    docker exec -it myhadoop /bin/bash

进入/root主目录，新建安装目录，解压软件包到目录，hadoop.tar.gz包含了hadoop程序和jdk

> hadoop-2.7.3.tar
> 
> jdk-8u144-linux-x64.tar.gz 

    cd ~
    mkdir training
    tar xvzf hadoop.tar.gz
    tar xvf hadoop-2.7.3.tar -C training/
    tar zxvf jdk-8u144-linux-x64.tar.gz -C training/

安装完后删除软件包，一会儿要制作镜像，删掉多余的东西

    rm *.tar *.tar.gz

修改环境变量

    vi .bash_profile

增加以下内容

    JAVA_HOME=/root/training/jdk1.8.0_144
    HADOOP_HOME=/root/training/hadoop-2.7.3
    export JAVA_HOME HADOOP_HOME
    
    PATH=$PATH.:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
    export PATH

使环境变量生效

    source .bash_profile

# 5.测试hadoop

    cd ~
    mkdir -p temp/input
    mkdir -p temp/output

temp/input下打开一个data.txt，输入一段文字

    vi temp/input/data.txt
可以直接输入以下内容

    I love Beijing
    I love China
    I am Chinese

# 6.单机模式启动hadoop

进入hadoop目录，运行一个example的wordcount试试

    cd training/hadoop-2.7.3/share/hadoop/mapreduce/
    hadoop jar hadoop-mapreduce-examples-2.7.3.jar wordcount ~/temp/input/data.txt ~/temp/output/wc/

查看结果

    [root@bigdata112 mapreduce]# cat ~/temp/output/wc/part-r-00000 
    Beijing 1
    China   1
    Chinese 1
    I   3
    am  1
    love    2

单机模式运行hadoop完成

# 7.制作镜像

    docker stop myhadoop
    docker commit -a "剑神一笑" -m "单机模式hadoop" myhadoop sylucky2004/myhadoop:v1.2

    [docker@jus-zhan ~]$ docker images
    REPOSITORY                    TAG                 IMAGE ID                CREATED             SIZE
    sylucky2004/myhadoop          v1.2                bef9c9dbac5f        9     seconds ago       1.12GB

然后登录docker hub，上传到我的镜像库，下回就可以直接用了

    [docker@jus-zhan ~]$ docker login
    Authenticating with existing credentials...
    WARNING! Your password will be stored unencrypted in /home/docker/.docker/    config.json.
    Configure a credential helper to remove this warning. See
    https://docs.docker.com/engine/reference/commandline/login/#credentials-store
    
    Login Succeeded
    [docker@jus-zhan ~]$ docker push sylucky2004/myhadoop:v1.2
    The push refers to repository [docker.io/sylucky2004/myhadoop]
    031a95d18e6c: Pushing [=====>                                             ]      104.5MB/923.8MB
    f972d139738d: Layer already exists
