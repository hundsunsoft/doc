---
title: docker实验室（二）--Docker安装与配置centOS环境
date: 2019-04-02 02:02:12
tags: 
    - docker
    - nginx
categories: 
    - 教程
    - docker
---

# 1.Docker介绍

阅读本文之前，你需要了解Docker能干什么。
菜鸟上有教程可以阅读
http://www.runoob.com/docker/docker-tutorial.html

## I. 简介

Docker是一种新兴的虚拟化技术，能够一定程度上的代替传统虚拟机。不过，Docker 跟传统的虚拟化方式相比具有众多的优势。我也将Docker类比于Python虚拟环境，可以有效的配置各个版本的开发环境，比如深度学习与Java环境。

## II. 基本概念

镜像(Image)

镜像，从认识上简单的来说，就是面向对象中的类，相当于一个模板。从本质上来说，镜像相当于一个文件系统。Docker 镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建之后也不会被改变。

容器(Container)

容器，从认识上来说，就是类创建的实例，就是依据镜像这个模板创建出来的实体。容器的实质是进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的命名空间。因此容器可以拥有自己的root 文件系统、自己的网络配置、自己的进程空间，甚至自己的用户ID 空间。容器内的进程是运行在一个隔离的环境里，使用起来，就好像是在一个独立于宿主的系统下操作一样。这种特性使得容器封装的应用比直接在宿主运行更加安全。

仓库(Repository)

仓库，从认识上来说，就好像软件包上传下载站，有各种软件的不同版本被上传供用户下载。镜像构建完成后，可以很容易的在当前宿主机上运行，但是，如果需要在其它服务器上使用这个镜像，我们就需要一个集中的存储、分发镜像的服务，Docker Registry 就是这样的服务。

# 2.Docker安装

## Win10
win10安装与使用docker过程中会遇到许多问题，比如环境变量问题，比如映射端口问题，比如Docker Quickstart Terminal启动不了等问题，虽然都有解决方案，但是不推荐。
下载：https://docs.docker.com/docker-for-windows/install/

Docker支持64 位版本的Windows 10 Pro，且必须开启Hyper-V。开启方式为：打开“控制面板”->“程序”-> “启动或关闭Windows功能”，找到Hyper-V并勾选，确定重启电脑。

安装下载好的Docker for Windows Installer.exe，安装即可。

## CentOS安装
我使用的是阿里云的ECS，就图方便，不用本地开虚拟机损耗我本已不多的内存。当然你至少需要支付午饭的一顿鸡腿钱。

    [root@jus-zhan html]# cat /etc/redhat-release 
    CentOS Linux release 7.6.1810 (Core) 
    [root@jus-zhan html]# 
在CentOS系统中安装较为简单，官方提供了脚本供我们进行安装。

    sudo apt install curl
    curl -fsSL get.docker.com -o get-docker.sh
    sudo sh get-docker.sh

执行这个命令后，Docker就开始安装了。

启动Docker

    sudo systemctl enable docker
    sudo systemctl start docker

建立docker 用户组

    sudo groupadd docker
    sudo usermod -aG docker $USER

注销当前用户，重新登录Ubuntu，输入docker info，此时可以直接出现信息。

    docker info

重新启动服务

    sudo systemctl daemon-reload
    sudo systemctl restart docker

# 3.Docker使用

启动一次操作容器：docker run IMAGE_NAME [COMMAND] [ARG…]

国际惯例，还是先来一个“hello world”案例，启动一个容器输出hello world。

    docker run ubuntu echo 'hello world'

由于刚装上Docker，没有任何镜像，所以你的docker会自己下载一个最新的ubuntu的docker镜像。一次操作容器在处理完操作后会立即关闭容器。

查看镜像

    docker images

查看容器

    docker ps -a

细心的同学会发现，此时的docker是处于Exited，已退出状态。因为我们运行容器没有启动交互式方式。下面启动一次操作容器。

启动交互式容器：docker run -t -i IMAGE_NAME /bin/bash

启动交互式的容器，并进入容器内：

    docker run -i -t ubuntu /bin/bash

容器内可以进行一系列操作，退出容器可以用exit。

重新启动停止的容器：docker start 容器名

我们先查看一下容器

    docker ps -a

发现，系统给我们刚建的容器随机起了个名字，这名字太难记了，一般情况需要重命名：docker rename 原容器名 新容器名

    docker rename festive_joliot myubuntu

原容器名依据各自系统情况，通过查看容器得到。启动容器：docker start 容器名

    docker start myubuntu

有启动就有停止，相信大家已经猜到命令了。停止容器：docker stop 容器名，强制停止容器：docker kill 容器名

    docker stop myubuntu
    docker kill myubuntu

启动后并没有进入到容器中。想要再次进入，需要使用attach命令：docker attach name | id

    docker attach myubuntu

删除停止的容器：docker rm name | id

    docker rm myubuntu

很多时候，我们需要容器长期稳定运行，所以我们需要守护式容器，当我们执行完需要的操作退出容器时，不要使用exit退出，可以利用Ctrl+P Ctrl+Q代替，以守护式形式退出容器。

查看容器日志，当守护式容器在后台运行时，我们可以利用docker的日志命令查看其输出：docker logs 容器名

    docker logs myubuntu

查看容器内进程，对运行的容器查看其进程：docker top IMAGE_NAME

    docker top myubuntu

# 4.docker实际运用
案例：在容器中部署静态网站，容器中部署Nginx服务

创建映射80端口的交互式容器，并进入容器内部

    docker run -p 80 --name myweb -i -t ubuntu /bin/bash

更新源，更新到当前最新版本

    apt-get update
安装Nginx服务

    apt-get install -y nginx
安装Vim（什么鬼，vim都没有）
多啰嗦一句，由于docker容易崇尚轻量级，所以会把系统不是必要的全部去掉。我们实际使用中需要使用的，需要自行安装。

    apt-get install -y vim

创建静态页面（当然，不做这一步也可以，nginx会自带默认页面）

    mkdir -p /var/www/html
    cd /var/www/html
    vim index.html

index.html代码，可以用自己的网站html

    <!DOCTYPE html>
    <html>
    <head>
        <title>RWD</title>
        <style>
            .city{
                float: left;
                width: 300px;
                height: 300px;
                margin: 5px;
                border: 1px solid black;
                padding: 5px;
            }
        </style>
    </head>
    <body>
    <h1>W3School Demo</h1>
    <h2>Resize this responsive page!</h2>
    <br>
    <iframe src="https://www.baidu.com"></iframe>
    <div class="city">
    <h2>London</h2>
    <p>London is the capital city of England.</p>
    <p>It is the most populous city in the United Kingdom,
    with a metropolitan area of over 13 million inhabitants.</p>
    </div>
    
    <div class="city">
    <h2>Paris</h2>
    <p>Paris is the capital and most populous city of France.</p>
    </div>
    
    <div class="city">
    <h2>Tokyo</h2>
    <p>Tokyo is the capital of Japan, the center of the Greater Tokyo Area,
    and the most populous metropolitan area in the world.</p>
    </div>
    
    </body>
    </html>

修改Nginx配置文件:
查看Nginx安装位置

    whereis nginx
修改配置文件

    vim /etc/nginx/sites-enabled/default

容器内启动nginx

    nginx
容器内查看进程确认nignx启动

    ps -ef

退出容器Ctrl+P Ctrl+Q，不要用exit退出，稍后还指望容器提供服务呢。
centOS查看容器进程

    docker top myweb
centOS查看容器端口映射情况

    docker port myweb
    [docker@jus-zhan ~]$ docker port myweb
    80/tcp -> 0.0.0.0:32769

系统映射端口80到32769
8.访问网站
直接在centOS下测试

    curl "http://127.0.0.1:32769/index.html"

用浏览器试试
http://47.93.243.71:32769/index.html
47.93.243.71是我云服务器地址，大家可以用自己的，或者本地测试都可以。

其实，是可以直接下载nginx镜像使用的，使用docker run命令可以自动下载。
docker会本地查找nignx:latest，查找不到会去网上找镜像资源。

    docker run nginx
等待2分钟，就下载好了。

    [docker@jus-zhan ~]$ docker images
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    sylucky2004/myweb01           latest              6c63ddc9a1c1        4 hours ago         197MB
    sylucky2004/myweb01           v1.0                6c63ddc9a1c1        4 hours ago         197MB
    [docker@jus-zhan ~]$ docker run nginx
    Unable to find image 'nginx:latest' locally
    latest: Pulling from library/nginx
    f17d81b4b692: Already exists 
    82dca86e04c3: Pull complete 
    046ccb106982: Pull complete 
    Digest: sha256:d59a1aa7866258751a261bae525a1842c7ff0662d4f34a355d5f36826abc0341
    Status: Downloaded newer image for nginx:latest
    [docker@jus-zhan ~]$ docker images
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    sylucky2004/myweb01           latest              6c63ddc9a1c1        4 hours ago         197MB
    sylucky2004/myweb01           v1.0                6c63ddc9a1c1        4 hours ago         197MB
    nginx                         latest              62f816a209e6        4 weeks ago         109MB
    [docker@jus-zhan ~]$ 

# 5.docker镜像
镜像基本操作

列出镜像：docker images [OPTIONS] [REPOSITORY]

删除镜像：docker rmi [OPTIONS] IMAGE [IMAGE]

查找镜像：docker search [OPTIONS] TEAM

拉取镜像：docker pull [OPTIONS] NAME [:TAG]

推送镜像：docker push NAME [:TAG]

Docker允许上传我们自己构建的镜像，需要注册DockerHub的账户。也可以上传到阿里云，地址：https://cr.console.aliyun.com/#/namespace/index

构建镜像

构建Docker镜像，可以保存对容器的修改，并且再次使用。构建镜像提供了自定义镜像的能力，以软件的形式打包并分发服务及其运行环境。Docker中提供了两种方式来构建镜像：

通过容器构建：docker commit

使用commit命令构建镜像

命令：docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]

参数：-a，–author=“”，指定镜像的作者信息

​ -m，–message=“”，提交信息

​ -p，–pause=true，commit时是否暂停容器

commit命令构建镜像

    docker commit -a "作者：剑神一笑" -m "myweb test commit an image" myweb sylucky2004/myweb:v1.0
docker images查看刚刚新建的镜像

    [docker@jus-zhan ~]$ docker images
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    sylucky2004/myweb             v1.0                4cb34f352ace        59 seconds ago      224MB

通过Dockerfile：docker build
使用Dockerfile文件构建镜像

Docker允许我们利用一个类似配置文件的形式来进行构建自定义镜像，在文件中可以指定原始的镜像，自定义镜像的维护人信息，对原始镜像采取的操作以及暴露的端口等信息。比如：

    vi Dockerfile
    # Sample Dockerfile
    FROM ubuntu:16.04
    MAINTAINER wgp "https://www.zhihu.com/people/sylucky.zhihu.io/posts"
    RUN apt-get update
    RUN apt-get install -y nginx
    EXPOSE 80

命令：docker build [OPTIONS] DockerFile_PATH | URL | -

    docker build -t="sylucky2004/myweb01" .


# 6.镜像迁移

我们制作好的镜像，一般是作为自己其他项目或者环境使用或分享给其他需要的人，所以提供了几种将我们的镜像迁移、分享给其他人的方式。推荐镜像迁移应该直接使用Docker Registry，无论是直接使用Docker Hub还是使用内网私有Registry都可以。使用镜像频率不高，镜像数量不多的情况下，我们可以选择以下两种方式。

上传Docker Hub

首先，需要在[Docker Hub](https://hub.docker.com/)上申请注册一个帐号。然后我们需要创建仓库，指定仓库名称。

在终端中登录你的Docker Hub账户，输入docker login，输入用户名密码即可登录成功。

    docker login

查看需要上传的镜像，并将选择的镜像打上标签，标签名需和Docker Hub上新建的仓库名称一致，否则上传失败。给镜像打标签的命令：docker tag <existing-image> <hub-user>/<repo-name>[:<tag>]

    docker tag sylucky2004/myweb01 sylucky2004/myweb01:v1.0
其中existing-image代表本地待上传的镜像名加tag，后面<hub-user>/<repo-name>[:<tag>]则是为上传更改的标签名，tag不指定则为latest。

可以看到，我们重新为ubuntu:16.04的镜像打上标签，接下来，我们利用push命令直接上传镜像：docker push <hub-user>/<repo-name>:<tag>

    docker push sylucky2004/sylucky2004/myweb01:v1.0
下面是实际操作

    [docker@jus-zhan ~]$ docker login
    Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
    Username: sylucky2004
    Password: 
    WARNING! Your password will be stored unencrypted in /home/docker/.docker/config.json.
    Configure a credential helper to remove this warning. See
    https://docs.docker.com/engine/reference/commandline/login/#credentials-store
    Login Succeeded
    [docker@jus-zhan ~]$ docker tag sylucky2004/myweb01 sylucky2004/myweb01:v1.0
    [docker@jus-zhan ~]$ docker images
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    sylucky2004/myweb01           latest              6c63ddc9a1c1        3 hours ago         197MB
    sylucky2004/myweb01           v1.0                6c63ddc9a1c1        3 hours ago         197MB
    The push refers to repository [docker.io/sylucky2004/sylucky2004/myweb01]
    An image does not exist locally with the tag: sylucky2004/sylucky2004/myweb01
    [docker@jus-zhan ~]$ docker push sylucky2004/myweb01:v1.0
    The push refers to repository [docker.io/sylucky2004/myweb01]
    d4f397b190f8: Pushed 
    b3a2120ac64b: Pushed 
    f1dfa8049aa6: Mounted from library/ubuntu 
    79109c0f8a0b: Mounted from library/ubuntu 
    33db8ccd260b: Mounted from library/ubuntu 
    b8c891f0ffec: Mounted from library/ubuntu 
    v1.0: digest: sha256:c1f9c965eec192aed9580d40376a97a89f800752910eea718a2977adcb165ec3 size: 1574 
    
我们已经上传成功。

# 7.Docker镜像的导入导出
Docker 还提供了 docker load 和 docker save 命令，用以将镜像保存为一个tar文件。

    docker save sylucky2004/myweb01:v1.0 | gzip > myweb01.tar.gz
    ls
    docker load -i myweb01.tar.gz

下面是实际操作，save一个镜像，删除原镜像，再load镜像。

    [docker@jus-zhan ~]$ docker save sylucky2004/myweb01:v1.0 | gzip > myweb01.tar.gz
    [docker@jus-zhan ~]$ ls -ltr myweb01.tar.gz
    -rw-rw-r-- 1 docker docker 84088604 12月  8 13:07 myweb01.tar.gz
    [docker@jus-zhan ~]$ docker rmi sylucky2004/myweb01:v1.0
    Untagged: sylucky2004/myweb01:v1.0
    [docker@jus-zhan ~]$ docker images
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    sylucky2004/myweb01           latest              6c63ddc9a1c1        3 hours ago         197MB
    [docker@jus-zhan ~]$ docker load -i myweb01.tar.gz 
    Loaded image: sylucky2004/myweb01:v1.0
    [docker@jus-zhan ~]$ docker images
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    sylucky2004/myweb01           latest              6c63ddc9a1c1        3 hours ago         197MB
    sylucky2004/myweb01           v1.0                6c63ddc9a1c1        3 hours ago         197MB
    [docker@jus-zhan ~]$ 
