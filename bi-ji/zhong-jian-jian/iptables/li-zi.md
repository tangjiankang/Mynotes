# 例子

```text
[root@ali-hk-bw3-backup ~]# cat /etc/sysconfig/iptables
# Generated by iptables-save v1.4.7 on Tue Nov 19 10:19:10 2019
*filter
:INPUT DROP [1:40]  #所有入站drop
:FORWARD ACCEPT [24:2416] 
:OUTPUT ACCEPT [556:54085]
-A INPUT -p icmp -j ACCEPT 
-A INPUT -i lo -j ACCEPT 
-A INPUT -i eth0 -j ACCEPT 内网接受
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT 
COMMIT
# Completed on Tue Nov 19 10:19:10 2019
# Generated by iptables-save v1.4.7 on Tue Nov 19 10:19:10 2019
*nat  #nat表优先于filter，还没进入filter，就没转发走了，所以直接用阿里云安全组做的端口控制
:PREROUTING ACCEPT [84319:6509735]
:POSTROUTING ACCEPT [16370:967945]
:OUTPUT ACCEPT [16373:968101]
-A PREROUTING -p tcp -m tcp --dport 40000 -j DNAT --to-destination 172.19.91.174:3306 
# 访问本机的40000端口全部转发到172.19.91.174:3306
-A PREROUTING -p tcp -m tcp --dport 40004 -j DNAT --to-destination 172.31.0.128:3717 
-A POSTROUTING -p tcp -m tcp --dport 3306 -j SNAT --to-source 10.28.188.90 
# 所有目的地为3306端口的封包，都将其来源ip改为10.28.188.90（172.19.91.174:3306的安全策略允许10.28.188.90访问，其他地址不行）
# 为什么修改源IP地址？最常见的就是我们内网多台机器需要共享一个或几个公网IP访问 Internet。因为我们的内网地址是私有的，假如就让Linux给路由出去，源地址也不变，这个包能访问到目的地，但却回不来。因为 Internet上的路由节点不会转发私有地址的数据包，也就是说，不用合法IP，我们的数据包有去无回。
-A POSTROUTING -p tcp -m tcp --dport 3717 -j SNAT --to-source 10.28.188.90 
-A POSTROUTING -p tcp -m tcp --dport 20000 -j SNAT --to-source 10.28.188.90 
COMMIT
# Completed on Tue Nov 19 10:19:10 2019
```
