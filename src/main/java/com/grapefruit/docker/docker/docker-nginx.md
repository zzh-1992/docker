
运行容器
docker run --name nginx -p 80:80 -d nginx

关闭
docker stop 容器名

删除容器
docker rm [OPTIONS] 容器名

操作nginx内部

docker exec -it nginx04 bash

docker rm d6df77101997

重启docker
systemctl restart  docker

docker start 'docker ps -a -q'

nginx相关目录:
/usr/sbin/nginx
/usr/lib/nginx
/etc/nginx (配置文件目录)
/usr/share/nginx  站点目录（50x.html  index.html）

docker_nginx原始目录
nginx:
/usr/sbin/nginx
/usr/lib/nginx
/etc/nginx
/usr/share/nginx


宿主机
/usr/mynginx/conf:/etc/nginx
/usr/mynginx/html:/usr/share/nginx/html
/usr/mynginx/log:/var/log/nginx
=======================================================================
docker run --name nginx01 -d -p 90:80 nginx

docker run --name mynginx -d -p 80:80 -v /usr/mynginx/conf:/etc/nginx -v /usr/mynginx/html:/usr/share/nginx/html -v /usr/mynginx/log:/var/log/nginx nginx
=======================================================================

从容器里面拷文件到宿主机(docker cp 容器名：要拷贝的文件在容器里面的路径       要拷贝到宿主机的相应路径 )
docker cp 3abf05d4dbe0:/etc/nginx/.   /usr/mynginx/conf

docker cp 3abf05d4dbe0:/usr/share/nginx/html/.   /usr/mynginx/html

启动nginx容器
docker start 容器名

阿里云nginx
docker run --name nginx -d -p 80:80 -v /usr/nginx/conf:/etc/nginx -v /usr/nginx/dist:/usr/share/nginx/html -v /usr/nginx/log:/var/log/nginx nginx

docker run --name nginx -d -p 80:80 -v /usr/nginx/conf:/etc/nginx -v /usr/nginx/:/usr/share/nginx/html -v /usr/nginx/log:/var/log/nginx nginx

nginx官网命令介绍
链接:http://nginx.org/en/docs/beginners_guide.html
nginx -s quit
nginx -s reload
kill -s QUIT 1628

mac
docker run --name myNginx -d -p 80:80 -v /Users/myName/software/DockerMount/nginx/conf:/etc/nginx -v /Users/myName/software/DockerMount/nginx/html:/usr/share/nginx/html -v /Users/myName/software/DockerMount/nginx/log:/var/log/nginx nginx
docker cp abcdb0c5e016:/etc/nginx/. /Users/myName/software/DockerMount/nginx/conf
docker cp abcdb0c5e016:/usr/share/nginx/html/. /Users/myName/software/DockerMount/nginx/html
/Users/myName/software/DockerMount/nginx/log

upstream grapefruit {
                server myHost:8888;
                server myHost:9999;
                #server 192.168.2.100:8081;
                #server 192.168.2.100:8080;
}
location / {
        #root   /usr/share/nginx/html;
        #index  index.html index.htm;
        proxy_pass http://grapefruit;
}

nginx官方配置示例
    http://nginx.org/en/docs/example.html

```conf
user  www www;

worker_processes  2;

pid /var/run/nginx.pid;

#                          [ debug | info | notice | warn | error | crit ]

error_log  /var/log/nginx.error_log  info;

events {
    worker_connections   2000;

    # use [ kqueue | epoll | /dev/poll | select | poll ];
    use kqueue;
}

http {

    include       conf/mime.types;
    default_type  application/octet-stream;


    log_format main      '$remote_addr - $remote_user [$time_local] '
                         '"$request" $status $bytes_sent '
                         '"$http_referer" "$http_user_agent" '
                         '"$gzip_ratio"';

    log_format download  '$remote_addr - $remote_user [$time_local] '
                         '"$request" $status $bytes_sent '
                         '"$http_referer" "$http_user_agent" '
                         '"$http_range" "$sent_http_content_range"';

    client_header_timeout  3m;
    client_body_timeout    3m;
    send_timeout           3m;

    client_header_buffer_size    1k;
    large_client_header_buffers  4 4k;

    gzip on;
    gzip_min_length  1100;
    gzip_buffers     4 8k;
    gzip_types       text/plain;

    output_buffers   1 32k;
    postpone_output  1460;

    sendfile         on;
    tcp_nopush       on;
    tcp_nodelay      on;
    send_lowat       12000;

    keepalive_timeout  75 20;

    #lingering_time     30;
    #lingering_timeout  10;
    #reset_timedout_connection  on;


    server {
        listen        one.example.com;
        server_name   one.example.com  www.one.example.com;

        access_log   /var/log/nginx.access_log  main;

        location / {
            proxy_pass         http://127.0.0.1/;
            proxy_redirect     off;

            proxy_set_header   Host             $host;
            proxy_set_header   X-Real-IP        $remote_addr;
            #proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;

            client_max_body_size       10m;
            client_body_buffer_size    128k;

            client_body_temp_path      /var/nginx/client_body_temp;

            proxy_connect_timeout      70;
            proxy_send_timeout         90;
            proxy_read_timeout         90;
            proxy_send_lowat           12000;

            proxy_buffer_size          4k;
            proxy_buffers              4 32k;
            proxy_busy_buffers_size    64k;
            proxy_temp_file_write_size 64k;

            proxy_temp_path            /var/nginx/proxy_temp;

            charset  koi8-r;
        }

        error_page  404  /404.html;

        location = /404.html {
            root  /spool/www;
        }

        location /old_stuff/ {
            rewrite   ^/old_stuff/(.*)$  /new_stuff/$1  permanent;
        }

        location /download/ {

            valid_referers  none  blocked  server_names  *.example.com;

            if ($invalid_referer) {
                #rewrite   ^/   http://www.example.com/;
                return   403;
            }

            #rewrite_log  on;

            # rewrite /download/*/mp3/*.any_ext to /download/*/mp3/*.mp3
            rewrite ^/(download/.*)/mp3/(.*)\..*$
                    /$1/mp3/$2.mp3                   break;

            root         /spool/www;
            #autoindex    on;
            access_log   /var/log/nginx-download.access_log  download;
        }

        location ~* \.(jpg|jpeg|gif)$ {
            root         /spool/www;
            access_log   off;
            expires      30d;
        }
    }
}
```    