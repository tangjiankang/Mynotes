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
