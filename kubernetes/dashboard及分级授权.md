Dashboard:
    1、kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
    2、将Service改为NodePort或者使用ingress
    3、认证：
            认证时的帐号必须为ServiceAccount：被dashboard pod 拿来给kubernetes进行认证;
            token:
                创建ServiceAccount，根据其管理目标，使用rolebinding或clusterrolebinding绑定至合理的role或clusterrole;
        