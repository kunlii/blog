---
title: "论文记录-Libra: An Efficient Mechanism for Merging Cross-chain Transactions Verification"
date: 2024-09-06 10:21:36
tags:
  - 审稿意见
  - blockchain
categories: 论文
description: infocom2025的审稿
---
## Abstract
跨链交易验证主要方法是看这个交易的Merkle证明，但是不同跨链交易的Merkle证明可能有重叠，这增加了计算开销。本文提出了Libra，设计了构造合并Merkle证明的递归算法，考虑到执行限制问题，设计了近似比率分析的启发式算法，把跨链交易划分开，然后合并每组的Merkle证明。仿真实验评估了验证开销，存储量和计算量分别减少了1.4 ~ 5.2倍和1.5 ~ 5.2倍。

 ## Intro
跨链交易的简单支付验证（SPV）的核心思路是检查被验证交易的哈希及其父节点和一路往上的若干节点的哈希值是否能计算得到区块头中存储的根哈希。当有很多跨链交易需要验证时，现有的方案是挨个验证，但不同跨链交易转到同一个目标链时，大概率会有一部分相同的路径哈希，这样挨个验证就会产生重复计算。例如，在比特币中，给定$n$笔交易，$m$笔跨链交易，目标链上的验证具有$O（mlog n）$的时间和空间复杂度。如果跨链事务在树中相邻，则冗余重叠至少为$O（m · log（n m））$，因为从它们的共同祖先到根的路径在每个跨链事务的Merkle证明中重复。

由此，本文提出Libra，在证明时降低冗余，允许目标链以较低的成本验证一组跨链交易。这需要解决两个问题：
1. 如何高效去重？本文设计了递归默克尔证明合并算法，实现去除默克尔证明过程中的冗余计算。
2. 如何处理证明聚合的大小限制？设计了启发式算法，对跨链交易分组，仅合并同组的默克尔证明，避免证明量超过目标链的限制。

本文第一个关注这个问题并解决，主要贡献概括如下：
1. 提出了Libra机制，这是一种在考虑目标链执行限制的情况下，提高跨链交互验证效率的机制。Libra设计了两个核心模块：首先使用模块2对交易进行分区，然后使用模块1为每个组构建和验证合并的Merkle证明。 
2. 针对模块1中减少冗余验证的问题，提出了一种递归算法来在构建过程中派生合并的Merkle证明以及相应的验证算法，并分析了其性能。 
3. 针对模块2中确保合并验证保持在执行限制内的跨链交易分区问题，提出了一种启发式算法，并分析了该NP难题的近似比。 
4. 通过实验模拟评估了所提出算法的性能。结果显示，Libra可以将存储和计算开销分别减少1.4倍至5.2倍和1.5倍至5.2倍。

## PRELIMINARY
### 跨链系统
$CS = (B_{src}\cup B_{dst} \cup R_c \cup Info_c)$，括号里的四个东西分别表示源链、目标链、中继节点和跨链交易。

跨链系统中的SPV包含两层：
1. 同步层：中继节点检查源链生成的块，并按顺序将区块头提交给目标链；
2. 验证层：中继节点检测新区块中的新跨链交易，构造默克尔证明，并把证明和交易都发给目标链去验证。
3. 目标链上的智能合约根据新跨链交易的哈希值逐层计算区块头的哈希值，并与区块头中存的哈希对比，如果相等，说明跨链交易在源链已上链，可被目标链信任。

### Libra框架
总体思路如下图所示，假设有来自同一个区块的4个新跨链交易，如果每个交易单独验证，则每个交易都需要4个哈希值来计算根哈希，总共要16个；而如果能合起来算，则总共只需要再来6个哈希，剩下10个都可以计算过程中得到。

![libra-overview](https://github.com/kunlii/image/blob/master/libra-1.png?raw=true)

总的来说这个东西分两个模块，模块2用来根据目标链的限制合理划分大小，确保验证的时候不会超出限制，模块1用来把一群交易的默克尔证明合并起来减少计算量。

==这部分内容感觉图不太好理解，结合图与文字不太能理解俩模块是咋回事，以及为啥模块1就包含了2、3、4，而模块2则包含了1。此外，这部分的文字描述有点冗余了。==

==看完后面我理解了，这里是说模块1包含了图中的第2、3、4步，也就是构建发送和验证，并不是说包含了三个节点。==
### 问题描述
给定跨链系统$CS$，其交互频率是$\alpha$，即叶节点表示$Info_c$到$B_{dst}$的概率是$\alpha$（这句话完全没理解）。

给定指定区块$b$的默克尔树$T=(V,E,H)$，其每个叶节点都有$\alpha$的概率表示到$B_{dst}$的跨链交易$Info_c$。树的节点是$V$，数量从0开始按顺序递增，顺序是从叶到根、从左到右。树的边是$E$，高度是$H$。

**问题1**：模块1的关键是解决冗余验证问题，其目标是在$B_{dst}$上同时验证所有跨链交易的默克尔证明。该问题转化为：构造一个合并的默克尔证明，包含了所有跨链交易，并具有最少的边数。$V_L\in V$是所有表示跨链交易的叶节点的标识符，上述问题可形式化为：
$$
\begin{align}
& max \sum_{u,v\in V}E_{u,v} \tag{1}\\
& s.t.\\
& E_{v,u}=1, \forall v\in V_L,\forall u\in p(v)\tag{2}\\
& E_{w,v}\leq E_{v,u}, \forall w\in V,\forall v\in p(w),\forall u\in p(v)\tag{3}\\
& E_{i,j}\in\{0,1\},i<j \tag{4}
\end{align}
$$

其中$p(v)$表示$v$的前驱节点。

**问题2**：模块2的关键问题是解决跨链交易划分问题，概括来说就是在区块限制的条件下尽可能多地合并证明，形式化为：
$$
\begin{align}
& max\sum_{G_i\in G}\sum_{V_j\in V_L}Reward(\{z_j:x_{ij}\cdot V_j\})\tag{5}\\
& s.t.\\
& \sum_{G_i\in G}x_{ij}=1,\forall V_j\in V_L\tag{6}\\
& l_i\leq L, \forall G_i\in G\tag{7}\\
& l_i = Cost(\{z_j:x_{ij}\cdot V_j\}),\forall G_i\in G,\forall V_j\in V_L\tag{8}\\
& x_{ij}\in\{0,1\} \tag{9}
\end{align}
$$

其中，约束6表示每个叶节点所表示的跨链交易应该只属于1个分组，约束7表示每组的总成本不能超过目标链的限制。

这是个NP问题。

==这部分的问题主要在于几个符号表达，显然V是集合，V_L中也不止一个节点，想必也是集合，那就不该用\in 这个符号连接它们，然后这俩问题的形式化公式有点不好理解，反正我看了半天是没搞明白。==

### 成本与奖励模型
成本体现在目标链的限制上，在以太坊上体现为gas的消耗量，本质上是验证默克尔证明所需要执行的操作复杂度。用合并后的默克尔证明的边数表示总成本$C$。

定义1：给定默克尔树$T=(V,E,H)$，一个包含确定叶节点$G_i=\{V_j|j=1,2,...,m_i,V_j\in V_L\}$的分组的成本可以如下计算：

$$
C(G_i)=H+h_1+h_2+...+h_{m_i-1}
$$

其中$h_i$表示$V_i$和$V_{i+1}$的最近的共同祖先的高度。

分组的奖励$R$是指一个组不合并默克尔证明与合并默克尔证明相比，所需要多计算的那些重复边的数量。

定义2：给定默克尔树$T=(V,E,H)$，一个包含确定叶节点$G_i=\{V_j|j=1,2,...,m_i,V_j\in V_L\}$的分组的奖励可以如下计算：

$$
R(G_i)=0+(H-h_1)+(H-h_2)+...+(H-h_{m_i-1})
$$

$h_i$和前面一样。

根据这两个定义可得

$$
C(G_i)+r(G_i)=m_i\cdot H \tag{10}
$$

其中$m_i=|G_i|$，$m_i$指$G_i$中表示跨链交易的叶节点的数量。

==定义1和定义2中的表达有问题，应该是cost/reward of a group ... can be calculated as follows.==

## 合并默克尔证明的递归算法

这个算法在前面已经介绍得比较清楚了，实际上没什么技术点，大致流程描述如下：
1. 整个流程是从根往叶走的，先判断一下当前是否已经到叶节点所在的层，如果是，则结束递归，返回空，否则开始走下面的流程；
2. 当前层的哈希赋值给$L_c$；
3. 遍历这一层中的所有节点：
	1. 如果当前节点和下一个节点共享父节点，则合并其父节点，哈希只需要算一次；
	2. 否则：
		1. 偶数节点的情况，则找到下一个相邻节点并将其哈希值添加到 $P_c$。
		2. 奇数节点的情况，则找到下一个相邻节点并将其哈希值添加到 $P_c$。
4.  这一层的$P_c$添加到 $P_m$。
5. 递归调用算法处理下一层。
6. 返回最终合并好的默克尔证明。

==这个算法是真的不太好理解，首先二叉树为啥不直接用指针找父节点，而是log的方式去算；其次，前面说输出是Pc，伪代码里说Pc合并到Pm，最后return的又是Pp，到底返回哪个呢？==

上面这个是算法1，用来合并，还有一个算法2，是说构造好默克尔证明之后，给出一串交易，怎么验证正确性，这里和SPV是一样的，就不记录了。

==这里有个问题，验证为true还好说，万一验证为false，怎么判断是哪个交易出的错？==

性能分析就对比了复杂度，没什么好说的。

## 划分交易的算法

属性1：给定二叉树$T=(V,E,H)$和分组$G_i, G_j,i<j,V_k<V_l,\forall V_k\in G_i,\forall V_l\in G_j$，如果有另一个节点$V_c>V_k,\forall V_k\in G_j$，则

$$
\Delta\frac{R}{C}|\{V_c\}\cup G_j\geq\Delta\frac{R}{C}|\{V_c\}\cup G_i \tag{11}
$$

证明：$h$表示$V_c$和$G_i$中索引号最大的那个节点之间的最近的共同祖先的高度。R和C的定义如前所述。如果给$G_i$添加新的节点$V_c$，则每单位成本所对应的奖励可以如下计算：
$$
RPUC(h)=\frac{R(G_i)+H-h}{C(G_i)+h}=\frac{\sum_{j=1}^{|G_i|-1}(H-h_j)+H-h}{H+\sum_{j=1}^{|G_i|-1}h_j+h}
$$

可以看出，$h$与前面的R和C无关，则

$$
RPUC'(h)=\frac{-2h-(2H+\sum_{j=1}^{|G_i|-1}h_j+\sum_{j=1}^{|G_i|-1}(H-h_j))}{(H+\sum_{j=1}^{|G_i|-1}h_j+h)^2}
$$

显然，h增加时，RPUC(h)单调减。我们还可以通过相反的论证来证明$h(V_c,G_j)$不会超过$h(V_c,G_i)$。

令$V_\ast^l$是$G_\ast$的索引最大的节点，则$V_c>V_j^l>V_i^l$。

假设$h(V_c, V_j^l)>h(V_c, V_i^l)$，则$2^{h(V_c, V_j^l)}>2^{h(V_c, V_i^l)}$。这意味着$T$上$V_c$和$V_j$之间的叶节点数量多于$V_c$和$V_i$之间的，由此可知，$V_c-V_j^l>V_c-V_i^l$。但这与给定条件冲突。

因此，如果节点按顺序添加，则将节点放置到相邻组的奖励增量将不小于加入更远的组的奖励增量。

基于性质1，我们为问题2提供了一种贪心算法，称为G-PARTITION。我们尽可能将每个节点放入最近的前一组中。然而，还有一个因子β来限制每组共同祖先的最大高度。此外，选择合适的β可以进一步提高总奖励。

接下来分析了可行性和逼近率，讨论了分区数量等问题。

## 实验
为了进行性能评估，我们实现了 Libra 的原型，并通过以太坊上的智能合约验证合并后的 Merkle 证明。我们在配备 80 核 20 线程 Intel(R) Xeon(R) Gold 6230 CPU @ 2.10GHz 和 256G RAM、运行 CentOS Linux 7 的机器上进行实验。为了进行比较，我们还实现了验证每笔交易的基本 SPV具有非合并默克尔证明。我们随机生成具有给定跨链交互频率α的跨链交易。我们从三个方面来评价Libra。
1. 对验证开销的影响
2. 对默克尔证明构建开销的影响
3. 分区算法中几个参数的影响

## 总体意见
### summary
The paper presents a novel approach to optimize cross-chain transaction verification by introducing the Libra mechanism, which focuses on merging Merkle proofs to reduce redundant verification overhead. The paper addresses the challenge of overlapping Merkle proofs in cross-chain interactions and proposes both recursive and heuristic algorithms to manage the execution limits of target blockchains, such as Ethereum's gas limits. The key goal is to reduce redundancy in hash computations during cross-chain transaction verification, ensuring efficient handling of transactions by sharing hash paths and minimizing computational costs. The paper introduces several theoretical properties to support the proposed algorithm's correctness and efficiency. Extensive simulations demonstrate the proposed method's effectiveness in reducing computational and storage overhead.

### strength：
1. The paper includes thorough experimental simulations to validate the performance of the proposed Libra mechanism. The metrics used—gas consumption, proof size, and computational calculations—are relevant and well-chosen to showcase the benefits of the approach.
2. The recursive and heuristic algorithms are well-formulated, and the paper provides rigorous proofs for the proposed optimization, including the derivation of the approximation ratio and performance bounds. This theoretical rigor adds credibility to the approach.
3. The paper does a good job of clearly defining the problem and breaking it down into two key challenges: reducing redundant verifications and handling execution limits on target chains. The structure of the paper is logical and easy to follow.

### weakness：
1. There is noticeable repetition between the Introduction and Overview sections, with both covering similar points about cross-chain verification and the Libra mechanism. The language could be made more concise to avoid redundancy and improve readability.
2. In Section 2.C, notations such as V, V_l, and V_j are clearly representing sets, but they are incorrectly linked using the '\in' operator. This notation is misleading, and the connection between these sets should be clarified. Additionally, formulas (1) to (9) are difficult to follow, and the accompanying textual descriptions should be more straightforward to facilitate understanding.
3. The wording in Definitions 1 and 2 is problematic. Instead of "the cost/reward **can** be calculated as follows," it should state "the cost/reward of a group **is** calculated as follows" for clarity and precision in defining these terms.
4. Algorithm 1 is hard to comprehend. First, it is unclear why binary trees are not using pointers to directly find parent nodes, but instead rely on logarithmic calculations. Moreover, the output specification is confusing—initially, the output is described as P_c​, but the pseudocode mentions merging P_c​ into P_m​, and the return value is P_pP. It is unclear which output is being returned, and this should be clarified.
5. The paper does not address how to diagnose issues when Algorithm 1 returns a "false" result. In such cases, it is important to identify which specific transaction caused the verification failure. The lack of error tracking makes it difficult to trace faults within the transactions.
6. The figures presented in the simulations section are not vector-based, which impacts their clarity and scalability. Switching to vector graphics would significantly enhance the readability and visual quality of the graphs, especially in printed or zoomed-in formats.
7. The Related Work section lacks depth in its analysis of existing studies. The discussion of prior research is too brief and does not thoroughly explore the differences, similarities, or gaps compared to the current work. A more detailed analysis would strengthen the positioning of the paper in the context of existing research.