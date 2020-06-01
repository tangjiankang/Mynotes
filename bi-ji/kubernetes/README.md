# kubernetes

[https://github.com/kubernetes/kubernetes/tree/master/cluster/addons](https://github.com/kubernetes/kubernetes/tree/master/cluster/addons) cluster下的addons是稳定的一些软件等，经过验证的。

## 查验失败

除此之外，在 Kubernetes 的部署中，我们经常可以碰到容器镜像没有更新、集群资源不足、校验错误、持久化卷挂载失败等问题，开发人员可以使用一些简单命令进行快速定位，比如，kubectl describe deployment/；kubectl describe replicaset/；kubectl get pods；kubectl describe pod/；kubectl logs--previous 等命令可以被用来定位常见的大部分失败问题。

