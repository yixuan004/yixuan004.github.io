---
title: 基于Docker进行项目部署-以智能114项目为例
date: 2022-11-26 16:22:17
tags:
    - docker
categories:
	- develop
	- docker
	- docker使用
---

背景：该篇博客记录使用docker进行项目部署的例子，介绍了Dockerfile以及一些常用的docker命令、一些遇到问题的QA等

主要涉及`uWSGI+Django`、`NGINX`、`Redis`这几个服务的Dockerfile以及build、run运行方式

未来将在这个博客中补充`MySQL`与`ElasticSearch`的镜像制作

<!--more-->

# 基于Docker进行项目部署-以智能114项目为例

## 1. docker简介&操作过程中的QA

### 1.1 常见问题——制作docker镜像启动后立刻退出

该问题的原因一般是因为程序是以后台方式进行挂载的，而docker的镜像中要保持有前台运行的程序，才能以`run -d`的后台方式启动

#### 1.1.1 uWSGI+Django

uWSGI的配置文件`uwsgi.ini`中可能设置了`daemonize`参数，该参数的作用是将日志文件输出到指定的路径中，如`daemonize = /home/lyx/_114/zfx-114-query-system/_114django/wsgi_logs/wsgi.log`，然而如果加入了该参数的方式启动，就会出现上述说明的问题，一般来说注释掉这个参数就可以；

#### 1.1.2 NGINX

nginx启动后立即退出也是一样的问题，但是nginx的问题不在配置文件中，要改一下Dockerfile中的CMD内容，改成下面这种方式取消daemon就可以了

```shell
CMD ["nginx", "-g", "daemon off;"]
```

#### 1.1.3 Redis

redis在普通的启动方式中，为了避免退出ssh链接就用不了了，需要把配置文件中的`daemonize`参数换成`yes`，而如果使用docker的方式启动，那么为了避免他直接去后台运行了，还要把这个参数改回`no`

```shell
daemonize no
```

### 1.2 常见问题——docker内部的ip访问问题

个人理解，在使用docker进行环境搭建的时候，尽量避免使用`localhost`、`0.0.0.0`、`127.0.0.1`这样的ip，而是改用绝对ip的方式，直接写上主机地址，比如`10.109.246.1xx`这样的方式，各个依赖环境之间通过这个ip+端口的方式来通信；

## 2. Dockerfile的说明

在执行`docker build`命令的时候，会自动读取指定目录下存在的Dockerfile文件，按照Dockerfile文件中的内容进行build，内部分为几个步骤，将在下面的操作过程中进行学习，下面简述一下Dockerfile的几个组成部分，以及一般编写流程

### 2.1 Dockerfile组成部分

大概顺序解释一下下面这个文件中的内容：

- `FROM python:3.6`：在一个官方python3.6的基础上进行docker的构建；
- `MAINTAINER LiuYixuan`：该镜像的开发者是我；
- `ADD ./ /home/lyx/_114/zfx-114-query-system`：个人的感觉上来说，是本地路径是`./`也就是dockerfile所在的这个路径，在执行`docker build`的时候，要从这个目录操作。然后后面一个路径是docker内部的一个路径，这个感觉来说和外面没有绝对的对应关系，但是为了项目路径的统一，这里写的还是前缀比较长的`/home/lyx/_114/zfx-114-query-system`；
- `WORKDIR`：这个路径似乎会是在docker run起来后执行的路径；
- `ENV`：添加到环境变量中的内容，如Java-jdk的一些环境变量；
- `RUN ...`：安装项目的各项内容，中间通过 `&& \`来对各行做一个连接，来避免镜像层数过多体积增加；
- `EXPOSE`：暴露端口，外部可以访问内部的什么端口；

- `CMD`：docker run的时候自动执行什么，如果是写一个`CMD ["/bin/bash"] `就可以卡在bash来运行；

### 2.2 个人编写过程中的Dockerfile编写流程

主要是说`RUN`那个步骤的流程，一般来说要先更新apt的镜像源，然后安装`vim/unzip/tar`等：

```shell
# 更新apt的镜像源，并安装
sed -i s@/deb.debian.org/@/mirrors.aliyun.com/@g /etc/apt/sources.list && \
apt-get clean && \
apt-get update && \
apt-get install vim -y && \
apt-get install unzip && \
apt-get install tar && \
apt-get install procps -y && \
```

然后再安装一下java jdk那些的，这里还添加了环境变量，要加在RUN操作的上面

```shell
ENV JAVA_HOME=/usr/local/java/jdk1.8.0_351
ENV JRE_HOME=$JAVA_HOME/jre
ENV CLASSPATH=.:$JAVA_HOME/lib:${JRE_HOME}/lib
ENV PATH=$JAVA_HOME/bin:$PATH

RUN \
...
# 安装jdk8，将JAVA_HOME/bin 添加至PATH环境变量
mkdir /usr/local/java/ && \
tar -xvf /home/lyx/_114/zfx-114-query-system/packages/jvm/jdk-8u351-linux-x64.tar.gz -C /usr/local/java/
...
```

后续，再进行程序的安装，如`requirements.txt`的安装；

## 3. 自己项目部署操作过程中常用的docker命令

### 3.1 `docker build -t [NAME]:[TAG] .`

将项目打包为镜像，示例`docker build -t 114_uwsgi_django:v1 .`

`-t`： 镜像的名字及标签，通常 name:tag 或者 name 格式；可以在一次构建中为一个镜像设置多个标签；

`[NAME]:[TAG]`：标记名字和版本，例如`114_uwsgi_django:v1`；

`.`：在当前这个路径下，读取Dockerfile；

### 3.2 `docker images`

查看当前有的docker镜像内容

```shell
(base) lyx@h9:~$ docker images
REPOSITORY         TAG       IMAGE ID       CREATED         SIZE
114_uwsgi_django   v1        f0bc2cd90666   4 hours ago     8.62GB
114_nginx          v1        f8b8ed535b64   21 hours ago    198MB
114_redis          v1        614e3bc05a87   3 days ago      169MB
redis              6.2       48da0c367062   2 weeks ago     113MB
redis              latest    3358aea34e8c   2 weeks ago     117MB
nginx              latest    88736fe82739   2 weeks ago     142MB
python             3.6       54260638d07c   11 months ago   902MB
```

### 3.3 `docker rmi -f [IMAGE ID]`

一般配合`docker images`来使用，来删除一些镜像，如下删除`114_uwsgi_django`这个镜像

```shell
docker rmi -f f0bc2cd90666
```

### 3.4 `docker ps`

查看运行中的docker镜像

```shell
(base) lyx@h9:~$ docker ps
CONTAINER ID   IMAGE                 COMMAND                  CREATED        STATUS        PORTS                                                 NAMES
57b17e5e8c21   114_uwsgi_django:v1   "uwsgi --ini ./_114d…"   4 hours ago    Up 4 hours    0.0.0.0:5002->5002/tcp, :::5002->5002/tcp             mystifying_blackwell
fa1939b0e915   114_nginx:v1          "/docker-entrypoint.…"   21 hours ago   Up 21 hours   80/tcp, 0.0.0.0:8082->8082/tcp, :::8082->8082/tcp     agitated_meitner
9144e2cd2c7f   114_redis:v1          "docker-entrypoint.s…"   3 days ago     Up 3 days     6379/tcp, 0.0.0.0:6380->6380/tcp, :::6380->6380/tcp   optimistic_visvesvaraya
```

可以加入`-a`参数，查看处在不运行状态的镜像

<font color='red'>这里看起来有彩蛋hh，不指定名字的话，像是会随机一个名字一样</font> 

```shell
(base) lyx@h9:~$ docker ps -a
CONTAINER ID   IMAGE                 COMMAND                  CREATED        STATUS                      PORTS                                                 NAMES
57b17e5e8c21   114_uwsgi_django:v1   "uwsgi --ini ./_114d…"   4 hours ago    Up 4 hours                  0.0.0.0:5002->5002/tcp, :::5002->5002/tcp             mystifying_blackwell
4e3093d46a86   3fedf6bfe88b          "uwsgi --ini ./_114d…"   4 hours ago    Exited (137) 4 hours ago                                                          laughing_proskuriakova
350b34c97216   0022c7522caf          "uwsgi --ini ./_114d…"   5 hours ago    Exited (137) 4 hours ago                                                          jovial_meninsky
c347d9c11265   aa4aae1cd0d9          "uwsgi --ini ./_114d…"   5 hours ago    Exited (137) 5 hours ago                                                          compassionate_solomon
5882b99cf01e   90471887ed85          "uwsgi --ini ./_114d…"   20 hours ago   Exited (137) 5 hours ago                                                          hardcore_diffie
dd2c38df5a2d   33ee6e4b8a40          "uwsgi --ini ./_114d…"   20 hours ago   Exited (137) 20 hours ago                                                         silly_lamarr
```

### 3.5 停止正在运行的镜像——`docker kill [CONTAINER ID]`

```shell
docker kill 57b17e5e8c21
```

 ### 3.6 批量清除处于Exited状态的容器

```shell
#显示所有的容器，过滤出Exited状态的容器，取出这些容器的ID，
docker ps -a|grep Exited|awk '{print $1}'

#查询所有的容器，过滤出Exited状态的容器，列出容器ID，删除这些容器
docker rm `docker ps -a|grep Exited|awk '{print $1}'`
```

### 3.7 docker镜像启动——`docker run [-p port:port] [-v out:in] [-itd NAME:TAG]`

docker启动的时候使用`docker run`命令，有很多可选参数，也是非常关键的一项内容

另外备注，这个run一般会有好多行，可以用`\`来做换行符

用下面这个114系统的启动为例子

```shell
docker run \
    -p 5002:5002 \
    -v /data/sdb2/lyx/mount-files-202211270941/_114config.json:/home/lyx/_114/zfx-114-query-system/_114django/_114config.json \
    -v /data/sdb2/lyx/mount-files-202211270941/Configs:/home/lyx/_114/zfx-114-query-system/_114django/Configs \
    -v /data/sdb2/lyx/mount-files-202211270941/debug-logs:/home/lyx/_114/zfx-114-query-system/_114django/debug-logs \
    -v /data/sdb2/lyx/mount-files-202211270941/logs:/home/lyx/_114/zfx-114-query-system/_114django/logs \
    -v /data/sdb2/lyx/mount-files-202211270941/wsgi_logs:/home/lyx/_114/zfx-114-query-system/_114django/wsgi_logs \
    -v /data/sdb2/lyx/mount-files-202211270941/stopwords.txt:/usr/local/lib/python3.6/site-packages/pyhanlp/static/data/dictionary/stopwords.txt \
    -v /data/sdb2/lyx/mount-files-202211270941/CustomDictionary1.txt:/usr/local/lib/python3.6/site-packages/pyhanlp/static/data/dictionary/custom/CustomDictionary1.txt \
    -d 114_uwsgi_django:v1
```

## 4. 制作uWSGI+Django的镜像并运行

### 4.1 Dockerfile

```dockerfile
FROM python:3.6

MAINTAINER LiuYixuan

ADD ./ /home/lyx/_114/zfx-114-query-system

WORKDIR /home/lyx/_114/zfx-114-query-system

ENV JAVA_HOME=/usr/local/java/jdk1.8.0_351
ENV JRE_HOME=$JAVA_HOME/jre
ENV CLASSPATH=.:$JAVA_HOME/lib:${JRE_HOME}/lib
ENV PATH=$JAVA_HOME/bin:$PATH

# 安装vim和requirements，并配置pyhanlp的数据
RUN \
    # 更新apt的镜像源，并安装
    sed -i s@/deb.debian.org/@/mirrors.aliyun.com/@g /etc/apt/sources.list && \
    apt-get clean && \
    apt-get update && \
    apt-get install vim -y && \
    apt-get install unzip && \
    apt-get install tar && \
    apt-get install procps -y && \
    
    # 安装jdk8，将JAVA_HOME/bin 添加至PATH环境变量
    mkdir /usr/local/java/ && \
    tar -xvf /home/lyx/_114/zfx-114-query-system/packages/jvm/jdk-8u351-linux-x64.tar.gz -C /usr/local/java/ && \
    # export "JAVA_HOME=/usr/local/java/jdk1.8.0_351" >> ~/.bashrc && \
    # export "JRE_HOME=${JAVA_HOME}/jre" >> ~/.bashrc && \
    # export "CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib" >> ~/.bashrc && \
    # export "PATH=${JAVA_HOME}/bin:$PATH" >> ~/.bashrc && \
    # source ~/.bashrc && \

    # requirements
    pip install /home/lyx/_114/zfx-114-query-system/packages/pip/pip-21.1.2-py3-none-any.whl && \
    pip install /home/lyx/_114/zfx-114-query-system/packages/base/* --no-deps && \
    pip install -e /home/lyx/_114/zfx-114-query-system/packages/cpca/chinese_province_city_area_mapper-master/. --no-deps && \
    pip install -e /home/lyx/_114/zfx-114-query-system/packages/cpca/chinese_province_city_area_mapper/. --no-deps && \
    
    # hanlp
    ## jar
    cp /home/lyx/_114/zfx-114-query-system/packages/hanlp/hanlp-1.8.1.jar /usr/local/lib/python3.6/site-packages/pyhanlp/  && \
    cp /home/lyx/_114/zfx-114-query-system/packages/hanlp/hanlp-1.8.1.jar /usr/local/lib/python3.6/site-packages/pyhanlp/static/ && \
    ## properties
    cp /home/lyx/_114/zfx-114-query-system/packages/hanlp/hanlp.properties /usr/local/lib/python3.6/site-packages/pyhanlp/static/ && \
    cp /home/lyx/_114/zfx-114-query-system/packages/hanlp/hanlp.properties.in /usr/local/lib/python3.6/site-packages/pyhanlp/static/ && \
    ## data
    cp -r /home/lyx/_114/zfx-114-query-system/packages/hanlp/data /usr/local/lib/python3.6/site-packages/pyhanlp/static/ && \
    ## custom
    rm -rf /usr/local/lib/python3.6/site-packages/pyhanlp/static/data/dictionary/custom && \
    cp -r /home/lyx/_114/zfx-114-query-system/packages/hanlp/custom /usr/local/lib/python3.6/site-packages/pyhanlp/static/data/dictionary/ && \
    
    ## stopwords
    rm -rf /usr/local/lib/python3.6/site-packages/pyhanlp/static/data/dictionary/stopwords.txt && \
    rm -rf /usr/local/lib/python3.6/site-packages/pyhanlp/static/data/dictionary/stopwords.txt.bin && \
    cp /home/lyx/_114/zfx-114-query-system/packages/hanlp/stopwords.txt /usr/local/lib/python3.6/site-packages/pyhanlp/static/data/dictionary/ && \
    cp /home/lyx/_114/zfx-114-query-system/packages/hanlp/stopwords.txt.bin /usr/local/lib/python3.6/site-packages/pyhanlp/static/data/dictionary/ && \
    ## CustomDictionary1
    cp /home/lyx/_114/zfx-114-query-system/packages/hanlp/CustomDictionary1.txt /usr/local/lib/python3.6/site-packages/pyhanlp/static/data/dictionary/custom/ && \

    # 把CustomDictionary1.txt和stopwords.txt给软链接挂载出来到Configs（挂载-v可能就不需要这样了）
    # ln -s /usr/local/lib/python3.6/site-packages/pyhanlp/static/data/dictionary/custom/CustomDictionary1.txt /home/lyx/_114/zfx-114-query-system/_114django/Configs/CustomDictionary1.txt && \
    # ln -s /usr/local/lib/python3.6/site-packages/pyhanlp/static/data/dictionary/stopwords.txt /home/lyx/_114/zfx-114-query-system/_114django/Configs/stopwords.txt && \

    # pwd查看一下
    pwd
    
EXPOSE 5002

# runserver启动django
# CMD ["python", "./_114django/manage.py", "runserver", "0.0.0.0:4444"]

# 卡到一个bash来挂载
# CMD ["/bin/bash"] 

# uwsgi挂载
CMD ["uwsgi", "--ini", "./_114django/uwsgi-online.ini"]

```

### 4.2 镜像build&run

#### 4.2.1 build

```shell
docker build -t 114_uwsgi_django:v1 .
```

#### 4.2.2 run

```shell
docker run \
    -p 5002:5002 \
    -v /data/sdb2/lyx/mount-files-202211270941/_114config.json:/home/lyx/_114/zfx-114-query-system/_114django/_114config.json \
    -v /data/sdb2/lyx/mount-files-202211270941/Configs:/home/lyx/_114/zfx-114-query-system/_114django/Configs \
    -v /data/sdb2/lyx/mount-files-202211270941/debug-logs:/home/lyx/_114/zfx-114-query-system/_114django/debug-logs \
    -v /data/sdb2/lyx/mount-files-202211270941/logs:/home/lyx/_114/zfx-114-query-system/_114django/logs \
    -v /data/sdb2/lyx/mount-files-202211270941/wsgi_logs:/home/lyx/_114/zfx-114-query-system/_114django/wsgi_logs \
    -v /data/sdb2/lyx/mount-files-202211270941/stopwords.txt:/usr/local/lib/python3.6/site-packages/pyhanlp/static/data/dictionary/stopwords.txt \
    -v /data/sdb2/lyx/mount-files-202211270941/CustomDictionary1.txt:/usr/local/lib/python3.6/site-packages/pyhanlp/static/data/dictionary/custom/CustomDictionary1.txt \
    -d 114_uwsgi_django:v1
```

## 5. 制作nginx的镜像

Reference：https://blog.csdn.net/weixin_43810267/article/details/119883350

### 5.1 Dockerfile

```shell
FROM nginx:latest

MAINTAINER LiuYixuan

ADD ./ /usr/src/nginx

WORKDIR /usr/src/nginx

RUN \

    # 更新apt的镜像源，并安装一些常用依赖（后续可以取消）
    sed -i s@/deb.debian.org/@/mirrors.aliyun.com/@g /etc/apt/sources.list && \
    apt-get clean && \
    apt-get update && \
    apt-get install vim -y && \
    apt-get install unzip && \
    apt-get install tar && \
    apt-get install procps -y && \
    
    # nginx
    cp /usr/src/nginx/nginx.conf /etc/nginx/nginx.conf && \
    mkdir /var/log/nginx/logs && \
    touch /var/log/nginx/logs/access.http.log && \

    # 查看
    pwd

EXPOSE 8082

# 卡到bash运行=
# CMD ["/bin/bash"]

CMD ["nginx", "-g", "daemon off;"]
```

### 5.2 镜像build&run

#### 5.2.1 build

```shell
docker build -t 114_nginx:v1 .
```

#### 5.2.2 run

```shell
docker run -p 8082:8082 -d 114_nginx:v1
```

### 5.3 一些报错的解决

- curl: (56) Recv failure: 连接被对方重设

可能的原因之一是，用交互模式卡在`/bin/bash`的时候，忘记启动nginx

## 6. 制作redis的镜像

Reference：https://www.cnblogs.com/kendoziyu/p/15814418.html

### 6.1 Dockerfile

```shell
FROM redis:6.2

MAINTAINER LiuYixuan

ADD ./ /home/redis1

WORKDIR /home/redis1

RUN \

    # 更新apt的镜像源，并安装一些常用依赖（后续可以取消）
    sed -i s@/deb.debian.org/@/mirrors.aliyun.com/@g /etc/apt/sources.list && \
    apt-get clean && \
    apt-get update && \
    apt-get install vim -y && \
    apt-get install unzip && \
    apt-get install tar && \
    apt-get install procps -y && \
    
    # 查看
    pwd

EXPOSE 6380

CMD ["/usr/local/bin/redis-server", "/home/redis1/redis.conf"]
# CMD ["/bin/bash"]
```

### 6.2 镜像build&run

#### 6.2.1 build

```shell
docker build -t 114_redis:v1 .
```

#### 6.2.2 run

```shell
docker run -p 6380:6380 -d 114_redis:v1
```









