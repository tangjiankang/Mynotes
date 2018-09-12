## Centos 7.x Mysql 5.7.x安装
```
echo "begin install mysqld"
echo "[mysql57-community]" >/etc/yum.repos.d/mysql-community.repo
echo "name=MySQL 5.7 Community Server" >>/etc/yum.repos.d/mysql-community.repo
echo "baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/7/x86_64/" >>/etc/yum.repos.d/mysql-community.repo
echo "enabled=1" >>/etc/yum.repos.d/mysql-community.repo
echo "gpgcheck=0" >>/etc/yum.repos.d/mysql-community.repo

#sudo yum update

sudo yum install -y mysql-community-server

systemctl enable mysqld.service
```
## Ubuntu 16.04 Mysql 5.7.x安装
```
wget http://dev.mysql.com/get/mysql-apt-config_0.8.2-1_all.deb
Next, install it using dpkg
sudo dpkg -i mysql-apt-config_0.8.2-1_all.deb
sudo apt-get update
sudo apt-get install mysql-server
/etc/mysql/mysql.conf.d/mysqld.cnf#是真实的配置文件路径
```
# 
```
sudo rm /var/lib/mysql/ -R # 删除数据库目录
sudo rm /etc/mysql/ -R #删除启动脚本、配置文件等
sudo apt-get autoremove mysql* --purge # 卸载mysql所有文件
sudo apt-get remove apparmor # 这个apparmor是在装mysql-server时装上的，和安全有关 
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P # 清理残余mysql文件
```
## Mysql5.7初始密码及改密码及重置密码
```
mysqld --initialize --user=mysql --basedir=/mnt/mysql/ --datadir=/mnt/mysql/data/ 5.7初始化
grep 'temporary password' /var/log/mysqld.log

ALTER USER 'root'@'localhost' IDENTIFIED BY 'Qb!bZ_~2buXr7';
ALTER USER 'root'@'%' IDENTIFIED BY 'Qb!bZ_~2buXr7';
update user set host='10.%' where user='root'
Qb#!b&Z$_~2)buXr5F
重置密码
update mysql.user set authentication_string=password('abc123') where user='root';
```
## Ubuntu 16.04下mysql5.7初始化
需要修改mysql的存储路径为/data/mysql
```
mysqld --initialize --user=mysql --datadir=/data/mysql
```
初始化不成功，建好了说已存在，没建好说没权限创建，明明权限都给mysql用户了

经查询
因为Ubuntu有个AppArmor，是一个Linux系统安全应用程序，类似于Selinux,AppArmor默认安全策略定义个别应用程序可以访问系统资源和各自的特权，如果不设置服务的执行程序，即使你改了属主属组并0777权限，也是对服务起不到作用。

ok，apt安装下MySQL默认数据目录是/var/lib/mysql，其它的目录权限都不可。开始修改：
```
# vim /etc/apparmor.d/usr.sbin.mysqld
找到：
# Allow data dir access
/var/lib/mysql/ r,
/var/lib/mysql/** rwk,
修改为：
# Allow data dir access
/data/mysql/ r,
/data/mysql/** rwk,
重启apparmor服务：
service apparmor restart
```
重新初始化，启动成功。

对于Mysql 5.7.6以后的5.7系列版本，Mysql使用mysqld --initialize或mysqld --initialize-insecure命令来初始化数据库，后者可以不生成随机密码。但是安装Mysql时默认使用的是前一个命令，这个命令也会生成一个随机密码。改密码保存在了Mysql的日志文件中。
```
root@ali-hk-bw-blue-qa-master-01:/data/mysql# cat /var/log/mysql/error.log | grep temporary
2018-09-03T02:05:18.023335Z 0 [Note] InnoDB: Creating shared tablespace for temporary tables
2018-09-03T02:05:19.778360Z 0 [Note] InnoDB: Removed temporary tablespace data file: "ibtmp1"
2018-09-03T02:05:21.539515Z 0 [Note] InnoDB: Creating shared tablespace for temporary tables
2018-09-03T02:13:37.560582Z 0 [Note] InnoDB: Removed temporary tablespace data file: "ibtmp1"
2018-09-03T02:13:37.926449Z 0 [Note] InnoDB: Creating shared tablespace for temporary tables
2018-09-03T02:15:37.918591Z 0 [Note] InnoDB: Removed temporary tablespace data file: "ibtmp1"
2018-09-03T02:59:56.812595Z 0 [Note] InnoDB: Creating shared tablespace for temporary tables
2018-09-03T03:00:01.771496Z 0 [Note] InnoDB: Removed temporary tablespace data file: "ibtmp1"
2018-09-03T03:00:07.033020Z 0 [Note] InnoDB: Creating shared tablespace for temporary tables
2018-09-03T03:02:00.065706Z 0 [Note] InnoDB: Removed temporary tablespace data file: "ibtmp1"
2018-09-03T03:08:56.821179Z 1 [Note] A temporary password is generated for root@localhost: kwplXI.jg6El
```
