**启动kafka**
kafka启动时先启动zookeeper，再启动kafka；关闭时相反，先关闭kafka，再关闭zookeeper
启动zookeeper：
        bin/zookeeper-server-start.sh config/zookeeper.properties &
启动kafka：
        bin/kafka-server-start.sh config/server.properties &
————————————————

##create topic
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
##list topic
bin/kafka-topics.sh --list --zookeeper localhost:2181
###producer
bin/kafka-console-producer.sh --broker-list 172.31.38.176:9092 --topic test

###cumster
bin/kafka-console-consumer.sh --bootstrap-server 172.31.38.176:9092 --topic test --from-beginning --partition 0



在dockerup上面根据wurstmeister/kafka:2.12-2.1.0，查看该镜像可以配置哪些环境变量
k8s的kafka通过这条命令强制使用这个地址，即使在外部用了nodeportIP最终转发进kafka会跳转到这个地址
advertised.listeners=PLAINTEXT://52.25.141.94:9092


bin/zookeeper-shell.sh zk:2181 <<< "get /brokers/ids/0"

