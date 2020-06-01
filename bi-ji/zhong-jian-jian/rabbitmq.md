# rabbitmq

官方文档：[http://www.rabbitmq.com/install-debian.html\#apt](http://www.rabbitmq.com/install-debian.html#apt)

## **ubuntu 16.04安装**

1.Signing Key In order to use the repository, add a key used to sign RabbitMQ releases to apt-key: `wget -O - 'https://dl.bintray.com/rabbitmq/Keys/rabbitmq-release-signing-key.asc' | sudo apt-key add -` 2.`echo "deb https://dl.bintray.com/rabbitmq/debian xenial main" | sudo tee /etc/apt/sources.list.d/bintray.rabbitmq.list` 3.provides Erlang/OTP packages. Add Repository Signing Key

```text
curl -fsSL https://github.com/rabbitmq/signing-keys/releases/download/2.0/rabbitmq-release-signing-key.asc | sudo apt-key add -
# 或者
sudo apt-key adv --keyserver "hkps.pool.sks-keyservers.net" --recv-keys "0x6B73A36E6026DFCA"
```

1. It is possible to list multiple repositories, for example, one that provides RabbitMQ and one that provides Erlang/OTP packages.

   ```text
   sudo tee /etc/apt/sources.list.d/bintray.rabbitmq.list <<EOF
   deb https://dl.bintray.com/rabbitmq-erlang/debian xenial erlang
   deb https://dl.bintray.com/rabbitmq/debian xenial main
   EOF
   ```

   **ubuntu 18.04安装**

1.Signing Key In order to use the repository, add a key used to sign RabbitMQ releases to apt-key: `wget -O - "https://packagecloud.io/rabbitmq/rabbitmq-server/gpgkey" | sudo apt-key add -` 2.`echo "deb https://dl.bintray.com/rabbitmq/debian bionic main" | sudo tee /etc/apt/sources.list.d/bintray.rabbitmq.list` 3.provides Erlang/OTP packages. Add Repository Signing Key

```text
curl -fsSL https://github.com/rabbitmq/signing-keys/releases/download/2.0/rabbitmq-release-signing-key.asc | sudo apt-key add -
# 或者
sudo apt-key adv --keyserver "hkps.pool.sks-keyservers.net" --recv-keys "0x6B73A36E6026DFCA"
```

1. It is possible to list multiple repositories, for example, one that provides RabbitMQ and one that provides Erlang/OTP packages. 

   ```text
   sudo tee /etc/apt/sources.list.d/bintray.rabbitmq.list <<EOF
   deb https://dl.bintray.com/rabbitmq-erlang/debian bionic erlang
   deb https://dl.bintray.com/rabbitmq/debian bionic main
   EOF
   ```

2. **安装**

   \`\`\`

   apt-get update

   apt-get install rabbitmq-server

   rabbitmq-plugins enable rabbitmq\_management\#启用管理插件

   rabbitmqctl add\_user twdev LeanWork2015

   rabbitmqctl set\_user\_tags twdev administrator?????

rabbitmqctl add\_user msc abc123 rabbitmqctl add\_vhost msc-mq rabbitmqctl set\_permissions -p msc-mq msc '._' '._' '.\*' rabbitmqctl list\_users; rabbitmqctl list\_vhosts

```text
## **guest 用户如何禁用**
```

rabbitmq-plugins enable rabbitmq\_management

rabbitmqctl add\_user bwpre admin rabbitmqctl set\_permissions bwpre "._" "._" ".\*" rabbitmqctl set\_user\_tags bwpre administrator chmod 700 /var/lib/rabbitmq/.erlang.cookie echo -n "leanwork" &gt; /var/lib/rabbitmq/.erlang.cookie chmod 400 /var/lib/rabbitmq/.erlang.cookie service rabbitmq-server restart rabbitmq-plugins enable rabbitmq\_management rabbitmqctl join\_cluster rabbit@ali-hk-bwpre-mq1 --ram

```text
### **常用命令**
```

rabbitmqctl list\_queues --vhost brokerwork rabbitmqctl delete\_queue --vhost brokerwork BW\_CUSTOMER\_RCV\_QUEUE

\`\`\`

## **centos 7.4安装**

1 `https://www.rabbitmq.com/install-rpm.html#package-cloud` `Using Bintray Yum Repository`

2 `curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash` `curl -s https://packagecloud.io/install/repositories/rabbitmq/erlang/script.rpm.sh | sudo bash`

