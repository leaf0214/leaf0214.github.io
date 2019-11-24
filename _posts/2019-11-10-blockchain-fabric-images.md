---
layout: post
title: Fabric示意图
categories: BlockChain
description: Fabric示意图。
keywords: BlockChain
---

1. fabric-ca.png

![](/images/posts/fabric/fabric-ca.png)

有两种方式与 Fabric CA 服务端交互：通过 Fabric CA 客户端，或者 Fabric SDK，所有与 Fabric CA 的交互都是通过 REST APIs 来实现的。REST APIs 的 swagger 说明文档见
[fabric-ca/swagger/swagger-fabric-ca.json](https://github.com/hyperledger/fabric-ca/commit/daf28ad)

[swagger/swagger-fabric-ca.json APIs 的文档](https://jira.hyperledger.org/browse/FABC-38)

Fabric CA 客户端或者 SDK 可能会连接到 Fabric CA 集群中某一个 Fabric CA 服务端，这一部分可以通过上图右上部分获得更好的理解。客户端连接的是一个 HA 代理节点，这个 HA 代理节点为 Fabric CA 集群作负载均衡。所有的 Fabric CA 服务端共享同一个数据库。数据库用来保存用户和证书信息。如果配置了 LDAP，那么用户信息将会保存在 LDAP 中，而不是数据库中。

1. fabric-ca 运行时流程图.png

![](/images/posts/fabric/fabric-runtime-flowchat.png)

1. 两个不同的chaincode并行进行背书和共识处理的过程.png

![](/images/posts/fabric/fabric-parallel-endorsement.png)

1. illustration-of-one-possible-transaction-flow.png

![](/images/posts/fabric/illustration-of-one-possible-transaction-flow.png)

该流程图对应的交易处理步骤如下：
1、Client发起交易，这个场景下的Client是通过Submitting Peer代理和其他Peer以及交易共识排序系统交互的，Client的接入合法性可由Submitting Peer来控制。主要看系统是如何设计的；
2、Submitting Peer按照背书策略，继续发送交易给其他节点（Endorsing Peer）,模拟执行智能合约（Chain Code），暂存合约执行结果（Key-Value读写集），但执行结果不会真正更新到本地账本和 Key-Value 状态数据库中；
3、Endorsing Peer验证交易签名，验证读写集版本依赖关系是否有效，并将结果发送给Submitting Peer；
4、Submitting Peer收集Endorsing Peer的签名的执行结果和交易数据，发送到共识排序服务（Consensus Service，又称Ordering Service）；
5、共识排序系统按特定的共识算法将多笔交易排序打包成区块，并将区块递交给同一通道内的全部Peer；
6、接收到区块的全部Peer检查验证区块里的每一笔交易，比对模拟执行读写集结果，根据比对结果设置交易是否生效，设定好标记，并更新本地账本和状态数据库，这时，交易才真正反映到区块链上；
7、Submitting Peer需将交易是否执行成功等信息反馈给Client,或者Client可以通过调用SDK接收Fabric“事件”（event）得知交易执行结果。
