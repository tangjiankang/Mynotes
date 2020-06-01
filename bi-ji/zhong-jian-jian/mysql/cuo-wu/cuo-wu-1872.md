# 错误1872

Slave failed to initialize relay log info structure from the repository

解决方案：

复制代码 mysql&gt; start slave; ERROR 1872 \(HY000\): Slave failed to initialize relay log info structure from the repository mysql&gt; reset slave; Query OK, 0 rows affected \(0.04 sec\)

mysql&gt; start slave IO\_THREAD; Query OK, 0 rows affected \(0.07 sec\)

mysql&gt; stop slave IO\_THREAD; Query OK, 0 rows affected \(0.01 sec\)

mysql&gt; reset slave; Query OK, 0 rows affected \(0.10 sec\)

mysql&gt; start slave; Query OK, 0 rows affected \(0.20 sec\)

mysql&gt; show slave status\G

