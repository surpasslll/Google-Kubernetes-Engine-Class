* Kubernetes概述
k8s是一个开源的，用于管理云平台中多个主机上的容器化的应用
核心的特点就是能够自主的管理容器来保证云平台中的容器按照用户的期望状态运行着
在Kubenetes中，所有的容器均在Pod中运行,一个Pod可以承载一个或者多个相关的容器
一个Pod也可以包含O个或者多个磁盘卷组
功能：
1. 调度： 把用户提交的容器放到 Kubernetes 管理的集群的某一台节点上去比如说它所需要的 CPU以及它所需要的 memory，
然后在集群中找一台相对比较空闲的机器来进行一次 placement，也就是一次放置的操作
2. 自动修复: Kubernetes 会把运行在这些失败节点上的容器进行自动迁移，迁移到一个正在健康运行的宿主机上，来完成集群内容器的一个自动恢复
3. 水平伸缩: 如果这个业务本身的 CPU 利用率过高，或者响应时间过长，它可以对这个业务进行一次扩容

* Kubernetes 的架构
比较典型的二层架构和 server-client 架构。Master 作为中央的管控节点，会去与 Node 进行一个连接。
Kubernetes 的 Master 包含四个主要的组件：API Server、Controller、Scheduler 以及 etcd
etcd：是一个分布式的一个存储系统，API Server 中所需要的这些原信息都被放置在 etcd 中，
   etcd 本身是一个高可用系统，通过 etcd 保证整个 Kubernetes 的 Master 组件的高可用性
API Server：Kubernetes 中所有的组件都会和 API Server 进行连接，组件与组件之间一般不进行独立的连接，都依赖于 API Server 进行消息的传送；
  Controller：控制器，它用来完成对集群状态的一些管理。
  比如刚刚我们提到的两个例子之中，第一个自动对容器进行修复、第二个自动进行水平扩张，都是由 Kubernetes 中的 Controller 来进行完成的；
Scheduler：调度器，“调度器”顾名思义就是完成调度的操作，
  就是我们刚才介绍的第一个例子中，把一个用户提交的 Container，依据它对 CPU、对 memory 请求大小，找一台合适的节点，进行放置；
  
** Node
Node 是真正运行业务负载的，每个业务负载会以 Pod 的形式运行
一个 Pod 中运行的一个或者多个容器，真正去运行这些 Pod 的组件的是叫做 kubelet,它通过 API Server 接收到所需要 Pod 运行的状态，然后提交到Container Runtime 组件中。
