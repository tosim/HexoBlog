---
title: CAP和BASE理论
date: 2019-02-28 15:21:19
categories: 分布式
tags:
---
CAP理论和BASE理论概括理解
<!-- more -->
## CAP
- Consistency(一致性)
- Availability(可用性)
- Partition tolerance(分区容错性)

## BASE
- Basicclly Available（基本可用）
- Soft state (软状态)
- Eventually consistent (最终一致性)

## 几个概念
### 可用性和可靠性
- 可用性指一定时间内，系统能够提供服务的数量级
- 可靠性指一定时间内，系统能够提供服务的时间比率

### 分区容错性
系统中一台或若干台机器出现故障后系统的容错能力

### 一致性
系统中数据是否正确流通，没有脏数据

### 基本可用
系统出现不可预知故障时，允许损失部分可用性

### 最终一致性
系统中数据不要求实时一致，最终数据符合业务含义就可以

### 软状态
允许系统中数据存在中间状态 
