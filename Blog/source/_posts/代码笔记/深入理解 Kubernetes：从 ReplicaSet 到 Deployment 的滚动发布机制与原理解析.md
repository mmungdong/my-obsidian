---
title: 深入理解 Kubernetes：从 ReplicaSet 到 Deployment 的滚动发布机制与原理解析
date: 2026-03-12 20:30:00
categories:
  - 代码笔记
tags:
  - Kubernetes
  - 云原生
archive: false
hide: false
---

## 核心知识点导读

- **ReplicaSet 核心机制**：掌握 ReplicaSet 如何通过 Label Selector 管理 Pod，以及期望状态与实际状态的对比逻辑。
- **控制器哲学 (Reconcile Loop)**：理解 Kubernetes 声明式 API 的底层基石——持续逼近预期状态的控制循环机制。
- **Informer 与 Workqueue 架构**：剖析控制器如何通过 List-Watch 机制高效感知资源变更，并利用工作队列实现事件去重与重试。
- **Deployment 的分层架构**：理解 Deployment 如何通过管理多个 ReplicaSet 来实现应用的版本控制与平滑切换。
- **滚动更新 (RollingUpdate) 策略**：深度解构 `maxSurge` 与 `maxUnavailable` 参数对发布平滑度与集群资源的影响。
- **生产级安全发布基线**：掌握结合 `ReadinessProbe`（就绪探针）与优雅停机（Graceful Shutdown）的安全滚动发布规范。
- **控制器源码导航**：定位 Kubernetes 核心仓库中 Deployment 控制器的代码流转路径。

---

## 一分钟通俗理解

为了让你快速建立直觉，我们可以用"餐饮门店管理"和"商场换电梯"的生活场景来映射 Kubernetes 的核心组件。

**1. ReplicaSet：只盯人数的"值班主管"**
ReplicaSet 就像餐厅后厨的"值班主管"，他的唯一 KPI 是：**确保当前时刻，符合"厨师"标签的人数必须是 3 个**。
- 发现少了一个人（Pod 挂了）？赶紧拉一个新厨师顶上。
- 发现多了一个人（人为误操作多建了 Pod）？强制让一个人下班。
- **局限性**：他不管餐厅要不要换新菜单，也不管新老厨师怎么交接，只认死理保数量。

**2. Deployment：统筹大局的"店长"**
Deployment 则是"店长"。当店长决定升级新菜单（发布新版本）时，他不会自己去盯每个厨师，而是直接给"值班主管（ReplicaSet）"下发指令。
- 升级时，店长会任命一个"新主管（新 ReplicaSet）"带一批新厨师逐步上岗，同时让"老主管（旧 ReplicaSet）"手下的老厨师逐步下班。
- 这种新老交替的机制，保证了餐厅在换菜单期间依然能正常营业。

**3. 滚动更新（RollingUpdate）：商场换电梯**
商场有 10 部电梯（10 个 Pod），现在要全部换成新款。
- **错误做法**：全停了统一换（业务宕机）。
- **滚动更新**：先建 1-2 部新电梯（对应 `maxSurge` 临时多开数量），确认新电梯运行正常后，再停掉 1-2 部老电梯（对应 `maxUnavailable` 允许停用的数量）。边建边拆，顾客几乎无感知。

**4. Informer 与 Workqueue：高效的客服中心**
- **Informer** 是客服中心的"消息订阅员"，一有风吹草动（资源变化）就记录到本地账本（Cache）上。
- **Workqueue** 是"待办工单池"，把需要处理的任务排好队，去重防抖，交给后面的真正干活的业务员（Worker / Reconcile）慢慢处理。

---

## 底层原理解析

*(注：本节进入严谨的技术原理探讨，剥离所有比喻词汇。)*

### 1. 控制器核心模式：Reconcile 循环
Kubernetes 控制器的核心本质是一个基于事件驱动的反馈闭环（Feedback Loop）。
对于 ReplicaSet Controller 而言，其 `reconcile` 逻辑如下：
1. **获取期望状态 (Desired State)**：读取 `spec.replicas` 的设定值。
2. **获取实际状态 (Actual State)**：通过 Label Selector 查询当前集群中状态为 Active 且匹配的 Pod 数量。
3. **计算差异 (Diff)**：`Diff = Actual - Desired`。
4. **执行收敛动作**：
   - 若 `Diff < 0`：基于 `spec.template` 调用 API Server 创建新的 Pod。
   - 若 `Diff > 0`：根据特定策略（如优先删除未就绪或运行时间较短的 Pod）删除多余的 Pod。
   - 若 `Diff == 0`：不执行任何操作（体现了接口的幂等性）。

### 2. Informer 与 Workqueue 机制原理
如果没有 Informer，Controller 需要高频轮询 API Server，这会导致集群雪崩。K8s 通过 `Informer + Workqueue` 实现了高效的事件驱动：
- **Reflector (List-Watch)**：首先通过 `List` API 获取目标资源的全量状态，随后通过 `Watch` API 维持长连接，监听增量事件（Add/Update/Delete）。
- **Indexer (Local Cache)**：将获取到的资源对象缓存在本地内存中，Controller 后续查询状态时直接读缓存，极大降低 API Server 压力。
- **Workqueue**：事件回调函数（EventHandler）触发后，不直接执行业务逻辑，而是将对象的 Key（通常为 `namespace/name`）推入 Workqueue。队列自带去重防抖、限速和延迟重试功能，最终由多个 Worker Goroutine 并发提取 Key 并执行 `reconcile`。

### 3. Deployment 与 ReplicaSet 的拓扑关系
Deployment 属于声明式的高级控制器，它并不直接管理 Pod，而是管理 ReplicaSet。
- **版本哈希 (pod-template-hash)**：每次修改 Deployment 的 `PodTemplate`（如修改镜像版本），Deployment Controller 都会计算一个新的 Hash 值，并创建一个带有该 Hash 标签的全新 ReplicaSet。
- **层级流转**：API Server 接收 Deployment 变更 -> Deployment Controller 调整新旧 ReplicaSet 的 `replicas` 值 -> ReplicaSet Controller 侦测到 `replicas` 变化 -> 最终执行 Pod 的增删。

### 4. 滚动更新策略 (Strategy) 深度解构
Deployment 控制更新平滑度的核心在于 `strategy.rollingUpdate` 的两个关键参数：
- `maxSurge` (最大超载值)：发布过程中，允许存在的总 Pod 数量上限超过期望 `replicas` 的数量或比例。该值决定了**发布期间所需的额外集群资源分配**。
- `maxUnavailable` (最大不可用值)：发布过程中，允许处于不可用状态的 Pod 的最大数量或比例。该值决定了**发布期间服务容量的最低保障底线**。

*(补充知识：K8s 在计算可用性时，严格依赖 Pod 的就绪状态。如果一个新创建的 Pod 未通过 ReadinessProbe 检查，它将不会被计入"可用副本"，旧 Pod 也不会被继续销毁，从而阻断了错误版本的蔓延。)*

### 5. 源码导航路径
若需深入 Kubernetes 源码验证上述逻辑，主干代码位于官方仓库 `kubernetes/kubernetes` 下的 `pkg/controller/deployment/` 目录：
- `deployment_controller.go`：控制器的初始化入口，注册 Informer EventHandler 以及 Workqueue 的流转。
- `sync.go`：`syncDeployment` 核心同步函数，处理对象的新旧 RS 区分及扩缩容指令分发。
- `rolling.go`：包含 `rolloutRolling` 函数，基于 `maxSurge` 和 `maxUnavailable` 进行精确的数学计算，控制新 RS 的扩容步长和旧 RS 的缩容步长。

---

## 实战代码演示

以下提供一份符合生产安全基线的 Deployment YAML 模板。此配置不仅指定了滚动更新参数，还补充了服务稳定必不可少的探针与优雅停机机制。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-backend-service
  namespace: prod
  labels:
    app: backend
spec:
  replicas: 4
  progressDeadlineSeconds: 600 # 避免发布卡死，600秒未完成则标记为失败
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1           # 资源充裕时，每次多启动 1 个新 Pod
      maxUnavailable: 0     # 绝不容忍可用副本数低于 4，确保高并发不掉量
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      terminationGracePeriodSeconds: 30 # 给予 Pod 30 秒的缓冲时间处理进行中的请求
      containers:
        - name: application
          image: myrepo/backend:v2.0.0
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 8080
          resources:
            requests:
              cpu: "500m"
              memory: "512Mi"
            limits:
              cpu: "1000m"
              memory: "1024Mi"
          # 核心防线：就绪探针，通过后 K8s 才会将流量切入，并继续销毁旧 Pod
          readinessProbe:
            httpGet:
              path: /actuator/health
              port: 8080
            initialDelaySeconds: 10
            periodSeconds: 5
            failureThreshold: 3
          # 存活探针：检测应用是否进入死锁
          livenessProbe:
            httpGet:
              path: /actuator/health
              port: 8080
            initialDelaySeconds: 30
            periodSeconds: 10
          # 优雅退出钩子：在接收到 SIGTERM 信号前，先主动休眠几秒，等待注册中心/Ingress 摘除流量
          lifecycle:
            preStop:
              exec:
                command: ["/bin/sh", "-c", "sleep 10"]
```

### 常用实战排查与管理指令

```bash
# 提交/更新部署清单
kubectl apply -f deployment.yaml

# 实时观察滚动更新的过程与进度
kubectl rollout status deployment/my-backend-service -n prod

# 假如发布存在问题（如探针失败卡住），一键回滚到上一个正常版本
kubectl rollout undo deployment/my-backend-service -n prod

# 查看该 Deployment 管理的所有相关 ReplicaSet 及哈希标识
kubectl get rs -l app=backend -n prod
```

---

## 参考资料

1. [Kubernetes 官方文档: Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
2. [Kubernetes 官方文档: ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)
3. [Kubernetes 源码仓库 (GitHub): Deployment Controller](https://github.com/kubernetes/kubernetes/tree/master/pkg/controller/deployment)
4. [K8s 官方博客: Building K8s Controllers (Informer & Workqueue)](https://kubernetes.io/docs/concepts/architecture/controller/)