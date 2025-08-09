---
title: errgroup - golang sync 的扩展库
date: 2025-08-09 15:07:46
categories:
  - 代码笔记
tags:
  - go3方库
archive: true
hide: false
---
# 介绍

## errgroup 是什么？


errgroup 是Go官方扩展库 `golang.org/x/sync` 中的并发原语，用于管理多个 goroutine 的协同执行和错误处理。它解决了标准库 `sync.WaitGroup` 无法便捷传递错误的问题，支持在并发任务中统一处理错误，并支持在首个错误发生时取消所有任务。

## 核心特性

1. **错误聚合与传播**  
    当任意一个 goroutine 返回错误时，errgroup 会立即取消其他未完成的任务，并将错误传递给主 goroutine，避免资源浪费。
2. **批量任务取消**  
    通过 `context.Context` 实现任务组的级联取消，一旦某个任务失败，整个任务组会被终止。
3. **超时控制**  
    支持结合 `context.WithTimeout` 设置任务组超时时间，防止长时间阻塞。

## 与标准库 `sync.WaitGroup` 的对比

相较于 `sync.WaitGroup` 仅提供等待机制，errgroup增加了以下能力：

- 错误自动收集与传播；
- 任务取消的原子性操作；
- 更简洁的并发任务管理代码结构。


# 使用案例

## 安装

```shell
go get github.com/golang/sync
```


# 参考
- [https://github.com/golang/sync](https://github.com/golang/sync)
- [mp.weixin.qq.com/s/JD6FDfCEWO6uQZhyvrIWkA](https://mp.weixin.qq.com/s/JD6FDfCEWO6uQZhyvrIWkA)
- [Go 并发控制：errgroup 详解-CSDN博客](https://blog.csdn.net/ra681t58cjxsgckj31/article/details/143616687)
- 