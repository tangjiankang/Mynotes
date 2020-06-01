# 安装

**1.安装**

```text
https://redis.io/download
wget http://download.redis.io/releases/redis-3.2.3.tar.gz

tar zxvf redis-3.2.3.tar.gz 
cd redis-3.2.3
mkdir -p /usr/local/redis

make
make PREFIX=/usr/local/redis install -j8
mkdir -p /usr/local/redis/etc
mkdir -p /usr/local/redis/var/
mkdir -p /usr/local/redis/logs/
cp redis.conf /usr/local/redis/etc/
#utils下有个redis_init_script的文件
cp utils/redis_init_script /etc/init.d/
sudo chmod +x /etc/init.d/redis
sudo  sed -i s/"daemonize no"/"daemonize yes"/g /usr/local/redis/etc/redis.conf
sudo /etc/init.d/redis start
sudo chkconfig --add redis
sudo chkconfig --level 3 redis on
echo "redis install complete"
exit
```

设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH 命令提供密码，默认关闭

```text
requirepass foobared
```

**2. 安装**

```text
$ wget http://download.redis.io/releases/redis-5.0.5.tar.gz
$ tar xzf redis-5.0.5.tar.gz
$ cd redis-5.0.5
$ make
```

The binaries that are now compiled are available in the`src`directory. Run Redis with:

```text
$ src/redis-server
```

You can interact with Redis using the built-in client:

```text
$ src/redis-cli
redis> set foo bar
OK
redis> get foo
"bar"
```

## Redis 最大连接数查询与设置、释放超时链接

