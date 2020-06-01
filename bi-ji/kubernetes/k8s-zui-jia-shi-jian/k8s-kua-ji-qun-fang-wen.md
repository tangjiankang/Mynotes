# k8s跨集群访问

引用：[https://jimmysong.io/kubernetes-handbook/guide/authenticate-across-clusters-kubeconfig.html](https://jimmysong.io/kubernetes-handbook/guide/authenticate-across-clusters-kubeconfig.html) Kubernetes 的认证方式对于不同的人来说可能有所不同。

**在开启了 TLS 的集群中，每当与集群交互的时候少不了的是身份认证，使用 kubeconfig（即证书） 和 token 两种认证方式是最简单也最通用的认证方式**

* 运行 kubelet 可能有一种认证方式（即证书）。
* 用户可能有不同的认证方式（即令牌）。
* 管理员可能具有他们为个人用户提供的证书列表。
* 我们可能有多个集群，并希望在同一个地方将其全部定义——这样用户就能使用自己的证书并重用相同的全局配置。

所以为了能够让用户轻松地在多个集群之间切换，对于多个用户的情况下，我们将其定义在了一个 kubeconfig 文件中。

此文件包含一系列与昵称相关联的身份验证机制和集群连接信息。它还引入了一个（用户）认证信息元组和一个被称为上下文的与昵称相关联的集群连接信息的概念。

如果明确指定，则允许使用多个 kubeconfig 文件。在运行时，它们与命令行中指定的覆盖选项一起加载并合并
