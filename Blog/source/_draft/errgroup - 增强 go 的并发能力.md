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


errgroup 是Go官方扩展库 `golang.org/x/sync` 中的并发原语，用于管理多个 goroutine 的协同执行和错误处理。它解决了标准库`sync.WaitGroup`无法便捷传递错误的问题，支持在并发任务中统一处理错误，并在首个错误发生时取消所有任务。

# 使用案例

# 参考
- [https://github.com/golang/sync](https://github.com/golang/sync)
- [mp.weixin.qq.com/s/JD6FDfCEWO6uQZhyvrIWkA](https://mp.weixin.qq.com/s/JD6FDfCEWO6uQZhyvrIWkA)
- [Go 并发控制：errgroup 详解-CSDN博客](https://blog.csdn.net/ra681t58cjxsgckj31/article/details/143616687)
- 