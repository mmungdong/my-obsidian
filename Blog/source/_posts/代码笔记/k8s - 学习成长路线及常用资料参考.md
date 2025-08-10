---
title: k8s - 学习成长路线及常用资料参考
categories:
  - 代码笔记
tags:
  - k8s
archive: true
hide: false
date: 2025-08-08 01:03:39
---
这一阶段，我们需要熟练掌握至少一个组件的原理和使用，如果你没有自己需要的或倾向的，这里比较推荐：kube-scheduler。因为企业对 Kubernetes 调度器的二次开发需求很多。另外，如果你有时间和精力也建议学习和使用某个网络插件，例如：Cilium。Cilium 功能复杂，代码量多。如果你没有特别多精力，可以学习下 flannel，建议阅读下 flannel 的源码。其他网络插件的实现原理，跟 flannel 有很多相似之处。

# 1. 源码阅读

  

## 1.1. 基础准备

  

1. **基础知识了解：**这里只推荐一本书 [Kubernetes权威指南：从Docker到Kubernetes实践全接触](https://book.douban.com/subject/36926473/)（选择最新版本的阅读）。这本书，可以理解为是学习 Kubernetes 必备书籍。
2. **源码相关推荐书籍：**

- [Kubernetes源码剖析](https://book.douban.com/subject/35100082/)，这本书解读的 Kubernetes 版本有点旧，但里面很多知识仍然适用。
- [深入理解Kubernetes源码 (豆瓣)](https://book.douban.com/subject/36978517/)

  

## 1.2. 本地部署 k8s

  

- todo

  

## 1.3. 选择一个 Kubernetes 组件进行阅读

  

1. **找到阅读入口：**Kubernetes 源码 main 文件都保存在 cmd/<组件名>/*.go文件中，如果找不到，你可以直接搜：grep 'package main' cmd/<组件名>/*.go，来找 main 函数所在的源码文件；
2. **阅读 example 和单元测试用例：**Kubernetes 源码仓库下没有什么可以帮助你阅读源码的 example 文件，所以这个可以忽略。另外，Kubernetes 仓库目录下有很多单测文件，这些单测文件，我自己阅读的时候，很少去看，你可以根据需要选择去看，或者不看，也没啥影响；
3. **阅读代码注释：**这个很重要，Kubernetes 的源码注释都写的比较详细，而且也不是那种凑数的源码注释，很多注释是有阅读价值的。所以，你阅读源码时，有些地方不明白，可以优先看看这些注释，这也是最便捷的方式；
4. **借助 ChatGPT 和网络搜索：**这个根据需要自行搜索即可。我阅读源码时，偶尔会用 ChatGPT，直接把整段代码贴上去，看看 ChatGPT 的回答，如果感觉它在胡说八道，在在网上去搜下。不过一般搜不到我想要的答案。更多的时候，我对着源码硬磕，认真琢磨一会儿后，往往能够理解这些源码。
5. **写注解、做笔记、勤思考：**非常好的精细化阅读方法，不过我阅读的时候，很多时候是粗度，不会做这些注解和笔记。做了，我也不咋看；
6. **写单测协助理解代码：**也是很不错的精细化阅读方法，但我也不咋用，因为成本高；
7. **调试阅读：**这个我偶尔会用，在有些关键地方不明白的时候，我会在代码里加上 fmt.Println这类语句，编译、重新部署后，看看源码运行流程和效果。建议你至少魔改一次 Kubernetes 源码，编译并重新部署 Kubernetes 源码，测试看看自己的魔改效果。

  

## 1.4. 魔改、运行并测试自己修改后的代码

  

# 2. k8s 进阶路线

## 2.1. 初级阶段

  

- 掌握 Kubernetes 基础知识
    - [https://book.douban.com/subject/36926473/](https://book.douban.com/subject/36926473/)
    - [Kubernetes源码剖析](https://book.douban.com/subject/35100082/)
- 学会部署 Kubernetes
    - Kind：可参考 [Quick Start](https://kind.sigs.k8s.io/docs/user/quick-start/)；
    - Minikube：可参考 [minikube start](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download)；
    - 手撸一个 Kubernetes 集群：可参考知识星球中，**Kubernetes 集群安装向导** 系列课程。
- 掌握 Kubernetes 基本使用方式
    - 基本操作：可阅读 [Learn Kubernetes Basics](https://kubernetes.io/docs/tutorials/kubernetes-basics/)，里面有很多实操的案例

  

## 2.2. 中级阶段

- 熟悉其中至少一个组件的原理和使用方式
- 了解 Kubernetes 核心资源及其使用方式
- 能够熟练使用 Kubernetes，并排障
- 能够使用 client-go 操作 Kubernetes 资源
- 能够编写控制器

> 这一阶段，我们需要熟练掌握至少一个组件的原理和使用，如果你没有自己需要的或倾向的，这里比较推荐：kube-scheduler。因为企业对 Kubernetes 调度器的二次开发需求很多。另外，如果你有时间和精力也建议学习和使用某个网络插件，例如：Cilium。Cilium 功能复杂，代码量多。如果你没有特别多精力，可以学习下 flannel，建议阅读下 flannel 的源码。其他网络插件的实现原理，跟 flannel 有很多相似之处。

  

## 2.3 高级阶段

- 对 Kubernetes 的扩展机了如指掌
- 能够完成其中某个扩展的设计和开发
- 能够熟练使用 client-go 操作 Kubernetes 资源，并了解 client-go 的原理
- 能够熟练开发 Operator，熟练使用相关的工具和包，最好能够掌握这些包的原理和实现

> Kubernetes 这么受企业欢迎的一个原因是因为其具有强大的可扩展性。这要归功于 Kubernetes 提供了强大的 [扩展机制](https://kubernetes.io/docs/concepts/extend-kubernetes/)。这个阶段，我们应该能够熟悉 Kubernetes 的各种扩展机制，并根据工作需要，开发自己的 Kubernetes 扩展，满足企业的需求。这个阶段，你越来越多的从理论，走向实践。而且具备自己独立开发一个以 Kubernetes 为集基石的云原生项目。

  

## 2.4 专家阶段

- 对 Kubernetes 很熟悉，最好是 360 度无死角的那种
- 了解 Kubernetes 社区，社区中相关项目的功能、使用方式、原理等
- 能够基于 Kubernetes 独立开发出自己的云原生项目

  

# 3. 云原生 CNCF 其他项目学习

> [!info] CNCF Landscape  
>  
> [https://landscape.cncf.io/](https://landscape.cncf.io/)  

|                        |                                                                                                                                                       |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| **开源项目**               | **功能描述**                                                                                                                                              |
| Helm                   | 作为 Kubernetes 生态里的 brew、dnf、dpkg，Helm 为 Kubernetes 提供了包管理能力，方便用户快速部署安装各种服务                                                                            |
| Harbor                 | Harbor 与 Kubernetes 无直接关系，但作为云原生环境下最常用的镜像仓库解决方案，了解 Harbor 十分重要                                                                                        |
| Prometheus             | Prometheus 是云原生环境下最重要的监控组件                                                                                                                            |
| Istio                  | Istio 是服务网格的关键项目，但较为复杂，可以尝试简单了解                                                                                                                       |
| controller-runtime     | Kubernetes 社区提供的一个开发框架，旨在帮助开发者快速高效地开发控制器。它是基于 client-go 构建的，通过提供一系列的工具和抽象，使得开发者可以专注于业务逻辑的实现，而不必关心控制器的生命周期管理和事件异常处理等底层细节                               |
| cluster-api            | Cluster API 是一个 Kubernetes 项目，它将声明式 Kubernetes 风格的 API 用于集群的创建、配置和管理。它在核心 Kubernetes 之上，提供可选的附加功能来管理 Kubernetes 集群的生命周期                               |
| kubebuilder            | kubebuilder 旨在简化 Kubernetes 应用程序控制器的开发过程。它通过提供脚手架文件和工具，使得开发者能够更容易地实现 Kubernetes API 和控制器                                                              |
| kubefed                | Kubernetes Federation v2，又称 KubeFed，是 Kubernetes 官方提供的一种解决方案，用于管理跨多个 Kubernetes 集群的资源。它提供了一个单独的控制平面，用于管理和协调多个 Kubernetes 集群中的资源，从而实现集中化的控制和管理         |
| kind                   | 用来快速在本地创建一个开发、测试用的 Kubernetes 集群                                                                                                                      |
| descheduler            | Descheduler 是一个用于解决 Kubernetes 集群中节点资源利用问题的工具，它通过重调度的方式来优化集群中 Pod 的分布，以改善节点资源的利用率                                                                     |
| scheduler-plugins      | scheduler-plugins 是 Kubernetes 调度系统中的一个重要组成部分，它允许开发者通过编写插件来扩展 Kubernetes 的调度功能。这些插件可以在 Kubernetes 调度过程中的不同阶段发挥作用，从而实现对调度逻辑的自定义和增强                     |
| node-feature-discovery | Node Feature Discovery（NFD）是一个由 Intel 创建的项目，旨在帮助 Kubernetes 集群更智能地管理节点资源。它通过检测每个节点的硬件特性（如 CPU 型号、GPU 型号、内存大小等），并将这些特性以标签的形式发送到 Kubernetes 集群的 API 服务器 |
| kwok                   | 轻量级的 Kubernetes 模拟集群工具                                                                                                                                |
| kustomize              | Kustomize 是一个用于 Kubernetes 配置管理的工具，它允许用户通过定义基线配置和补丁来创建自定义的 Kubernetes 资源配置。Kustomize 的主要特点是其灵活性和可扩展性，使得它成为处理复杂逻辑和动态生成内容的强大工具                          |
| autoscaler             | Cluster Autoscaler 是 Kubernetes 提供的一个集群节点弹性伸缩组件，它根据 Pod 的调度状态及资源使用情况对集群的节点进行自动扩容 / 缩容。这个组件的主要目的是优化集群资源的利用效率，确保集群能够根据实际需求动态调整节点数量，从而更好地适应工作负载的变化       |

  

# 补充

> 上面进阶完成后可以根据才云科技的路线进行补充学习：[caicloud/kube-ladder: Learning Kubernetes, The Chinese Taoist Way](https://github.com/caicloud/kube-ladder)

  

# 📎 参考文章

- [10 | Kubernetes 源码阅读方法及进阶指南](https://articles.zsxq.com/id_ivav4uquqsqb.html)