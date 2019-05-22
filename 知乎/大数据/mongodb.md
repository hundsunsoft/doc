1. aria2c -c -x16 -s20 -j20 https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-rhel70-4.0.6.tgz
2. mongod --dbpath database --logpath log/mongolog --port 27017


# 主从
0:创建目录
mkdir -p data/r0 data/r1 data/r2


1:启动3个实例,且声明实例属于某复制集
mongod --port 27017 --dbpath data/r0 --smallfiles --replSet rsa --fork --logpath log/mongo17.log
mongod --port 27018 --dbpath data/r1 --smallfiles --replSet rsa --fork --logpath log/mongo18.log
mongod --port 27019 --dbpath data/r2 --smallfiles --replSet rsa --fork --logpath log/mongo19.log

2:配置
rsconf = {
    _id:'rsa',
    members:
    [
        {_id:0,
        host:'127.0.0.1:27017'
        }
    ]
}


3: 根据配置做初始化
rs.initiate(rsconf);

4: 添加节点
rs.add('127.0.0.1:27018');
rs.add('127.0.0.1:27019');

5:查看状态
rs.status();



6:删除节点
rs.remove('192.168.1.201:27019');

7:主节点插入数据
>use test
>db.user.insert({uid:1,name:'lily'});

8:连接secondary查询同步情况
./bin/mongo --port 27019
>use test
>slaveOk();
>show tables