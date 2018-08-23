## 以下都是 kubernetes 中的 Object，这些对象都可以在 yaml文件中作为一种API类型来配置。
# 资源对象
Pod、ReplicaSet、ReplicationController、Deployment、StatefulSet、
DaemonSet、Job、CronJob、HorizontalPodAutoscaling
# 配置对象
Node、Namespace、Service、Secret、ConfigMap、Ingress、Label、
ThirdPartyResource、 ServiceAccount
# 存储对象
Volume、Persistent Volume
# 策略对象
SecurityContext、ResourceQuota、LimitRange
# 描述 Kubernetes 对象
spec 必须提供，它描述了对象的 期望状态—— 希望对象所具有的特征。
Kubernetes Deployment 的必需字段和对象 spec:
```
apiVersion: apps/v1beta1 # for versions before 1.6.0 use extensions/v1beta1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```
必需字段
在想要创建的 Kubernetes 对象对应的 .yaml 文件中，需要配置如下的字段：
+ apiVersion - 创建该对象所使用的 Kubernetes API 的版本
+ kind - 想要创建的对象的类型
+ metadata - 帮助识别对象唯一性的数据，包括一个 name 字符串、UID 和可选的
namespace
也需要提供对象的 spec 字段。对象 spec 的精确格式对每个 Kubernetes 对象来说是不同
的，包含了特定于该对象的嵌套字段。
# 参考地址https://github.com/kubernetes/community/blob/master/contributors/devel/api-conventions.md
## Pod概览
# 理解Pod
Pod是kubernetes中你可以创建和部署的最小也是最简的单位。一个Pod代表着集群中运行的
一个进程。
Pod中封装着应用的容器（ 有的情况下是好几个容器） ，存储、独立的网络IP，管理容器如何
运行的策略选项。Pod代表着部署的一个单位：kubernetes中应用的一个实例，可能由一个或
者多个容器组合在一起共享资源。
在Kubrenetes集群中Pod有如下两种使用方式：
+ 一个Pod中运行一个容器。“每个Pod中一个容器”的模式是最常见的用法；在这种使用方
式中，你可以把Pod想象成是单个容器的封装，kuberentes管理的是Pod而不是直接管理
容器。
+ 在一个Pod中同时运行多个容器。一个Pod中也可以同时封装几个需要紧密耦合互相协作
的容器，它们之间共享资源。这些在同一个Pod中的容器可以互相协作成为一个service
单位——一个容器共享文件，另一个“sidecar”容器来更新这些文件。Pod将这些容器的存
储资源作为一个实体来管理。
## 什么是Pod？
Pod就像是豌豆荚一样，它由一个或者多个容器组成（ 例如Docker容器） ，它们共享容器存
储、网络和容器运行配置项。Pod中的容器总是被同时调度，有共同的运行环境。你可以把单
个Pod想象成是运行独立应用的“逻辑主机”——其中运行着一个或者多个紧密耦合的应用容器
——在有容器之前，这些应用都是运行在几个相同的物理机或者虚拟机上。
# Pod和Controller
Controller可以创建和管理多个Pod，提供副本管理、滚动升级和集群级别的自愈能力。例
如，如果一个Node故障，Controller就能自动将该节点上的Pod调度到其他健康的Node上。
包含一个或者多个Pod的Controller示例：
Deployment
StatefulSet
DaemonSet
通常，Controller会用你提供的Pod Template来创建相应的Pod。
# Pod Templates
Pod模版是包含了其他object的Pod定义，例如Replication Controllers，Jobs和
DaemonSets。Controller根据Pod模板来创建实际的Pod。

