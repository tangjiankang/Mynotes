update的作用是从/etc/apt/source.list文件中定义的源中去同步包的索引文件，即运行这个命令其实并没有更新软件，而是相当于windows下面的检查更新，获取的是软件的状态。

而upgrade则是更据update命令同步好了的包的索引文件，去真正地更新软件。

需要注意的一点是，每回更新之前，我们需要先运行update，然后才能运行upgrade和dist-upgrade，因为相当于update命令获取了包的一些信息，比如大小和版本号，然后再来运行upgrade去下载包，如果没有获取包的信息，那么upgrade就是无效的啦！



## ubuntu 16.0.4 改名字
hostname 名字         临时
vim /etc/hostname

## centos 7 改名字
hostnamectl set-hostname 名字

## yum (rpm) 和 apt-get的对应关系

![](../images/screenshot_1552272816637.png)
查看dpkg的帮助。
选择 dpkg -l来查看软件的状态。
选择 dpkg -P来卸载软件。因为dpkg --remove只是删除安装的文件，但不删除配置文件。而dpkg --purge则安装文件和配置文件都删除。