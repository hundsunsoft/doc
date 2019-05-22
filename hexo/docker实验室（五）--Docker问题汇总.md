---
title: docker实验室（五）--Docker问题汇总
date: 2019-04-02 05:02:12
tags: 
    - sshd
categories: 
    - 问题
    - docker
---

# 1.网卡不能启动问题--centos环境

Error: Connection activation failed: No suitable device found for this connection.

需要查找的配置及日志

> /etc/hosts
> /etc/hostname
> /etc/sysconfig/network-script/ifcfg-ens33
> ip a
> ifconfig


    [root@bigdata112 network-scripts]# cat /etc/hostname
    bigdata112
    [root@bigdata112 network-scripts]# cat /etc/hosts
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
    172.16.26.112   bigdata112
    [root@bigdata112 network-scripts]# cat /etc/sysconfig/network-scripts/    ifcfg-ens33 
    TYPE=Ethernet
    PROXY_METHOD=none
    BROWSER_ONLY=no
    BOOTPROTO=static
    DEFROUTE=yes
    IPV4_FAILURE_FATAL=no
    IPV6INIT=yes
    IPV6_AUTOCONF=yes
    IPV6_DEFROUTE=yes
    IPV6_FAILURE_FATAL=no
    IPV6_ADDR_GEN_MODE=stable-privacy
    NAME=ens33
    UUID=5413d182-8f20-446d-9762-8c2aba9df68z
    HWADDR=00:0c:29:9d:aa:f8
    DEVICE=ens33
    ONBOOT=yes
    IPADDR=172.16.26.112
    PREFIX=24
    IPV6_PRIVACY=no
    [root@bigdata112 network-scripts]# ifconfig
    lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
            inet 127.0.0.1  netmask 255.0.0.0
            inet6 ::1  prefixlen 128  scopeid 0x10<host>
            loop  txqueuelen 1  (Local Loopback)
            RX packets 12  bytes 848 (848.0 B)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 12  bytes 848 (848.0 B)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
    
    virbr0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
            inet 192.168.122.1  netmask 255.255.255.0  broadcast 192.168.122.255
            ether 52:54:00:ec:66:09  txqueuelen 1000  (Ethernet)
            RX packets 0  bytes 0 (0.0 B)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 0  bytes 0 (0.0 B)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
    [root@bigdata112 network-scripts]# ip a
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
           valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
           valid_lft forever preferred_lft forever
    2: ens33: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
        link/ether 00:0c:29:9d:aa:f8 brd ff:ff:ff:ff:ff:ff
    3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state     DOWN qlen 1000
        link/ether 52:54:00:ec:66:09 brd ff:ff:ff:ff:ff:ff
        inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
           valid_lft forever preferred_lft forever
    4: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast master virbr0     state DOWN qlen 1000
        link/ether 52:54:00:ec:66:09 brd ff:ff:ff:ff:ff:ff

在ifcfg-ens33中，HWADDR填写ip a命令得到的MAC地址，还找不到就把UUID换一个值（随便改一个数字即可），原因就是系统通过network-functions计算得出UUID的设备不存在

然后ifup ens33启动网卡，不行就service network restart，再不行就reboot

# 2.启动/usr/sbin/sshd报错

    [root@75e5348189a2 ~]# /usr/sbin/sshd
    Could not load host key: /etc/ssh/ssh_host_rsa_key
    Could not load host key: /etc/ssh/ssh_host_ecdsa_key
    Could not load host key: /etc/ssh/ssh_host_ed25519_key
    sshd: no hostkeys available -- exiting.

需要重新生成ssh主机密钥，然后启动sshd服务，具体要生成哪些主机密钥要看sshd_config配置

    ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
    ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
    ssh-keygen -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key 
    ssh-keygen -t dsa -f /etc/ssh/ssh_host_ed25519_key


启动sshd服务

    /usr/sbin/sshd

# 3.docker attach进入容器异常

改用 docker exec 进入容器

顺便加一点docker run运行参数

命令格式：docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Usage: Run a command in a new container

常用选项说明

* -d, --detach=false， 指定容器运行于前台还是后台，默认为false
* -i, --interactive=false， 打开STDIN，用于控制台交互
* -t, --tty=false， 分配tty设备，该可以支持终端登录，默认为false
* -u, --user=""， 指定容器的用户
* -a, --attach=[]， 登录容器（必须是以docker run -d启动的容器）
* -w, --workdir=""， 指定容器的工作目录
* -c, --cpu-shares=0， 设置容器CPU权重，在CPU共享场景使用
* -e, --env=[]， 指定环境变量，容器中可以使用该环境变量
* -m, --memory=""， 指定容器的内存上限
* -P, --publish-all=false， 指定容器暴露的端口
* -p, --publish=[]， 指定容器暴露的端口
* -h, --hostname=""， 指定容器的主机名
* -v, --volume=[]， 给容器挂载存储卷，挂载到容器的某个目录
* --volumes-from=[]， 给容器挂载其他容器上的卷，挂载到容器的某个目录
* --cap-add=[]， 添加权限，权限清单详见：http://linux.die.net/man/7/capabilities
* --cap-drop=[]， 删除权限，权限清单详见：http://linux.die.net/man/7/capabilities
* --cidfile=""， 运行容器后，在指定文件中写入容器PID值，一种典型的监控系统用法
* --cpuset=""， 设置容器可以使用哪些CPU，此参数可以用来容器独占CPU
* --device=[]， 添加主机设备给容器，相当于设备直通
* --dns=[]， 指定容器的dns服务器
* --dns-search=[]， 指定容器的dns搜索域名，写入到容器的/etc/resolv.conf文件
* --entrypoint=""， 覆盖image的入口点
* --env-file=[]， 指定环境变量文件，文件格式为每行一个环境变量
* --expose=[]， 指定容器暴露的端口，即修改镜像的暴露端口
* --link=[]， 指定容器间的关联，使用其他容器的IP、env等信息
* --lxc-conf=[]， 指定容器的配置文件，只有在指定--exec-driver=lxc时使用
* --name=""， 指定容器名字，后续可以通过名字进行容器管理，links特性需要使用名字
* --net="bridge"， 容器网络设置:
* * bridge 使用docker daemon指定的网桥
* * host //容器使用主机的网络
* * container:NAME_or_ID >//使用其他容器的网路，共享IP和PORT等网络资源
* * none 容器使用自己的网络（类似--net=bridge），但是不进行配置
* --privileged=false， 指定容器是否为特权容器，特权容器拥有所有的capabilities
* --restart="no"， 指定容器停止后的重启策略:
* * no：容器退出时不重启
* * on-failure：容器故障退出（返回值非零）时重启
* * always：容器退出时总是重启
* --rm=false， 指定容器停止后自动删除容器(不支持以docker run -d启动的容器)
* --sig-proxy=true， 设置由代理接受并处理信号，但是SIGCHLD、SIGSTOP和SIGKILL不能被代理
## 示例
* 运行一个在后台执行的容器，同时，还能用控制台管理：docker run -i -t -d ubuntu:latest
* 运行一个带命令在后台不断执行的容器，不直接展示容器内部信息：docker run -d * ubuntu:latest ping www.docker.com
* 运行一个在后台不断执行的容器，同时带有命令，程序被终止后还能重启继续跑，还能用控制台管理，docker run -d --restart=always ubuntu:latest ping www.docker.com
* 为容器指定一个名字，docker run -d --name=ubuntu_server ubuntu:latest
* 容器暴露80端口，并指定宿主机80端口与其通信(: 之前是宿主机端口，之后是容器需暴露的端口)，docker run -d --name=ubuntu_server -p 80:80 ubuntu:latest
* 指定容器内目录与宿主机目录共享(: 之前是宿主机文件夹，之后是容器需共享的文件夹)，docker run -d --name=ubuntu_server -v /etc/www:/var/www ubuntu:latest

# 4.虚拟机RTNETLINK answers: File exists错误解决方法

在centos下出现该故障的原因是启动网络的两个服务有冲突：/etc/init.d/network 和 /etc/init.d/NetworkManager这两个服务有冲突吧。

从根本上说是NetworkMaganager（NM）的带来的冲突，停用NetworkManager即可解决。重启即可。

chkconfig --level 35 network on
chkconfig --level 0123456 NetworkManager off

service NetworkManager stop
service network stop

service network start
