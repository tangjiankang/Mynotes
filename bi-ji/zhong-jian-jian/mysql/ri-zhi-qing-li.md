# 日志清理

## 日志清理

其他blog的解决办法

```text
    binlog就有40多G，原来根源出在这里，查看了一下my.cnf，看到binlog的size是1G就做分割，但没有看到删除的配置，在mysql里show了一下variables， 
    mysql> show variables like '%log%';
```

查到了 \| expire\_logs\_days \| 0 \| 这个默认是0，也就是logs不过期，这个是一个global的参数，所以需要执行

set global expire\_logs\_days=5; 这样2天前的log就会被删除了，如果有回复的需要，请做好备份工作，但这样设置还不行，下次重启mysql了，配置又恢复默认了，所以需在my.cnf中设置

expire\_logs\_days = 8 这样重启也不怕了，

PURGE MASTER LOGS BEFORE DATE\_SUB\(CURRENT\_DATE, INTERVAL 2 DAY\); //删除10天前的MySQL binlog日志,附录2有关于PURGE MASTER LOGS手动 PURGE MASTER LOGS TO 'master-binlog.002195';

ueidrh.broker.lwork.com crm.fxhuge.cn

## 错误日志不重启清理

shell&gt; mv host\_name.err host\_name.err-old shell&gt; mysqladmin -uroot -p flush-logs

Note For the server to recreate a given log file after you have renamed the file externally, the file location must be writable by the server. This may not always be the case. For example, on Linux, the server might write the error log as /var/log/mysqld.log, where /var/log is owned by root and not writable by mysqld. In this case, the log-flushing operation will fail to create a new log file.

To handle this situation, you must manually create the new log file with the proper ownershiop after renaming the original log file. For example, execute these commands as root:

shell&gt; mv /var/log/mysqld.log /var/log/mysqld.log.old shell&gt; install -omysql -gmysql -m0644 /dev/null /var/log/mysqld.log

