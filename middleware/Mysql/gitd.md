### Gtid功能的增强
在MySQL5.7中对Gtid做了一些增强，现在进行一些说明。<br>
GTID即全局事务ID（global transaction identifier），GTID实际上是由UUID+TID（Sequence Number）组成的。<br>
其中UUID是一个MySQL实例的唯一标识。TID代表了该实例上已经提交的事务数量，并且随着事务提交单调递增，所以GTID能够保证每个MySQL实例事务的执行（不会重复执行同一个事务，并且会补全没有执行的事务）。<br>
下面是一个GTID的具体形式：<br>
4e659069-3cd8-11e5-9a49-001c4270714e:1<br>
GTID的目的是简化复制的使用过程和降低复制集群维护的难度，不再依赖Master的binlog文件名和文件中的位置。<br>
CHANGE MASTER TO MASTER_LOG_FILE=‘Master-bin.000010’, MASTER_LOG_POS=‘214’;<br>
简化成：<br>
CHANGE MASTER TO MASTER_AUTO_POSITION=1;<br>
### MASTER_AUTO_POSITION的原理：
MySQL Server记录了所有已经执行了的事务的GTID，包括复制过来的（可以通过select @@global.gtid_executed查看）。<br>
Slave记录了所有从Master接收过来的事务的GTID（可以通过Retrieve_gtid_set查看）。<br>
Slave连接到Master时，会把gtid_executed中的gtid发给Master，Master会自动跳过这些事务，只将没有复制的事务发送到Slave去。
### 上面介绍的是GTID的基本概念，GTID相关变量：
binlog_gtid_simple_recovery ：MySQL5.7.7之后默认on，这个参数控制了当mysql启动或重启时，mysql在搜寻GTIDs时是如何迭代使用binlog文件。<br>
该参数为真时，mysql-server只需打开最老的和最新的这2个binlog文件，gtid_purged参数的值和gtid_executed参数的值可以根据这些文件中的Previous_gtids_log_event或者Gtid_log_event计算得出。<br>
这确保了当mysql-server重启或清理binlog时，只需打开2个binlog文件。<br>
当这个参数设置为off，在mysql恢复期间，为了初始化gtid_executed，所有以最新文件开始的binlog都要被检查。<br>
并且为了初始化gtid_purged，所有的binlog都要被检查。这可能需要非常长的时间，建议开启。<br>
注意：MySQL5.6中，默认为off，调整这个选项设置也同样会提升性能，但是在一些特殊场景下，计算gtids值可能会出错。而保持这个选项值为off，能确保计算总是正确。
* enforce_gtid_consistency：默认off，可选值有on和warn。根据该变量的值，服务器只允许可以安全使用GTID记录的语句通过，强制GTID一致性。在启用基于GTID复制之前将此变量需要设置为on。
* OFF  ：不检测是否有GTID不支持的语句和事务。Warn ：当检测到不支持GTID的语句和事务，返回警告，并在日志中记录。
* ON   ：当检测到不支持GTID的语句和事务，返回错误。
- gtid_mode：控制是否开启GTID，默认OFF。可选值有OFF、OFF_PERMISSIVE、ON、ON_PERMISSIVE。
- OFF            ：不产生GTID，Slave只接受不带GTID的事务
- OFF_PERMISSIVE ：不产生GTID，Slave即接受不带GTID的事务，也接受带GTID的事务
- ON_PERMISSIVE  ：产生GTID，Slave即接受不带GTID的事务，也接受带GTID的事务
- ON             ：产生GTID，Slave只能接受带GTID的事务。
- session_track_gtids：控制用于捕获GTIDs和在OK PACKE返回的跟踪器。 
- OFF       ：关闭OWN_GTID  ：返回当前事务产生的
- GTIDALL_GTIDS ：返回系统执行的所有GTID，也就是GTID_EXECUTED
- gtid_purged：已经被删除的binlog的事务。
- gtid_owned： 表示正在执行的事务的gtid以及对应的线程ID。
- gtid_executed：表示已经在该实例上执行过的事务（mysql.gtid_executed）， 执行RESET MASTER会将该变量置空（清空mysql.gtid_executed），可以通过设置GTID_NEXT执行一个空事务，来影响GTID_EXECUTED。GTID_NEXT是SESSION级别变量，表示下一个将被使用的GTID。
gtid_executed_compression_period：默认1000个事务，表示控制每执行多少个事务，对此表（mysql.gtid_executed）进行压缩。
## 介绍了GTID的概念和变量，现在说下MySQL5.7下GTID增强的一些特性：
①：在线开启GTID。MySQL5.6开启GTID的功能需要重启服务器生效。
```
mysql> set global gtid_mode=on;ERROR 1788 (HY000): The value of @@GLOBAL.GTID_MODE can only be changed one step at a time: OFF <-> OFF_PERMISSIVE <-> ON_PERMISSIVE <-> ON. Also note that this value must be stepped up or down simultaneously on all servers. See the Manual for instructions.
mysql> set global gtid_mode=OFF_PERMISSIVE;Query OK, 0 rows affected (0.17 sec)
mysql> set global gtid_mode=ON_PERMISSIVE;Query OK, 0 rows affected (0.14 sec)
#等一段时间, 让不带GTID的binlog events在所有的服务器上执行完毕 
mysql> set global gtid_mode=ON;ERROR 3111 (HY000): SET @@GLOBAL.GTID_MODE = ON is not allowed because ENFORCE_GTID_CONSISTENCY is not ON.
mysql> set global enforce_gtid_consistency=on;Query OK, 0 rows affected (0.00 sec)
mysql> set global gtid_mode=ON;Query OK, 0 rows affected (0.16 sec)
```
在线开启GTID的步骤：不是直接设置gtid_mode为on，需要先设置成OFF_PERMISSIVE，再设置成ON_PERMISSIVE，再把enforce_gtid_consistency设置成ON，最后再将gtid_mode设置成on，如上面所示。<br>
若保证GTID重启服务器继续有效，则需要再配置文件里添加：<br>
gtid-mode=onenforce-gtid-consistency=on<br>
在线启用GTID功能的好处：不需要重启数据库，配置过程在线，整个复制集群仍然对外提供读和写的服务；不需要改变复制拓扑结构；可以在任何结构的复制集群中在线启用GTID功能。<br>
②：存储GTID信息到表中，slave不需要再开启log_bin和log_slave_updates。表存在在mysql.gtid_executed，MySQL5.6上GTID只能存储在binlog中，所以必须开启Binlog才能使用GTID功能。<br>
如何记录GTID到表中？这里有2种情况：<br>
- 如果开启了binlog，在切换binlog时将当前binlog的所有GTID插入gtid_executed表中。插入操作等价于一个或多个INSERT语句。
INSERT INTO mysql.gtid_executed(UUID, 1, 100)
- 如果没有开启binlog，每个事务在提交之前会执行一个等价的INSERT的操作。 此操作是该事务的一部分，和事务的其他操作整体保持原子性。 需要保证gtid_executed是innodb存储引擎。
BEGIN;...INSERT INTO mysql.gtid_executed(UUID, 101, 101); COMMIT;<br>
为什么把GTID记录到表中，原因是什么？<br>
MySQL5.6中必须配置参数log_slave_updates的最重要原因在于当slave重启后，无法得知当前slave已经运行到的GTID位置，因为变量gtid_executed是一个内存值，所以MySQL 5.6的处理方法就是启动时扫描最后一个二进制日志，获取当前执行到的GTID位置信息。<br>
如果不小心将二进制日志删除了，那么这又会带来灾难性的问题。<br>
因此MySQL5.7将gtid_executed这个值给持久化了。因为gtid写表了，表gtid_executed中的记录会增长，所以MySQL 5.7又引入了新的线程，用来对此表进行压缩，通过参数gtid_executed_compression_period用来控制每执行多少个事务，对此表进行压缩，默认值为1000个事务。<br>
表（mysql.gtid_executed）压缩前后对比：

  压缩前：    
+--------------------------------------+----------------+--------------+
| source_uuid                          | interval_start | interval_end |
+--------------------------------------+----------------+--------------+
| xxxxxxxx-4733-11e6-91fe-507b9d0eac6d |              1 |            1 |
+--------------------------------------+----------------+--------------+
| xxxxxxxx-4733-11e6-91fe-507b9d0eac6d |              2 |            2 |
+--------------------------------------+----------------+--------------+
| xxxxxxxx-4733-11e6-91fe-507b9d0eac6d |              3 |            3 |
+--------------------------------------+----------------+--------------+
压缩后：
+--------------------------------------+----------------+--------------+
| source_uuid                          | interval_start | interval_end |
+--------------------------------------+----------------+--------------+
| xxxxxxxx-4733-11e6-91fe-507b9d0eac6d |              1 |            3 |
+--------------------------------------+----------------+--------------+

通过命令：SET GLOBAL gtid_executed_compression_period = N(事务的数量) 来控制压缩频率。
③：GTID受限制的语句。 
1）使用CREATE TABLE ... SELECT... 语句。
2）事务中同时使用了支持事务和不支持事务的引擎。 
3）在事务中使用CREATE/DROP TEMPORARY TABLE。
不支持的语句出现，会报错： 
ERROR 1786 (HY000): Statement violates GTID consistency:...
④：测试，具体GTID的测试可以看看MySQL5.6 新特性之GTID。
注意：主和从要一起开启GTID，只开启任意一个都会报错：
Last_IO_Errno: 1593Last_IO_Error: The replication receiver thread cannot start because the master has GTID_MODE = ON and this server has GTID_MODE = OFF.
搭建GTID的复制环境，可以查看官方文档。
MySQL5.7.4之前的slave必须要开启binlog和log_slave_updates，之后不需要开启，原因上面已经说明。

slave 关闭了binlog：mysql> show variables like 'log_%';
+----------------------------------------+-------------------------------+
| Variable_name                          | Value                         |
+----------------------------------------+-------------------------------+
| log_bin                                | OFF                           |
| log_slave_updates                      | OFF                           |
+----------------------------------------+-------------------------------+

执行change：
```
mysql> CHANGE MASTER TO MASTER_HOST='10.0.3.141',MASTER_USER='repl',MASTER_PASSWORD='Repl_123456',MASTER_AUTO_POSITION=1;Query OK, 0 rows affected, 2 warnings (0.29 sec)
mysql> start slave;Query OK, 0 rows affected (0.01 sec)
mysql> show slave status\G
```
GTID复制增加了一个master_auto_position参数，该参数不能和master_log_file和master_log_pos一起出现，否则会报错：
ERROR 1776 (HY000): Parameters MASTER_LOG_FILE, MASTER_LOG_POS, RELAY_LOG_FILE and RELAY_LOG_POS cannot be set when MASTER_AUTO_POSITION is active. 
检查是否开启了GTID的复制：

Master上：mysql> show processlist\G;*************************** 1. row ***************************
           Id: 4
         User: repl
         Host: 10.0.3.219:35408
           db: NULL
      Command: Binlog Dump GTID
         Time: 847
        State: Master has sent all binlog to slave; waiting for more updates
         Info: NULL    Rows_sent: 0Rows_examined: 0
mysql> show binlog events in  'mysql-bin-3306.000002';
+-----------------------+-----+----------------+-----------+-------------+---------------------------------------------+
| Log_name              | Pos | Event_type     | Server_id | End_log_pos | Info                                                              |
+-----------------------+-----+----------------+-----------+-------------+---------------------------------------------+
| mysql-bin-3306.000002 |   4 | Format_desc    |         1 |         123 | Server ver: 5.7.13-6-log, Binlog ver: 4                           |
| mysql-bin-3306.000002 | 123 | Previous_gtids |         1 |         194 | 7b389a77-4423-11e6-8e6b-00163ec0a235:1-4                          |
| mysql-bin-3306.000002 | 194 | Gtid           |         1 |         259 | SET @@SESSION.GTID_NEXT= '7b389a77-4423-11e6-8e6b-00163ec0a235:5' |
| mysql-bin-3306.000002 | 259 | Query          |         1 |         346 | BEGIN                                                             |
| mysql-bin-3306.000002 | 346 | Query          |         1 |         475 | use `dba_test`; insert into gtid values(1,'AAAAA'),(2,'BBBBBB')   |
| mysql-bin-3306.000002 | 475 | Xid            |         1 |         506 | COMMIT /* xid=35 */                                               |
+-----------------------+-----+----------------+-----------+-------------+---------------------------------------------+

⑤：错误跳过和异常处理：gtid_next、gtid_purged。之前的文章MySQL5.6 新特性之GTID介绍了如何跳过一些常见的复制错误，这里再大致的说明下大致的处理步骤。
 1）gtid_next（SQL线程报错）：主键冲突，表、数据库不存在，row模式下的数据不存在等。

mysql> show slave status\G*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
             Slave_IO_Running: Yes
            Slave_SQL_Running: No
                   Last_Errno: 1062
                   Last_Error: Coordinator stopped because there were error(s) in the worker(s). The most recent failure being: Worker 0 failed executing transaction '7b389a77-4423-11e6-8e6b-00163ec0a235:10' at master log mysql-bin-3306.000002, end_log_pos 1865. See error log and/or performance_schema.replication_applier_status_by_worker table for more details about this failure or others, if any.

GTID的复制对于错误信息的可读性不好，不过可以通过错误代码（1062）或监控表（replication_applier_status_by_worker）查看：

mysql> select * from performance_schema.replication_applier_status_by_worker where LAST_ERROR_NUMBER=1062\G
*************************** 1. row ***************************
         CHANNEL_NAME:
            WORKER_ID: 1
            THREAD_ID: NULL
        SERVICE_STATE: OFFLAST_SEEN_TRANSACTION: 7b389a77-4423-11e6-8e6b-00163ec0a235:10  #出现错误的GTID
    LAST_ERROR_NUMBER: 1062   LAST_ERROR_MESSAGE: Worker 0 failed executing transaction '7b389a77-4423-11e6-8e6b-00163ec0a235:10' at master log mysql-bin-3306.000002, end_log_pos 1865; Error 'Duplicate entry '1' for key 'uk_id'' on query. Default database: 'dba_test'. Query: 'insert into gtid values(1,'ABC')' LAST_ERROR_TIMESTAMP: 2016-07-28 13:21:48

可以看到具体SQL的报错信息。那如何跳过错误信息呢？开启GTID不能使用sql_slave_skip_counter跳过错误：
ERROR 1858 (HY000): sql_slave_skip_counter can not be set when the server is running with @@GLOBAL.GTID_MODE = ON. Instead, for each transaction that you want to skip, generate an empty transaction with the same GTID as the transaction
使用GTID跳过错误的方法：找到错误的GTID跳过（通过Exec_Master_Log_Pos去binlog里找GTID，或则通过上面监控表找到GTID，也可以通过Executed_Gtid_Set算出GTID），这里使用监控表来找到错误的GTID。找到GTID之后，跳过错误的步骤：
```
mysql> stop slave;                                                         #停止同步Query OK, 0 rows affected (0.02 sec)
mysql> set @@session.gtid_next='7b389a77-4423-11e6-8e6b-00163ec0a235:10';  #跳过错误的GTID，可以不用session，用session的目的是规范Query OK, 0 rows affected (0.00 sec)
mysql> begin;Query OK, 0 rows affected (0.00 sec)                                       #提交一个空事务，因为设置gtid_next后，gtid的生命周期就开始了，必须通过显性的提交一个事务来结束，否则报错：ERROR 1790 (HY000): @@SESSION.GTID_NEXT cannot be changed by a client that owns a GTID.
mysql> commit;Query OK, 0 rows affected (0.01 sec)
mysql> set @@session.gtid_next=automatic;                                  #设置回自动模式  Query OK, 0 rows affected (0.00 sec)
mysql> start slave;Query OK, 0 rows affected (0.02 sec)
```
2）gtid_purged（IO线程报错）：事务被purge之后再进行change的场景。

Master：
mysql> show master logs;
+-----------------------+-----------+
| Log_name              | File_size |
+-----------------------+-----------+
| mysql-bin-3306.000001 |       983 |
| mysql-bin-3306.000002 |       836 |
| mysql-bin-3306.000003 |       685 |
+-----------------------+-----------+3 rows in set (0.00 sec)
mysql> purge binary logs to 'mysql-bin-3306.000003';Query OK, 0 rows affected (0.09 sec)
mysql> show master logs;
+-----------------------+-----------+
| Log_name              | File_size |
+-----------------------+-----------+
| mysql-bin-3306.000003 |       685 |
+-----------------------+-----------+1 row in set (0.00 sec)
Slave：
mysql> CHANGE MASTER TO MASTER_HOST='10.0.3.141',MASTER_USER='repl',MASTER_PASSWORD='Repl_123456',MASTER_AUTO_POSITION=1;Query OK, 0 rows affected, 2 warnings (0.32 sec)
mysql> start slave;Query OK, 0 rows affected (0.01 sec)
mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State:
             Slave_IO_Running: No
            Slave_SQL_Running: Yes
                Last_IO_Errno: 1236
                Last_IO_Error: Got fatal error 1236 from master when reading data from binary log: 'The slave is connecting using CHANGE MASTER TO MASTER_AUTO_POSITION = 1, but the master has purged binary logs containing GTIDs that the slave requires.'

因为是IO线程报错，通过监控表等上面说的方法看不到GTID信息，但错误信息提示的意思是主使用了purge binary log，导致复制失败。因为通过GTID的复制都是没有指定MASTER_LOG_FILE和MASTER_LOG_POS的，所以通过GTID复制都是从最先开始的事务开始，而最开始的binlog被purge了，导致报错。解决办法：

#在主上执行，查看被purge的GTID：
mysql> show variables like 'gtid_purged';
+---------------+------------------------------------------+
| Variable_name | Value                                    |
+---------------+------------------------------------------+
| gtid_purged   | 7b389a77-4423-11e6-8e6b-00163ec0a235:1-5 |
+---------------+------------------------------------------+

#在从上执行：
mysql> stop slave;Query OK, 0 rows affected (0.00 sec)
mysql> set global gtid_purged='7b389a77-4423-11e6-8e6b-00163ec0a235:1-5';  #设置和主一样的purgeQuery OK, 0 rows affected (0.01 sec)
mysql> start slave;Query OK, 0 rows affected (0.01 sec)
mysql> show slave status\G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes

关于gtid_purge还有一个场景，就是新建（还原）一个从库（把主库备份还原到新的从库）：

1：主库执行备份：root@t22:~# mysqldump -uroot -p123 --default-character-set=utf8 --master-data=2 --set-gtid-purged=ON  dba_test  > dba_test.sql
2：检查目标实例（新从库）是否有GTID的脏数据：mysql> select * from mysql.gtid_executed;  ##是否有数据mysql> show variables like 'gtid_purged';  ##是否有值
3：如果上面的查询都是空的，表示该实例Gtid还没被使用，可以直接还原。若上面的查询结果是有数据库的，则需要在该实例上执行：mysql> reset master;   #执行到上面的查询不到结果，再接下去执行。若是多源复制，需要先执行stop slave，再reset master
4：还原数据：root@t23:~# mysql -uroot -p123 --default-character-set=utf8 dba_test <dba_test.sql 要是第2步查出来GTID是有脏数据的话，还原会报错：ERROR 1840 (HY000) at line 24: @@GLOBAL.GTID_PURGED can only be set when @@GLOBAL.GTID_EXECUTED is empty.
5：change同步：CHANGE MASTER TO MASTER_HOST='10.0.3.141',MASTER_USER='repl',MASTER_PASSWORD='Repl_123456',MASTER_AUTO_POSITION=1;

3）Gtid和多源复制应用测试
上面已经介绍了基于binlog和position的老版复制，现在在这个基础上加上GTID，看看会有什么问题。 

CHANGE MASTER TO MASTER_HOST='10.0.3.141',MASTER_USER='repl',MASTER_PASSWORD='Repl_123456',MASTER_AUTO_POSITION=1 FOR CHANNEL 't22';
CHANGE MASTER TO MASTER_HOST='10.0.3.162',MASTER_USER='repl',MASTER_PASSWORD='Repl_123456',MASTER_AUTO_POSITION=1 FOR CHANNEL 't21';
CHANGE MASTER TO MASTER_HOST='10.0.3.219',MASTER_USER='repl',MASTER_PASSWORD='Repl_123456',MASTER_AUTO_POSITION=1 FOR CHANNEL 't23';
因为channel t10是MySQL5.5版本，不支持GTID功能，而从库开启了GTID的功能，所以在开启GTID的情况下，MySQL5.5到MySQL5.7的复制是建立不起来的

补充：主从要么都开启GTID，要么都关闭GTID，开启任意一个会报错，复制不成功。
基于GTID的多源复制如何跳过某个channel的错误？因为每个MySQL实例的GTID都不一样，所以直接用gtid_next来跳过具体错误的gtid就行了，不需要纠结到底是哪个channel了。具体跳过错误的步骤和上面错误跳过和异常处理里的方法一致。下面是多源复制的接收执行的信息：（1从3主，要是从库开启了binlog，则在executed_gtid_set里会有4行）
           Retrieved_Gtid_Set: 3b8ec9cb-4424-11e6-9780-00163e7a3d5a:1-11            Executed_Gtid_Set: 3b8ec9cb-4424-11e6-9780-00163e7a3d5a:1-11,7a9582ef-382e-11e6-8136-00163edc69ec:1-4,7b389a77-4423-11e6-8e6b-00163ec0a235:1-6
注意：因为是多源复制，所以从上的mysql.gtid_executed和gtid_purged看到有多行信息：

 View Code
所以再新建（还原）一个channel的从库（mysqldump下来），需要保证上面2个变量没有数据（保证GTID信息干净），也需要执行
mysql> reset master;
但是由于其他channel的从库一直有数据写入，会导致mysql.gtid_executed和gtid_purged一直有数据。所以需要停止所有从库同步再清理gtid：

mysql> stop slave;         #停止所有库的同步，防止GTID变量数据有数据。Query OK, 0 rows affected (0.05 sec)
mysql> reset master;       #清理gtid信息Query OK, 0 rows affected (0.00 sec)
mysql> show variables like '%gtid_purged%'\G
*************************** 1. row ***************************
Variable_name: gtid_purged        Value: 1 row in set (0.00 sec)
mysql> select * from mysql.gtid_executed;Empty set (0.00 sec)

最后还原，建立一个新的channel从库：
root@t24:~# mysql -uroot -p123 t23 < t23.sql 
mysql> CHANGE MASTER TO MASTER_HOST='10.0.3.219',MASTER_USER='repl',MASTER_PASSWORD='Repl_123456',MASTER_AUTO_POSITION=1 FOR CHANNEL 't23';
注意：主从复制的实例版本最好是同一个大版本，如：主从都是5.7。若主是5.6，从是5.7的话，可能会出现意想不到的bug。因为老版本（5.6）对于有些event没有记录并行复制的信息，对于开启并行复制的从（5.7）会报错：
             Slave_IO_Running: Yes            Slave_SQL_Running: No               Last_SQL_Errno: 1755               Last_SQL_Error: Cannot execute the current event group in the parallel mode. Encountered event Gtid, relay-log name ./mysqld-relay-bin-demo_clinic.000004, position 3204513 which prevents execution of this event group in parallel mode. Reason: The master event is logically timestamped incorrectly..
上面这个解决办法就是（bug页面也提到了）让slave设置不并行复制：
stop slave;   #关闭set global slave_parallel_workers =0;  #设置不并行start slave;  #开启
要是多源从库的话，则需要：
mysql> stop slave for channel 'xx';   #先关闭出错的channel的复制
mysql> set global slave_parallel_workers=0;  #设置成单线程复制，只对stop slave之后设置的channel有效，因为没有stop的channel线程一直在连接（不受影响）Query OK, 0 rows affected (0.00 sec)
mysql> start slave for channel 'xx';    #开启复制
在下面图标记出来的地方看出：其中的一个channel从库是单线程复制，其他channel都是多线程复制。

当然这报错也可以直接用上面介绍的gtid_next跳过和重新change master来解决，但这只是治标不治本的做法。
...
5，半同步复制增强
MySQL默认的复制都是异步的，在服务器崩溃时丢失事务是使用异步复制不可避免的结果。而5.5之后推出的一项新功能：半同步复制，可以限制事务丢失的数量。关于MySQL5.5/5.6的半同步复制可以看初识 MySQL 5.5、5.6 半同步复制，现在说明下MySQL5.7在5.6/5.5的基础上增强了几点功能：
1）无数据丢失
MySQL5.6/5.5半同步复制的原理：提交事务的线程会被锁定，直到至少一个Slave收到这个事务，由于事务在被提交到存储引擎之后才被发送到Slave上，所以事务的丢失数量可以下降到最多每线程一个。因为事务是在被提交之后才发送给Slave的，当Slave没有接收成功，并且Master挂了，会导致主从不一致：主有数据，从没有数据。如下面的情况：（AFTER_COMMIT）

客户端执行一个事务，master接收到之后提交后并把事务发送给slave，在发送的期间网络出现波动，但要等待slave把binlog写到本地的relay-log，然后给master一个返回信息，等待以rpl_semi_sync_master_timeout参数设置的超时为准（默认为10秒）响应。在这等待的10秒里，其他会话查可以看到Master上的事务，此时一旦master发生宕机，由于事务没有发送给slave，而master已经提交了，导致数据不一致。例子：A客户端执行的事务将字段Z从0修改为1。1.A提交事务到master2.master写binlog3.master commit事务到存储引擎，再把事务发送给slave4.master commit成功了！说明：此时还未收到slave确认，A还在等待slave的响应，但是另外客户端B已经可以看到字段Z为1了。假如此时master崩溃，如果slave实际收到刚才的事务仅仅是master未收到确认，那么此时slave的数据还是正确的也是Z=1，客户端切换到slave后，都看到Z=1，但是如果slave没有实际收到刚才的事务，那么此时slave上的z=0，导致主从数据不一直。

MySQL5.7在Master事务提交的时间方面做了改进（rpl_semi_sync_master_wait_point：AFTER_COMMIT\AFTER_SYNC），事务是在提交之前发送给Slave（默认，after_sync），当Slave没有接收成功，并且Master宕机了，不会导致主从不一致，因为此时主还没有提交，所以主从都没有数据。MySQL5.7也支持和MySQL5.5\5.6一样的机制：事务提交之后再发给Slave（after_commit）。如下面的情况：(AFTER_SYNC)

客户端执行一个事务，master接收到之后就把事务发送给slave，slave收到事务之后，然后给master一个返回信息，master再提交事务。在slave返回信息的时间里（以rpl_semi_sync_master_timeout参数为准，默认为10秒），其他会话查看不到Master上的最新事务，因为master都还没提交事务，此时一旦master发生宕机，由于事务没有发送给slave，并且master也没有提交数据，主从数据都没有更改，所以不会出现数据不一致。例子：A客户端执行的事务讲字段Z从0修改为1。
1.A提交事务到master2.master写binlog
3.master发送事务给slave，不提交！4.master等待slave确认此时z=0，没有任何客户端能看到z=1的结果，因为master还没提交。5.master收到slave确认，master开始commit到存储引擎6.master commit成功了！master返回结果给客户端说明：假如第4步时master崩溃，客户端切换到slave，如果slave接收到事务，并响应master，那么此时主从的z=1，如果slave未接收到事务和响应，那么此时z=0，无论哪种状态，对于所有客户端数据库都是一致，事务都没有丢失。

参数rpl_semi_sync_master_wait_point：该参数控制半同步复制在哪个点（提交后再等待响应还是响应后再提交）等待slave的响应，默认AFTER_SYNC（slave响应后再提交），可选值有AFTER_COMMIT（提交后再等待响应）。
after_commit：master把每一个事务写到二进制日志并保存到磁盘上，并且提交（commit）事务，再把事务发送给从库，开始等待slave的应答。响应后master返回结果给客户端，客户端才可继续。
after_sync  ：master把每一个事务写到二进制日志并保存磁盘上，并且把事务发送给从库，开始等待slave的应答。确认slave响应后，再提交（commit）事务到存储引擎，并返回结果给客户端，客户端才可继续。
总之，MySQL5.7是在Master收到Slave应答之后才Commit事务，MySQL5.6/5.5是在Master Commit之后才等待Slave的应答。MySQL5.7半同步的好处就是在确认事务复制到Slave之前，并发的其他线程看不到当前事务的数据。当Master故障时，要么提交的事务已经复制到Slave，要么全部都没提交，这样就保证了数据的一致性，推荐阅读MySQL 5.7 深度解析: 半同步复制技术。 
2）更快的半同步复制。
MySQL5.5/5.6的半同步复制是一个单工通讯方式，master把事务发送完毕后，要接收和处理slave的应答，处理完应答之后才能继续发送下一个事务，示意图如下：

MySQL5.7的半同步复制创建了单独的应答接收线程，变成了双工模式，发送和接收互不影响。因为有了相应的线程处理，发送效率得到大幅提升，相比MySQL5.5/5.6延迟会小很多，性能得到大幅提升。示意图如下：

注意：MySQL5.7单独的应答接收线程在开启半同步复制的时候默认就创建了，不需要额外的设置。
3）等待多个Slave应答。
在半同步复制中，Master发送事务默认至少有一个Slave得到响应才能继续下一个事务。MySQL5.7之后用户可以设置应答的Slave数量，并且可以通过参数rpl_semi_sync_master_wait_for_slave_count：该变量控制slave应答的数量，默认是1，表示master接收到几个slave应答后才commit。在多从的环境下，设置大于1可以提高数据的可靠性。
4）半同步复制的建立、监控。
 如何建立半同步复制：可以看官方文档或则之前写的初识 MySQL 5.5、5.6 半同步复制

主上执行：mysql> INSTALL PLUGIN rpl_semi_sync_master SONAME 'semisync_master.so';Query OK, 0 rows affected (0.07 sec)
mysql> SET GLOBAL rpl_semi_sync_master_enabled=1;Query OK, 0 rows affected (0.00 sec)为了保证重启后继续生效，需要在配置文件里加入：rpl_semi_sync_master_enabled = 1
从上执行：mysql> INSTALL PLUGIN rpl_semi_sync_slave SONAME 'semisync_slave.so';Query OK, 0 rows affected (0.04 sec)
mysql> SET GLOBAL rpl_semi_sync_slave_enabled = 1;Query OK, 0 rows affected (0.00 sec)为了保证重启后继续生效，需要在配置文件里加入：rpl_semi_sync_slave_enabled = 1
开启复制：设置好半同步复制的插件和开启半同步功能之后，复制模式就默认用半同步了mysql> CHANGE MASTER TO MASTER_HOST='10.0.3.141',MASTER_USER='repl',MASTER_PASSWORD='Repl_123456',MASTER_LOG_FILE='mysql-bin-3306.000001',MASTER_LOG_POS=154;Query OK, 0 rows affected, 2 warnings (0.30 sec)
mysql> start slave;Query OK, 0 rows affected (0.01 sec)
开启成功后，slave的error log里会出现：半同步复制是跟 IO_THREAD 有直接关系，跟 SQL_THREAD 没关系。也就是说SLAVE 从库接收完二进制日志后给 master 主库一个确认，并不管relay-log中继日志是否正确执行完。即使SQL线程报错了，半同步复制还是不会切换成异步复制[Note] Slave I/O thread: Start semi-sync replication to master 'repl@10.0.3.141:3306' in log 'mysql-bin-3306.000001' at position 154

如何监控半同步复制：可以看官方文档或则之前写的初识 MySQL 5.5、5.6 半同步复制

主上：
mysql> show variables like 'rpl_semi%';
+-------------------------------------------+------------+
| Variable_name                             | Value      |
+-------------------------------------------+------------+
| rpl_semi_sync_master_enabled              | ON         |
| rpl_semi_sync_master_timeout              | 10000      |
| rpl_semi_sync_master_trace_level          | 32         |
| rpl_semi_sync_master_wait_for_slave_count | 1          |
| rpl_semi_sync_master_wait_no_slave        | ON         |
| rpl_semi_sync_master_wait_point           | AFTER_SYNC |
+-------------------------------------------+------------+ 6 rows in set (0.00 sec)
mysql> show global status like 'rpl_semi%';
+--------------------------------------------+-------+
| Variable_name                              | Value |
+--------------------------------------------+-------+
| Rpl_semi_sync_master_clients               | 1     |
| Rpl_semi_sync_master_net_avg_wait_time     | 0     |
| Rpl_semi_sync_master_net_wait_time         | 0     |
| Rpl_semi_sync_master_net_waits             | 0     |
| Rpl_semi_sync_master_no_times              | 0     |
| Rpl_semi_sync_master_no_tx                 | 0     |
| Rpl_semi_sync_master_status                | ON    |
| Rpl_semi_sync_master_timefunc_failures     | 0     |
| Rpl_semi_sync_master_tx_avg_wait_time      | 0     |
| Rpl_semi_sync_master_tx_wait_time          | 0     |
| Rpl_semi_sync_master_tx_waits              | 0     |
| Rpl_semi_sync_master_wait_pos_backtraverse | 0     |
| Rpl_semi_sync_master_wait_sessions         | 0     |
| Rpl_semi_sync_master_yes_tx                | 0     |
+--------------------------------------------+-------+14 rows in set (0.00 sec)
从上：
mysql> show variables like 'rpl_semi%';
+---------------------------------+-------+
| Variable_name                   | Value |
+---------------------------------+-------+
| rpl_semi_sync_slave_enabled     | ON    |
| rpl_semi_sync_slave_trace_level | 32    |
+---------------------------------+-------+
2 rows in set (0.00 sec)
mysql> show global status like 'rpl_semi%';
+----------------------------+-------+
| Variable_name              | Value |
+----------------------------+-------+
| Rpl_semi_sync_slave_status | ON    |
+----------------------------+-------+
1 row in set (0.00 sec)

半同步成功开启之后，在主上show processlist可以看到：
Waiting for semi-sync ACK from slave;
针对上面的参数和变量说明：

主上：
rpl_semi_sync_master_enabled：表示主上是否开启半同步复制功能，可以动态修改。可选值：ON\OFF
rpl_semi_sync_master_timeout：为了防止半同步复制中主在没有收到S发出的确认发生堵塞，用来设置超时，超过这个时间值没有收到信息，则切换到异步复制，执行操作。默认为10000毫秒，等于10秒，这个参数动态可调，表示主库在某次事务中，如果等待时间超过10秒，那么则降级为异步复制模式，不再等待SLAVE从库。如果主库再次探测到，SLAVE从库恢复了，则会自动再次回到半同步复制模式。可以设置成1000，即1秒。
rpl_semi_sync_master_wait_for_slave_count：控制slave应答的数量，默认是1，表示master接收到几个slave应答后才commit。
rpl_semi_sync_master_wait_no_slave ：当一个事务被提交，但是Master没有Slave连接，这时M不可能收到任何确认信息，但M会在时间限制范围内继续等待。如果没有Slave链接，会切换到异步复制。是否允许master每个事务提交后都要等待slave的接收确认信号。默认为on，每一个事务都会等待。如果为off，则slave追赶上后，也不会开启半同步复制模式，需要手工开启。
rpl_semi_sync_master_wait_point：该参数表示半同步复制的主在哪个点等待从的响应，默认AFTER_SYNC，在得到slave的应答后再commit，可选值AFTER_COMMIT。
从上：
rpl_semi_sync_slave_enabled：表示从上是否开启半同步复制功能，可以动态修改。可选值：ON\OFF
Rpl_semi_sync_master_clients ：说明支持和注册半同步复制的已连Slave数。
Rpl_semi_sync_master_net_avg_wait_time ：master等待slave回复的平均等待时间，单位毫秒。
Rpl_semi_sync_master_net_wait_time ：master总的等待时间。
Rpl_semi_sync_master_net_waits ：master等待slave回复的的总的等待次数，即半同步复制的总次数，不管失败还是成功，不算半同步失败后的异步复制。
Rpl_semi_sync_master_no_times ：master关闭半同步复制的次数。 
Rpl_semi_sync_master_no_tx ：master没有收到slave的回复而提交的次数，可以理解为master等待超时的次数，即半同步模式不成功提交数量。
Rpl_semi_sync_master_status ：ON是活动状态（半同步），OFF是非活动状态（异步），用于表示主服务器使用的是异步复制模式，还是半同步复制模式。
Rpl_semi_sync_slave_status ：Slave上的半同步复制状态，ON表示已经被启用，OFF表示非活动状态。
Rpl_semi_sync_master_tx_avg_wait_time ：master花在每个事务上的平均等待时间。 
Rpl_semi_sync_master_tx_wait_time ：master总的等待时间。
Rpl_semi_sync_master_tx_waits ：master等待成功的次数，即master没有等待超时的次数，也就是成功提交的次数
Rpl_semi_sync_master_wait_pos_backtraverse ：master提交后来的先到了，而先来的还没有到的次数。
Rpl_semi_sync_master_wait_sessions ：前有多少个session因为slave的回复而造成等待。
Rpl_semi_sync_master_yes_tx ：master成功接收到slave的回复的次数，即半同步模式成功提交数量。  

总之，关于半同步复制的测试说明可以看初识 MySQL 5.5、5.6 半同步复制这篇文章。半同步复制的好处：半同步复制可以有效的限制事务丢失的数量，更好的保证数据的安全和一致性；半同步复制的坏处：更新、插入、删除的速度要比异步复制要慢，因为多了一个"从返回信息给主"的步骤。要是出现异常：网络问题或则数据库问题，半同步复制和异步复制就会来回切换，导致主库的更新、插入、删除操作会受到影响。
...
总结：
本文从MySQL5.7的多源复制、基于组提交的并行复制、在线修改Replication Filter、GTID增强、半同步复制增强等比较常用的replication的特性进行了简单介绍和说明，后面会持续更新有遗漏的部分。可以看到MySQL5.7比之前的版本在复制上有了很大的提升，增加了不少功能。到现在为止，大致对MySQL5.7的相关特性方面进行了介绍：新增配置参数、安全相关特性、功能性能的提升，后面还会再介绍说明下MySQL5.7新增的sys库和innodb的N-gram分词。
