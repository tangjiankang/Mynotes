```
user  nginx;
worker_processes  2;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


worker_rlimit_nofile 65535;

load_module modules/ngx_http_headers_more_filter_module.so;

events {
    use epoll;
    multi_accept on;
    worker_connections  65535;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    map $proxy_add_x_forwarded_for  $clientRealIp {
             ""    $remote_addr;
             ~^(?P<firstAddr>[0-9\.]+),?.*$     $firstAddr;
       }

    log_format access_json

                    '$clientRealIp | [$time_local] | '
                    '$http_host | '
                    '$remote_addr | '
                    '$request | '
                    '$status | '
                    '$body_bytes_sent | '
                    '$http_referer | '
                    '$request_body | '
                    '$request_time | '
                    '$upstream_addr | '
                    '$upstream_response_time | '
                    '[$http_user_agent] ';

    access_log  /var/log/nginx/access.log  access_json;

    sendfile        on;
    #tcp_nopush     on;
    server_tokens  off;
    #resolver 10.143.22.116;
    resolver 10.143.22.116 10.143.22.118 223.5.5.5 223.6.6.6;

    keepalive_timeout  65;

    #gzip  on;
    gzip  on;
    gzip_min_length 1k;
    gzip_buffers 4 16k;
    gzip_http_version 1.1;
    gzip_comp_level 3;
    gzip_types text/plain application/x-javascript text/css application/xml text/javascript application/json image/jpeg image/gif image/png;
    gzip_vary on;
    client_max_body_size 50m;
    server_names_hash_max_size 512;
    server_names_hash_bucket_size 1280;

    include /etc/nginx/support.d/*.conf;
    #include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/preconf.d/*.conf;
    #include /etc/nginx/preprod.d/*.conf;
    include /etc/nginx/prod.d/*.conf;
    #include /etc/nginx/update.d/*.conf;


    upstream  staticlist{                                                                                
                server   10.47.33.18:8080;
                server   10.172.76.149:8080;
        }

    upstream gw_facade{
		server 10.144.100.117:8014;
		server 10.30.74.217:8014;
	}
    upstream  bw_facade{                                                                                
                server   10.29.162.127:10200; 
                server   10.24.185.209:10200;
                server   10.26.149.229:10200;
        }
    upstream  prebw_facade{
                server   172.16.179.68:10200;
                server   172.16.179.63:10200;
                server   172.16.179.81:10200;
        }

    upstream  sc_facade{
                server   10.29.155.198:10601;
                server   10.26.149.229:10601;
        }
    upstream  presc_facade{
                server   172.16.179.64:10601;
                server   172.16.179.76:10601;
        }

     upstream  ops_facade{
                server   10.29.155.198:10600;
                server   10.26.149.229:10600;
        }  

     upstream  preops_facade{
                server   172.16.179.64:10600;
                server   172.16.179.69:10600;
        }  

     upstream  bw_openapi{
                server   10.26.7.123:10500;
                server   10.26.7.104:10500;
        }

}
```