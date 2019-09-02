CNI
    + flannel:网络配置
    + calico:网络配置，网络策略
    + canel :前两种搭配使用
## service:Kubernetes Service 定义了外界访问一组特定 Pod 的方式。Service 有自己的 IP 和端口，并把这个IP和后端的Pod所跑的服务的关联起来。Service 为 Pod 提供了负载均衡。
    工作模式：userspace,iptables,ipvs
        ipvs:1.11+
    类型：
        ExternalName,ClusterIP,NodePort,LoadBalancer
## 过程
`NodePort: client->NodeIP:NodePORT->ClusterIP:ServicePort->PodIP:containerPort`
 **No CclusterIP:Headless Service**无头服务
        ServiceName->PodIP
需求，k8s集群中一个pod client要访问集群外部的一个服务
通过service代理到外部服务。外部服务先转给nodeIP再转给service，再转给内部pod client
就是ExternalName

    资源记录：
        SVC_NAME.NS_NAME.DOMAIN.LTD.
        redis.default.svc.cluster.local.
service到endpoint到pod
`kubectl explain svc.spec.ports`
    - port: 80service监听的端口
      targetPort: 80容器的端口
      nodePort: 30080保证不被其他service占用，service ip上的端口映射为nodePort30080。节点的端口
redis-svc.yaml
```
apiVersion: v1
kind: Service
metadata:
    name: redis
    namespace: default
spec:
    selector:
        app: redis
        role: logstor
    clusterIP: 10.97.97.97可以不指定，会自动分配，指定的情况下，要保证不冲突
    type: ClusterIP(是私网地址，因此只集群内部可达，可以被各pod访问，被节点本身所访问)
    ports:
    - port: 6379 service端口
      targetPort: 6379容器端口
```
myapp-svc.yaml
```
apiVersion: v1
kind: Service
metadata:
    name: myapp
    namespace: default
spec:
    selector:
        app: myapp
        release: canary
    clusterIP: 10.98.98.98可以不指定，会自动分配，指定的情况下，要保证不冲突
    type: NodePort
    ports:
    - port: 80service监听的端口
      targetPort: 80容器的端口
      nodePort: 30080保证不被其他service占用，service ip上的端口映射为nodePort30080。节点的端口
```