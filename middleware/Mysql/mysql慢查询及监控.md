show variables like '%quer%';<br>
主要掌握以下的几个参数：<br>
1. slow_query_log的值为ON为开启慢查询日志，OFF则为关闭慢查询日志。
2. slow_query_log_file 的值是记录的慢查询日志到文件中（注意：默认名为主机名.log，慢查询日志是否写入指定文件中，需要指定慢查询的输出日志格式为文件，相关命令为：show variables like ‘%log_output%’；去查看输出的格式）。
3. long_query_time 指定了慢查询的阈值，即如果执行语句的时间超过该阈值则为慢查询语句，默认值为10秒。
4. log_queries_not_using_indexes 如果值设置为ON，则会记录所有没有利用索引的查询（注意：如果只是将log_queries_not_using_indexes设置为ON，而将slow_query_log设置为OFF，此时该设置也不会生效，即该设置生效的前提是slow_query_log的值设置为ON），一般在性能调优的时候会暂时开启。<br>
设置MySQL慢查询的输出日志格式为文件还是表，或者两者都有？<br>
通过命令：show variables like '%log_output%';<br>
慢查询写到文本中后，监控。<br>

