---
title: 内存屏障和CPU缓存
date: 2019-02-25 22:19:36
categories: java
tags:
---
java内存屏障和CPU缓存, 缓存同步协议，内存可见性和指令重排序（as-if-serial原则）。

<!-- more -->

## CPU缓存

L1 32-4096kb
L2
基本上每个CPU上都有L1， L2

L3共享缓存

## 缓存同步协议
MESI
- 修改态
- 专有态
- 共享态
- 无效态

多处理器时，单个CPU对缓存数据改动时，需要通知其他CPU

volatile即当单CPU修改时，将其他CPU缓存对应数据的缓存设置为无效态，当其他CPU读此数据时，重新从主存读取。

## 运行时指令重排
单线程时，无依赖关系的代码可能会重新排序，但是保证单线程执行结果不变。遵循as-if-serial。

## 内存屏障

1. 写内存屏障
   在指令后插入Store Barrier， 修改缓存后，强制写入主存，让其他CPU可见。  
   即保证可见性时， 修改数据后更新到主存，并将其他CPU对应的缓存设置为无效态。
2. 读内存屏障
   在指令前插入Load Barrier， 读数据前，强制从主存读取。  
   即保证可见性时， 读取数据前，如果缓存中为无效态，则从主存读取。

## 保证可见性的几个场景
什么是保证可见性？
多个线程读取同一数据时，保证读到的是最新的而不是其他线程修改前的数据。
1. 数据用volatile修饰
2. synchronize，JVM保证了同步代码块中数据的可见性
3. Lock（ReentertLock等），获得锁后保证了数据的可见性

可以从运行时编译代码中看到类似实现可见性的代码（具体是什么忘记了...，待补充）
   

