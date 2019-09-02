http://nginx.org/en/linux_packages.html#stable

[http://nginx.org/en/docs/http/ngx\_http\_proxy\_module.html#proxy\_pass](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass)

## 屏蔽访问某文件夹
```
    location ~* /(.git|.gitignore){
        deny all;
    }
```
