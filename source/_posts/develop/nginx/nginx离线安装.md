---
title: nginx离线安装
date: 2022-11-25 09:28:20
tags:
---

离线安装nginx，用到的nginx-1.22.0的百度网盘地址（这个安装包不知道是不是已经把依赖给涵盖了，在完全全新的服务器上安装感觉可能还是有问题）：

链接: https://pan.baidu.com/s/1HbMrAcLd5FT6bz0ji_vZWg 提取码: 6guw 

<!--more-->


# nginx离线安装

## 1. 操作

离线安装nginx，用到的nginx-1.22.0的百度网盘地址（这个安装包不知道是不是已经把依赖给涵盖了，在完全全新的服务器上安装感觉可能还是有问题）：

链接: https://pan.baidu.com/s/1HbMrAcLd5FT6bz0ji_vZWg 提取码: 6guw 

nginx是可以用root用户安装的，之间通过sock通信，sock是针对每个用户的

```shell
./configure --prefix=/usr/local/nginx-1.22.0
make
make install
cd /usr/local/nginx-1.22.0
cd logs/
touch access.log
touch error.log
cd /usr/local/nginx-1.22.0/sbin
./nginx
```

配置文件使用的是这个conf文件，用这个.conf文件替换`/usr/local/nginx-1.22.0/conf/nginx.conf`这个文件

```json
user  root;
worker_processes  2;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  warn;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
##########################################################
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/logs/access.http.log  main;
    error_log  /var/log/nginx/logs/error.http.log  warn;
    sendfile        on;
    client_body_timeout  200s;
    keepalive_timeout  65;
    client_header_timeout 200s;
    send_timeout   200s;
limit_conn_zone $binary_remote_addr zone=limit_conn_zone:100m;
limit_rate 100m;

######################################
    server {
        listen 85;
        listen 8081;
     #####################
   access_log  /var/log/nginx/logs/access.server.log  main;
    error_log  /var/log/nginx/logs/error.server.log  warn;
    sendfile        on;
    client_body_timeout  200s;
    keepalive_timeout  65;
    client_header_timeout 200s;
    send_timeout   200s;
######################
        location / {
            root /home/svr4data/record/asr114;
            autoindex on;
##################
limit_conn limit_conn_zone 100;
##############
        }
        location /call {
           alias /home/svr4data/record/_port_;
           autoindex on;
##################
limit_conn limit_conn_zone 100;
##############
        }
    }


    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;
#####################
   access_log  /var/log/nginx/logs/access.server.log  main;
    error_log  /var/log/nginx/logs/error.server.log  warn;
    sendfile        on;
    client_body_timeout  200s;
    keepalive_timeout  65;
    client_header_timeout 200s;
    send_timeout   200s;
######################
        location / {
            root   html;
            index  index.html index.htm;
         ##################
limit_conn limit_conn_zone 100;
##############
        }

        #error_page  404              /404.html;
        ################################
        error_page  400   /ok.html;
        error_page  404   /ok.html;
        error_page  413   /ok.html;
        error_page  502   /ok.html;
        error_page  504   /ok.html;
        error_page  500   /ok.html;
        ################################
        # redirect server error pages to the static page /50x.html
        #
    #    error_page   500 502 503 504  /50x.html;
    #    location = /50x.html {
    #        root   html;
    #   }
     # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }
	# 这里是114项目的配置，主要是监听8082端口，并且和uwsgi配置连接
    server {
        listen       8082;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;
        proxy_buffer_size 64k;
        proxy_buffers 4 32k;
        proxy_busy_buffers_size 64k;
        proxy_temp_file_write_size 64k;

        location / {
            include uwsgi_params;
            #uwsgi_pass 127.0.0.1:5002;
            uwsgi_pass unix:/tmp/_114.sock;
            uwsgi_send_timeout 600;
            uwsgi_read_timeout 600;
            uwsgi_connect_timeout 600;
            uwsgi_buffer_size 32k;
            uwsgi_buffers 4 32k;
            uwsgi_busy_buffers_size 64k;
            uwsgi_temp_file_write_size 64k;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }

    server{
    listen       8088;
          charset utf-8;
          server_name  localhost;
          index index.html index.htm index.php;
          root  /home/bupt_114;
          location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|amr|mp3|wav)$
          {
               expires      30d;
           }
  location = /ok.html {
      return 444;
        }

          location ^~/manage114/ai/log/ {
            alias /home/bupt_114/_114/zfx-114-query-system/_114django/debug-logs/;
          }
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
server_tokens off;
}
```

这里可以单独把`/usr/local/nginx-1.22.0/sbin/nginx`这个文件给软链接出来，相当于创建一个快捷方式，这样就不用cd到目录下然后使用`./nginx`的方式，而是直接`nginx`就可以了

```shell
ln -s /usr/local/nginx-1.22.0/sbin/nginx /usr/bin/nginx
```

个人感觉也可以把nginx这个目录加到环境变量的方式，但可能这样的操作就不是很规范，root用户（全局可以使用`/etc/profile`）

```shell
vim /etc/profile
export PATH=$PATH:/usr/local/nginx-1.22.0/sbin/
```

## 2. 重新启动/加载配置文件

可以使用`nginx -t -c`命令检查配置文件

```shell
[root@svr6 nginx]# nginx -t -c nginx-20220812.conf 
nginx: the configuration file /etc/nginx/nginx-20220812.conf syntax is ok 
nginx: configuration file /etc/nginx/nginx-20220812.conf test is successful
```

网上的一些博客说，可以用`nginx -s reload -c`命令不重启nginx的情况下，修改配置文件，但自己尝试不成功

```shell
nginx -s reload -c nginx-20220812.conf
```

比较保险的方式还是进入到`/nginx-1.22.0/conf/`这个路径下，首先重命名原来的`nginx.conf`（比如重命名成`nginx.conf.20221125`），然后把新的配置文件放在这里名重命名为`nginx.conf`，之后kill掉nginx的方式，直接从头启动

## 3. 报错解决

### 3.1 open() ".../access.http.log" failed (2: No such file or directory)

在一次操作过程中，报错`nginx: [emerg] open() "/var/log/nginx/logs/access.http.log" failed (2: Noe such file or directory)`

可能是由于`nginx.conf`文件中配置了相关log的保存路径，mkdir一下这个路径，然后再touch一个access.http.log就行了