# 项目模块功能整理

## 一、核心模块功能（基于代码仓库分析）

### 1. 核心控制器模块

- Service Controller：管理 EAS 服务生命周期的 K8s 控制器
- Vipserver Controller：管理注册在每个 EAS 服务中的 vipserver 端点的 K8s 控制器
- 使用 Kubernetes Informer 机制实现事件驱动的控制器模式
- 支持多种服务类型：传统服务、外部服务、无服务器服务等

### 2. 云服务相关模块

- Cloud Controller：管理云资源的控制器，包含以下能力：
    - 私有链接资源管理
    - 服务域 DNS 记录管理
    - DNS 敲门服务管理
    - 负载均衡网关 Pod 管理
    - Redis 云资源管理
    - 网络负载均衡器集成
    - Nacos 服务发现集成
- 支持阿里云服务集成（私有链接、DNS、NLB、Redis、Nacos 等）

### 3. AI / 机器学习相关模块

#### （1）LLM Gateway：大型语言模型网关服务

- 支持多种调度模式（调度器模式、重调度模式、网关模式）
- 实现负载均衡和请求处理
- 支持多种调度策略（基于指标、前缀缓存、最少请求等）

#### （2）AIGC Gateway：AI 生成内容网关

- 支持 SD API 和 ComfyUI API
- 提供 AI 服务的统一入口

### 4. 网络和网关模块

#### （1）Gateway Controller：网关控制器

- 基于 controller-runtime 框架实现
- 集成多种云服务提供商（MSE、NLB、DNS 等）
- 支持 Contour 网关供应器

#### （2）Proxy Server：代理服务器

- 实现 TCP 连接代理
- 支持 WebSocket 升级
- 提供连接复用和监控功能

### 5. 存储和数据相关模块

#### （1）Storage Cache：存储缓存系统

- 实现基于 LRU 的并发缓存机制
- 支持分片机制减少锁竞争
- 提供对象版本比较功能

#### （2）StateDB：状态数据库

- 管理服务状态（OK、PREPARE、FAIL、EXIT）
- 支持状态变更事件监听
- 提供状态持久化功能

#### （3）Cache Server：缓存服务器

- 实现 K8s 资源的缓存服务
- 支持 REST API 接口
- 提供认证和访问控制

#### （4）Database Integration：数据库集成

- 支持 MySQL、SQLite 等关系型数据库
- 集成 InfluxDB 时序数据库
- 提供 ORM 封装和连接管理

#### （5）Storage Volumes：存储卷管理

- 支持多种存储类型（OSS、NFS、云盘、Git 等）
- 实现存储卷的挂载和配置管理
- 支持加密和只读等存储选项

### 6. 监控和运维模块

- Persist Controller：持久化控制器
    - 管理各种资源的缓存
    - 实现资源变更检测
    - 提供缓存管理功能
- Supervisor：服务监控
    - 管理服务状态和生命周期
    - 提供状态报告和健康检查

### 7. 工具和辅助模块

- EAS Init：系统初始化工具
    - 数据库初始化
    - 配置加载和执行
- Command Line Tools：命令行工具
    - 提供各种组件的命令行接口
    - 支持 cobra 命令行框架

## 二、如何阅读这个代码仓库

1. 从 README 开始：了解项目基本结构和编译方法
2. 理解核心概念：熟悉 K8s 控制器模式和 EAS 服务架构
3. 按模块分析：按照上述模块分类逐一分析相关文件
4. 关注 cmd 目录：每个可执行程序的入口点
5. 理解 pkg 结构：按功能划分的包结构
6. 查看配置文件：了解系统的配置方式和参数

## 项目架构总结

该项目采用微服务架构，各个控制器组件职责分离，可独立部署。大量使用 Kubernetes 的 Informer 机制实现事件驱动的反应器模式，同时集成了多种云服务（如阿里云）和数据库技术。