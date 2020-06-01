# 获取镜像

从 Docker 镜像仓库获取镜像的命令是`docker pull`。其命令格式为：

```text
docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
```

具体的选项可以通过`docker pull --help`命令看到

* Docker 镜像仓库地址：地址的格式一般是`<域名/IP>[:端口号]`。默认地址是 Docker Hub。
* 仓库名：如之前所说，这里的仓库名是两段式名称，即`<用户名>/<软件名>`。对于 Docker Hub，如果不给出用户名，则默认为`library`，也就是官方镜像。

从下载过程中可以看到我们之前提及的分层存储的概念，镜像是由多层存储所构成。下载也是一层层的去下载，并非单一文件。下载过程中给出了每一层的 ID 的前 12 位。并且下载结束后，给出该镜像完整的`sha256`的摘要，以确保下载一致性。

## 从本机物理拷贝镜像 导入其他无网络服务器

`sudo docker images`

```text
REPOSITORY                                        TAG                            IMAGE ID            CREATED             SIZE
registry-hantec.bthub.com/citylist                v1.0.8                         c0afc906593e        2 months ago        196MB
registry-hantec.bthub.com/pay                     v1.4                           122fa3baa951        3 months ago        558MB
```

`sudo docker save 122fa3baa951 > /home/learnwork/pay.tar` `sudo docker load < pay.tar`

