使用国内镜像站
```
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
sudo curl -s https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | sudo apt-key add -
sudo tee /etc/apt/sources.list.d/kubernetes.list <<-'EOF'  
deb https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial main  
EOF
sudo apt-get update  
sudo apt-cache madison kubeadm
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
sudo apt-mark hold package 标记该软件包不被自动更新
```
## **systemctl list-unit-files --type=service 查看服务是否开机自启**
```
docker pull mirrorgooglecontainers/kube-apiserver:v1.15.0
docker pull mirrorgooglecontainers/kube-controller-manager:v1.15.0
docker pull mirrorgooglecontainers/kube-scheduler:v1.15.0
docker pull mirrorgooglecontainers/kube-proxy:v1.15.0
docker pull mirrorgooglecontainers/pause:3.1
docker pull mirrorgooglecontainers/etcd:3.3.10
docker pull mirrorgooglecontainers/coredns:1.3.1

docker tag mirrorgooglecontainers/kube-apiserver:v1.15.0 k8s.gcr.io/kube-apiserver:v1.15.0
docker tag mirrorgooglecontainers/kube-controller-manager:v1.15.0 k8s.gcr.io/kube-controller-manager:v1.15.0
docker tag mirrorgooglecontainers/kube-scheduler:v1.15.0 k8s.gcr.io/kube-scheduler:v1.15.0
docker tag mirrorgooglecontainers/kube-proxy:v1.15.0 k8s.gcr.io/kube-proxy:v1.15.0
docker tag mirrorgooglecontainers/pause:3.1 k8s.gcr.io/pause:3.1
docker tag mirrorgooglecontainers/etcd:3.3.10 k8s.gcr.io/etcd:3.3.10
docker tag coredns/coredns:1.3.1 k8s.gcr.io/coredns:1.3.1
```
安装指定版本 --kubernetes-version v1.12.1