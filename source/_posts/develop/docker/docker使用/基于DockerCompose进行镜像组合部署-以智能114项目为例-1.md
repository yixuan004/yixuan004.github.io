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

## DockerCompose

