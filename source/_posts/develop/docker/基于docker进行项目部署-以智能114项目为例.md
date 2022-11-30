---
title: 基于docker进行项目部署-以智能114项目为例
date: 2022-11-26 16:22:17
tags:
    - docker
categories:
	- develop
	- docker
---





# 基于docker进行项目部署-以智能114项目为例

## 1. 过程中的问题QA





## 2. 制作python+Django+uWSGI（系统代码部分）的镜像并运行

### 2.1 基于Dockerfile镜像制作

使用vscode进行服务器的链接，在`zfx-114-query-system`这个项目目录下，编写`Dockerfile`文件，该文件的绝对路径是`/data/sdb2/lyx/zfx-114-query-system/Dockerfile`；

大概顺序解释一下下面这个文件中的内容：

- `FROM python:3.6`：在一个官方python3.6的基础上进行docker的构建；
- `MAINTAINER LiuYixuan`：该镜像的开发者是我；
- `ADD ./ /home/lyx/_114/zfx-114-query-system`：个人的感觉上来说，是本地路径是`./`也就是dockerfile所在的这个路径，在执行`docker build`的时候，要从这个目录操作。然后后面一个路径是docker内部的一个路径，这个感觉来说和外面没有绝对的对应关系，但是为了项目路径的统一，这里写的还是前缀比较长的`/home/lyx/_114/zfx-114-query-system`；
- `WORKDIR`：这个路径似乎会是在docker run起来后执行的路径；
- `RUN ...`：安装项目的各项内容；
- `EXPOSE`：暴露端口，外部可以访问内部的什么端口；

- `CMD`：docker run的时候自动执行什么；

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

# CMD ["python", "./_114django/manage.py", "runserver", "0.0.0.0:4444"]

# 卡到一个bash来挂载
CMD ["/bin/bash"]

# docker build -t 114_uwsgi_django:v1 .
```

对于这个dockerfile，

### 2.2 镜像build/run/rmi

- build镜像`docker build -t [REPO_NAME]:[TAG]`

在`Dockerfile`的同目录下（`/data/sdb2/lyx/zfx-114-query-system/`）执行：

```shell
docker build -t test:v1 .
```

- 删除镜像`docker rmi -f [IMAGE ID]`

```shell
(base) lyx@h9:~$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
test         v1        04bef312ebfb   37 minutes ago   7.72GB
python       3.6       54260638d07c   11 months ago    902MB
(base) lyx@h9:~$ docker rmi -f 04bef312ebfb
```

- run镜像，这里需要把本机的文件路径和docker内部做挂载，这样做的目的是，直接在本机的文件路径上做文件的修改，然后内外是可以同步的（数据持久化），内部的修改会同步到外面，外面的修改也会对内部生效；

个人感觉，由于docker需要root权限（docker组），那么从docker内部往本机外部生成的文件会是需要root权限的，而从外部往内部的文件则只需要用户权限；

```shell
docker run \
    -p 5002:5002 \
    -v /data/sdb2/lyx/mount-files-202211270941/Configs:/home/lyx/_114/zfx-114-query-system/_114django/Configs \
    -v /data/sdb2/lyx/mount-files-202211270941/debug-logs:/home/lyx/_114/zfx-114-query-system/_114django/debug-logs \
    -v /data/sdb2/lyx/mount-files-202211270941/logs:/home/lyx/_114/zfx-114-query-system/_114django/logs \
    -v /data/sdb2/lyx/mount-files-202211270941/wsgi_logs:/home/lyx/_114/zfx-114-query-system/_114django/wsgi_logs \
    -v /data/sdb2/lyx/mount-files-202211270941/stopwords.txt:/usr/local/lib/python3.6/site-packages/pyhanlp/static/data/dictionary/stopwords.txt \
    -v /data/sdb2/lyx/mount-files-202211270941/CustomDictionary1.txt:/usr/local/lib/python3.6/site-packages/pyhanlp/static/data/dictionary/custom/CustomDictionary1.txt \
    -it 114_uwsgi_django:v1
```

## 3. 制作nginx的镜像，并与uWSGI+Django进行串联

https://blog.csdn.net/weixin_43810267/article/details/119883350







### 4. 制作redis

https://www.cnblogs.com/kendoziyu/p/15814418.html











docker curl: (56) Recv failure: 连接被对方重设

忘记启动nginx











