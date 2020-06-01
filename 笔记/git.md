## 公钥私钥免登

生成新的密钥：
~~~
ssh-keygen
~~~
生成了二个文件（未改名的时候）：id\_rsa 和 id\_rsa.pub
### 2.拷贝公钥到你的github
打开id\_rsa.pub（公钥） 复制全部内容到你github中，目录见下图
![](images/screenshot_1557131582436.png)
![](images/screenshot_1557131594755.png)
git clone git@gitlab.tools.test.com:ui/leanwork-dev.com.git
保证gitlab.tools.test.com22端口对密钥生成的服务器开放

在gitlab和jenkins中，

gitlab配置中，添加了test@qq.com这个邮箱用户的 id_rsa.pub公钥文件， 
jenkins配置中，因此添加test@qq.com这个邮箱用户的 id_rsa私钥文件，

这样就实现了服务器之间身份验证，于是就可以拉取代码。

gitlab和jenkins中，通过webhook通讯检测gitlab中是否有代码更新，如果有更新，就出发jenkins去拉取代码。
--------------------- 
# **docker 安装gitab-ce**
[https://docs.gitlab.com/omnibus/docker/](https://docs.gitlab.com/omnibus/docker/)
```
sudo docker run --detach \
  --hostname gitlab.example.com \
  --publish 8929:80 --publish 2289:22 \
  --name gitlab \
  --restart always \
  --volume /srv/gitlab/config:/etc/gitlab \
  --volume /srv/gitlab/logs:/var/log/gitlab \
  --volume /srv/gitlab/data:/var/opt/gitlab \
  docker-public.lwork.com:5000/gitlab-ce
You then need to appropriately configure`gitlab.rb`
1.  Set`external_url`:
     # For HTTP
     external_url "http://gitlab.example.com:8929"
    
     or
    
     # For HTTPS (notice the https)
     external_url "https://gitlab.example.com:8929"  
    For more information see the[NGINX documentation](https://docs.gitlab.com/omnibus/settings/nginx.html).
    
2.  Set`gitlab_shell_ssh_port`:
     gitlab_rails['gitlab_shell_ssh_port'] = 2289
```
重启生效，在gitlab.example.com上的复制的克隆地址会自动加上2289

