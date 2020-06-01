# 常用命令实列

`find . -name “*.tar” -exec mv {} ./backup/ ;` 注解：find –name 主要用于查找某个文件名字，-exec 、xargs可以用来承接前面的结果，然后将要执行的动作，一般跟find在一起用的很多，find使用我们可以延伸-mtime查找修改时间、-type是指定对象类型（常见包括f代表文件、d代表目录），-size 指定大小，例如经常用到的：查找当前目录30天以前大于100M的LOG文件并删除。 `find . -name "*.log" –mtime +30 –type f –size +100M |xargs rm –rf {} ;`

**3、sed常用命收集：test.txt做测试**

如何去掉行首的.字符: sed -i 's/^.//g' test.txt 在行首添加一个a字符: sed 's/^/a/g' test.txt 在行尾添加一个a字符: sed 's/$/a/' tets.txt 在特定行后添加一个c字符: sed '/wuguangke/ac' test.txt 在行前加入一个c字符: sed '/wuguangke/ic' test.txt 更多sed命令请查阅相关文档。

**4、如何判断某个目录是否存在，不存在则新建，存在则打印信息** if \[ ! –d /data/backup/ \];then Mkdir –p /data/backup/ else echo "The Directory already exists,please exit" fi 注解：if …;then …else ..fi：为if条件语句,!叹号表示反义“不存在“，-d代表目录。

**5、监控linux磁盘根分区，如果根分区空间大于等于90%，发送邮件给Linux SA** \(1\)、打印根分区大小 `df -h |sed -n '//$/p'|awk '{print $5}'|awk –F ”%” '{print $1}'` 注解：awk ‘{print $5}’意思是打印第5个域，-F的意思为分隔，例如以%分隔，简单意思就是去掉百分号，awk –F. ‘{print $1}’分隔点.号。 \(2\)、if条件判断该大小是否大于90，如果大于90则发送邮件报警 while sleep 5m do for i in `df -h |sed -n '//$/p' |awk '{print $5}' |sed 's/%//g'` do echo $i if \[ $i -ge 90 \];then echo “More than 90% Linux of disk space ,Please Linux SA Check Linux Disk !” \|mail -s “Warn Linux / Parts is $i%” wugk@map.com fi done done 注解：使用while ..;do ...;done为while循环，for、while、until、if等循环和条件语句可以参考以下文章：[http://www.cnblogs.com/chengmo/archive/2010/10/14/1851434.html](http://www.cnblogs.com/chengmo/archive/2010/10/14/1851434.html)

**6、统计Nginx访问日志，访问量排在前20 的 ip地址** `cat access.log |awk '{print $1}'|sort|uniq -c |sort -nr |head -20` 注解：sort排序、uniq（ 检查及删除文本文件中重复出现的行列 ）

**7、sed另外一个用法找到当前行，然后在修改该行后面的参数** `sed -i '/SELINUX/s/enforcing/disabled/' /etc/selinux/config` Sed冒号方式 sed -i ‘s:/tmp:/tmp/abc/:g’test.txt意思是将/tmp改成/tmp/abc/。 11、打印出一个文件里面最大和最小值： `cat a.txt |sort -nr|awk ‘{}END{print} NR==1′` `cat a.txt |sort -nr |awk ‘END{print} NR==1′` 这个才是真正的打印最大最小值：`sed ‘s/ / /g’ a.txt |sort -nr|sed -n ’1p;$p’` 12、使用snmpd抓取版本为v2的cacti数据方式： `snmpwalk -v2c -c public 192.168.0.241` 13、修改文本中以jk结尾的替换成yz： `sed -e ‘s/jk$/yz/g’ b.txt` 14、网络抓包：tcpdump `tcpdump -nn host 192.168.56.7 and port 80` 抓取56.7通过80请求的数据包。 `tcpdump -nn host 192.168.56.7 or ! host 192.168.0.22 and port 80` 排除0.22 80端口！ tcp/ip 7层协议 物理层–数据链路层-网络层-传输层-会话层-表示层-应用层。 15、H3C配置团体名配置：首先设置snmp版本如下： snmp-agent sys-info version v1 v2c ，然后设置团体名：snmp-agent community read public 16、显示最常用的20条命令： `cat .bash_history |grep -v ^# |awk ‘{print $1}’ |sort |uniq -c |sort -nr |head -20`

引用LinuxTone.ORG社区经典shell 菜鸟练手下:

17、写一个脚本查找最后创建时间是3天前，后缀是_.log的文件并删除。 \`find . -mtime +3 -name "_.log" \|xargs rm -rf {} ; `18、写一个脚本将某目录下大于100k的文件移动至/tmp下。`find . -size +100k -exec mv {} /tmp ; \`

19、写一个防火墙配置脚本，只允许远程主机访问本机的80端口。 iptables -F iptables -X iptables -A INPUT -p tcp --dport 80 -j accept iptables -A INPUT -p tcp -j REJECT 或者 `iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT`

20、写一个脚本进行nginx日志统计，得到访问ip最多的前10个\(nginx日志路径：/home/logs/nginx/default/access.log\)。 cd /home/logs.nginx/default sort -m -k 4 -o access.logok access.1 access.2 access.3 ..... cat access.logok \|awk '{print $1}'\|sort -n\|uniq -c\|sort -nr \|head -10 22.替换文件中的目录 `sed 's:/user/local:/tmp:g' test.txt` 或者 `sed -i 's//usr/local//tmp/g' test.txt`

