---
title: 论文记录-Truthful Mobile Crowd Sensing with Interdependent Valuations
date: 2023-08-31 21:35:43
tags:
  - trustworthiness
  - crowdsourcing
  - mechanism-design
category: 论文
description: 具有相互依赖的估值的真实移动群智感知
---
## 综述
1. 阅读本文的原因：真值挖掘相关
2. 本文研究目的：为移动众包系统设计激励机制，使得用户能够真实地报告它们的传感数据。
3. 本文与我研究的相关性：也是防谎报的机制，感觉能迁移到其他场景
4. 本文的潜在假设：用户的报酬函数会受到其他用户报告的传感数据的影响，即存在互相关联的报酬。
5. 本文研究方法：设计了一个叫做T-SAB的机制，它包括两个阶段。在第一阶段，用户报告它们的传感数据。在第二阶段，用户根据一个代理函数提交出价，平台根据出价分配资源和确定支付。这种机制可以在用户的信息规模较小时实现资源的有效分配和近似的真实报告。
6. 本文研究结论：随着用户数量的增加，用户扭曲报告的能力会降低，因此T-SAB机制可以实现真实报告的平衡。
7. 是否进一步阅读：是

## Intro
1. 本文针对的场景：移动网络资源 MCS（如频谱感应、WiFi 热点和蜂窝网络覆盖）、交通监控和智能停车
2. 该场景的特点：
	1. 移动用户可以同时充当工作者和消费者；
	2. MCS 平台既要收集感知数据，又要分配资源。即，用户通过完成移动通信服务任务来发现资源；基于感知数据，移动通信服务平台力求充分利用所发现的资源，激发用户的长期参与，例如，通过向用户有效分配资源来满足他们的需求。
3. 本文关注的挑战：资源发现 MCS 带来了估值相互依存的挑战，即每个用户对所分配资源/服务的估值取决于其他用户的私人感知数据。这样的相互依赖使得传统机制不可用，后续章节会具体分析。
4. 关键问题1：在相互依存的评价环境中，平台应如何激励真实的传感数据报告？
5. 已有研究：VCG机制可在信息量较小的情况下实现高效分配和近似真实
6. VCG不可用的原因：
	1. 该机制假设用户的效用函数是全局已知的。然而，在实践中，用户的效用信息往往是私有的，用户也可能策略性地误报这些信息，从而操纵分配结果。
	2. 网络资源（如频谱和带宽）可以被分割（即可以无限分割），在这种情况下，每个用户的效用函数都是无限维的。在这种情况下，[13] 中的 VCG 型机制在大规模 MCS 系统中可能会产生令人望而却步的通信开销（在向其他用户完全描述效用函数方面）。
7. 关键问题2：在私人效用信息和相互依存的估值情况下，平台应如何设计一种机制，以实现高效分配和真实的传感数据报告？（感觉和关键问题1没区别啊）
8. 本文贡献：设计了一种基于代理函数的机制，只需对每种资源进行一维信令，就能揭示用户的边际效用信息，因此通信开销很小。为了克服相互依赖的估价问题，我们证明了代理函数能带来有效的分配，同时每个用户的数据对分配的影响相当有限，从而阻止了用户的误报。
	1. 问题表述： 我们提出了真实多传感器感知数据激发和有效分配的联合问题，其中考虑到了战略用户相互依赖的估值和私人效用信息。据我们所知，这是第一个研究 MCS 系统相互依存估值问题的论文。
	2. T-SAB 机制： 我们设计了一种具有相互依赖估值和私人效用信息的 "真实感与出价"（T-SAB）机制。当用户的信息量较小时，该机制会产生一个完美贝叶斯均衡（PBE），它具有高效分配结果、近似真实性、个体理性和近似预算平衡，这在大型 MCS 系统中是一个合理的条件。
	3. 多资源扩展： 我们提出的 T-SAB 机制适用于多资源分配问题，它概括了 [30, 31] 中的单资源代理函数机制，克服了用户估值和决策耦合的难题。
	4. 数值结果与启示： 在认知无线电网络资源分配的示例中，我们的数值结果表明，当用户数量众多且个人感知数据准确时，T-SAB 机制是真实的，并且预算平衡。此外，与因潜在误报而未充分利用 MCS 的基准相比，T-SAB 机制的社会福利收益可达 20%。

## RELATED WORK

## SYSTEM MODEL
1. MCS系统包含一个平台（管理者）和一组用户集合$\mathcal{I}={1\leq i\leq I}$
2. MCS任务旨在估计一组可分割资源$\mathcal{N}={1\leq n\leq N}$的可访问性和质量
3. 用户既是MCS任务的worker，也是资源的消费者
### System Overview
1. 世界状态：$\omega \triangleq\left\{\omega_n\right\}_{n \in \mathcal{N}}$表示未知随机的世界状态，$\Omega$表示所有可能的世界状态组成的有限集合，每个条目$\omega_n$代表资源$n$的质量和可用性，例如，无线信道$n$上的干扰级别或第$n$个WiFi接入点的拥塞级别。
2. 传感数据： 每个用户$i$获取随机传感数据$\theta_i\in \Theta_i$，可被用于估计世界状态$\omega$，需要提交给平台。上报的传感数据记作$\tilde{\theta}_i \in \Theta_i$，该数据可能是谎报的。$\theta_i$可能是多维的，$\theta \triangleq\left\{\theta_i\right\}_{i \in I} \in \Theta$表示用户传感数据概况。在所有其他用户的传感数据公开之前，每个用户对其他用户的数据$\theta_{-i} \triangleq\left\{\theta_j\right\}_{j \neq i}$有自己的先验信念，该信念用条件概率$P_{\Theta_{-i}}\left(\theta_{-i} \mid \theta_i\right)$来表示。通过收集所有用户的感官数据$\theta$，我们可以使用条件概率函数$P_{\Omega}(\omega \mid \theta)$来进行贝叶斯估计。
3. 网络资源： 资源发现平台旨在有效地将资源集分配给用户。令$x_i=\left\{x_{i, n}\right\}_{n \in \mathcal{N}} \in \mathbb{R}_{+}^N$表示平台的分配决策，其中$x_{i, n}$表示分配给用户$i$的资源量$n$（例如，第$n$频谱通道分配给用户$i$的时间份额）。用$x=\left\{x_i\right\}_{i \in I}$表示分配概况，$\mathcal{X}$表示所有$x$的集合，则：		
	$$
		\mathcal{X}=\left\{x: \sum_{i \in \mathcal{I}} x_{i, n} \leq C_n, x_{i, n} \geq 0, \forall i \in \mathcal{I}, \forall n \in \mathcal{N}\right\} \tag{1}
  $$
    
    其中，$C_n$表示资源$n$的容量。
4. 用户效用：用户$i$的效用函数$u_i(x_i,\omega)$，取决于世界状态$\omega$，是随$x_i$递增的Concave函数（凹函数，指二阶导小于0的那种，也就是增长速度越来越慢）。该函数表征了用户在特定状态$\omega$消耗分配资源所获得的收益。
5. 期望效用：由于确切的世界状态$\omega$未知，每个用户根据自己的后验效用来评估分配到的$x_i$的价值，条件是$\theta$，即：
	$$
	\bar{U}_i\left(x_i \mid \theta\right)=\sum_{\omega \in \Omega} u_i\left(x_i, \omega\right) P_{\Omega}(\omega \mid \theta) \tag{2}
	$$
	这被称为相互依存估值，因为每个用户的效用都取决于所有用户的传感数据。这里的后验指的是 "$\theta$ 实现后"，而不是 "$\omega$ 实现后"。

### Problem Formulation
MCS平台有两个目标：
1. 传感数据的真值揭示
2. 网络资源的高效分配

平台通过求解以下社会财富最大化问题来实现两个目标：
$$
\text { (SWM) } \max _{x \in \mathcal{X}} \sum_{i \in I} \bar{U}_i\left(x_i \mid \theta\right) \tag{3}
$$
该问题是一个凸优化问题。此外，$𝑢_𝑖(x_i,\omega)$ 随$x_i$的严格凹性意味着该函数的严格凹性，从而产生唯一的最优解$x^0$。

求解该SWM问题，需要平台了解完整且真实的$\theta$和$\left\{\overline{U}_i(\cdot|\cdot)\right\}$。然而，每个用户可能不愿意如实报告此类信息，因为操纵其报告可能会带来更有利的结果。因此，我们需要设计一种经济机制来有效地引发这种真实的传感数据报告并实现高效分配。理想情况下，这种机制应具有以下经济特性：
1. (E1) 分配效率：存在对应了SWM问题最优解的均衡
2. (E2) 真实性：在均衡下所有用户会报告真实传感数据
3. (E3) 个体理性：每个用户不会因为参与机制而变得更糟
4. (E4) 弱预算平衡：用户支出总额为非负数，即不需要平台投入资金

由于相互依赖的估值，设计满足这些属性的机制具有挑战性。从技术上讲，经济研究已经证明，一般来说，当存在相互依赖的估值时，（E1）和（E2）不可能同时实现。接下来我们将展示一个说明性示例。

### Winner’s Curse: An Illustrative Example（赢者诅咒）
以下示例表明，相互依赖的估值给设计机制带来了新的挑战。

考虑一个认知无线电网络中的频谱感知示例，该网络有$I$个次级用户和一个信道。每个用户都会收到一个频谱感知数据点$\theta_i\in [0, 1]$，该数据点给出了用户$i$的信道值。频谱管理者（主要用户）计划采用二价拍卖的方式拍卖该信道：每个用户都必须出价、出价最高者赢得该信道、并支付次高出价。

考虑以下两种情况：
1. 私有价值：每个用户的效用是$\theta_i$，这是典型的情况，其中每个用户的频谱效用仅取决于其自己的信号。众所周知，二价拍卖会促使用户如实报告其私人信息$\theta_i$，并且每个用户都会获得非负收益（效用减去价格）。
2. 公共价值：每个用户的效用是$\sum_{j=1}^I \theta_j / I$，这是一般相互依赖估值设置的特殊情况（其中用户具有相同的效用）。假设每个用户的信号$\theta_i$是服从$[0,1]$均匀分布的随机变量，每个用户都会向平台提交自己的信号$\theta_i$作为其出价。获胜者的付款是第二高的出价，遵循均匀分布的二阶统计，平均值为$(I−1)/(I+1)$。另一方面，该信道的期望效用是$\mathbb{E}\left[\sum_{i=1}^I \theta_i\right] / I=1 / 2$，这意味着当$I>3$时赢者的期望收益是$1 / 2-(I-1) /(I+1)<0$，且随$I$的增加而减少。也就是说，向用户宣布其获胜带来了“坏消息”。因此，每个用户都容易谎报$\theta_i$。在相互依赖的估值环境中，真实报告导致获胜者获得负收益的效果称为赢者诅咒。

因此，上面的例子说明用户可能不愿意如实报告他们的私人感官数据。我们已经证明，第二价格拍卖（VCG 机制在这种情况下的一个特例）在相互依赖的估值环境中不起作用。为了克服这一挑战，我们接下来设计了一种机制，可以精确地实现（E1）和（E3），同时仅近似地实现（E2）和（E4）。
## MECHANISM DESIGN WITH INTERDEPENDENT VALUATIONS
在本节中，我们设计了真实感知和投标（T-SAB）机制，并表明，在所提出的方案和 MCS 系统的合理条件（用户信息量较小）下，它满足属性 (E1)-( E4）。
### The T-SAB Mechanism
在本小节中，我们介绍 T-SAB 机制，讨论其直观性以及与现有机制的差异，并介绍诱导均衡概念。
#### 机制描述
如下图所示，T-SAB机制包含两阶段，在第一阶段，用户报告他们的传感数据（可能会谎报）。在第二阶段更新其估值后，每个用户进一步提交表明其私有效用信息的投标。根据提交的出价和预定义的代理函数，平台确定网络资源分配和用户支付，以实现高效、真实的均衡。

![T-SAB示意图](https://github.com/likun1208/image/blob/master/T-SAB-1.png?raw=true)
机制形式化的写法如下：
1. 阶段一（传感阶段）：
	- 信息空间：每个用户向平台和所有其他用户提交一份自己传感数据的报告$\tilde{\theta}_i \in \Theta_i$（可能是假的）。
2. 阶段二（分配阶段）：
	- 代理函数：平台向所有用户公开一个代理函数$f(x, \lambda): \mathbb{R}_{+}^2 \rightarrow \mathbb{R}$
	- 信息空间：每个用户向平台提交一个投标向量$\lambda_i=\left\{\lambda_{i, n}\right\}_{n \in \mathcal{N}} \in \mathbb{R}_{+}^N$，所有用户的投标记作$\lambda \triangleq\left\{\lambda_i\right\}_{i \in I}$
	- 收益函数：给定报告上来的传感信号$\tilde\theta$和提交的$\lambda$，平台选择一个分配方案$x^*(\lambda)=\left\{x_i^\ast(\lambda)\right\}_{i\in\mathcal{I}}$，使得：
		$$
		x^*(\lambda)=\arg\max_{x\in\mathcal{X}}\sum_{i\in \mathcal{I}}\sum_{n\in\mathcal{N}}f(x_{i,n},\lambda_{i,n}). \tag{4}
	$$
	每个用户$i$会被分配一笔支出
	
	$$
h_i(\boldsymbol{\lambda},\tilde{\boldsymbol{\theta}})=\max_{\boldsymbol{x}-i}\max_{\boldsymbol{j}\neq\boldsymbol{i}}\sum_{\boldsymbol{n}\in\boldsymbol{N}}f(x_{j,\boldsymbol{n}},\lambda_{j,\boldsymbol{n}})-\sum_{j\neq i}\sum_{n\in\mathcal{N}}f(x_{j,n}^*(\boldsymbol{\lambda}),\lambda_{j,n})-\epsilon\frac{P_{\Theta_{-i}}(\tilde{\boldsymbol{\theta}}_{-i}|\tilde{\boldsymbol{\theta}}_i)}{\left\|P_{\Theta_{-i}}(\tilde{\boldsymbol{\theta}}_{-i}|\tilde{\boldsymbol{\theta}}_i)\right\|_2}, \tag{5}
  $$

其中，$\epsilon\frac{P_{\Theta_{-i}}(\tilde{\theta}_{-i}|\tilde{\theta}_i)}{\left\|P_{\Theta_{-i}}(\tilde{\theta}_{-i}|\tilde{\theta}_i)\right\|_2}$是奖励函数，$\epsilon$是待设计的正近似系数，$x_{-i}=\{x_j\}_{j\neq i}$，且$\mathcal{X}_{-i}=\{x_{-i}:\sum_{j\in\mathcal{I},j\neq i}x_{j,n}\leq C_n,\forall n\in\mathcal{N},x_{j,n}\geq0,\forall n\in\mathcal{N},\forall j\neq i\}$。

#### 直观感受
用户提交投标从而基于平台预设的代理函数$f(x,\lambda)$决定他们的分配和收益情况。给定函数$f(x,\lambda)$，用户仅需在第二阶段向平台提交N维信息，通信负担很小。此外，在均衡的情况下，满足一定条件的代理函数能够刺激用户提交其边际效用满足特定条件的出价，从而提高均衡时的分配效率（E1）。另一方面，(5) 中设计的支付结构限制了每个用户通过虚假报告操纵 (4) 和 (5) 中结果的能力（将在第 4.3 节中说明）；(5) 中的奖励函数用于诱导每个用户真实地获取感官数据 (E2)。(5) 中奖励函数的选择依赖于$P_{\Theta_i}(\cdot|\cdot)$的设计，可根据第 5 节中说明的系统模型推导得出。

#### 代理函数
为了实现（或近似实现）属性（E1）-（E4），平台选择代理函数来满足以下正则条件：

定义1：正则。如果一个代理函数满足以下条件，则称它是正则的：
1. $f(x,\lambda)$是严格凹函数、严格增函数，且在$x$上连续可导；
2. 对于所有$\gamma\in(0,\infty)$且$x\geq 0$，都存在一个$\lambda>0$使得$f'(x,\lambda)=\gamma$。

一个正则函数的例子是$f(x,\lambda)=\lambda log(x)$。下面，我们将首先分析基于任意正则代用函数的 T-SAB 机制的特性，然后从第 4.3 节开始重点分析特定的正则函数。

#### 开销和与现有机制之间的差异
$T-SAB$机制的计算包含公式4中的一个优化问题以及公式5中的$I$个优化问题（每个用户一个）。给定根据定义1选择的严格凹的代理函数，上述$I+1$个优化问题都是凸的，可以高效求解。

接下来看通信开销。每个用户需要向平台和所有其他用户发送传感数据$\tilde{\theta_i}$，向平台发送报价$\lambda_i$，由此可得第一阶段的复杂度是$\mathcal{O}(|\Theta_i|\cdot I)$，第二阶段的复杂度是$\mathcal{O}(N)$。与之相反，参考文献12和13中假设用户的效用函数是全局公开的。我们注意到，12和13中考虑的经典 VCG 型机制需要报告携带无穷维信息的整个效用函数，这会产生难以承受的通信开销。

#### 信念系统和均衡概念
尽管阶段1上报的传感数据$\tilde\theta_i$并没有出现在阶段2公式4的收益函数中，但报告的传感数据会影响每个用户的评价（由于评价相互依赖的特点），从而影响用户在阶段2的决策。为了描述这样的特点，我们引入了针对每个用户$i$的信念系统$\beta_i(\cdot|\cdot)$。具体来说，在阶段1中给定报告值$\tilde\theta_{-i}=\left\{\tilde\theta_j\right\}_{j\neq i}$的情况下，每个用户$i$选择一个关于真值$\theta_{-i}=\left\{\theta_j\right\}_{j\neq i}$的信念$\beta_i(\theta_{-i}|\tilde\theta_{-i})$，因此，阶段2中每个用户在其传感数据$\theta_i$和其他用户传感数据$\tilde\theta_{-i}$条件下的后验期望收益是：
$$
J_{i}^{\beta_{i}}(\lambda|\theta_{i},\widetilde{\theta}_{-i})=\sum_{\theta_{-i}\in\Theta_{-i}}\bar{U}_{i}(x_{i}^{\ast}(\lambda)|\theta)\beta_{i}(\theta_{-i}|\widetilde{\theta}_{-i})-h_{i}(\lambda,\widetilde{\theta}). \tag{6}
$$
因为我们关注所有用户都说真话的均衡，我们考虑下述真实均衡概念：

定义2：真实完美贝叶斯均衡（PBE）。一个真实PBE是一个三元组$(\widetilde{\theta}^{\ast},\lambda^{\ast}(\widetilde{\theta}^{\ast}),\{\beta_{i}^{\ast}\}_{i\in I})$，使得：
$$
\lambda_i^*(\widetilde{\theta}^*)\in\arg\max_{\lambda_i\in\mathbb{R}^N},J_i^{\beta_i^*}(\lambda_i,\lambda_{-i}^*(\widetilde{\theta}^*)|\theta_i,\widetilde{\theta}_{-i}^*), \tag{7a}
$$
$$
\beta_i^*(\theta_{-i}|\tilde{\theta}_{-i})=\mathbb{I}(\theta_{-i}=\tilde{\theta}_{-i}), \tag{7b}
$$
其中，$\mathbb{I}(X)$是指示函数，当X为真时该函数取值1，当X为假是该函数取值为0。另一方面，$\tilde\theta^\ast$满足：
$$
\widetilde\theta_i^*\in\arg\max_{\tilde\theta_i\in\Theta_i}J_i^{\beta_i^*}(\lambda^*(\widetilde\theta_i,\widetilde\theta_{-i}^*)|\theta_i,\widetilde\theta_{-i}^*), \tag{8}
$$
或：
$$
\widetilde{\theta}_i^*\in\arg\max_{\widetilde{\theta}_i\in\Theta_i}\sum_{\theta_{-i}\in\Theta_{-i}}J_i^{\beta_i^*}(\lambda^*(\widetilde{\theta}_i,\widetilde{\theta}_{-i}^*)|\theta_i,\widetilde{\theta}_{-i}^*)P_{\Theta_{-i}}(\theta_{-i}|\theta_i). \tag{9}
$$
为了理解定义 2，(7a) 与 (8) 或 (9) 一起表征了用户的理性，即每个用户的策略应该是与其信念相关的期望最优策略。此外，(7b) 意味着信念一致性，即每个用户都根据贝叶斯规则更新其信念[42]。因此，(7b) 意味着在一个真实的 PBE 中，所有用户都相信其他用户是真实报告的。

我们注意到，(8)和(9)描述了在存在相互依赖的估值时两种不同的可信的真实性概念[13, 43]。特别是，(8) 意味着事后真实性，而 (9) 意味着事前真实性。事后真实性表明，在所有用户的真实传感数据被揭示后，每个用户都应该发现真实报告是其最优策略；而事前真实性则意味着，每个用户都期望真实报告是其当前可用信息（自身传感数据）条件下的最优策略。
### Equilibrium Analysis in Stage II
我们接下来分析给出用户传感数据$\tilde\theta$的情况下，阶段2中的均衡$\lambda^\ast(\tilde\theta)$。为了简化表达，我们在本节中使用$\lambda^\ast$来代替$\lambda^\ast(\tilde\theta)$。

因为我们关注真实PBE，所以本文仅考虑每个用户都认为其他用户会说真话的情况，即采用(7b)的信念系统。为了刻画阶段2中的用户交互，我们定义了T-SAB机制的以下子博弈，对于给定$\tilde\theta$：

博弈1（阶段2竞价子博弈）：阶段2中的竞价子博弈包含：
- 玩家：所有用户
- 策略空间：对于每个用户$i$都是$\lambda_{i}\in\mathbb{R}_{+}^{N}$
- 支付函数：每个用户$i$都是
$$
J_i^{\boldsymbol{\beta}_i^*}\left(\boldsymbol{\lambda}\Big|\boldsymbol{\theta}_i,\widetilde{\boldsymbol{\theta}}_{-\boldsymbol{i}}\right)=\bar{U}_i\left(\boldsymbol{x}_i^*(\boldsymbol{\lambda})\Big|\boldsymbol{\theta}_i,\widetilde{\boldsymbol{\theta}}_{-\boldsymbol{i}}\right)-\boldsymbol{h}_i\left(\boldsymbol{\lambda},\widetilde{\boldsymbol{\theta}}\right) \tag{10}
$$
其中，$x^{\ast}(\lambda)=\{x_{i}^{\ast}(\lambda)\}_{i\in I}$由公式4决定。

上述竞价子博弈包含以下均衡概念$\lambda^\ast$：

定义3（纳什均衡 NE）：博弈 1 的纳什均衡是满足 (7a) 的策略集$\lambda^\ast$。

假设其他用户都说真话，我们设计了一个NE来实现能最大化聚合期望效用的分配方案$x^\ast(\lambda^\ast)$，也就是说，我们将证明 NE 是以下问题的最优解：
$$
\max _{x \in \mathcal{X}} \sum_{i \in I} \bar{U}_i\left(x_i \mid \theta_i, \tilde{\theta}_{-i}\right)  \tag{11}
$$
我们从以下引理开始。

引理1：向量$\lambda$是博弈1的NE，当且仅当：
$$
x^*(\lambda) \in \arg \max _{x \in \mathcal{X}}\left[\bar{U}_i\left(x_i \mid \theta_i, \tilde{\theta}_{-i}\right)+\sum_{n \in \mathcal{N}} \sum_{j \neq i} f\left(x_{j, n}, \lambda_{j, n}\right)\right], \forall i \in I \tag{12}
$$
我们在附录 7.1 中给出了引理 1 的简要证明。直观上，(4) 和 (5) 中类似 VCG 的支付和分配结果会产生一个 NE，在该 NE 处，每个用户的兴趣与 (12) 中的问题一致。基于引理 1 以及 (12) 和 (11) 的 Karush-Kuhn-Tucker (KKT) 条件，我们有： 

推论 1（存在性）。博弈 1 存在一个NE  $\lambda^\ast$，可得出 (11) 中问题的最优解。

因此，使用正则代理函数$f(x,\lambda)$，每个用户选择$\lambda_i^\ast$表示其边际预期效用，就对应于一个 NE。我们可以进一步证明，所有 NE 都对应于 (11) 的最优解。

命题1（效率）：如果存在两个用户使得$\lim _{x_{i, n} \rightarrow 0} \partial u_i\left(x_i, \omega\right) / \partial x_{i, n}=\infty$ for all $n$，则阶段2的所有NE都对应了(11)的最优解。

主要的证明包括利用 (12) 和 (11) 的最优条件之间的相似性，证明存在一个近似值，可以得到 (11) 中问题的最优解。最后，如果有两个积极 "竞争 "的用户，他们的分配必须为正，即在 NE 上$x_i^*\left(\lambda^*\right)>0$，我们可以证明 (12) 的最优性条件等同于 (11) 的最优性条件，这意味着所有 NE 都应解决 (11) 中的问题。 
### Equilibrium Analysis in Stage I
基于对命题1的观察，我们可以进一步分析阶段1中的均衡结果。我们首先通过比较谎报用户$i$的传感数据所带来的改进，来确定 T-SAB 机制的特殊“增益受限”属性：

引理2：当所有其他用户都说真话时，每个用户$i$谎报传感数据的收益提升是有上限的：
$$
\begin{aligned}
& J_i^{\beta_i^*}\left(\lambda^*\left(\tilde{\theta}_i, \theta_{-i}\right) \mid \theta\right)-J_i^{\beta_i^*}\left(\lambda^*(\theta) \mid \theta\right) \\
\leq & \sum_{j \neq i} \sum_{n \in \mathcal{N}} f\left(\tilde{x}_{j, n}, \lambda_{j, n}^*\right)-\max _{x_{-i} \in \mathcal{X}_{-i}} \sum_{j \neq i} \sum_{n \in \mathcal{N}} f\left(x_{j, n}, \lambda_{j, n}^*\right) \\
& -\sum_{j \neq i} \sum_{n \in \mathcal{N}} f\left(\tilde{x}_{j, n}, \tilde{\lambda}_{j, n}\right)+\max _{x_{-i} \in \mathcal{X}_{-i}} \sum_{j \neq i} \sum_{n \in \mathcal{N}} f\left(x_{j, n}, \tilde{\lambda}_{j, n}\right)+\epsilon
\end{aligned} \tag{13}
$$
其中$\tilde{\lambda}=\lambda^*\left(\tilde{\theta}_i, \theta_{-i}\right), \tilde{x}=x^*(\tilde{\lambda})$, and $\lambda^*=\lambda^*(\theta)$.

根据定理 1 可以证明定理 2。定理 2 意味着，谎报的收益可以通过一个与用户效用函数无关的参数来确定上限。上述结果适用于任意规则代理函数。为了推导出更明确的性质，我们将重点放在正则代用函数族上：
$$
f(x, \lambda)=\lambda \phi^{(\alpha)}(x) \tag{14}
$$
其中$\phi^{(\alpha)}(x)$是[30,41]给出的$\alpha$-公平效用。

$$
\phi^{(\alpha)}(x)= \begin{cases}(1-\alpha)^{-1} x^{1-\alpha}, & \forall \alpha>0 \text { and } \alpha \neq 1 \\ \log (x), & \text { if } \alpha=1\end{cases} \tag{15}
$$
将M定义成所有用户的聚合收益的上限，即：

$$
\sum_{i \in \mathcal{I}} u_i\left(x_i, \omega\right) \leq M, \forall x \in \mathcal{X}, \omega \in \Omega \tag{16}
$$
$\mathcal{X}$的紧致性确保了M的存在性。给定(14)中指定的正则代理函数，我们可以细化（13）中的结果并推导出以下绑定结果：

命题2：利用 (14) 中的代用函数，并假设$\alpha \rightarrow 0$，则引理2中的界限可以被改写为：
$$
\begin{aligned}
& J_i^{\beta_i^*}\left(\lambda^*\left(\tilde{\theta}_i, \theta_{-i}\right) \mid \theta\right)-J_i^{\beta_i^*}\left(\lambda^*(\theta) \mid \theta\right) \\
\leq & \left\|P_{\Omega}(\cdot \mid \theta)-P_{\Omega}\left(\cdot \mid \tilde{\theta}_i, \theta_{-i}\right)\right\| M+\epsilon
\end{aligned} \tag{17}
$$
其中，$||\cdot||$表示$\mathcal l_1$范数，M定义在(16)中。

我们在附录7.2中给出了命题2的证明。命题2提供了一个当其他用户都说真话时用户$i$谎报所能带来的收益上界。直观上，如果用户通过谎报来操纵$P_\Omega(\cdot|\theta)$的能力受限，则操纵$\lambda^\ast(\tilde\theta_i,\theta_{-i})$同样困难。命题2的意义在于，它表明用户因谎报而获得的收益与用户的传感数据对后验概率分布$P_\Omega(\cdot|\theta)$的影响程度成正比。

我们指出，MCS 系统的主要优势在于它利用了人群中固有的不准确数据的多样性。因此，在用户数量相当多的情况下，每个用户的传感数据对$P_\Omega(\cdot|\theta)$的贡献很小，并且 (17) 中的界限接近于0。因此，T-SAB 机制可能会刺激用户如实报告$\theta_i$。

为了进一步表征用户真实性的诱导程度，我们引入以下定义[13]：

定义 4（信息大小）。用户$i$的信息量为：
$$
v_i=\max _{\theta_i, \tilde{\theta}_i} \min \left\{\epsilon \geq 0 \mid \operatorname{Prob}\left\{\tilde{\theta}_{-i} \in I_{i, \epsilon}\left(\theta_i, \tilde{\theta}_i\right)\right\} \leq \epsilon\right\} \tag{18}
$$
其中，
$$
I_{i, \epsilon}\left(\theta_i, \tilde{\theta}_i\right)=\left\{\theta_{-i} \mid\left\|P_{\Omega}(\cdot \mid \theta)-P_{\Omega}\left(\cdot \mid \tilde{\theta}_i, \theta_{-i}\right)\right\| \geq \epsilon\right\} . \tag{19}
$$
信息大小$v_i$表征了用户$i$通过谎报$\theta_i$操纵条件概率的$P_\Omega(\cdot|\theta_i,\theta_{-i})$的能力。较小的用户信息量表明T-SAB机制实现了可忽略不计的操纵能力。

为了衡量用户对（5）中奖励函数的响应有多敏感，我们进一步引入以下定义：

定义5（变化性）：用户$i$的变化性是：
$$
Z_i=\min _{\theta_i, \tilde{\theta}_i \in \Theta_i, \theta_i \neq \tilde{\theta}_i} \| \frac{P_{\Theta_{-i}\left(\cdot \mid \theta_i\right)}}{\left\|P_{\Theta_{-i}\left(\cdot \mid \theta_i\right)}\right\|_2}-\frac{P_{\Theta_{-i}\left(\cdot \mid \tilde{\theta}_i\right)}}{\left\|P_{\Theta_{-i}\left(\cdot \mid \tilde{\theta}_i\right)}\right\|_2 \|_2^2}, \tag{20}
$$
其中$||\cdot||_2$表示$\mathcal{l}_2$范数。

较大的$Z_i$意味着用户$i$对（5）中的奖励函数更敏感。基于命题2和定义4和5，我们得到以下主要结果：

定理 3（真实性）。选择(5)中的近似系数$\epsilon$以满足：
$$
\epsilon=\frac{2 M \sqrt{|\Theta|} \max _{i \in I} v_i}{\min _{i \in I} Z_i} \tag{21}
$$
给定(14)中的代理函数并令$\alpha\rightarrow 0$，则T-SAB机制满足以下两个真实性标准：
1. 事前真实性：存在满足(9)的PBE  $\tilde\theta^\ast$;
2. $\epsilon$事后真实性，定义为：
$$
\operatorname{Prob}\left\{J_i^{\beta_i^*}\left(\lambda^*\left(\tilde{\theta}_i, \theta_{-i}\right) \mid \theta\right)>J_i^{\beta_i^*}\left(\lambda^*(\theta) \mid \theta\right)+\epsilon\right\} \leq \epsilon \tag{22}
$$
我们在附录7.3中提供了定理3的证明。要理解事前真实性，选择满足（21）的近似系数，就可以确保（5）中诚实报告的收益大于错误报告的收益（特征见命题 2）。为了解释近似事后真实性，如果用户$i$的信息量很小，那么我们可以推导出：
$$
\operatorname{Prob}\left\{\left\|P_{\Omega}\left(\cdot \mid \theta_i, \theta_{-i}\right)-P_{\Omega}\left(\cdot \mid \tilde{\theta}_i, \theta_{-i}\right)\right\| \approx 0\right\} \approx 1 \tag{23}
$$
用户$i$可以认为近似说真话，因为
$$
\operatorname{Prob}\left\{J_i^{\beta_i^*}\left(\lambda^*\left(\tilde{\theta}_i, \theta_{-i}\right) \mid \theta\right)-J_i^{\beta_i^*}\left(\lambda^*(\theta) \mid \theta\right) \leq 0\right\} \approx 1 \tag{24}
$$
定理 3 有两方面的意义。首先，它证明了大型 MCS 系统中的用户真实性（由于 (21) 中的$\epsilon$较小），这将在第 5 节中进一步说明。其次，它还为进一步减少$\epsilon$提供了启示，即通过调整参数$|\Theta|$和$Z_i$来减少$\epsilon$。为此，平台可在第一阶段限制用户的报告选择。

(21)中$\epsilon$的取值取决于参数M的知识。我们注意到，可以在不知道𝑀的情况下修改 T-SAB 机制以保持近似的事后真实性（但牺牲事前真实性）。此外，还可以根据经验数据估计M。

接下来，我们考虑由(4)和(5)中类似于 VCG 的结果函数所产生的其余两个性质(E3)-(E4)。特别是，由于(12)在 NE 中的最大值（由于定理 1）和(5)中的正奖励函数，我们可以证明以下性质：

命题3（个体理性）：T-SAB机制实现个体理性（E3），即
$$
J_i^{\beta_i^*}\left(\lambda^*(\theta) \mid \theta\right) \geq 0, \forall i \in I \tag{25}
$$
我们在附录7.4中给出了简要证明。命题3的意义在于，用户永远不会因为参与T-SAB机制而变得更糟，例1中的赢家诅咒在这里也不会发生。

此外，(5) 中的支付结构得出以下近似预算平衡结果：

推论2（$\epsilon I$-近似预算平衡）T-SAB机制实现了近似预算平衡（E4），即：
$$
\sum_{i \in \mathcal{I}} h_i(\lambda, \tilde{\theta}) \geq-\epsilon I
$$
其中$\epsilon$在(21)中给出。

我们在附录 7.5 中提供了推论 2 的证明。可能出现预算不足的原因是需要激励用户如实报告。然而，当用户的信息量较小时（因为$\epsilon$较小），这种预算不足可以忽略不计。此外，正如我们将在第 5.2 节中用数字说明的，近似系数$\epsilon$随I的增大而迅速减小，这意味着随着I的增大，预算赤字$\epsilon I$也会减小。

简而言之，T-SAB 机制可以实现所有的经济特性（E1）-（E4），只要用户的信息量很小，这在大型 MCS 系统中是合理的。
## CASE STUDY: SPECTRUM SENSING AND ALLOCATION IN COGNITIVE RADIO
我们研究了认知无线电网络中的协作频谱感知场景[45]，作为具有网络资源共享的 MCS 系统的玩具示例。然后，我们进行数值分析，以研究用户信息量较小的条件以及与独立基准相比的性能增益。
### System Model
#### System overview
我们考虑由一个基站和一组（二级）用户$\mathcal{I}$组成的中心化二级网络，在我们的示例中，我们只考虑只有一个信道的小问题，以便清楚地说明主要思想，信道要么被占用，要么空闲，记作$\omega\in\left\{0,1\right\}=\Omega$，其中0表示占用，1表示空闲。每个用户$i$都应用能量检测来检测信道是否被占用，并接收二进制检测结果$\theta_{i}=\left\{0,1\right\}$，这是对$\omega$的估计。
#### Probability Model
为了表征$P_\Omega(\omega|\theta)$和$P_{\Theta_i}(\theta_{-i}|\theta_i)$的条件概率分布，我们采用了文献[45]中表 2 所列的误报、检测、漏检和漏报概率。具体来说，$\xi_i$表示能量检测阈值，$\gamma_i$表示用户$i$传感数据的信噪比SNR，$u$是能量检测器的时间带宽乘积，$\Gamma(\alpha,x)$是不完全伽马函数，由$\Gamma(a, x)=\int_x^{\infty} t^{a-1} e^{-t} d t$给出， $\Gamma(\alpha)$是伽玛函数，$Q_u(a,b)$是广义 Marcum Q 函数 [45]。

|$Prob(\theta_i\|\omega)$ | $\omega=0$|$\omega=1$|
|----------|----------|----------|
|$\theta_i=0$|$\frac{1-\Gamma\left(u,\zeta/2\right)}{\Gamma\left(u\right)}$|$1-Q_u(\sqrt{2y_i},\sqrt{\zeta})$|
|$\theta_i=1$|$\frac{\Gamma\left(u,\zeta/2\right)}{\Gamma\left(u\right)}$|$Q_u(\sqrt{2y_i},\sqrt{\zeta})$|

令$p(\omega)$表示世界状态的概率分布，因此，以所有其他用户的传感数据$\theta$为条件的世界状态的概率是
$$
\begin{aligned}P_{\Omega}(\omega|\boldsymbol{\theta})&=\frac{p(\omega)\prod_{\boldsymbol{i}\in\boldsymbol{I}}\mathrm{Prob}(\theta_{\boldsymbol{i}}|\omega)}{\sum_{\tilde{\omega}\in\{0,1\}}p(\tilde{\omega})\prod_{\boldsymbol{i}\in\boldsymbol{I}}\mathrm{Prob}(\theta_{\boldsymbol{i}}|\tilde{\omega})}\end{aligned} \tag{26}
$$
其中 (26) 中的乘积项是由独立条件概率$Prob(\theta_i|\omega)$引起的。每个用户$i$对其他用户的传感数据的先验信念由下式给出：
$$
\begin{aligned}P_{\Theta_{-i}}(\boldsymbol{\theta}_{-i}|\theta_{i})&=\frac{\sum_{\tilde{\omega}\in\{0,1\}}p(\tilde{\omega})\prod_{j\in I}\operatorname{Prob}(\theta_{j}|\tilde{\omega})}{\sum_{\tilde{\omega}\in\{0,1\}}p(\tilde{\omega})\text{Prob}(\theta_{i}|\tilde{\omega})}\end{aligned} \tag{27}
$$
#### Resource Allocation
为了避免用户传输之间的相互干扰，平台将频谱信道以分时方式分配给用户。令$x_i$表示用户$i$的时间份额。每个用户都有一个由$u_i(x_i|\omega)=\kappa_{i,\omega}\log(1+x_i)$给出的加权对数效用函数，其中当$\omega=0$时，参数$k_{i,\omega}$遵循区间$[0, 0.1]$上的 i.i.d 均匀分布，当$\omega=1$时，遵循区间$[0, 15]$的i.i.d均匀分布。
### Numerical Results
#### Simulation Setup
我们将占用概率设置为$p(0)=0.3$，空闲概率为$p(1)=0.7$，表2中的阈值$xi=16$，信道容量为$C=250$。
#### Standalone Benchmark Mechanism
为了进行性能比较，我们考虑了一种独立的基准机制，这种机制由于潜在的谎报而没有充分利用监控监听系统。具体来说，在不汇总所有用户传感数据的情况下，用户直接参与传统机制，并完全根据自己的感知数据$\theta_i$做出决策。
#### Simulation Results
在图 3 中，我们研究了不同用户数量I和信噪比下用户的信息量。我们发现，每个用户的信息量随着用户数量的增加呈指数级下降。直观地说，当用户数量增加时，每个用户改变后验概率分布$P_\Omega(\cdot|\cdot)$的能力就会减弱。此外，我们还观察到，当信噪比增加时，信息量也会减少。

其次，我们从图 4 中观察到定理 3 中近似系数$\epsilon$的类似趋势。也就是说，近似系数$\epsilon$会随I和信噪比的增大而减小，因为它在很大程度上取决于信息量的大小。这意味着，当有足够多的用户（例如，$I\geq 6$）且用户的传感数据准确（例如，信噪比= 5dB）时，用户几乎是真实的（根据定理 3），预算几乎是平衡的（根据推论 2）。

在图 5 中，我们绘制了在信噪比为 0 dB 时，与独立基准相比，T-SAB 机制实现的事后社会福利（即 (3) 中的目标值）。我们发现，性能增益从 14% 提高到 20%。这意味着，由于传感结果更加准确，更多用户可能会带来更显著的性能提升。

## CONCLUSIONS
资源发现 MCS 系统中相互依赖估值的特点会刺激移动用户误报他们的感知数据。在这项工作中，我们首次提出了 MCS 网络的相互依赖估值问题。考虑到自利用户的私人效用信息和相互依存的估值，我们提出了 T-SAB 机制。我们证明，当用户的信息量较小时，我们提出的 T-SAB 机制可以实现真实性和分配效率，这在大型 MCS 系统中是一个合理的条件。我们的案例研究验证了 T-SAB 机制。未来值得关注的方向包括：激励联合真实感官数据报告和参与 MCS 的机制设计，以及 MCS 系统中其他决策问题（如 Waze 的交通调度）的相互依存估值问题。

