# 镜像删除

```text
# 停止所有容器
docker ps -a | grep "Exited" | awk '{print $1 }'|xargs docker stop
# 确认不是运行状态容器都可以删除
# 删除所有容器
docker ps -a | grep "Exited" | awk '{print $1 }'|xargs docker rm
# 删除所有none镜像
docker images|grep none|awk '{print $3 }'|xargs docker rmi
# 强制删除镜像
docker rmi -f 8f5116cbc201
# 
#docker images |grep "31:5000"|awk '{print $1":"$2}'>1.txt         ??????
```

