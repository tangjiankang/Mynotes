# 错误1236

## 1、centos7安装mysql5。7

## 2、拷贝老从库datadir下所有内容到新服务器datadir，新服务器mysql配置文件，参考老从库

## 3、启动数据库,start slave;

\`错误信息如下：

Last\_IO\_Errno: 1236

```text
            Last_IO_Error: Got fatal error 1236 from master when reading data from binary log: 'The slave is connecting using CHANGE MASTER TO MASTER_AUTO_POSITION = 1, but the master has purged binary logs containing GTIDs that the slave requires.'`
```

1.在主库上执行以下命令，查询gtid\_purged，记录下改值 `mysql> show global variables like '%gtid%'\G` 2.在从库上执行以下命令停止同步线程及重置同步相关信息

```text
mysql> stop slave;
mysql> reset slave;
mysql> reset master;
```

3.在从库上设置gtid\_purged 该值有两个来源，一是在主库上查询的gtid\_purged，二是在从库上查询的已经执行过的gtid\_executed值（本机的就不需要，主库上gtid）

注意：一定记得加上从库上已经执行过的gtid，若只设置了主库上的gtid\_purged，此时从库会重新拉取主库上所有的二进制日志文件，同步过程会出现其他错误，导致同步无法进行 mysql&gt; set @@global.gtid\_purged='4fa9ab33-3077-11e6-8ee6-fcaa14d0751b:1-18240458,6e41a42e-8529-11e6-b72e-fcaa14d07546:1-56604052:56604054-56605629:56605631-56871196,9850e381-b601-11e6-8e46-fcaa14d07546:1-3126210,c5cdcae2-9cb0-11e6-909c-fcaa14d0751b:1-1189,10a59961-c02d-11e6-a2de-fcaa14d07546:1-13381418'; 4.重新开启同步 mysql&gt; change master to master\_host='192.168.1.15',master\_port=3306,master\_user='repl',master\_password='xxx',master\_auto\_position=1; mysql&gt; start slave;

