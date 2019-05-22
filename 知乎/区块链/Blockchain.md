#Blockchain

#搭建Fabric 1.0环境
1. https://yq.aliyun.com/articles/350112
2. 项目指定目录
    cd ../usr/local/go/src/github.com/hyperledger/fabric/examples/e2e_cli
3. Fabric网络启动
    ./network_setup.sh up
2. 进入CLI
    docker exec -it cli bash
3. 查询余额
    peer chaincode query -C mychannel -n mycc -c '{"Args":["query","a"]}'
4. a转20给b
    peer chaincode invoke -o orderer.example.com:7050  --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem  -C mychannel -n mycc -c '{"Args":["invoke","a","b","20"]}'
5. 关闭网络
    ./network_setup.sh down

#参考资料
1. 区块链基础概念相关
    https://yq.aliyun.com/users/1183972549078718
2. 环境搭建及解析
    https://yq.aliyun.com/articles/350112
    https://www.cnblogs.com/studyzy/p/7451276.html
3. Hyperledger Fabric 概述
    https://www.ibm.com/developerworks/cn/cloud/library/cl-top-technical-advantages-of-hyperledger-fabric-for-blockchain-networks/index.html
4. 区块链和 HyperLedger 微讲堂系列
    https://www.ibm.com/developerworks/community/blogs/3302cc3b-074e-44da-90b1-5055f1dc0d9c/entry/opentech-blockchain-01?lang=zh
5. IBM Blockchain 101：开发人员快速入门指南
    https://www.ibm.com/developerworks/cn/cloud/library/cl-ibm-blockchain-101-quick-start-guide-for-developers-bluemix-trs/index.html
    http://openblockchain.readthedocs.io/en/latest/         developer guides
6. IBM developerWorks
    https://www.ibm.com/developerworks/community/wikis/home?lang=zh#!/mywikis?following=true


#疑问
1. 区块链的作用
2. 银行业为何需要区块链
3. 区块链的原理
4. 我们公司或我们部门如何看待这门技术

展示一些学习过程中用到的网站等


#note
1. R3CEV是一家总部位于纽约的区块链创业公司,由其发起的R3区块链联盟,至今已吸引了42家巨头银行的参与
2. 智能合约
3. 感觉不应该是各个行业内部单独的变革，而至少是整个金融业的整合，当然这可能需要非常长的时间，目前能做到行业内的变革已是极致。


#区块链和 HyperLedger 微讲堂系列
1. 第一讲 区块链商用之道
    * 区块链的核心价值是什么？
    * 进入区块链2.0时代，人们开始关心区块链这项“黑科技”如何在产业中得以运用。商用区块链需要具备什么要素？区块链的产业实践从哪几个方面着手？
    * IBM正在以自身的创新能力在全球推动区块链的发展。那么，IBM是如何构建全球最大的基于区块链技术的供应链金融平台？国内的顶级金融机构在区块链方面又是和IBM怎样合作的？
    * 为企业客户服务，和有区块链能力的公司并肩合作，IBM在建立区块链生态方面有哪些计划？ 
2. Hyperledger项目与社区概览
    * Hyperledger Project 项目生命周期(lifecycle)
        - Proposal 提案阶段 发起人 维护者 设计方案等信息 提交给技术委员会
        - Incubation 实现项目功能并进行缺陷修复
        - Active 发布release
        - Deprecated
        - End of life
    * Hyperledger Fabric
        - 2015.12 golang
        - SDK Node.js Python java
            https://github.com./hyperledger/fabric-sdk-java
    * Hyperledger SawtoothLake
        - 2016.4 python
    * Hyperledger Iroha
        - 2016.10 C++
    * Hyperledger Blockchain Explorer
        - 2016.8 Node.js
    * Hyperledger Cello
        - 2017.1 python javascript
    * Hyperledger community
        - Linux Foundation Support
        - Organizations
            - Technical Streering Committee 技术委员会 主导开发情况
            - Governing Board 决策所有事务
            - Linux Foundation Staffs
        - TWG-China
    * reference
        - wiki.hyperledger.org(hyperledger wiki)
        - hyperledger-farbic.readthedocs.io(hyperledger doc)
        - ibm.com/ibm/cn/blockchain/(IBM区块链)
        - github.com/yease/blockchain_guide(区块链技术指南)
        - github.com/yease/docker_practice(Docker从入门到实践)
    * HyperLedger Fabric架构解读
        - Hyperledger 子项目
        - Hyperledger Fabric 0.6架构
        - Hyperledger Fabric 1.0架构
            - ChainCode的一次调用，形成一个block，也是执行了一次事务。
            - 事务类型：ChainCode Configuration custom
            - 事务处理流
                - 应用向一个或多个peer节点发送对事务的背书请求
                - 背书节点执行ChainCode，但并不将事务提交到本地账本，只是将结果返回给应用
                - 应用收集所有背书节点的结果后，将广播给Orderers
                - Orderers执行共识过程，并生成Block，通过消息通道批量的将Block发布给Peer节点
                - 各个Peer节点验证交易，并提交到本地账本中
            - Peer节点内的事务流
                - 每个ChainCode在部署时，都需要和背书(ESCC)和验证(VSCC)的系统ChainCode相关联
                - ESCC决定如何对Proposal进行背书(peer节点执行完ChainCode后，系统会执行ESCC进行背书，然后返回给Publication)
                - VSCC决定事务的有效性(Peer节点拿到从Orderer节点传来的Block后，首先调用VSCC对结果进行校验，包括被背书进行校验，成功后写入账本)
            - Ledger 账本
                - Block结构：文件系统方式存储(历史的交易记录，transaction的执行记录)
                - State状态：KV数据库维护(当前账户的余额，状态等)
        - Hyperledger Fabric 1.0环境搭建    
3. Bluemix上的区块链服务
4. ChainCode 实战
    * Fabric ChainCode概述
        - ChainCode是什么
            - 一个接口的实现代码
            - 部署在Fabric区块链网络节点上
            - 生成Transaction的唯一来源
            - 与Fabric区块链交互的唯一渠道
            - 智能合约在Fabric上的实现方式
        - 开发支持
            - go java
            - SDK $GOPATH/src/github.com/hyperledger/fabric/core/chaincode/shim
        - ChainCode运行原理
            - Fabric结点运行模式
                一般模式
                    Chaincode运行在docker容器中
                    开发调试过程复杂
                        部署 -> 调试 -> 修改 -> 创建docker镜像 -> 部署
                开发模式 --peer-chaincodedev
                    Chaincode运行在本地
                    开发调试相对容易
                        部署 -> 调试 -> 修改 -> 部署
            - 开发模式下
                Chaincode注册过程 Chaincode -> Peer
                Deploy/Invoke/Query 执行时 
                    CLI -> Peer
                    Peer -> CLI
    * Fabric0.6 ChainCode
        - 相关概念
            - Transaction：一次Chaincode函数的运行
                五类，其中一个是Undefined，其余均与chaincode的执行有关
                Transaction存储chaincode执行的相关信息，比如chaincodeID、函数名称、参数等、并不包含操作的数据
            - World State：Fabric区块链系统中所有变量的值的集合
                Transaction实际操作的是数据，每个chaincode都有自己的数据
                Fabric使用Rocksdb存储数据，一个key-value数据库。key -> 变量，value -> 值
                Fabric将每一对key-value叫做一个state，而所有的chaincode的state的合集就是WorldState。
        - 如何编写
            实现 Init Invoke Query 三个接口
                Init：Deploy中被调用，初始化工作，一般情况被调用一次
                Invoke：Invoke中被调用，更新world state，可多次被调用
                Query：Query中被调用，查询world state，不能更新world state，可多次被调用
            shim.ChaincodeStubInterface APIs
                state操作
                Table操作
                Chaincode相互调用
                Transaction相关 主要查询和验证操作
                Event操作(在Transaction写进Block时被调用)
                一些Log 注册 等API
        - 如何调试
            本地启动一个VP节点，开发模式
                peer node start --peer-chaincodedev --logging-level-debug
            编写Chaincode程序
            生成可执行程序
                go build
            运行可执行程序，向VP注册
                CORE_CHAINCODE_ID_NAME=mycc CORE_PEER_ADDRESS=0.0.0.0:7051 ./MyChaincode
            部署提交
                peer chaincode depoly -n mycc -c '{"Args":[...]}'
                peer chaincode invoke -n mycc -c '{"Args":[...]}'
                peer chaincode query -n mycc -c '{"Args":[...]}'
    * Fabric1.0 ChainCode
        - 相关概念
            Channel - 通道，子链。
                同一peer可加入不同channel。Chaincode的操作基于channel进行。同一channel上的peer节点同步其上chaincode执行的结果
            Endorser - 模拟执行Chaincode，不写入Block
                分离计算任务，减轻consensus（共识）节点负担，增加吞吐量。支持endorsementpolicy，更加灵活
            Orderer - 对chaincode执行结果consensus。
                支持solo/kafka/sBFT
            Committer - 将chaincode执行结果写入ledger
        - 如何编写
            实现 Init Invoke 两个接口
                查询操作不会产生transaction
            shim.ChaincodeStubInterface APIs 五类
                State操作 重点GetQueryResult支持peer节点对应的数据库所支持的语法
                Args读操作 获取输入操作
                Transaction读操作
                Chaincode相互调用
                Event设置
                辅助API
        - 如何调试：以下为一般模式
            本地启动一个Orderer结点，solo模式
                orderer
            本地启动一个peer结点
                CORE_PEER_ID=peer0 peer node start
            Install Chaincode程序
                peer chaincode install -p github.com/hyperledger/fabric/examples/chaincode/go/chaincode_example02/ -n mycc -v 1.0
            部署Chaincode程序
                peer chaincode instantiate -n mycc -v 1.0 -c '{"Args":["init","A","100","B","100"]}'
            提交Invoke transaction
                peer chaincode invoke -n mycc -c '{"Args":["invoke","A","B","10"]}'
            提交Query transaction
                peer chaincode query -n mycc -c '{"Args":["query","A"]}'
5. HyperLedger 中的共享账本
    * What is shared Ledger
    * Ledger for Fabric v1.0
        - 按内容划分
            Block ledger
                File system,only new Blocks appending
                Blocks are stored on all committers and optional subset of ordering service nodes
                ordering service nodes may not store all Bolcks ?
            State ledger
                World/Ledger state holds current value of smart contract data
                KVS hidden from developers by chaincode APIs
                Stored on all committers
            History ledger
                Holding historic sequence of all chain code transaction
                Index stored in KVS and hidden from develop by chaincode APIs
                Stored on all committers
                v1.0新增的ledger
        - 官方doc 
            peer ledger
                存储在背书节点和记账几点上的账本
            orderer ledger
                ordering service node上的账本
        存储方式
            Blockchain(File system)
            State Database 从File中读取出来放一个DB？
            History index LevelDB
            Block Index LevelDB
    * Ledger privacy with multiple channels
    * Ledger and Chaincode
        Chaincode源码存储在节点上
        Ledger存储Chaincode的hash等等
6. HyperLedger 中的共识机制**需要再看**
    * Blockchain - Hyperledger
    * Distuibuted system and its related issues
        - CAP theorem
    * Consensus
        - Permissioned (Voting) consensus
            - COC
            - CFT
            - BFT
        - BFT in details
        - Constraints related to (Vanilla) BFT
        - Consensus mechanism in Hyperledger v1.0
            - PBFT
    * Summary
    * Q&A
7. HyperLedger Fabric 的隐私和安全
    * PKI等密码学技术基础
        - PKI公钥体系的一些基本概念
        - PKI数字证书和CA
        - 区块链的业务安全需求
            - 不可更改的加密交易数据
            - 可追责、不可陷害
            - 隐私保护：交易匿名、交易不可关联
            - 监管和审计支持
        - Hyperledger Fabric 的系统ChainCode相关联支持环境
            - CA是Membership的重要组件之一
            - 满足于Fabric的安全需求，未参与各方实现：
                - 用户注册
                - 证书签发
                - 证书吊销
                - 发布证书链
            - 基于RESTful/CLI等多种接口方式，服务于Blockchain的各个环节，包括：
                - T-Cert - Transaction Certificate(交易证书证书)，执行交易时使用
                - E-Cert - Enrollment Certificate(注册证书)，携带实体信息的证书
                - CSR - 证书吊销列表
    * 区块链的基本数据模型
        区块数据的构成-区跨链
        区块数据的构成-事务数据
    * 如何保障交易数据的不可更改
    * 如何保障交易的私密性
        - 确保交易仅仅向有限的全体可见，不对非授权的全体公开
        - 简单来看，分别使用授权用户的"公钥"加密数据，只有授权用户能够用自己的"私钥"解密数据
        - 实际来看，则通过"对称加密和公钥加密"相结合的方式
    * 如何保障交易的可监管能力
        - "监管"是指无需交易方授权，监管者可以解密交易
        - 但监管不能侵犯"不可抵赖性"，即， 监管者不可以伪造别人的交易
        - 采用PKI体系的"双密钥对---签名密钥对和加密密钥对"模式来实现
            - 证书持有者有一对签名用途的密钥对
            - 证书持有者有一对加密用途的密钥对
            - CA签发证书时，对加密用途的密钥对进行备案，交由密钥管理中心存放
                - 特定情况下，提取某用户的解密私钥，解密相关的交易数据
            - 签名用途的密钥对仍然在用户端产生，不做备案
                - 无私钥的情况下，无法伪造签名，因此无法伪造别人的交易
                - 从证书申请，到证书生成，常规意义的CA/RA体系都可以保证签名密钥的用户私密性
        - 签名密钥对的管理
            - 签名密钥由签名私钥和验证公钥组成
            - 签名私钥是发送方身份的证明，具有日常生活中的公章、私章的效力
            - 签名私钥绝对不能够做备份和存档，丢失后只需重新生成新的密钥对
            - 验证公钥需要存档，用于验证旧的数字签名
        - 加密密钥对的管理
            - 加密秘钥对由加密公钥和解密私钥组成
            - 为防止秘钥流失时数据无法恢复，解密私钥应该进行备份，同时还可能需要存档，以便能在任何时候解密历史密文数据
            - 加密公钥则无需备份和存档，加密公钥流失时，只需重新生成密钥对即可
    * 如何保护隐私
        - 确保从交易中无法追溯交易创建者的信息
        - 问题
            - 由于交易中存在签名信息，而签名信息携带可以关联交易创建者证书的信息
            - 证书中包含交易创建者的识别信息
            - 如果不做实现特定的机制，交易中将可以追溯交易创建者的信息
        - 交易方持有多种类型的证书，交易不同环节将使用如下这些类型的证书
            - E-Cert(Enrollment Cert)
                - 长期持有，携带或可追溯使用者信息
                - 用于身份认证
            - T-Cert(Transaction Cert)
                - 每个交易时生成，用于交易的签名
            - TLS-Cert，长期持有，主要用于SSL/TLS通讯
        - 交易过程
            - 每次交易前使用E-Cert登录，然后CA会帮用户申请一个T-Cert用于交易
            - 每个交易使用一个新的T-Cert
            - T-Cert中不显示携带交易创建者的信息
            - T-Cert和E-Cert的关系被隐秘保护
        - Hyperledger Fabric Protocol Specification #Security
            - http://openblockchain.readthedocs.io/en/latest/protocol-spec/#4-security_1
            - https://github.com/hyperledger/fabric/bolb/master/docs/source/protocol-spec.rst