---
title: 基于DockerCompose进行镜像组合部署-以智能114项目为例
date: 2022-12-02 14:32:42
tags:
    - docker
categories:
	- develop
	- docker
	- docker使用
---

背景：单独使用docker构建了多个镜像了，然而在项目的时候，这些镜像需要一个个启动，以智能114系统为例，依赖环境包括`uWSGI+Django`、`NGINX`、`Redis`、`MySQL`、`ElasticSearch`，如果是一个个启动docker镜像，感觉总还是有不方便的时候

docker-compose应该是提供了一种编排方法，具体还要在学习的使用细看

<!--more-->

# 基于DockerCompose进行镜像组合-以智能114系统为例

## 1. docker-compose

提供了一种镜像编排方法，个人感觉有好几种方式来进行使用

### 1.1 在制作好了镜像的基础上，把文件组合来进行启动

我已经单独通过Dockerfile把一个个镜像测试过了，然后现在每个镜像都是可以单独启动的，那么我通过`docker-compose up [-d]`的方式把我这几个镜像的通过`docker-compose.yml`给拉起来，比如原来我要一个一个的启动很麻烦，现在可以一次性给拉起来了；

```shell
(base) lyx@h9:~$ docker images
REPOSITORY         TAG       IMAGE ID       CREATED         SIZE
114_uwsgi_django   v1        e529aef73a6b   2 hours ago     8.62GB
114_nginx          v1        f8b8ed535b64   2 days ago      198MB
114_nginx          v2        f8b8ed535b64   2 days ago      198MB
114_redis          v1        614e3bc05a87   5 days ago      169MB
redis              6.2       48da0c367062   2 weeks ago     113MB
redis              latest    3358aea34e8c   2 weeks ago     117MB
nginx              latest    88736fe82739   2 weeks ago     142MB
python             3.6       54260638d07c   11 months ago   902MB
(base) lyx@h9:~$
```

例如我要把上面的`114_uwsgi_django:v1`、`114_nginx:v1`、`114_redis:v1`给拉起来，这几个镜像已经自己单独测试过了，详见博客https://yixuan004.github.io/2022/11/26/develop/docker/docker%E4%BD%BF%E7%94%A8/%E5%9F%BA%E4%BA%8EDocker%E8%BF%9B%E8%A1%8C%E9%A1%B9%E7%9B%AE%E9%83%A8%E7%BD%B2-%E4%BB%A5%E6%99%BA%E8%83%BD114%E9%A1%B9%E7%9B%AE%E4%B8%BA%E4%BE%8B/，则需要执行以下几个步骤

1. 编写`docker-compose.yml`文件；
2. 执行`docker-compose up [-d]`，完成镜像的启动

#### 1.1.1 docker-compose.yml

简单对下面这个文件来解释一下，`version`应该是一个不用管的随便写的参数，`services`内部编排各个镜像，主要就是用哪个`image`，然后执行的`command`是啥，`volumes`挂载数据卷，`ports`端口映射，`restart`就是跪了后重启

如果几个镜像都做好了，那么这样的方式是可以的

```yaml
# 整个编排里包含了3项容器，uWSGI+Django、Redis、NGINX
# https://blog.csdn.net/weixin_42134789/article/details/115878467
version: "3"

services:
  uWSGI_Django:
    image: 114_uwsgi_django:v1
    command: uwsgi --ini ./_114django/uwsgi-docker.ini
    volumes:
      - /data/sdb2/lyx/mount-files-202211270941/_114config.json:/home/lyx/_114/zfx-114-query-system/_114django/_114config.json \
      - /data/sdb2/lyx/mount-files-202211270941/Configs:/home/lyx/_114/zfx-114-query-system/_114django/Configs \
      - /data/sdb2/lyx/mount-files-202211270941/debug-logs:/home/lyx/_114/zfx-114-query-system/_114django/debug-logs \
      - /data/sdb2/lyx/mount-files-202211270941/logs:/home/lyx/_114/zfx-114-query-system/_114django/logs \
      - /data/sdb2/lyx/mount-files-202211270941/wsgi_logs:/home/lyx/_114/zfx-114-query-system/_114django/wsgi_logs \
      - /data/sdb2/lyx/mount-files-202211270941/stopwords.txt:/usr/local/lib/python3.6/site-packages/pyhanlp/static/data/dictionary/stopwords.txt \
      - /data/sdb2/lyx/mount-files-202211270941/CustomDictionary1.txt:/usr/local/lib/python3.6/site-packages/pyhanlp/static/data/dictionary/custom/CustomDictionary1.txt \
    ports:
      - "5002:5002"
    restart: always  # always代表容器运行发生错误的时候，一直重启
  
  Redis:
    image: 114_redis:v1
    command: /usr/local/bin/redis-server /home/redis1/redis.conf
    ports:
      - "6380:6380"
    restart: always

  NGINX:
    image: 114_nginx:v1
    command: nginx -g "daemon off;"
    ports:
      - "8082:8082"
    restart: always
```

#### 1.1.2 `docker-compose up -d`

在`docker-compose.yml`的路径下，执行该句`docker-compose up -d`命令，其中`-d`代表后台启动

```shell
(base) lyx@h9:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
(base) lyx@h9:/data/sdb2/lyx/114-query-system-dependencies/docker$  docker-compose up -d
Starting docker_Redis_1        ... done
Starting docker_uWSGI_Django_1 ... done
Starting docker_NGINX_1        ... done
(base) lyx@h9:/data/sdb2/lyx/114-query-system-dependencies/docker$ docker ps
CONTAINER ID   IMAGE                 COMMAND                  CREATED             STATUS          PORTS                                                 NAMES
1940658e33e1   114_nginx:v1          "/docker-entrypoint.…"   About an hour ago   Up 22 seconds   80/tcp, 0.0.0.0:8082->8082/tcp, :::8082->8082/tcp     docker_NGINX_1
7bcf40a822c8   114_redis:v1          "docker-entrypoint.s…"   About an hour ago   Up 22 seconds   6379/tcp, 0.0.0.0:6380->6380/tcp, :::6380->6380/tcp   docker_Redis_1
f8a58c3ba53e   114_uwsgi_django:v1   "uwsgi --ini ./_114d…"   About an hour ago   Up 22 seconds   0.0.0.0:5002->5002/tcp, :::5002->5002/tcp             docker_uWSGI_Django_1
```

### 1.2 构建镜像`docker-compose build`+启动`docker-compose up`

未来再进一步补充这里的笔记，感觉上来说，这种方式不太好用，其实需要把每个单独测试好了，再一起启动，要么感觉很容易出问题

但是也保留此部分，未来可能会用到这个部分

## 2. docker-compose常见报错解决

### 2.1 启动时候缺少配置文件

未在`docker-compose.yml`路径执行该句命令

```shell
(base) lyx@h9:~$ docker-compose up -d
ERROR: 
        Can't find a suitable configuration file in this directory or any
        parent. Are you in the right directory?

        Supported filenames: docker-compose.yml, docker-compose.yaml
```



