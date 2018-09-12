#
```
mysqldump -uroot --complete-insert --no-create-info --set-gtid-purged=OFF -p $db >$db.sql
#直接dump出来的sql,里面带有删除表和新建表 
mysqldump -uleanewg -hrds86s534js41f6ih716.mysql.rds.aliyuncs.com -p trader_ewg ms_real_user --where="email='927059130@qq.com'" >ewg20170424.sql 
SELECT * FROM t_message WHERE to_user = '544034637@qq.com'; 
```
# Mysql 查看连接数,状态 最大并发数
```
show variables like 'max_connections' 最大连接数，show status like 'max_used_connections'响应的连接数。如下：
mysql> show variables like ‘max_connections‘;
+-----------------------+-------+
| Variable_name　       | Value |
+-----------------------+-------+
| max_connections       | 256　 |
+-----------------------+-------+
mysql> show status like 'max_used_connections';
+-----------------------+-------+
| Variable_name　       | Value |
+----------------------------+-------+
| max_used_connections       | 256|
+----------------------------+-------+
max_used_connections / max_connections * 100%（理想值≈ 85%），如果max_used_connections跟max_connections相同，那么就是max_connections设置过低或者超过服务器负载上限了，低于10%则设置过大。 
show processlist;
select count(1) from information_schema.processlist; 正在使用的连接数
```
