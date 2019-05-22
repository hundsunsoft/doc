---
title: docker实验室（八）--Docker使用flume+kafka_zookeeper框架实现日志采集
date: 2019-04-02 08:02:12
tags: 
    - flume
    - kafka
    - zookeeper
categories: 
    - 教程
    - 实战
    - docker
---

# docker实验室（八）--Docker使用flume+kafka_zookeeper框架实现日志采集

    version: '2'
    services:
      zookeeper:
        image: wurstmeister/zookeeper
        ports:
          - "2181:2181"
        container_name: zookeeper
        restart: always
    
      kafka:
        image: wurstmeister/kafka
        hostname: kafka
        ports:
          - "9092:9092"
        depends_on:
          - zookeeper
        environment:
        KAFKA_ADVERTISED_HOST_NAME: kafka
          KAFKA_ADVERTISED_PORT: 9092
    #      KAFKA_ADVERTISED_LISTENERS:     PLAINTEXT://kafka:9092,PLAINTEXT_HOST://localhost:9092
          KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092
          KAFKA_LISTENERS: PLAINTEXT://kafka:9092
          KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
          KAFKA_CREATE_TOPICS: "refrigerator-raw:1:1,smart-couch-raw:1:1,smart-watc    h-raw:1:1,car-fuel-raw:1:1"
          KAFKA_HEAP_OPTS: -Xmx512m -Xms512m
        volumes:
          - ./docker.sock:/var/run/docker.sock
        container_name: kafka
    #    links:
    #      - zookeeper
        restart: always
    
    flume:
    #    image: bde2020/flume:latest
        image: sylucky2004/flume:1.2
        depends_on:
          - zookeeper
          - kafka
        command: "bash -c /app/bin/flume-init"
        volumes:
          - ./flume_data:/var/lib/bde/flume/sc6/budgets
          - ./flume_a1:/usr/local/a1
    #    environment:
    #      - "constraint:node==flume"  
        container_name: flume
        restart: always


3.2.1、数据采集：采集实时产生的数据到kafka集群
思路：
a) 配置kafka，启动zookeeper和kafka集群；
b) 创建kafka主题；
c) 启动kafka控制台消费者（此消费者只用于测试使用）；
d) 配置flume，监控日志文件；
e) 启动flume监控任务；
f) 运行日志生产脚本；
g)、观察测试。


1) 配置kafka
2) 启动zookeeper
zkServer.sh start
2_1)启动kafka集群
/opt/module/kafka/bin/kafka-server-start.sh /opt/module/kafka/config/server.properties




3) 创建kafka主题




检查一下是否创建主题成功：
/opt/module/kafka/bin/kafka-topics.sh --zookeeper bigdata11:2181 --list



4) 启动kafka控制台消费者，等待flume信息的输入



5) 配置flume(flume-kafka.conf)


    # define
    a1.sources = r1
    a1.sinks = k1
    a1.channels = c1
    
    # source +0是从第零行开始
    
    a1.sources.r1.type = exec
    a1.sources.r1.command = tail -F -c +0 /opt/jars/calllog.csv
    a1.sources.r1.shell = /bin/bash -c
    
    # sink
    a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink
    a1.sinks.k1.brokerList = bigdata11:9092,bigdata12:9092,bigdata13:9092
    a1.sinks.k1.topic = calllog
    a1.sinks.k1.batchSize = 20
    a1.sinks.k1.requiredAcks = 1
    
    # channel
    a1.channels.c1.type = memory
    a1.channels.c1.capacity = 1000
    a1.channels.c1.transactionCapacity = 100
    
    # bind
    a1.sources.r1.channels = c1
    a1.sinks.k1.channel = c1

6) 进入flume根目录下，启动flume
/opt/module/apache-flume-1.7.0-bin/bin/flume-ng agent --conf conf/ --name a1 --conf-file /opt/jars/flume-kafka.conf
7) 运行生产日志的任务脚本，观察kafka控制台消费者是否成功显示产生的数据
$ sh productlog.sh

    
    ./opt/kafka_2.12-2.1.1/bin/kafka-topics.sh --zookeeper zookeeper:2181 --topic     calllog --create --replication-factor 1 --partitions 3
    kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic calllog     --from-beginning
    
    kafka-console-producer.sh --broker-list kafka:9092 --topic calllog
    
    $FLUME_HOME/bin/flume-ng agent --conf /config/ --name a1 --conf-file /config/flume-agent
