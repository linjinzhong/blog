---
title: process-thread
urlname: process-thread
top: false
categories:
  - 操作系统
tags:
  - 进程
  - 线程
author:
date: 2023-05-11 10:57:29
updated: 2023-05-11 10:57:29
---


运行中的程序，就被称为「进程」（Process）。


<!-- more -->

## 简介
1. 进程
    1. 进程的概念
    2. 进程的状态
    3. 进程的控制结构
    4. 进程的控制
    5. 进程的上下文切换
2. 线程
    1. 为什么使用线程？
    2. 什么是线程？
    3. 线程与进程的比较
    4. 线程的上下文切换
    5. 线程的实现
3. 调度
    1. 调度时机
    2. 调度原则
    3. 调度算法


## 进程
1. 进程的概念
    运行中的程序，就被称为「进程」（Process）。

    对于一个支持多进程的系统，CPU 会从一个进程快速切换至另一个进程，其间每个进程各运行几十或几百个毫秒。

    虽然单核的 CPU 在某一个瞬间，只能运行一个进程。但在 1 秒钟期间，它可能会运行多个进程，这样就产生并行的错觉，实际上这是并发。

    并发vs并行：并发是单核处理器快速交替执行多个进程，并行是多核处理器分别执行各自的进程。
    <img src="./并发与并行.webp" width = "50%" height = "50%" alt="并发与并行" align=center />

    试试

## 线程

## 调度



## REFERENCE
[进程管理](https://xiaolincoding.com/os/4_process/process_base.html#%E8%BF%9B%E7%A8%8B)