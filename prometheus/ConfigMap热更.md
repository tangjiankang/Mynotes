当使用
```
        envFrom:
        - configMapRef:
            name: env-config
```
获取环境变量的值
~~~
$ kubectl exec `kubectl get pods -l run=my-nginx  -o=name|cut -d "/" -f2` env|grep log_level
log_level=INFO
~~~
修改 ConfigMap
~~~
$ kubectl edit configmap env-config
~~~
修改`log_level`的值为`DEBUG`。
再次查看环境变量的值。
~~~
$ kubectl exec `kubectl get pods -l run=my-nginx  -o=name|cut -d "/" -f2` env|grep log_level
log_level=INFO
~~~
 *  实践证明修改 ConfigMap 无法更新容器中已注入的环境变量信息。
*   使用该 ConfigMap 挂载的 Volume 中的数据需要一段时间（实测大概10秒）才能同步更新
* Known Issue： 如果使用ConfigMap的**subPath**挂载为Container的Volume，Kubernetes不会做自动热更新:[https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/#mounted-configmaps-are-updated-automatically](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/#mounted-configmaps-are-updated-automatically)  

Prometheus**可以在运行时重新加载其配置**。如果新配置格式不正确，则不会应用更改。通过向Prometheus进程发送`SIGHUP`或向`/-/reload`端点发送HTTP POST请求（启用`--web.enable-lifecycle`标志时）来触发配置reload。 这也将重新加载任何配置的规则文件。
prometheus使用的configmap修改后，k8s中的pod并不会自动重新读取，需要使用reload。
`curl -X POST "http://10.96.112.113:9090/-/reload"`