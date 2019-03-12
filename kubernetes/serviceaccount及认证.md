任何客户端经apiserver做操作之前得先完成认证-->授权检查-->准入控制()
RBAC----基于角色的访问控制机制，只有许可授权
客户端对apiserver发起请求
    user：username，uid
    group：
    extra：
    API
    Request path
        例子/apis/apps/v1/namespaces/default/deployments/myapp-deploy/对这个增删改查 

k8s集群有2类认证值得用户账号
useraccount现实中的用户
serviceaccount---pod等应用程序想访问当前k8s集群的apiserver时里面要用到的认证信息。 
kubectl create serviceaccount myapp  新建个专用账号。
kubeconfig 是apiserver客户端连入apiserver时，

授权插件：Node，ABAC，RBAC，Webhook
    RBAC：Role-based AC 定义个角色，角色上绑定可以进行哪些操作
    角色（role）
    许可（）
role:
    operations
    objects
rolebinding:
    user account OR service account 
    role
集群级别和名称空间级别
clusterrole,clusterrolebinding
```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
    name: pods-reader
    namespace: default
rules:
  - apiGroups: 能对哪些api组内的操作
      - ""
    resources: 能对哪些资源进行操作
      - configmaps
      - pods
      - secrets
      - namespaces
    verbs:    能做什么操作
      - get
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: nginx-ingress-clusterrole
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
rules:
  - apiGroups:
      - ""
    resources:
      - configmaps
      - endpoints
      - nodes
      - pods
      - secrets
    verbs:
      - list
      - watch 
 ---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: RoleBinding
metadata:
  name: nginx-ingress-role-nisa-binding
  namespace: ingress-nginx
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
roleRef: 引用那个role
  apiGroup: rbac.authorization.k8s.io哪一类资源
  kind: Role
  name: nginx-ingress-role
subjects: 
  - kind: ServiceAccount
    name: nginx-ingress-serviceaccount授权给哪个用户
    namespace: ingress-nginx
```
kubectl describe rolebinding nginx-ingress-role-nisa-binding



 