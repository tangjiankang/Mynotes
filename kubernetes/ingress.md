Ingress  Controller 拥有7层代理和调度能力。有三种选择Nginx,Traefik,Envoy      
url 映射或虚拟主机
kubectl explain ingress
```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
    name: ingress-tomcat-tls
    namespace: default
    annotations:
        kubernetes.io/ingress.class: "nginx" 才能转化为相匹配的controller规则
spec:
    tls:
    - hosts:
      - tomcat.magedu.com
      secretName: tomcat-ingress-secret
    rules:定义规则
    - host: tomcat.magedu.com
      http:
        paths:
        - path: / 
          backend:
            serviceName: tomcat
            servicePort: 8080
```