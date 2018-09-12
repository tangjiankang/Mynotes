### gtid主从报错When@@SESSION.GTID_NEXT is set to a GTID
### 故障现象gtid主从报错信息 
When@@SESSION.GTID_NEXT is set to a GTID, you must explicitly set it to a differentvalue after a COMMIT or ROLLBACK. Please check GTID_NEXT variable manual pagefor detailed explanation. Current @@SESSION.GTID_NEXT is'039a0c0c-9cee-11e6-922c-525400a9bf0e:324065731'.<br> 
故障可能原因:<br>
1. enforce_gtid_consistency为OFF，主库执行create table as select * from xx;
2. myisam引擎表，主库执行insert delayed into xx values...
3. 主从引擎不一致，主库innodb引擎一个事务中写入两条数据，传到从库的myisam引擎执行这个事务 
原因分析:<br>
根本原因是每一个GTID需要与一个唯一的事务对应<br>
针对原因1，create table as select * from xx会自动转换成row模式，这时会拆成crate table和insert两个事务，这时传到slave时，slave执行完crate以后，多出一个insert事务没有gtid，于是报错；<br>
针对原因2，只有myisam引擎支持insert delayed语法，insert delay是异步写入，也就是一旦执行立即返回给客户端成功。mysql内部处理insert delay时，会将多个线程的insert合并后一起执行，但是只生成了一个GTID；于是传到slave后，由于是myisam表，从库的同样只能执行第一条SQL，于是报错<br>
针对原因3，主库innodb执行一个事务，只产生一个gtid，myisam不支持事务，事务的第一条执行完以后，第二个sql就没有gtid，于是报错.<br>
### 使用阿里云做主库，阿里云策略空间超过70就会自动清理binlog，恰好从库断掉的话，主从同步就失败了。
Got fatal error 1236 from master when reading data from binary log: 'The slave is connecting using CHANGE MASTER TO MASTER_AUTO_POSITION = 1, but the master has purged binary logs containing GTIDs that the slave requires.'

### 主库宕机，数据丢失，产生2个断点。重新初始化mysql 产生一个断点。主库导出数据，初始化从库，重新开始主从同步，用命令查出gtid_purged有2个。 
跳过这2个断点，开始同步，但是不成功。<br>
后来注意到主库导出的数据中git_purged有个单独的值.<br>
因此跳过三个断点，主从同步成功。<br>
(5.7初始化mysql mysqld --initialize --user=mysql --basedir=/mnt/mysql/ --datadir=/mnt/mysql/data/)<br>
处理过程：<br>
在主库上执行以下命令，查询gtid_purged，记录下改值:<br>
```
mysql> show global variables like '%gtid%'\G 
1使用mysqldump 主库所有库 
2从库 stop slave; reset master;reset slave; 
3将主库的备份sql导入到从库 (gtid=ON,默认开启) 
4SET @@GLOBAL.GTID_PURGED='6d438886-d2ea-11e6-a79a-00163e00887c:1-93797856'; 
5CHANGE MASTER TO MASTER_HOST='10.26.60.21',MASTER_USER='repl',MASTER_PASSWORD='nH@H29.kEA7',MASTER_PORT=3306,MASTER_AUTO_POSITION = 1; 
6start slave; 
7show slave status \G;
```
