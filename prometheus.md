主要监控：
*   Node:如主机CPU，内存，网络吞吐和带宽占用，磁盘I/O和磁盘使用等指标。node-exporter采集。
*   容器关键指标:集群中容器的CPU详细状况，内存详细状况，Network，FileSystem和Subcontainer等。通过cadvisor采集。
*   Kubernetes集群上部署的应用：监控部署在Kubernetes集群上的应用。主要是pod，service，ingress和endpoint。通过black-box和kube-apiserver的接口采集。

