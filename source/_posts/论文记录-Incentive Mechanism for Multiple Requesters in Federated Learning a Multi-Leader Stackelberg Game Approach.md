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
v_j()=\alpha_j\sum_{i\in W}l_{ij}+p_j\sum_{i\in W}s_{ij}-\beta_jg_j\tag{5}
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

RW互动：对于给定报酬，W希望通过设置合适的$s_{ij}$来最大化自己的效用；而R希望通过设置合适的$p_j$来在最小化成本的前提下吸引W为模型贡献

RR互动：每个R吸引到更多W的同时，意味着其他R面临的训练延迟更高
## 问题形式化
RW互动建模为二阶段Stackelberg博弈，R是leader，W是follower，包含两个子博弈：
1. WW：当所有R的策略都给定后，W们决定自己的策略，每个W的效用取决于其他W的策略，对于给定的所有其他W的策略集而言，能最大化自己效用的策略就是最好的，所有W的最优策略组成的集合就是该子博弈的均衡解。
2. RR：尽管R的成本与W的策略有关，但R在制定策略时并不知道W的策略。对于给定的W策略集以及其他所有R策略集而言，能最小化自己成本的策略就是最好的，所有R的最优策略组成的集合就是该子博弈的均衡解。

总的流程就是R先制定策略，即确定$b_i$，然后W根据R的策略决定策略，即确定$s_{ij}$，上述两个子博弈的均衡解合起来组成了整个RW博弈的均衡。

## 本文方案
### W博弈的均衡
找均衡解本质上就是找收益函数的极大值，这篇论文是找公式7的一阶导和二阶导。

一阶导写完整就是：

$$
\begin{align}
\left\{\begin{array}{c}
\frac{\partial u_{i}(\cdot)}{\partial s_{i 1}} & = b_{1}-2 \rho_{1}^{-1} s_{i 1}-\rho_{1}^{-1} \sum_{k \in W \backslash\{i\}} s_{k 1}-c_{i} & = 0, \\
\frac{\partial u_{i}(\cdot)}{\partial s_{i 2}} & = b_{2}-2 \rho_{2}^{-1} s_{i 2}-\rho_{2}^{-1} \sum_{k \in W \backslash\{i\}} s_{k 2}-c_{i} & = 0, \\
\vdots \\
\frac{\partial u_{i}(\cdot)}{\partial s_{i r}} & = b_{r}-2 \rho_{r}^{-1} s_{i r}-\rho_{r}^{-1} \sum_{k \in W \backslash\{i\}} s_{k r}-c_{i} & = 0 .
\end{array}\right.
\end{align}\tag{17}
$$

再进一步对每个$i$写完整：

$$
\begin{align}
\left\{\begin{array}{c}
b_{1}-2 \rho_{1}^{-1} s_{11}-\rho_{1}^{-1} \sum_{k \in W \backslash\{1\}} s_{k 1}-c_{1} & = 0, \\
b_{2}-2 \rho_{2}^{-1} s_{12}-\rho_{2}^{-1} \sum_{k \in W \backslash\{1\}} s_{k 2}-c_{1} & = 0, \\
b_{r}-2 \rho_{r}^{-1} s_{1 r}-\rho_{r}^{-1} \sum_{k \in W \backslash\{1\}} s_{k r}-c_{1} & = 0, \\
\vdots \\
b_{1}-2 \rho_{1}^{-1} s_{w 1}-\rho_{1}^{-1} \sum_{k \in W \backslash\{w\}} s_{k 1}-c_{w} & = 0, \\
b_{2}-2 \rho_{2}^{-1} s_{w 2}-\rho_{2}^{-1} \sum_{k \in W \backslash\{w\}} s_{k 2}-c_{w} & = 0, \\
\vdots \\
b_{r}-2 \rho_{r}^{-1} s_{w r}-\rho_{r}^{-1} \sum_{k \in W \backslash\{w\}} s_{k r}-c_{w} & = 0 .
\end{array}\right.
\end{align}\tag{18}
$$
这是关于W策略向量s的一组线性方程，可以转为矩阵形式：
$$
sM=E\tag{19}
$$
其中，$M_{w\times w}$是如下的分块矩阵：
$$
\begin{align}
M(k,l)=
\left\{\begin{array}{c}
M_1, &if k=l\\
M_2, &otherwise
\end{array}\right.
\end{align}\tag{20}
$$
矩阵$M_1,M_2$都是$r\times r$的方阵，分别如下：
$$
\begin{align}
M_1(k',l')=
\left\{\begin{array}{c}
2\rho_{k'}^{-1}, &if k'=l'\\
0, &otherwise
\end{array}\right.
\end{align}\tag{21}
$$
$$
\begin{align}
M_2(k',l')=
\left\{\begin{array}{c}
\rho_{k'}^{-1}, &if k'=l'\\
0, &otherwise
\end{array}\right.
\end{align}\tag{22}
$$
矩阵$E_{1\times w}$是：
$$
\begin{align}
E(1,k)&=(b_1-c_k,...,b_r-c_k)\\
&=(\textbf{b}-c_kJ)
\end{align}\tag{23}
$$
其中$\textbf{b}=(b_1,...,b_r), J=(1,...,1)$

一阶导为0对应的解$s$就是WW博弈的均衡解，根据公式19，$s^\ast=EM^{-1}$，所以只要矩阵$M$是可逆的，就说明存在均衡解$s^\ast$，接下来通过证明$det(M)\neq 0$来证明矩阵可逆，这里进行了一些矩阵初等变换。

然后计算二阶导，并得到了新的矩阵，证明了W的收益函数是concave的，由此一阶导得到的那个点是最大值，由此可以通过$EM^{-1}$计算均衡解如下：

$$
s_{ij}^\ast=\frac{\rho_j}{w+1}(b_j-wc_i+\sum_{k\in W\backslash\{i\}}c_k)\tag{34}
$$

### R博弈的均衡
对于R来说，他能预估到W会按照公式34的均衡解确定策略，那么就可以把公式34的结果代入到公式5中。首先把公式5写完整：
$$
v_j()=\alpha_j\sum_{i\in W}(\lambda_i\sum_{k\in R\backslash\{j\}}s_{ik})+b_j\sum_{i\in W}s_{ij}-\rho_j^{-1}(\sum_{i\in W}s_{ij})^2-\beta_jln(1+\delta_j\sum_{i\in W}s_{ij})\tag{35}
$$

代入$s_{ij}$后可得：
$$
v_j()=(D-D^2)b_j^2\rho_j+(F-2DF)b_j\rho_j-F^2\rho_j+\alpha_iC-\beta_jln(1+\delta_j(Db_j\rho_j+F\rho_j))\tag{36}
$$
其中：
$$
\begin{align}
C&=\sum_{i\in W}\lambda_i(\sum_{k\in R\backslash\{j\}}\frac{\rho_k}{w+1}(b_k-wc_i+\sum_{l\in W\backslash\{i\}} c_l))\\
D&=\frac{w}{w+1}\\
F&=-\frac{1}{w+1}\sum_{i\in W}c_i. \tag{37}
\end{align}
$$
可以看到每个R的决策会受到其他R决策的影响。本文的思路是证明函数$v_j$是convex的，从而最优策略就可以通过一阶导来找，而这需要二阶导为正，直接对公式36求导即可证明。一阶导得到的$b_j^\ast$就是均衡解。

这里存在一个问题：一阶导写出来是如下的形式：
$$
v_j^\prime=2(D-D^2)b_j\rho_j+(F-2DF)\rho_j-\beta_j\frac{\delta_jD\rho_j}{1+\delta_j(D\rho_jb_j+F\rho_j)}
$$
这其中涉及的参数包括：
1. $b_j$：是要计算的策略
2. $\rho_j$：R的数据量需求边际递减率，大致就是当数据量多起来以后，R就不那么需要数据了
3. $\beta_j$：R通过训练能得到的收益
4. $\delta_j$：R对训练数据价值的估值
5. D：涉及workers的数量
6. F：涉及所有workers的单位成本

前五个都好说，是公开的，第6个应该没法获取吧，即便让workers都主动说，也很可能报个假的

### 总博弈的均衡
前面两部分合起来就形成了总的均衡，因此整体流程如下：
1. 根据已有信息构造公式36的一阶导；
2. 计算一阶导对应的零点得到$b_j^\ast$；
3. 把前面这步得到的结果代入公式34，得到$s_{ij}^\ast$

然后分析了算法复杂度。
## 数值分析
1. 简而言之，W的效用与需求直接相关，与供应成反比。与此相反，R的成本随需求增加而增加，但随着供应增加而大致保持不变。得出这一结果的理由是，在斯塔克尔伯格博弈中，作为领导者的R比W具有先发优势，因此请求者总能优化自己的结果。
2. 简而言之，W的单位计算成本越高（即 ci 值越大），其效用就越低，但效用与工人的计算能力（$\lambda_i$)无关。
3. 总之，当某个工人的延迟成本较高（即 λi 值较大）或请求者以较高的价格评估延迟成本（即 αj 值较大）时，请求者的成本就会增加。如果某个请求者 j 更看重相同数量的训练数据（即具有更大的βj 或δj 值），或为相同数量的数据支付更少的单位数据费用（即具有更大的ρj 值），那么请求者本身和所有工作者都会受益，而所有其他请求者则会付出较小的代价。
## 结论
关注RR之间的互动，证明了均衡解存在

## 存在问题
1. Section 3第一章最后一句出现了energy，这个词应该是指computing power？建议相同的词统一表述，后面还出现了computation capability

### 审稿意见
#### Summary
本文关注联邦学习场景requesters和workers之间的博弈，将requesters确定基本单价、workers随之确定数据量分配方案的过程建模为stackelberg博弈，并使用推导+逆向归纳的方法寻找该博弈的均衡解。与已有研究不同在于本文重点关注requesters之间的interaction。最后通过数值分析得出了一系列与requesters成本和workers效用相关的结论。

This paper focuses on the game between requesters and workers in the context of federated learning. It models the process where requesters set the basic unit price and workers subsequently determine their data allocation schemes as a Stackelberg game. The equilibrium solution of this game is derived using forward and backward induction methods. Unlike existing studies, this paper emphasizes the interaction between requesters. Finally, numerical analysis is conducted to draw a series of conclusions related to the costs of requesters and the utilities of workers.
#### Strength
1. 作者关注了requesters之间对workers算力和数据的竞争博弈这一被其他研究所忽略的问题，which is innovative.
2. 作者通过巧妙的矩阵运算得到了一组可行的均衡解，which is XX.
3. The main idea of this paper is described clearly and the research work is complete, containing detailed data analysis.

- The authors focus on the competitive game between requesters for workers' computational power and data, an issue overlooked by other studies, which is innovative.
- The authors have derived a set of feasible equilibrium solutions through ingenious matrix operations, which is commendable.
- The main idea of this paper is described clearly, and the research work is complete, containing detailed data analysis.

#### Weakness
1. 作者声称requesters之间的interaction鲜有研究讨论，但有一些关注该问题的论文并没有在related works中列出，例如"Digital Twins Enabled On-Demand Matching for Multi-Task Federated Learning in HetVNets". 另一方面，本文中对联邦学习场景下requesters之间博弈的处理方式与crowdsourcing场景下的requesters之间的博弈分析非常相似。如果将调研拓展到crowdsourcing领域，则有更多这方面的研究。建议作者进行更充分的调研。
2. 本文对requester成本函数、workers收益函数以及双方博弈的建模过于理论化，建议补充一些实际数据以说明函数和参数设计的合理性。
3. 本文的主要目的是帮助requesters设计以基础单价为核心的激励机制，那么该机制与其他联邦学习中的用户激励机制相比有怎样的优势呢？
4. 本文在数值分析中的各参数取值有什么依据？这样完全由虚拟数据得到的实验现象说服力较弱。
5. 本文中先后出现了energy、computing power和computation capability，根据我的理解，这三个词表述的应该是相同含义，建议作者统一前后文的表述。
6. 本文的模型中涉及许多参数，而作者没有介绍这些参数是公开信息还是私有信息。
7. Requesters需要根据公式38来计算最优策略，这看起来要求每个requester都了解所有workers的单位成本，which is 不合理的。在实际场景中，workers并不会公开自己的训练成本，即便强制要求公开，workers也很可能谎报。

- The authors claim that the interaction between requesters has been rarely discussed in research, but some papers that address this issue are not listed in the related works section, such as "Digital Twins Enabled On-Demand Matching for Multi-Task Federated Learning in HetVNets." On the other hand, the approach to modeling the game between requesters in federated learning scenarios is very similar to the analysis of games between requesters in crowdsourcing scenarios. Expanding the research to include the crowdsourcing field would reveal more studies on this topic. The authors are advised to conduct a more comprehensive literature review.
- The modeling of the requester cost function, workers' utility function, and the game between both parties is overly theoretical. It is recommended to supplement with actual data to demonstrate the reasonableness of the function and parameter design.
- The main purpose of this paper is to help requesters design incentive mechanisms centered around the basic unit price. What are the advantages of this mechanism compared to other user incentive mechanisms in federated learning?
- What is the basis for the parameter values used in the numerical analysis? The experimental phenomena derived from entirely virtual data are less convincing.
- The terms "energy," "computing power," and "computation capability" appear sequentially in the paper. According to my understanding, these terms should represent the same concept. It is recommended that the authors unify the terminology throughout the text.
- The model in this paper involves numerous parameters, but the authors do not clarify whether these parameters are public information or private information.
- Requesters need to calculate the optimal strategy based on Equation 38, which appears to require each requester to know the unit cost of all workers. This is unrealistic. In practical scenarios, workers do not disclose their training costs, and even if forced to do so, they are likely to misreport.
