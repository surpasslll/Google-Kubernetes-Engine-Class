# Kubernetes概述
* k8s是一个开源的，用于管理云平台中多个主机上的容器化的应用
* 核心的特点就是能够自主的管理容器来保证云平台中的容器按照用户的期望状态运行着
* 在Kubenetes中，所有的容器均在Pod中运行,一个Pod可以承载一个或者多个相关的容器
* 一个Pod也可以包含O个或者多个磁盘卷组
* 功能：
1. 调度： 把用户提交的容器放到 Kubernetes 管理的集群的某一台节点上去比如说它所需要的 CPU以及它所需要的 memory，
然后在集群中找一台相对比较空闲的机器来进行一次 placement，也就是一次放置的操作
2. 自动修复: Kubernetes 会把运行在这些失败节点上的容器进行自动迁移，迁移到一个正在健康运行的宿主机上，来完成集群内容器的一个自动恢复
3. 水平伸缩: 如果这个业务本身的 CPU 利用率过高，或者响应时间过长，它可以对这个业务进行一次扩容

# Kubernetes 的架构
* 比较典型的二层架构和 server-client 架构。Master 作为中央的管控节点，会去与 Node 进行一个连接。
* Kubernetes 的 Master 包含四个主要的组件：API Server、Controller、Scheduler 以及 etcd
1. etcd：是一个分布式的一个存储系统，API Server 中所需要的这些原信息都被放置在 etcd 中，
   etcd 本身是一个高可用系统(高可用是系统架构设计中必须要考虑的，是指系统所能提供无故障服务的一种能力。简单的说就是避免因服务器宕机而造成的服务不可用的情况，像Elasticsearch并不会因为一节点的宕机而造成整个搜索服务不可用)，通过 etcd 保证整个 Kubernetes 的 Master 组件的高可用性
2. API Server：Kubernetes 中所有的组件都会和 API Server 进行连接，组件与组件之间一般不进行独立的连接，都依赖于 API Server 进行消息的传送；
3. Controller：控制器，它用来完成对集群状态的一些管理。
  比如刚刚我们提到的两个例子之中，第一个自动对容器进行修复、第二个自动进行水平扩张，都是由 Kubernetes 中的 Controller 来进行完成的；
4. Scheduler：调度器，“调度器”顾名思义就是完成调度的操作，
  就是我们刚才介绍的第一个例子中，把一个用户提交的 Container，依据它对 CPU、对 memory 请求大小，找一台合适的节点，进行放置；
  
## Node
* Node 是真正运行业务负载的，每个业务负载会以 Pod 的形式运行
* 一个 Pod 中运行的一个或者多个容器，真正去运行这些 Pod 的组件的是叫做 kubelet,它通过 API Server 接收到所需要 Pod 运行的状态，然后提交到Container Runtime 组件中。
![image](https://github.com/surpasslll/Google-Kubernetes-Engine-Class/blob/master/Node.png)
* Kubernetes 并不会直接进行网络存储的操作，他们会靠 Storage Plugin 或者是网络的 Plugin 来进行操作
* 调度示例：
1. 用户可以通过 UI 或者 CLI 提交一个 Pod 给 Kubernetes 进行部署
2. 这个 Pod 请求首先会通过 CLI 或者 UI 提交给 Kubernetes API Server
3. API Server 会把这个信息写入到它的存储系统 etcd
4. Scheduler 会通过 API Server 的 watch 或者叫做 notification 机制得到这个信息：有一个 Pod 需要被调度。
5. Scheduler 会根据它的内存状态进行一次调度决策
6. 在完成这次调度之后，它会向 API Server report 说：“OK！这个 Pod 需要被调度到某一个节点上。”
7. API Server 接收到这次操作之后，会把这次的结果再次写到 etcd 中，然后 API Server 会通知相应的节点进行这次 Pod 真正的执行启动。
8. 相应节点的 kubelet 会得到这个通知，kubelet 就会去调 Container runtime 来真正去启动配置这个容器和这个容器的运行环境，去调度 Storage Plugin 来去配置存储，network Plugin 去配置网络。


## Pod
* Kubernetes的一个最小调度以及资源单元
* 一个 Pod 简单来说是对一组容器的抽象，它里面会包含一个或多个容器
* 在 Pod 里面，我们也可以去定义容器所需要运行的方式。

## Volume
* 抽象的概念
* 用来管理 Kubernetes 存储的，是用来声明在 Pod 中的容器可以访问文件目录的
* 一个卷可以被挂载在 Pod 中一个或者多个容器的指定路径下面
* 可以支持本地的存储，可以支持分布式的存储, 也可以支持云存储
* K8s还支持使用Persistent Volume Claim即PVC这种逻辑存储，使用这种存储，使得存储的使用者可以忽略后台的实际存储技术（例如AWS，Google或GlusterFS和Ceph），而将有关存储实际技术的配置交给存储管理员通过Persistent Volume来配置。

----------------------  数据库及存储  ---------------------
1. 随着数据量的高速增长，关系型数据库逐渐暴露出一些局限性，比如数据库访问速度、扩展性
2. 传统的关系型数据库开始从集中式模型向分布式架构发展，基于关系型的分布式数据库在保留了传统数据库的数据模型和基本特征下，从集中式存储走向分布式存储，从集中式计算走向分布式计算
3. 分布式数据库是指利用高速计算机网络将物理上分散的多个数据存储单元连接起来组成一个逻辑上统一的数据库。
4. 分布式数据库的基本思想是将原来集中式数据库中的数据分散存储到多个通过网络连接的数据存储节点上，以获取更大的存储容量和更高的并发访问量。
5. 分布式数据库优势：更高的数据访问速度(可以并发地从多个备份服务器同时读取)、更强的可扩展性、更高的并发访问量
（More：阿里数据库课程 https://edu.aliyun.com/course/37/lesson/list）

----------------------  数据库及存储  ---------------------

## 持久存储卷（Persistent Volume，PV）和持久存储卷声明（Persistent Volume Claim，PVC）
* PV和PVC使得K8s集群具备了存储的逻辑抽象能力
* 在配置Pod的逻辑里可以忽略对实际后台存储技术的配置，而把这项配置的工作交给PV的配置者，即集群的管理者
* 存储的PV和PVC的这种关系，跟计算的Node和Pod的关系是非常类似的
* PV和Node是资源的提供者，根据集群的基础设施变化而变化，由K8s集群管理员配置
* 而PVC和Pod是资源的使用者，根据业务服务的需求变化而变化，由K8s集群的使用者即服务的管理员来配置

## 副本集（Replica Set，RS）
* RS是新一代RC，提供同样的高可用能力，区别主要在于RS后来居上，能支持更多种类的匹配模式。副本集对象一般不单独使用，而是作为Deployment的理想状态参数使用。

## Deployment
* 部署表示用户对K8s集群的一次更新操作
* Pod 这个抽象上更为上层的一个抽象
* 可以定义一组 Pod 的副本数目、以及这个 Pod 的版本
* 通过Controller维持Pod的数目，自动恢复失败的pod
* 通过controller以指定的策略控制版本，如滚动升级，重新生成，回滚
* 滚动升级一个服务，实际是创建一个新的RS


## Service
* Service 提供了一个或者多个 Pod 实例的稳定访问地址。
* 一个 Deployment 可能有两个甚至更多个完全相同的 Pod。对于一个外部的用户来讲，访问哪个 Pod 其实都是一样的，我只想访问某一个固定的Virtual IP 地址，而不希望得知每一个具体的 Pod 的 IP 地址
* 一个抽象, 把所有Pod的访问能力抽象成一个第三方的一个IP地址
* 支持多种访问方式实现：ClusterIP、NodePort、LoadBalancer

## Namespace
* 做一个集群内部的逻辑隔离
* Kubernetes 的每个资源，比如刚才讲的 Pod、Deployment、Service 都属于一个 Namespace
* 同一个 Namespace 中的资源需要命名的唯一性，不同的 Namespace 中的资源可以重名

# Kubernetes 的 API
* HTTP+JSON 组成的：用户访问的方式是 HTTP，访问的 API 中 content 的内容是 JSON 格式的。
* 如果我们去提交一个 Pod，或者 get 一个 Pod 的时候，它的 content 内容都是用 JSON 或者是 YAML 表达的
* 第一个部分，一般来讲会是 API 的 version，也会描述我在操作哪个资源（kind），然后在在 Metadata 中，就写上name/label(可以是一组 KeyValuePair,可以被 selector选择器所查询的,API 层就可以对这些资源进行一个筛选)/annotation(对资源的额外的一些用户层次的描述)
* 比较重要的一个部分叫做 Spec (希望 Pod 达到的一个预期的状态):比如说它内部需要有哪些 container 被运行；它的 image 是什么？它暴露的 port 是什么？
* 当我们从 Kubernetes API 中去获取这个资源的时候，一般来讲在 Spec 下面会有一个项目叫 status，它表达了这个资源当前的状态


