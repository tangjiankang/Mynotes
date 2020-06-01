# 常用命令

```text
mysqldump -uroot --complete-insert --no-create-info --set-gtid-purged=OFF -p $db >$db.sql
dump出来的sql,里面带有删除表和新建表
。也就是说再导入的时候会删除相关表再建立新表新数据

mysqldump -uleanewg -hrds86s534js41f6ih716.mysql.rds.aliyuncs.com -p trader_ewg ms_real_user --where="email='927059130@qq.com'" >ewg20170424.sql

SELECT * FROM t_message WHERE  to_user = '544034637@qq.com';

mysqldump 备份导出数据排除某张表
mysqldump -uusername -ppassword -h192.168.0.1 dbname --ignore-table=dbname.dbtanles > dump.sql
```

mysqldump没有排除某个库的功能

## 授权

```text
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, SHUTDOWN, PROCESS, FILE, REFERENCES, INDEX, ALTER, SHOW DATABASES, SUPER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'broker_user'@'%' identified by '9vQSZ1KhD2Hc7Ebv3108oQ==';
```

