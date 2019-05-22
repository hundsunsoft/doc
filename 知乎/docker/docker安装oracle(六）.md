docker pull oraclelinux

docker run -i -t --name guest oraclelinux /bin/bash

docker run -d --name http_server httpd

# --link 划重点
docker run --rm -t -i --name client1 --link http_server:server\
oraclelinux /bin/bash


    root @ client1~]＃ env
    OSTNAME = 10815c22e5b4
    ERM = xterm的
    ERVER_PORT = TCP：//172.17.0.16：80
    ATH =在/ usr / local / sbin中：在/ usr / local / bin目录：/ usr / sbin目录：在/     sr / bin中：/ sbin目录：/ bin中
    WD = /
    ERVER_PORT_80_TCP_PORT = 80
    ERVER_PORT_80_TCP_ADDR = 172.17.0.16
    ERVER_PORT_80_TCP = TCP：//172.17.0.16：80
    ERVER_PORT_80_TCP_PROTO = TCP
    HLVL = 1
    ERVER_NAME = /客户端1 /服务器
    OME = /
     = / USR / bin中/ env的
    root @ client1~]＃ ping -c 1 server
    ING服务器（172.17.0.16）56（84）字节的数据。
    来自服务器的64字节（172.17.0.16）：icmp_seq = 1 ttl = 64时间= 0.105 ms
    --服务器ping统计---
    个包发送，1个接收，0％丢包，时间0ms
    tt min / avg / max / mdev = 0.105 / 0.105 / 0.105 / 0.000 ms
    root @ client1~]＃ ping -c 1 172.17.0.16
    ING 172.17.0.16（172.17.0.16）56（84）字节的数据。
    来自172.17.0.16的64字节：icmp_seq = 1 ttl = 64时间= 0.171 ms
    -- 172.17.0.16 ping统计---
    个包发送，1个接收，0％丢包，时间0ms
    tt min / avg / max / mdev = 0.171 / 0.171 / 0.171 / 0.000 ms
    root @ client1~]＃ curl http://server
    在guest上运行的HTTP服务器
        root @ client1~]＃curl http://172.17.0.16
    在guest 虚拟机上运行的HTTP服务器


# 安装多线程下载工具
    yum install aria2
    aria2c https://public.dhe.ibm.com/ibmdl/export/pub/software/websphere/messaging/mqadv/mqadv_dev911_ubuntu_x86-64.tar.gz -s 10 --max-connection-per-server=16 --min-split-size=10M