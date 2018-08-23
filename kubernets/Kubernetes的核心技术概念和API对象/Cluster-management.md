为了管理异构和不同配置的主机，为了便于Pod的运维管理，Kubernetes中提供了很多集群管
理的配置和管理功能，通过namespace划分的空间，通过为node节点创建label和taint用于
pod的调度等。
# Node
Node是kubernetes集群的工作节点，可以是物理机也可以是虚拟机。
# Node的状态
Node包括如下状态信息：
+ Address
HostName：可以被kubelet中的 --hostname-override 参数替代。
ExternalIP：可以被集群外部路由到的IP地址。
InternalIP：集群内部使用的IP，集群外部无法访问。
+ Condition
OutOfDisk：磁盘空间不足时为 True
Ready：Node controller 40秒内没有收到node的状态报告为 Unknown ，健康
为 True ，否则为 False 。
MemoryPressure：当node没有内存压力时为 True ，否则为 False 。
DiskPressure：当node没有磁盘压力时为 True ，否则为 False 。
+ Capacity
CPU
内存
可运行的最大Pod个数
+ Info：节点的一些版本信息，如OS、kubernetes、docker等
## Namespace
因为namespace可以提供独立的命名空间，因此可以实现部分的环境隔离。当你的项目和人
员众多的时候可以考虑根据项目属性，例如生产、测试、开发划分不同的namespace。
# Label
Label是附着到object上（ 例如Pod） 的键值对。可以在创建object的时候指定，也可以在
object创建后随时指定。Labels的值对系统本身并没有什么含义，只是对用户才有意义。
```
"labels": {
"key1" : "value1",
"key2" : "value2"
}
```
Kubernetes最终将对labels最终索引和反向索引用来优化查询和watch，在UI和命令行中会对
它们排序。不要在label中使用大型、非标识的结构化数据，记录这样的数据应该用
annotation。
Label能够将组织架构映射到系统架构上（ 就像是康威定律） ，这样能够更便于微服务的管
理，你可以给object打上如下类型的label：
+ "release" : "stable" , "release" : "canary"
+ "environment" : "dev" , "environment" : "qa" , "environment" : "production"
+ "tier" : "frontend" , "tier" : "backend" , "tier" : "cache"
+ "partition" : "customerA" , "partition" : "customerB"
+ "track" : "daily" , "track" : "weekly"
+ "team" : "teamA" , "team:" : "teamB"
