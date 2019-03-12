官方文档：http://www.rabbitmq.com/install-debian.html#apt
ubuntu 16.04
1.Signing Key
In order to use the repository, add a key used to sign RabbitMQ releases to apt-key:
`wget -O - 'https://dl.bintray.com/rabbitmq/Keys/rabbitmq-release-signing-key.asc' | sudo apt-key add -`
2.`echo "deb https://dl.bintray.com/rabbitmq/debian xenial main" | sudo tee /etc/apt/sources.list.d/bintray.rabbitmq.list`
3.provides Erlang/OTP packages. 
`wget -O - 'https://dl.bintray.com/rabbitmq/Keys/rabbitmq-release-signing-key.asc' | sudo apt-key add -`
cat /etc/apt/sources.list.d/bintray.erlang.list
#See below for supported distribution and component values
deb https://dl.bintray.com/rabbitmq/debian xenial erlang
4 **安装**
```
apt-get update
apt-get install rabbitmq-server
rabbitmq-plugins enable rabbitmq_management#启用管理插件
rabbitmqctl add_user twdev LeanWork2015
rabbitmqctl set_user_tags twdev administrator?????

rabbitmqctl add_user msc abc123
rabbitmqctl add_vhost msc-mq
rabbitmqctl set_permissions -p msc-mq msc '.*' '.*' '.*'
rabbitmqctl list_users;
rabbitmqctl list_vhosts
```
## guest 用户如何禁用
```
rabbitmq-plugins enable rabbitmq_management
rabbitmqctl add_user bwpre admin
rabbitmqctl set_permissions  bwpre  ".*"  ".*"  ".*"
rabbitmqctl set_user_tags bwpre administrator
chmod 700 /var/lib/rabbitmq/.erlang.cookie
echo -n "leanwork" > /var/lib/rabbitmq/.erlang.cookie
chmod 400 /var/lib/rabbitmq/.erlang.cookie
service rabbitmq-server restart
rabbitmq-plugins enable rabbitmq_management
rabbitmqctl join_cluster rabbit@ali-hk-bwpre-mq1 --ram
```
# centos 7.4
1 `https://www.rabbitmq.com/install-rpm.html#package-cloud`
`Using Bintray Yum Repository`

2 
`curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash` 
`curl -s https://packagecloud.io/install/repositories/rabbitmq/erlang/script.rpm.sh | sudo bash`