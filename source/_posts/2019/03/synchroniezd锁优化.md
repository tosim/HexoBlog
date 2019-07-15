---
title: synchroniezd锁优化
date: 2019-03-01 14:23:14
categories: jvm虚拟机
tags:
---
1.6之后对synchronized做了优化，一个锁有不同阶段，获取的方式在底层不一样.
<!-- more -->
## mark word
每个java对象在堆中都有一个mark work标志，在不同阶段，其格式和内容都不一样

## synchronized从偏向锁到重量级锁
{% asset_img 偏向锁到重量级锁.png %}
1. 偏向锁
本质就是无锁，在mark word中标记当前获取锁的线程id，若没有其他线程争抢，则此线程获取锁时直接获得。出现争抢后就没用了。

2. 自旋锁（轻量级锁）
当出现锁的争抢时，通过cas修改mark word争抢锁，当自旋次数到达一定次数仍未抢到锁，则升级为重量级锁

3. 重量级锁-对象监视器（monitor）
- _entryList(多个争抢线程)
- _owner（抢到锁的线程）
- waitSet（调用wait等待的线程）
