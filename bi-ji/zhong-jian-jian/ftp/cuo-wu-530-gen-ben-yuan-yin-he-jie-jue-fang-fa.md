# 错误530根本原因和解决方法

/etc/pam.d/vsftpd 默认如下

```text
#%PAM-1.0
session    optional     pam_keyinit.so    force revoke
auth       required     pam_listfile.so item=user sense=deny file=/etc/vsftpd/ftpusers onerr=succeed
auth       required     pam_shells.so
auth       include      password-auth
account    include      password-auth
session    required     pam_loginuid.so
session    include      password-auth
```

可能导致530错误的有  
`auth required pam_listfile.so item=user sense=deny file=/etc/vsftpd/ftpusers onerr=succeed`  
该配置项的含义是 /etc/ftpusers 中的用户禁止登陆，如果文件不存在在默认所有用户均允许登录. 所以确保用户没在这个文件内。 和  
`auth required pam_shells.so` 配置项的含义为仅允许用户的shell为 /etc/shells 文件内的shell命令时，才能够成功 vim /etc/shells

```text
/bin/sh
/bin/bash
/sbin/nologin
/usr/bin/sh
/usr/bin/bash
/usr/sbin/nologin
/bin/tcsh
/bin/csh
```

**而创建ftp用户时，为了禁止ssh登录，一般多为/bin/false 、/usr/sbin/nologin 等，显然不是一个有效的bash，也就无法登录了。** 而我的虚拟用户是 `lw_ftp:x:1002:1002::/home/lw_ftp:/bin/nologin`恰好不在内。 修改/etc/pam.d/vsftpd  
将`auth required pam_shells.so`修改为-&gt;`auth required pam_nologin.so`即可 重启vsftpd

