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
