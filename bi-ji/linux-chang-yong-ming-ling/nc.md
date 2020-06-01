# nc

接收文件是服务器端。发送文件是客户端 先在服务器端`nc -v -l 12345 > demo_account.sql` \# 12345是端口 再在客户端 `nc 47.10.10.1 12345 < demo_account.sql`

nc -l 4444 \| tar xf - 接收文件夹 tar cf - nginx \| nc 192.168.60.227 4444 发送文件夹 192.168.60.227接收机的ip

