---
title: docker相关
tags:
  - docker
categories:
  - Deploy-Tec
---

<img width="300" align="center" src="/images/dockerlogo.png" alt="ronaldoxzb" /></p>

### 一、集装箱？

来自于知乎的通俗解释：
Docker 的思想来自于 集装箱，集装箱解决了什么问题？在一艘大船上，可以把货物规整的摆放起来。并且各种各样的货物被集装箱标准化了，集装箱和集装箱之间不会互相影响。那么我就不需要专门运送水果的船和专门运送化学品的船了。只要这些货物在集装箱里封装的好好的，那我就可以用一艘大船把他们都运走。

### docker 核心概念

#### 镜像(image)

`类似于虚拟机中的镜像（image），是一个包含有文件系统的面向Docker引擎的只读模板。任何应用程序运行都需要环境，而镜像就是用来提供这种运行环境的。镜像可以用来创建 Docker 容器`

#### 仓库(repository) [node.js 镜像仓库](https://hub.docker.com/_/node)

`仓库（Repository）是集中存放镜像文件的场所。类似于代码仓库，这里是镜像仓库。`

#### 容器(container)

`Docker 利用容器（Container）来运行应用。容器是从镜像创建的运行实例。可以看作一个简易版的 Linux 环境`

#### 对比记忆

> Java 号称“一次编译，到处运行”，因为 java 虚拟机解决平台的兼容性问题，所以有 java 虚拟机的地方就能跑 java 代码；

> Docker 是：“一次封装，到处运行”，因为 docker 解决了应用环境的问题，安装了 docker 的平台就能跑“docker 包”，这样就决绝了“开发环境能跑，一上线就崩”的尴尬。

#### 常用命令

```bash
images      List images
ps          List containers
pull        Pull an image or a repository from a registry
push        Push an image or a repository to a registry
rename      Rename a container
restart     Restart one or more containers
rm          Remove one or more containers
rmi         Remove one or more images
run         Run a command in a new container
```

#### 快速启动常用软件 [有道云笔记](http://note.youdao.com/s/XQGk25P1)

### 二、Dockerfile

#### 什么是 dockerfile?

`Dockerfile 是一个包含用于组合映像的命令的文本文档。可以使用在命令行中调用任何命令。 Docker 通过读取Dockerfile中的指令自动生成映像。`

`docker build命令用于从 Dockerfile 构建映像。可以在docker build命令中使用-f标志指向文件系统中任何位置的 Dockerfile。`

```bash
docker build -f /path/to/a/Dockerfile
```

#### Dockerfile 文件说明

`Docker 以从上到下的顺序运行 Dockerfile 的指令。为了指定基本映像，第一条指令必须是 FROM。一个声明以＃字符开头则被视为注释。可以在 Docker 文件中使用RUN，CMD，FROM，EXPOSE，ENV等指令。`

```bash
# node镜像
FROM node:12-alpine as build-stage
RUN echo "-------------------- web环境配置 --------------------"
# 指定接下来的工作路径为/app  - 类似于cd命令
WORKDIR /app
# 拷贝前端项目到app目录下
COPY ./web/package*.json ./
# 安装依赖
RUN npm i --registry=https://registry.npm.taobao.org
COPY ./web ./
# 打包 - 目的：丢到nginx下跑
RUN npm run build:prod
```

### 三、Docker Compose

#### 什么是 docker-compose?

`Docker-compose 是 docker 的服务编排工具，主要是用来构建多个服务，一般是构建构建多个有关联的服务。`

```bash
version: '2'
services:
  mysql5.7:
    image: mysql:5.7
    container_name: mysql
    volumes:
      - "./mysql5.7/data:/var/lib/mysql"
      - "./mysql5.7/conf:/etc/mysql/conf.d"
      - ./docker/mysql/init:/docker-entrypoint-initdb.d/ #执行默认脚本
    ports:
      - "3306:3306"
    user: root:root
    tty: true
    restart: always
    environment:
      - PROCESS_NUM=1
      - MYSQL_ROOT_PASSWORD=xier0571
      - TZ=Asia/Shanghai
      - TIME_STYLE="+%F %T"
      - LANG=en_US.UTF-8
  redis4:
    image: redis:4
    container_name: redis
    volumes:
      - "./redis4/conf:/usr/local/etc/redis"
      - "./redis4/data:/data"
    ports:
      - "6379:6379"
    tty: true
    restart: always
    command: redis-server --requirepass xier0571
```

#### 常用命令

```bash
up                 Create and start containers
down               Stop and remove resources
images             List images
ps                 List containers
top                Display the running processes
```
