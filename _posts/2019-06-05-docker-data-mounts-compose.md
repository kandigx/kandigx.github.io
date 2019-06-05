---
title:  "Docker 数据挂载与 Compose 集成"
permalink: /docker-data-mounts-compose/
date: 2019-06-05

categories: [基础, 教程]

tags: [docker, linux, data, volume, mount, compose]
---

[TOC]

# Docker 数据挂载与 Compose 集成

## 数据挂载

数据管理的两种方式

1. 数据卷(Volumes)
2. 挂载主机目录(Bind mounts)

### 数据卷

一个可供一个或者多个容器使用的特殊目录，它绕过 UFS，可以提供很多有用的特性

其对数据卷的修改会立即生效，不会影响镜像，默认会一直存在

注：数据卷的使用，类似于 Linux 下对目录或文件进行 mount，镜像中的被指定为挂载点的目录中的文件会被隐藏掉，能显示看的是挂载的数据卷。

两种挂载方式，-v 和 -mount，推荐使用 -mount 参数

创建一个数据卷

```shell
docker volume create my-volume
```

查看指定数据卷的信息

```shell
docker volume inspect my-volume
docker volume ls #查看所有数据卷
```

启动一个挂载数据卷的容器

```shell
docker run --name session-web -d -p 8888:8080 \
#-v my-volume:/webapp \
--mount source=my-volume,target=/webapp session-web:latest
```

创建一个名为 session-web 的容器，并加载一个数据卷到容器的 /webapp 目录

在用 docker run 命令的时候，使用 --mount 标记来将数据卷挂载到容器里。在一次 docker run 中可以挂载多个书卷

删除数据卷

```shell
docker volume rm my-volume
```

如果需要在删除容器的同时移除数据卷，可以在删除容器的时候使用 docker rm -v 这个命令

清理无主的数据卷

```shell
docker volume prune
```

### 挂载主机目录

使用 --mount 标记可以指定挂在一个本地主机的目录到容器中去

```shell
docker run --name session-web -d -p 8888:8080 \
# -v my-volume:/webapp \
--mount type=bind,source=/src/webapp,target=/opt/webapp \
session-web:latest
```

加载主机的 /src/webapp 目录到 /opt/webapp 目录。测试的时候可以放置一些程序到本地目录中，来查看容器是否正常工作

挂载主机目录的默认权限是读写，可以通过增加 readonly 指定为只读

--mount 标记也可以从主机挂载单个文件到容器中

```shell
docker run --rm -it \
# -v $HOME/.bash_history:/root/.bash_history \
--mount type=bind,source=$HOME/.bash_history,target=/root/.bash_history \
ubuntu:17.10 \
bash
```

这样就可以在容器中显示输入过的命令了

## Compose 集成式应用组合

compose 项目负责实现对 Docker 容器集群的快速编排。

可以实现多个容器配合，如加载 Web 服务容器、数据库服务容器、负载均衡服务容器

允许用户通过一个单独的 docker-compose.yml 模板文件来定义一组关联到应用容器为一个项目(project)

有两个重要的概念

1. 服务(service)：一个应用的容器，实际上可以包括若干运行相同镜像的容器实例
2. 项目(projcet)：由一组关联的应用容器组成的一个完整的业务单元

compose 默认管理对象是项目。compose 项目由 Python 编写，实际上调用了 Docker 服务厅的 API 来对容器进行管理

### Linux 安装 docker-compose

#### 二进制包

```shell
sudo curl -L https://github.com/docker/compose/releases/download/1.17.1/docker-compose-'uname -s'-'uname -m' > /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose #赋予可执行权限
```

#### PIP 安装

计算机架构为 ARM ，建议使用 pip 安装

```shell
sudo pip install -U docker-compose
```

### 使用

一个项目可以由多个服务(容器)关联而成，compose 面向项目进行管理

以 compose 构建 dubbo-admin 服务 为例：

#### 获取 dubbo-admin

在 github 上获取 dubbo-admin 的 master 分支源码，修改配置文件，使用 maven 进行打包编译

```shell
mvn clean package -Dmaven.test.skip=true
```

#### dockerfile

在 dubbo-admin 目录下编写 Dockerfile 文件

```shell
FROM openjdk:8-jdk-alpine #使用 jdk1.8 作为基础镜像
MAINTAINER kandigx<kandigx.com>
VOLUME /temp
ADD ./target/dubbo-admin-0.0.1-SNAPSHOT.jar app.jar #拷贝文件并重命名
ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/.urandom", "-jar", "/app.jar"]
```

使用 `docker build -t dubbo-admin:1.0` 命令进行构建

#### docker-compose.yml

在项目根目录下编写 docker-compose.yml 文件

```yml
version: '3.4'
service:
	zk_server:
		image: zookeeper:3.4
		post:
			- 2181:2181
	dubbo-admin:
		image: dubbo-admin:1.0
		links:
			- zk_server:zookeeper
		ports:
			- 7001:7001
```

#### 运行 compose 项目

在 docker-compose.yml 文件所在目录执行

```shell
docker-compose up
```

在浏览器中访问 http://服务器ip:7001 进行验证

### compose 命令说明

``` shell
docker-compose [COMMAND] --help #查看某个具体命令的使用格式，或者
docker-compose help [COMMAND]
```

命令基本使用格式

```shell
docker-compose [-f=<args>...] [options] [COMMAND] [ARGS...]
```

模板文件是使用 compose 的核心，默认名称是 docker-compose.yml，其中常见的指令有

- build 

  指定 Dockerfile 所在文件件的路径，compose 将会利用它自动构建这个镜像然后使用

  ```yml
  version:'3'
  services:
  	webapp:
  		build:
  			context:./dir #路径
  			dockerfile:Dockerfile-alternate #dockerfile名称
  			args:
  				buildno:1
  ```

- command
- container_name
- configs
- deploy
- devices
- depends_on
- dns
- environment
- expose
- extra_hosts
- healthcheck
- image
- labels
- links
- network_mode
- networks
- ports
- volumes
- ulimits
- 