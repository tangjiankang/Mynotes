# 连接数，最大并发数

show variables like 'max\_connections' 最大连接数 show status like 'max\_used\_connections'响应的连接数。如下： mysql&gt; show variables like ‘max\_connections‘; +-----------------------+-------+ \| Variable\_name \| Value \| +-----------------------+-------+ \| max\_connections \| 256 \| +-----------------------+-------+ mysql&gt; show status like ‘max\_used\_connections‘; +-----------------------+-------+ \| Variable\_name \| Value \| +----------------------------+-------+ \| max\_used\_connections \| 256\| +----------------------------+-------+ max\_used\_connections / max\_connections \* 100%（理想值≈ 85%） 如果max\_used\_connections跟max\_connections相同，那么就是max\_connections设置过低或者超过服务器负载上限了，低于10%则设置过大。

