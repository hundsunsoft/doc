---
title: docker实验室（六）--Docker安装与配置hadoop全分布环境
date: 2019-04-02 06:02:12
tags: 
    - docker
    - hadoop
categories: 
    - 教程
    - docker
---

# 1.准备Dockfile文件
选择用Dockfile制作容器并配置HDFS分布式集群环境：

Dockfile

    FROM ubuntu:14.04
    
    MAINTAINER shuijun <ccmxsj@126.com>
    
    WORKDIR /root
    
    # install openssh-server, openjdk and wget
    RUN apt-get update && apt-get install -y openssh-server openjdk-7-jdk wget
    
    # install hadoop 2.7.2
    RUN wget https://archive.apache.org/dist/hadoop/core/    hadoop-2.7.2/hadoop-2.7.2.tar.gz && \
        tar -xzvf hadoop-2.7.2.tar.gz && \
        mv hadoop-2.7.2 /usr/local/hadoop && \
        rm hadoop-2.7.2.tar.gz
    
    # set environment variable
    ENV JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64 
    ENV HADOOP_HOME=/usr/local/hadoop 
    ENV PATH=$PATH:/usr/local/hadoop/bin:/usr/local/hadoop/sbin 
    
    # ssh without key
    RUN ssh-keygen -t rsa -f ~/.ssh/id_rsa -P '' && \
        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
    
    RUN mkdir -p ~/hdfs/namenode && \ 
        mkdir -p ~/hdfs/datanode && \
        mkdir $HADOOP_HOME/logs
    
    COPY config/* /tmp/
    
    RUN mv /tmp/ssh_config ~/.ssh/config && \
        mv /tmp/hadoop-env.sh /usr/local/hadoop/etc/hadoop/hadoop-env.sh && \
        mv /tmp/hdfs-site.xml $HADOOP_HOME/etc/hadoop/hdfs-site.xml && \ 
        mv /tmp/core-site.xml $HADOOP_HOME/etc/hadoop/core-site.xml && \
        mv /tmp/mapred-site.xml $HADOOP_HOME/etc/hadoop/mapred-site.xml && \
        mv /tmp/yarn-site.xml $HADOOP_HOME/etc/hadoop/yarn-site.xml && \
        mv /tmp/slaves $HADOOP_HOME/etc/hadoop/slaves && \
        mv /tmp/start-hadoop.sh ~/start-hadoop.sh && \
        mv /tmp/run-wordcount.sh ~/run-wordcount.sh
    
    RUN chmod +x ~/start-hadoop.sh && \
        chmod +x ~/run-wordcount.sh && \
        chmod +x $HADOOP_HOME/sbin/start-dfs.sh && \
        chmod +x $HADOOP_HOME/sbin/start-yarn.sh 
    
    # format namenode
    RUN /usr/local/hadoop/bin/hdfs namenode -format
    
    CMD [ "sh", "-c", "service ssh start; bash"]

# 2.build-image.sh脚本

运行脚本sh build-image.sh获取镜像

    #!/bin/bash
    echo -e "\nbuild docker hadoop image\n"
    ###tanzhou/hadoop:1.0,这个可以改成你自己的名称，改完之后，start.sh脚本里面启动的镜像    就要改成####自己的这个镜像名称，注意
    docker build -t tanzhou/hadoop:1.0 .
    echo -e "\n镜像打包成功,镜像名称是tanzhou/hadoop:1.0"

# 3.启动start.sh

运行sh start.sh启动容器，默认启动2个datanode，可以改脚本多启动几个

    #!/bin/bash
    # the default node number is 3
    N=${1:-3}    
    
    # start hadoop master container
    docker rm -f hadoop-master &> /dev/null
    echo "start hadoop-master container..."
    docker run -itd \
                    --net=bridge \
                    -p 50070:50070 \
                    -p 8088:8088 \
                    --name hadoop-master \
                    --hostname hadoop-master \
                    tanzhou/hadoop:1.0 &> /dev/null
    
    
    # start hadoop slave container
    i=1
    while [ $i -lt $N ]
    do
        docker rm -f hadoop-slave$i &> /dev/null
        echo "start hadoop-slave$i container..."
        docker run -itd \
                        --net=bridge \
                        --name hadoop-slave$i \
                        --hostname hadoop-slave$i \
                        tanzhou/hadoop:1.0 &> /dev/null
        i=$(( $i + 1 ))
    done 
    
    # get into hadoop master container
    docker exec -it hadoop-master bash

# 4.修改hosts

通过以下命令分别进入主从服务器，修改hosts

    docker exec -it hadoop-master bash
    docker exec -it hadoop-slave1 bash
    docker exec -it hadoop-slave2 bash

hosts文件目录为/etc/hosts，增加集群中其他服务器的hostname，这里需要查看其他服务器的ip

    172.17.0.2      hadoop-master
    172.17.0.3      hadoop-slave1
    172.17.0.4      hadoop-slave2

# 5.start-hadoop.sh

进入hadoop-master的容器的根目录下执行 sh start-hadoop.sh启动hadoop

    #!/bin/bash
    
    # the default node number is 3
    N=${1:-3}    
    
    # start hadoop master container
    docker rm -f hadoop-master &> /dev/null
    echo "start hadoop-master container..."
    docker run -itd \
                    --net=bridge \
                    -p 50070:50070 \
                    -p 8088:8088 \
                    --name hadoop-master \
                    --hostname hadoop-master \
                    tanzhou/hadoop:1.0 &> /dev/null
    
    
    # start hadoop slave container
    i=1
    while [ $i -lt $N ]
    do
            docker rm -f hadoop-slave$i &> /dev/null
                    echo "start hadoop-slave$i container..."
            docker run -itd \
                            --net=bridge \
                            --name hadoop-slave$i \
                            --hostname hadoop-slave$i \
                            tanzhou/hadoop:1.0 &> /dev/null
            i=$(( $i + 1 ))
    done
    
    # get into hadoop master container
    docker exec -it hadoop-master bash

要注意看日志，是否有报错的地方，及时排查。

    root@hadoop-master:~# sh start-hadoop.sh 
    -e 
    
    Starting namenodes on [hadoop-master]
    hadoop-master: Warning: Permanently added 'hadoop-master,172.17.0.2' (ECDSA)     to the list of known hosts.
    hadoop-master: starting namenode, logging to /usr/local/hadoop/logs/    hadoop-root-namenode-hadoop-master.out
    hadoop-slave4: ssh: Could not resolve hostname hadoop-slave4: Name or service     not known
    hadoop-slave3: ssh: Could not resolve hostname hadoop-slave3: Name or service     not known
    hadoop-slave2: Warning: Permanently added 'hadoop-slave2,172.17.0.4' (ECDSA)     to the list of known hosts.
    hadoop-slave1: Warning: Permanently added 'hadoop-slave1,172.17.0.3' (ECDSA)     to the list of known hosts.
    hadoop-slave1: starting datanode, logging to /usr/local/hadoop/logs/    hadoop-root-datanode-hadoop-slave1.out
    hadoop-slave2: starting datanode, logging to /usr/local/hadoop/logs/    hadoop-root-datanode-hadoop-slave2.out
    Starting secondary namenodes [0.0.0.0]
    0.0.0.0: starting secondarynamenode, logging to /usr/local/hadoop/logs/    hadoop-root-secondarynamenode-hadoop-master.out
    -e 
    
    starting yarn daemons
    starting resourcemanager, logging to /usr/local/hadoop/logs/    yarn--resourcemanager-hadoop-master.out
    hadoop-slave4: ssh: Could not resolve hostname hadoop-slave4: Name or service     not known
    hadoop-slave3: ssh: Could not resolve hostname hadoop-slave3: Name or service     not known
    hadoop-slave2: Warning: Permanently added 'hadoop-slave2,172.17.0.4' (ECDSA)     to the list of known hosts.
    hadoop-slave1: Warning: Permanently added 'hadoop-slave1,172.17.0.3' (ECDSA)     to the list of known hosts.
    hadoop-slave2: starting nodemanager, logging to /usr/local/hadoop/logs/    yarn-root-nodemanager-hadoop-slave2.out
    hadoop-slave1: starting nodemanager, logging to /usr/local/hadoop/logs/    yarn-root-nodemanager-hadoop-slave1.out
    -e

# 6.jps查看进程

hadoop-master进程

hadoop-slave1进程

hadoop-slave2进程

# 7.run-wordcount.sh

如果启动了容器，用下面的脚本测试下

    sh run-wordcount.sh

运行成功

# 8.网页查看

到网页上看看我们的系统
通过IP:50070登录，可以查看文件系统以及节点状况

通过IP:8088登录，可以查看任务执行情况