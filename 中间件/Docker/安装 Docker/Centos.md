### 参考文档
*   [Docker 官方 CentOS 安装文档](https://docs.docker.com/install/linux/docker-ce/centos/)。
## Get Docker CE for CentOS
### OS requirements
To install Docker CE, you need a maintained version of **CentOS 7**. Archived versions aren’t supported or tested.
The`centos-extras`repository must be enabled. This repository is enabled by default, but if you have disabled it, you need to[re-enable it](https://wiki.centos.org/AdditionalResources/Repositories).
The`overlay2`storage driver is recommended.
### Uninstall old versions
Older versions of Docker were called`docker`or`docker-engine`. If these are installed, uninstall them, along with associated dependencies.
~~~
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
~~~
### Install using the repository

Before you install Docker CE for the first time on a new host machine, you need to set up the Docker repository. Afterward, you can install and update Docker from the repository.

#### SET UP THE REPOSITORY

1.  Install required packages.`yum-utils`provides the`yum-config-manager`utility, and`device-mapper-persistent-data`and`lvm2`are required by the`devicemapper`storage driver.
    
    ~~~
    sudo yum install -y yum-utils \
    device-mapper-persistent-data \
    lvm2
    
    ~~~
    
2.  Use the following command to set up the **stable** repository.
    
    ~~~
    #国内源
    sudo yum-config-manager \
        --add-repo \
        https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo
    # 官方源
    sudo yum-config-manager \
        --add-repo \
        https://download.docker.com/linux/centos/docker-ce.repo
    ~~~
#### INSTALL DOCKER CE
~~~
sudo yum makecache fast
sudo yum install docker-ce
~~~

1.  Install the*latest version*of Docker CE and containerd, or go to the next step to install a specific version:
    ~~~
    sudo yum install docker-ce docker-ce-cli containerd.io
    ~~~
To install a*specific version*of Docker CE, list the available versions in the repo, then select and install:
~~~
yum list docker-ce --showduplicates | sort -r
~~~