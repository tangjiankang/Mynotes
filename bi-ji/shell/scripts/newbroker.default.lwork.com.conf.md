# newbroker.default.lwork.com.conf

```text
server {
    listen       80 default;
    server_name _;
    access_log  /var/log/nginx/broker-prod.access.log  access_json;

    set $ui_url "https://broker-static.oss-cn-hangzhou.aliyuncs.com/prod";
    set $ui_dir prod;

    if ($geoip2_data_country_code != 'CN') {
    set $ui_url https://static.test.com/prodwai;
    set $ui_dir prodwai;
       }


    set $getui $cookie_feVersion;

    if ($getui != "") {
     set $a 0;
     }

    if ($getui != "undefined") {
     set $a 0;
     }

    if ($a = 0) {
     set $ui_version $getui;
     }

    if ($getui = "") {
     set $a 1;
     }
    if ($getui = "undefined") {
     set $a 1;
     }
    if ($a = 1) {
     set $ui_version v6.12.33;
     }

    location ^~ /api/v1/static/
                {
        proxy_set_header Host $host; # 就是可设置请求头-并将头信息传递到服务器端。不属于请求头的参数中也需要传递时 重定义下就行啦
        proxy_set_header X-Real-IP $clientRealIp;
        proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
        proxy_connect_timeout   60s;
        proxy_send_timeout      60s;
        proxy_read_timeout      60s;
        proxy_pass http://staticlist/v1/static/;
    more_clear_headers 'Last-Modified';
        proxy_redirect off;
                }


    location ^~ /api/v {
        proxy_pass   http://bw_facade/v;
    more_clear_headers 'Last-Modified';
        proxy_connect_timeout   180;
        proxy_send_timeout      180;
        proxy_read_timeout      180;

        proxy_set_header   Host             $host;
        proxy_set_header   X-Real-IP        $clientRealIp;
        proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
               }

    location ^~ /ali {
        proxy_pass   http://bw_facade;
    more_clear_headers 'Last-Modified';
        proxy_connect_timeout   180;
        proxy_send_timeout      180;
        proxy_read_timeout      180;

        proxy_set_header   Host             $host;
        proxy_set_header   X-Real-IP        $clientRealIp;
        proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
               }

    location ^~ /introduce {
        proxy_pass   http://bw_facade;
    more_clear_headers 'Last-Modified';
        proxy_connect_timeout   180;
        proxy_send_timeout      180;
        proxy_read_timeout      180;

        proxy_set_header   Host             $host;
        proxy_set_header   X-Real-IP        $clientRealIp;
        proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
               }


     location =/ {
        proxy_pass http://static.test.com/prod/dist2/$ui_version/index/index.html;
    more_clear_headers 'Last-Modified';
        proxy_redirect off;

      }

     location ^~ / {
      rewrite /(.*)$  /prod/dist2/$ui_version/index/index.html break;
      proxy_pass http://static.test.com/prod/dist2/$ui_version/index/index.html;
    more_clear_headers 'Last-Modified';
      proxy_redirect off;
      }


        location /static/images/bg_404.png {
            proxy_set_header Host leanwork-static.oss-cn-hangzhou.aliyuncs.com;
            proxy_pass http://leanwork-static.oss-cn-hangzhou.aliyuncs.com/static/images/bg_404.png;
            proxy_redirect off;
        }

        error_page  404              /404.html;
                location = /404.html {
                proxy_set_header Host leanwork-static.oss-cn-hangzhou.aliyuncs.com;
                proxy_pass http://leanwork-static.oss-cn-hangzhou.aliyuncs.com/static/404.html;
                proxy_redirect off;
        }
        location /static/images/bg_5XX.png {
            proxy_set_header Host leanwork-static.oss-cn-hangzhou.aliyuncs.com;
            proxy_pass http://leanwork-static.oss-cn-hangzhou.aliyuncs.com/static/images/bg_5XX.png;
            proxy_redirect off;
        }
        error_page   401 403 500 502 503 504 http://$host/error.html?sc=$status;
        location /error.html {
                proxy_set_header Host leanwork-static.oss-cn-hangzhou.aliyuncs.com;
                proxy_pass http://leanwork-static.oss-cn-hangzhou.aliyuncs.com/static/5XX.html$is_args$args;
                proxy_redirect off;
        }

        if ($request_uri ~* (autoconfig|configprops|beans|dump|mappings|metrics|shutdown|trace)){
                rewrite ^(.*)$ http://$host/404.html permanent;
        }

}
```

