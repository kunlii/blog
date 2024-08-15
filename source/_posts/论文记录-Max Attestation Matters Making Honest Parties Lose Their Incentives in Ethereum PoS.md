---
title: "论文记录-Max Attestation Matters: Making Honest Parties Lose\rTheir Incentives in Ethereum PoS"
date: 2024-08-15 15:56:40
tags:
  - game-theory
  - blockchain
  - PoS
categories: 论文
description: 针对以太坊PoS激励机制的攻击
---
# Max Attestation Matters: Making Honest Parties Lose Their Incentives in Ethereum PoS

## Abstract
攻击对象：以太坊2.0的PoS激励机制
攻击效果：诚实验证者会受到惩罚
攻击条件：控制29.6%股权
攻击原理：与验证者的数量和证明的最大数量（也就是票数）`MAX_ATTESTATIONS`相关。当前系统设置下（900,000 validators and MAX_ATTESTATIONS =128)，攻击成功率80.25%。

## Intro
以太坊2.0采用PoS，其核心共识是Gasper，BFT协议

1. 传统BFT假设攻击者无法控制超过$\frac{1}{3}$节点（验证者）
2. 中本聪共识（比特币和以太坊1.0）假设攻击者控制不超过50%算力
3. PoS假设攻击者控制不超过$\frac{1}{3}$股权，股权通常等价于验证者的账户余额

以太坊的PoS假设了一个部分同步网络，存在消息处理与传输的未知上界，该协议是Casper FFG和HLMD GHOST的结合，协议的基本单元是epoch，每个epoch根据物理时钟划分为多个slot。HLMD GHOST在每个epoch从收到的区块proposal中选择规范链，Casper用于计票。

几乎所有PoS都作出了类似BFT的假设：
1. 所有诚实验证者总是在线
2. 系统不支持未知数量的验证者沉睡

显然这两个假设有点离谱，一些研究关注沉睡共识，而以太坊设计了一些激励机制来鼓励验证者在线，由奖励和惩罚两部分组成。


