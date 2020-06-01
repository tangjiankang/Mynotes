# 安装

nginx 1.14.2,centos7.6 nginx官方epl源 vim /etc/yum.repos.d/nginx.repo

```text
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/7/$basearch/
gpgcheck=0
enabled=1
```

yum安装最新版nginx，nginx -V 看下有什么默认模块，好编译用 wget [https://nginx.org/download/nginx\-1.14.2.tar.gz](https://nginx.org/download/nginx\-1.14.2.tar.gz) tar zxf nginx-1.14.2.tar.gz [https://github.com/leev/ngx\_http\_geoip2\_module](https://github.com/leev/ngx_http_geoip2_module)说明 wget [https://github.com/leev/ngx\_http\_geoip2\_module/archive/3.2.tar.gz](https://github.com/leev/ngx_http_geoip2_module/archive/3.2.tar.gz) tar zxf 3.2.tar.gz 安装相关依赖 `yum -y install zlib zlib-devel openssl openssl-devel pcre pcre-devel` cd nginx-1.14.2 根据nginx -V的模块最后加一条--add-dynamic-module=/root/ngx\_http\_geoip2\_module-3.2 ./configure 模块，报错./configure: error: the geoip2 module requires the maxminddb library yum -y intall git `git clone --recursive https://github.com/maxmind/libmaxminddb` cd libmaxminddb 安装依赖 `yum -y install autoconf automake libtool`

```text
./bootstrap
./configure
make && make install
sudo ldconfig
```

使用时可能会有.so未加载成功，参考官网处理方案 maxminddb官网 [https://github.com/maxmind/libmaxminddb](https://github.com/maxmind/libmaxminddb)

## 用法示例：

```text
http {
    ...
    geoip2 /etc/maxmind-country.mmdb {
        auto_reload 5m;
        $geoip2_metadata_country_build metadata build_epoch;
        $geoip2_data_country_code default=US source=$variable_with_ip country iso_code;
        $geoip2_data_country_name country names en;
    }

    geoip2 /etc/maxmind-city.mmdb {
        $geoip2_data_city_name default=London city names en;
    }
    ....

    fastcgi_param COUNTRY_CODE $geoip2_data_country_code;
    fastcgi_param COUNTRY_NAME $geoip2_data_country_name;
    fastcgi_param CITY_NAME    $geoip2_data_city_name;
    ....
}

stream {
    ...
    geoip2 /etc/maxmind-country.mmdb {
        $geoip2_data_country_code default=US source=$remote_addr country iso_code;
    }
    ...
}
```

```text
$ mmdblookup --file /usr/share/GeoIP/GeoIP2-Country.mmdb --ip 8.8.8.8

  {
    "country":
      {
        "geoname_id":
          6252001 <uint32>
        "iso_code":
          "US" <utf8_string>
        "names":
          {
            "de":
              "USA" <utf8_string>
            "en":
              "United States" <utf8_string>
          }
      }
  }

$ mmdblookup --file /usr/share/GeoIP/GeoIP2-Country.mmdb --ip 8.8.8.8 country names en

  "United States" <utf8_string>
```

**ubuntu 16.04 安装最新版**

```text
vim /etc/apt/sources.list
#添加
deb http://nginx.org/packages/mainline/ubuntu/ xenial nginx
deb-src http://nginx.org/packages/mainline/ubuntu/ xenial nginx
#添加key
wget http://nginx.org/keys/nginx_signing.key
sudo apt-key add nginx_signing.key
#install
sudo apt update
sudo apt install nginx
```

**ubuntu 18.04 编译安装** 依赖 `apt-get install build-essential libpcre3 libpcre3-dev libssl-dev zlib1g-dev`

报错 `objs/Makefile:595: recipe for target 'objs/src/core/ngx_murmurhash.o' failed` 找到对应的Makefile 删除掉 -Werror

