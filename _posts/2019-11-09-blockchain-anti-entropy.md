---
layout: post
title: 分布式存储系统设计 反熵(Anti-Entropy)
categories: BlockChain
description: Anti-Entropy 机制。
keywords: BlockChain
---

Anti-Entropy 机制被用来保证在不同节点上的备份(replica)都持有最新版本。

由于涉及的处理很大，一般情况下，这种机制只用于永久性的错误恢复，而不用于普通的 read repair。如同 amazon dynamo一样。

另外，为了将节点间的数据传输降到最低，在实际数据传输前，各节点交换的是自己那份数据的 message digest。实现中将采用 MerkleTree 
(其叶子节点存储的是数据文件，而非叶子节点存储的是其子节点的 Message Digest)。

处理流程

   ```
    1. recover过程从 Node A 创建一个repair session

    2. recover过程从 Node A 开始 repair

    3. Node A从请求信息中得到数据 replica所在的节点（例子中，除了 Node A外，还有 Node B 和 Node C)

    4. 迭代这个 replica 节点列表

        4.1.通过本地的消息服务发送 anti-entropy 请求到 Node B

           4.1.1 Node B接收并执行请求

        4.2.通过本地的消息服务发送 anti-entropy 请求到 Node C

            4.2.1 Node C接收并执行请求

        4.3.通过本地的消息服务发送 anti-entropy 请求到 本地 Node A（自己）

            4.3.1本地 Node A接收并执行请求

    5. 初始化 MerkleTree

    6. 对每行数据计算其 hash 值并加入到 merkleTree

    7. 各节点将自己的MerkleTree作为 anti-entropy response 送给本地的消息服务

        7.1各节点的本地的消息服务将 anti-entropy response 返回给 Node A 的消息服务

    8. ode A收集各个 anti-entropy response 中的MerkleTree与本地的 MerkleTree (4.3.1 收到为本地的) 的不同 (diff)

    9. 如果需要, 通过 pipelineOut向远程节点发送更加新的数据

    10. 如要需要，通过 pipelineIn向远程节点请求理加新的数据
   ```

![](/images/posts/blockchain/anti-entropy.png)
