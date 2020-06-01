# 镜像加速器

国内从 Docker Hub 拉取镜像有时会遇到困难，此时可以配置镜像加速器。Docker 官方和国内很多云服务商都提供了国内加速器服务，例如：

* [Docker 官方提供的中国 registry mirror`https://registry.docker-cn.com`](https://docs.docker.com/registry/recipes/mirror/#use-case-the-china-registry-mirror)
* [阿里云加速器\(需登录账号获取\)](https://cr.console.aliyun.com/cn-hangzhou/mirrors)
* [七牛云加速器`https://reg-mirror.qiniu.com/`](https://kirk-enterprise.github.io/hub-docs/#/user-guide/mirror)

## Ubuntu 16.04+、Debian 8+、CentOS 7

对于使用[systemd](https://www.freedesktop.org/wiki/Software/systemd/)的系统，请在`/etc/docker/daemon.json`中写入如下内容（如果文件不存在请新建该文件）

```text
{
  "registry-mirrors": [
    "https://registry.docker-cn.com"
  ]
}
```

> 注意，一定要保证该文件符合 json 规范，否则 Docker 将不能启动。 之后重新启动服务。
>
> ```text
> sudo systemctl daemon-reload
> sudo systemctl restart docker
> ```

