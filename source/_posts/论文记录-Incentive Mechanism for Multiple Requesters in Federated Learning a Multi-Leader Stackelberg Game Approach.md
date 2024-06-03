---
title: "论文记录-Incentive Mechanism for Multiple Requesters in Federated Learning: a Multi-Leader Stackelberg Game Approach"
date: 2024-06-03 13:51:36
tags:
  - mechanism-design
  - stackelberg-game
  - federated-learning
  - 审稿意见
categories: 论文
description: TSC的审稿
---
## Abstract
联邦学习是分布式学习技术，需要激励机制来使得用户愿意参与。现有激励机制普遍针对单个请求者（requester），个别研究会考虑多个请求者，但限制了worker同时训练多个模型的可能性。本文移除了这个约束，除了常规的workers之间以及workers与请求者之间的互动外，还关注了请求者之间的互动。建模为stackelberg博弈，推导+逆向归纳确定均衡。进行了一系列分析研究workers和请求者之间的均衡，结果说明了请求者的先发优势，揭示了请求者和工人之间互利的可能性。
## Intro
1. RR、WW和RW分别表示请求者之间、workers之间以及请求者与workers之间的互动
2. RW建模为stackelberg博弈，R是leader，W是follower
3. WW和RR之间的互动是两个非合作子博弈
4. 模拟实验表明：
	1. 与W相比，R有先发优势
	2. 找到了影响R成本和W效用的关键因素
	3. 解释了一个R和所有W可以在其他R付出小成本的情况下共赢
5. 与已有研究的不同点主要在于关注了RR，已有研究中只有两个也关注RR，但这两个本质上是多个RW，做了简化。
## 系统模型
1. $R=\{1,2,...,r\}$和$W=\{1,2,...,w\}$分别表示请求者和workers的集合
2. 系统中所有参与者相互独立，不共谋
3. 每个请求者有自己的全局模型，需要worker的数据和算力来训练
4. 每个worker能训练至少一个模型，但没有义务为模型训练提供数据集和算力

### W的效用和R的成本
1. W的目标是通过设置给每个模型贡献的数据量来最大化自己的收益，$s_{ij}$表示worker i给请求者j的模型提供的数据量
2. worker i的策略用向量$s_i=(s_{i1},...,s_{ir})$表示
3. 所有W的策略集用$S_i$表示
4. 假设worker i从请求者j那里收到报酬$p_j$并为单位数据的训练支出成本$c_i$，则该worker的效用函数是：
$$
u_i(s_i)=\sum_{j\in R}(p_j-c_i)s_{ij} \tag{1}
$$
5. R的成本分为两部分
	1. 训练延迟：W同时能训练多个模型，受限于W的算力和任务调度策略，R的任务不一定在什么时候完成，最差的情况要等其他R的任务完成以后才能轮到。假设单任务训练时间与数据大小成比例，为了估计训练延迟，我们用$l_{ij}=\lambda_i \sum_{k\in R\backslash\{j\}}s_{ik}$来表示延迟的最差情况，其中$\lambda_i$表示worker i的算力权重因子。这部分不由R控制。这个公式浅显一些来说，就是worker i给其他模型分配的数据量加起来，再乘以权重，直观来看是表达worker i的延迟对模型j的影响。
	2. 支付给worker的报酬：与worker对模型精确度的贡献有关。$b_j\geq 0$表示基础单价，即R愿意为单位训练数据支付的最高价。请求者j为单位训练数据支付的报酬如下，其中$\rho_j>0$是需求减小率，以使 $p_j$ 与为 j 的模型训练贡献的训练数据总量$\sum_{i\in W}s_{ij}$之间的关系符合供求规律。

$$
p_j=b_j-\rho_j^{-1}\sum_{i\in W}s_{ij} \tag{3}
$$
6. 除了成本以外，R还会收到模型训练所带来的收益。训练数据量的大小对模型准确性的边际贡献递减，因此请求者j从训练中获得的收益如下，其中$\delta_j>0$表示R对训练数据的价值评估。该参数反映了：相同数据能为不同模型带来的收益不同。

$$
g_j=ln(1+\delta_j\sum_{i\in W}s_{ij})\tag{4}
$$
7. 请求者j的成本函数$v_j()$被定义为其成本减去训练所得收益，如下，其中$\alpha_j\geq 0,\beta_j\geq 0$是用于将训练延迟和训练收益转为货币的参数。R的目标函数是最小化$v_j()$。

$$
v_j()=\alpha_j\sum_{i\in W}l_{ij}+p_j\sum_{i\in W}s_{ij}-\beta_jg_j
$$

### 三类交互
根据公式3，worker的效用函数可以改写为：
$$
\begin{align}
u_i(s_i,s_{-i})&=\sum_{j\in R}(p_j-c_i)s_{ij}\\
&=\sum_{j\in R}((b_j-\rho_j^{-1}\sum_{k\in W}s_{kj})-c_i)s_{ij}
\end{align}\tag{7}
$$
其中$s_{-i}$表示除了i以外其他所有worker的策略向量，意味着每个worker的收益和其他worker的策略有关。worker的目标是最大化该函数。

在给定$p_j> c_i$的情况下，worker会尽可能设置一个大的$s_{ij}$。但这样做间接降低了其他所有workers的收益。另一方面，如果所有worker为R带来了更多收益，那么R就有更多钱能付给worker。这两个相互冲突的因素构成了WW互动。


## 问题形式化

## 本文方案

## 数值分析

## 结论

## 存在问题
1. Section 3第一章最后一句出现了energy，这个词应该是指computing power？建议相同的词统一表述，后面还出现了computation capability