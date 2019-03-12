## 在CentOS 6和CentOS 7上安裝PHP 5.4、5.5、5.6或7.0、7.1版本

### For CentOS 7 (including EPEL install)有问题
```
wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
wget http:``//rpms``.famillecollet.com``/enterprise/remi-release-7``.rpm
rpm -Uvh remi-release-7*.rpm epel-release-7*.rpm
```
If you already have EPEL installed:
```
wget http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
rpm -Uvh remi-release-7*.rpm
```
### For CentOS 6 (including EPEL install)
```
wget http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm
wget http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
rpm -Uvh remi-release-6*.rpm epel-release-6*.rpm`
```
If you already have EPEL installed:
```
wget http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
rpm -Uvh remi-release-6*.rpm
```
centos6中，yum list php* > 1.txt
可以看到php54,55,56都在，选择需要的安装
```
yum -y install php54-php.x86_64 php54-php-mbstring.x86_64 php54-php-mysqlnd.x86_64
```
centos7.6下php7.2安装
```
1.  sudo yum -y install epel-release
sudo yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
sudo yum install yum-utils
sudo yum-config-manager --enable remi-php72  
sudo yum update
安装php7.2
sudo yum install php72
### 安装 php-fpm 和一些其他模块
sudo yum install php72-php-fpm php72-php-gd php72-php-json php72-php-mbstring php72-php-mysqlnd php72-php-xml php72-php-xmlrpc php72-php-opcache
```
~~~
sudo systemctl enable php72-php-fpm.service
~~~
php72 -v
php --version