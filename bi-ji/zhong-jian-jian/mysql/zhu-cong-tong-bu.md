# 主从同步

1、主： \[mysqld\]

## GTID:

server\_id=135 \#服务器id gtid\_mode=on \#开启gtid模式 enforce\_gtid\_consistency=on \#强制gtid一致性，开启后对于特定create table不被支持

## binlog

log\_bin=master-binlog log\_bin=/var/lib/mysql/master-binlog \#二选一。指定binlog存放位置。 log-slave-updates=1  
binlog\_format=row \#强烈建议，其他格式可能造成数据不一致 binlog\_cache\_size = 1M expire\_logs\_days=10

## relay log

lower\_case\_table\_names=1 表名称以小写形式存储在磁盘上，名称比较不区分大小写。 MySQL在存储和查找时将所有表名转换为小写。 此行为也适用于数据库名称和表别名。 log\_bin\_trust\_function\_creators=1 影响MySQL如何强制对存储函数和触发器创建的限制 sync\_binlog=1 所有事务在提交之前都将同步到二进制日志 slave-skip-errors = all

2、从： \[mysqld\]

## GTID:

gtid\_mode=on enforce\_gtid\_consistency=on server\_id=143 slave-skip-errors=all \#跳过所有同步错误,从库重启的话，会自动同步。

## relay log

lower\_case\_table\_names=1 log\_bin\_trust\_function\_creators=1 max\_connections=5000 datadir=/mnt/mysql/data socket=/tmp/mysql.sock basedir=/mnt/mysql user=mysql sync\_binlog=1 slave-skip-errors = all transaction-isolation = READ-COMMITTED group\_concat\_max\_len = 1024000 character-set-server=utf8 init\_connect='SET NAMES utf8' skip-name-resolve

### QA的配置

slave复制进程不随mysql启动而启动skip-slave-start 主： mysql&gt;grant replication slave, replication client on _._ to 'repl'@'172.16.179.104' identified by 'nH@H29.kEA7'; 从： mysql&gt;CHANGE MASTER TO MASTER\_HOST='172.16.179.102',MASTER\_USER='repl',MASTER\_PASSWORD='nH@H29.kEA7',MASTER\_PORT=3306,MASTER\_AUTO\_POSITION = 1; CHANGE MASTER TO MASTER\_HOST='10.26.60.21',MASTER\_USER='repl',MASTER\_PASSWORD='nH@H29.kEA7',MASTER\_PORT=3306,MASTER\_LOG\_FILE='master-binlog.001814',MASTER\_LOG\_POS=961360719 MASTER\_AUTO\_POSITION = 0;

mysql&gt; START slave; 查看slave状态 mysql&gt; show slave status\G

开启GTID时，slave在做同步复制时，无须找到binlog日志和POS点，直接change master to master\_auto\_position=1即可，自动找点同步。

