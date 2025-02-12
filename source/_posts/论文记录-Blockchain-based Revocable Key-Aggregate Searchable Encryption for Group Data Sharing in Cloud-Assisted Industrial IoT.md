---
title: "论文记录-Blockchain-based Revocable Key-Aggregate Searchable\rEncryption for Group Data Sharing in Cloud-Assisted\rIndustrial IoT"
date: 2024-10-08 16:59:36
tags:
  - 审稿意见
  - blockchain
categories: 论文
description: TII的审稿
password: reisi1001
---
## Abstract
数据安全共享在工业物联网领域内很重要，为了实现选择性的数据共享，大部分人采用密钥聚合可搜索加密（KASE），但现有方案很少考虑用户撤销的问题，面临：数据所有者要和云交互、未被撤销的用户可能意外撤销 这两个问题。本文提出基于区块链的可撤销KASE系统（BC-RKASE），实现了非交互撤销和可信撤销用户管理。实验表明方法的有效性和先进性。

 ## Intro
这一章的内容基本上是摘要的扩写，没什么新的信息。但是提出了一个新的现有方法存在的问题：被撤销的用户可能和云共谋来恢复权限。这部分的概括与摘要中的描述是不一致的，倒是不冲突，但还是建议统一。

本文核心贡献是两个：
1. 非交互的密钥撤销
2. 可信的用户管理

## 问题形式化
### 系统模型
系统由四个实体组成：
1. 数据所有者DO
2. 数据用户DU
3. 云服务CS
4. 区块链BC

系统的工作流程：
1. 系统初始化阶段：DO和DU生成初始化参数以及各自的公钥与私钥，然后DO分发聚合密钥给授权用户，让他们可以访问需要的数据类。
2. 数据外包阶段：DO加密传感器收集上来的数据，并外包给CS。
3. 数据共享阶段：DO为每个未被撤销的用户生成密钥更新，这些用户的身份在当前时间间隔内未被包括在撤销列表中，还生成一个防止篡改的证据。这些密钥更新被存储在CS上以释放用户的移动的设备上的成本。
4. 数据检索阶段：当用户需要检索加密数据时，他们使用聚合密钥生成一个陷门并将其提交给BC，BC记录搜索信息并创建一个调整后的陷门。然后，CS使用该调整后的陷门和对应于用户的密钥更新来检索和部分解密数据，从而进一步减少用户的移动的设备上的负担。将部分解密的结果返回给用户以获得原始数据。
5. 用户撤销阶段：DO将包含被撤销用户的标识的间隔撤销列表上传到BC。最后，CS根据该列表删除所有先前的密钥更新。

### 威胁模型
1. CS半可信：遵循协议，但会尝试破解加密的工业数据
2. DO完全可信
3. 未授权的DU可能伪造成未撤销的DU

考虑：
1. 选择明文攻击下的不可否认性（IND-CPA）安全性：被信任的云服务器和未授权的用户不能区分两个挑战明文的密文
2. 关键字对自适应选择关键字攻击的不可否认性（IND-CKA）安全性：半信任的云服务器和未授权的用户不能区分两个挑战关键字的密文

我的问题：
不太理解这里CS是干啥的，都区块链了为啥还要把数据存CS，直接形成分布式group就可以吧

## 系统说明
### 系统初始化
1. DO和DU生成初始参数：DO生成双线性对$BG=(G,G_T,p,e:G\times G\rightarrow G_T)$，其中$p$是$G,G_T$的阶，从$G$随机选出$g$，从$Z_p^\ast$随机选$\alpha$，计算$g_i=g^{\alpha^i}$，其中$i\in\{1,2n\}(i\neq n+1)$。选两个哈希函数$H:\{0,1\}^\ast\rightarrow G, H_1:G\times G\rightarrow G$，然后设置系统参数$pp=\{BG,(g,g_i),H,H_1\}(i\in\{1,2n\}, i\neq n+1)$。然后随机选择$v,y\in Z_p$并设置$mpk=(V,Y)=(g^v,g^y),msk=(v,y)$，选择随机值$t_0\in Z_p$来初始时间间隔$T_0$，其中$pk_{own,0}=g^{t_0}, sk_{own,0}=t_0$。最后初始化撤销列表$RL_0=\emptyset$。
2. DO和DU生成公私钥：每个$DU_i$随机选择$\gamma \in Z_p$，计算公私钥$(pk_i,sk_i)=(g^\gamma,\gamma)$。
3. DU将身份发给DO，DO生成聚合密钥发回去：DO收到身份标识$UID_i$和需要的分类集$S_i$，随机选择满足$q_i(0)=v$的多项式$q_i$，计算聚合密钥$K_{S_i}=\prod_{k\in S_i}(g_{n+1-k})^{q_i(1)/y}$。

这部分是可搜索加密的基础操作，没什么新东西。

### 数据外包
为了安全共享数据，DO把加密的工业数据外包给CS存储：
1. DO随机选择$t_j\in Z_p$并更新$(pk_{own,j},sk_{own,j})=(g^{t_j},t_j)$
2. 针对输入的数据$M,(mpk, msk, pk_{own,j})$,以及时间间隔$T_j$对应的分类$l$的关键字集合KW，DO随机选择$z\in Z_p$并输出$CT_{l,j}=\{C_{l,0}, C_{l,1}, C_{l,2}, C_{l,3},CM_l, CW_l\}$，其中，$C_{l,0}=g^z,C_{l,1}=(V\cdot g_l^y)^z,C_{l,2}=Y^z,C_{l,3}=(pk_{own,j})^z,CM_l=M\cdot e(g_1,g_n)^{yz},CW_l =e(\prod_{w\in KW}H(w),Y)^z/e(g_1,g_n)^{yz}$

### 数据共享
为了确保安全，DO要每隔一段时间更新一次聚合密钥，然后发给CS。具体来说，DO收到UID后，重新调用多项式$q_i$和新的随机数，计算新的密钥和相应的证明。然后把UID、密钥和证明一起发给CS。CS收到之后，校验密钥和证明以及UID是否属于未撤销用户。

这里的问题是，我知道RL_j表示撤销列表，但IRL_j是什么呢？没看到解释，感觉是相似的东西。看到后面知道了，是当前时间段的撤销列表。

### 数据检索
1. DU首先用聚合密钥生成陷门发给BC。
2. BC记录检索请求并调用调整算法来调整陷门以访问各类数据。
3. CS接收到调整后的陷门，运行Test算法检索数据。
4. CS执行Transform算法把解密后的数据传给DU。

### 用户撤销
每个时间段输入上一时间段的撤销列表、当前时间段、当前时间段要撤销的列表，DO把当前撤销列表和上一时间段撤销列表合并起来，得到新的时间段撤销列表，发给BC，BC存起来，CS删去更新的密钥。

## 安全证明
常规的证明

## 性能分析
实验是用以太坊和华为服务器做的，分析了加密解密的时间开销、以太坊的gas消耗等。

## 总体意见
看下来最主要的感受就是不知道这里区块链是用来干啥的，以及区块链系统本身要怎么维持下去。

先说区块链的作用，看起来是用来存撤销用户列表的，但是用户管理问题是由中心CS的不可靠引起的，现在都区块链了，为啥不直接分布式服务器自动校验密钥来传数据呢？

从实验部分看，论文用的是以太坊这样的区块链平台，运行智能合约所需要消耗的gas等要用户出吗？和其他方法相比，这需要用户支付更多的费用，用户愿意吗？以及如何确保信息及时上链。

总体来说创新性不强，感觉就是可搜索加密结合了一下区块链，而在如何结合区块链这方面也没重点讲。

### summary
This article presents a blockchain-based revocable key-aggregate searchable encryption (BC-RKASE) system for group data sharing in cloud-assisted industrial IoT environments. The system allows for secure and efficient data sharing among groups while enabling the revocation of access rights for users who leave the group. It employs a searchable encryption scheme that supports aggregate keys for efficient data retrieval and a blockchain to manage user revocation. The paper details the system's design, including user registration, data outsourcing, data sharing, data retrieval, and user revocation phases. It also provides a correctness analysis and security proof based on the Decisional Bilinear Diffie-Hellman (DBDH) assumption. The system's performance is evaluated through experiments using a cloud server and IoT devices, demonstrating its practicality and efficiency for real-world applications.

### strength：
1. The paper integrates blockchain technology with searchable encryption to enable secure and efficient data sharing in industrial IoT environments. It  proposes a system that achieves IND-CPA and IND-CKA security, which are strong security guarantees for encrypted data storage and retrieval.
2. The paper addresses the challenge of user revocation, ensuring that once a user leaves a group, their access to the data is immediately revoked, which is crucial for maintaining data integrity and security.
3. The paper considers the constraints of IoT devices and aims to minimize the computational and communication overhead, making the solution suitable for resource-constrained environments. The use of a real-world dataset for performance evaluation adds to the paper's strength by demonstrating the system's applicability to real-world scenarios.

### weakness：
1. The paper may not thoroughly address how the proposed system scales with an increasing number of users or data volume. As the user base grows, the complexity of managing keys and revocation lists could become significant.
2. The system's reliance on blockchain for storing revocation lists and other metadata means it is subject to the throughput and latency of the blockchain network, which might not be optimal for real-time industrial applications.
3. The manuscript identifies user management issues stemming from the inherent unreliability of the centralized CS. Given that the authors propose integrating a blockchain system, it would be logical to explore the complete removal of the CS and instead utilize a fully decentralized blockchain-based infrastructure for both managing and transmitting data. This approach could potentially offer a more robust solution to the identified problems. The authors should justify their design choice if they opt not to follow this route.
4. The implementation of blockchain technology introduces new operational costs, such as gas fees. The paper does not clarify whether these costs should be absorbed by DUs, CS, or DO. A clear economic model is needed to address how these entities can be incentivized to participate in the system despite the added financial burden. The authors need to elaborate on this aspect to ensure practical feasibility.
5. The paper employs a significant number of symbolic representations, which can be challenging for readers to follow. To enhance readability and comprehension, it would be beneficial for the authors to compile a comprehensive table summarizing all the symbols used in the manuscript, along with their meanings and contexts.
6. The language used throughout the paper could be improved to enhance clarity and professionalism. The authors are encouraged to revise the text to ensure it is precise, concise, and free of any ambiguities that might detract from the quality of the work.
