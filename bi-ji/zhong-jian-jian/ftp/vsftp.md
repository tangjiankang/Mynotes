# vsftp

[https://wiki.archlinux.org/index.php/Very\_Secure\_FTP\_Daemon\_\(简体中文](https://wiki.archlinux.org/index.php/Very_Secure_FTP_Daemon_%28简体中文)\)

## vsftpd服务访问模式有三种：**匿名用户模式**，**系统用户模式**和**虚拟用户模式**！

yum –y install vsftpd 默认ftp文件夹是/var/ftp/pub，上传需要写的权限 chmod o+w /var/ftp/pub/ service vsftpd start 上传.

#### **系统用户配置**

匿名模式可以让任何人使用ftp服务，比较公开！多适用于共享文件！如果我们想要特定用户使用，就需要使用系统用户登录访问！这种模式，需要我们新建不同用户。\(不常用\)

### **虚拟用户配置**

系统用户模式虽然可以控制访问，但是如果用户过多，就会影响服务器系统的管理，对服务器安全造成威胁！而且我们需要的仅仅是可以使用搭建在服务器的FTP服务而已！ 那么就需要我们设置虚拟用户进行登录，这也是推荐的方式！这种方式更加安全！

虚拟用户就是没有实际的真实系统用户，而是通过映射到其中一个真实用户以及设置相应的权限来实现访问验证，虚拟用户不能登录Linux系统，从而让系统更加的安全可靠。

### **下面有关linux中ftp的设置项：**

限制用户只能在自己的目录中 Chroot\_list\_enable=yes 文件中的名单可以调用 Chroot\_list\_file=/etc/vsftpd.chroot\_list 前提是chroot\_local\_user=no，在文件中加入用户名vsftpd.conf的参数 程序代码： Anonymous\_enable=yes 允许匿名登陆 Dirmessage\_enable=yes 切换目录时，显示目录下.message的内容 Local\_umask=022 FTP上本地的文件权限，默认是077 Connect\_form\_port\_20=yes 启用FTP数据端口的数据连接 Xferlog\_enable=yes 激活上传和下传的日志 Xferlog\_std\_format=yes 使用标准的日志格式 dual\_log\_enable=YES vsftpd\_log\_file=/var/log/vsftpd.log Ftpd\_banner=XXXXX 显示欢迎信息 Pam\_service\_name=vsftpd 验证方式 Listen=yes 独立的VSFTPD服务器 Anon\_upload\_enable=yes 匿名用户上传权限 Anon\_mkdir\_write\_enable=yes 创建目录的同时可以在此目录中上传文件 Write\_enable=yes 本地用户写的权限 Anon\_other\_write\_enable=yes 匿名帐号可以有删除的权限 Anon\_world\_readable\_only=no 匿名用户浏览权限 Ascii\_upload\_enable=yes 启用上传的ASCII传输方式 Ascii\_download\_enable=yes 启用下载的ASCII传输方式 Banner\_file=/var/vsftpd\_banner\_file 用户连接后欢迎信息使用的是此文件中的相关信息 Idle\_session\_timeout=600（秒） 用户会话空闲后10分钟 Data\_connection\_timeout=120（秒） 将数据连接空闲2分钟断 Accept\_timeout=60（秒） 将客户端空闲1分钟后断 Connect\_timeout=60（秒） 中断1分钟后又重新连接 Local\_max\_rate=50000（bite） 本地用户传输率50K Anon\_max\_rate=30000（bite） 匿名用户传输率30K Pasv\_min\_port=5000 将客户端的数据连接端口改在 Pasv\_max\_port=6000 5000—6000之间 Max\_clients=200 FTP的最大连接数 Max\_per\_ip=4 每IP的最大连接数 Listen\_port=5555 从5555端口进行数据连接 Local\_enble=yes 本地帐户能够登陆 Write\_enable=no 本地帐户登陆后无权删除和修改文件 Chroot\_local\_user=yes 本地所有帐户都只能在自家目录 Chroot\_list\_enable=yes 文件中的名单可以调用 Chroot\_list\_file=/etc/vsftpd.chroot\_list 前提是chroot\_local\_user=no Userlist\_enable=yes 在指定的文件中的用户不可以访问 Userlist\_deny=yes Userlist\_file=/etc/vsftpd.user\_list Banner\_fail=/路径/文件名 连接失败时显示文件中的内容 Ls\_recurse\_enable=no Async\_abor\_enable=yes one\_process\_model=yes Listen\_address=10.2.2.2 将虚拟服务绑定到某端口 Guest\_enable=yes 虚拟用户可以登陆

18、chroot\_local\_user、 chroot\_list\_enable、chroot\_list\_file 这个组合用于指示用户能否访问指定目录外的其他文件。 其中，chroot\_list\_file默认是/etc/vsftpd.chroot\_list，该文件定义一个用户列表。 若chroot\_local\_user 设置为NO，chroot\_list\_enable设置为NO，则所有用户都是可以访问指定目录外的其他文件。 若chroot\_local\_user 设置为YES，chroot\_list\_enable设置为NO，则锁定FTP登录用户只能在其默认目录活动，不允许访问指定目录外的其他文件。 若chroot\_local\_user 设置为YES，chroot\_list\_enable设置为YES，则chroot\_list\_file所指定的文件里面的用户列表都可以访问指定目录外的其他文件，而列表以外的用户则被限定在各自的默认目录活动。 若chroot\_local\_user设置为NO，chroot\_list\_enable设置为YES，则chroot\_list\_file所指定的文件里面的用户列表都被限定在各自的默认目录活动，而列表以外的用户则可以访问指定目录外的其他文件。 建议设置：chroot\_local\_user与chroot\_list\_enable都设置为YES。这样就只有chroot\_list\_file所指定的文件里面的用户列表可以访问指定目录外的其他文件，而列表以外的用户则被限定在各自的默认目录活动！ 好处：所有人都被限制在特定的目录里面。如果某些特定用户需要访问其他目录的权限，只需将其用户名写入chroot\_list\_file文件就可以赋予其访问其他目录的权限！

19、userlist\_file、userlist\_enable、userlist\_deny 这个组合用于指示用户可否访问FTP服务。 其中，userlist\_file默认是/etc/vsftpd.user\_list，该文件定义一个用户列表。 若userlist\_enable设置为YES，userlist\_deny设置为NO，则只有userlist\_file所指定的文件里面的用户列表里面的用户可以访问FTP。 若userlist\_enable设置为YES，userlist\_deny设置为YES，则userlist\_file所指定的文件里面的用户列表里面的用户都被拒绝访问FTP。 若userlist\_enable设置为NO，userlist\_deny设置为YES，则这个列表没有实际用处，起不到限制的作用！因为所有用户都可访问FTP。 建议设置：userlist\_enable与userlist\_deny都设置为YES。这样则userlist\_file所指定的文件里面的用户列表里面的用户都被拒绝访问FTP。 好处：只需将某用户帐号加入到userlist\_file所指定文件里面的用户列表，就可以起到暂时冻结该用户的功能！ 20、user\_config\_dir 指定一个目录用于存放针对每个用户各自的配置文件，比如用户kkk登录后，会以该用户名建立一个对应的配置文件。 比如指定user\_config\_dir=/etc/vsftpd\_user\_conf, 则kkk登录后会产生一个/etc/vsftpd\_user\_conf/kkk的文件，这个文件保存的配置都是针对kkk这个用户的。可以修改这个文件而 不用担心影响到其他用户的配置。

2.查看配置

vsftpd的配置，配置文件中限定了vsftpd用户连接控制配置。  
vsftpd.ftpusers：位于/etc目录下。它指定了哪些用户账户不能访问FTP服务器，例如root等。  
vsftpd.user\_list：位于/etc目录下。该文件里的用户账户在默认情况下也不能访问FTP服务器，仅当vsftpd .conf配置文件里启用userlist\_enable=NO选项时才允许访问。  
vsftpd.conf：位于/etc/vsftpd目录下。来自定义用户登录控制、用户权限控制、超时设置、服务器功能选项、服务器性能选项、服务器响应消息等FTP服务器的配置。

## vsftpd.conf

```text
anonymous_enable=NO
local_enable=YES
use_localtime=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES
xferlog_std_format=YES

connect_from_port_20=YES
listen=YES
listen_port=2121

connect_timeout=60
accept_timeout=60
data_connection_timeout=300
idle_session_timeout=300
max_clients=200
max_per_ip=50

pam_service_name=vsftpd
tcp_wrappers=YES
pasv_enable=YES
pasv_min_port=30000
pasv_max_port=31000

chroot_local_user=YES
chroot_list_enable=NO
#chroot_list_file=/etc/vsftpd/chroot_list

userlist_enable=YES
userlist_deny=NO
userlist_file=/etc/vsftpd/user_list #只允许这个文件里的用户登录

user_config_dir=/etc/vsftpd 
#给这个用户单独其用一个配置文件，配置他的指定活动目录local_root=/home/www_test_com_qa
```

