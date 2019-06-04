---
title:  "Docker 安装、命令、Dockerfile 及私有仓库"
permalink: /docker-install-command-dockerfile-registry/
date: 2019-06-04

categories: [基础, 教程]

tags: [docker, linux, install, tutorial, dockerfile, registry]
---

[TOC]

# Docker 安装、命令、Dockerfile及私有仓库

## Docker 安装及卸载

### 卸载旧版本

旧版本为 docker 或者 docker-engine

```shell
sudo yum remove docker docker-common docker-selinux docker-engine
```

### CentOS 安装 Docker

如果使用的是 centos7 minimal 系统，需要安装必要的依赖软件

```shell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

使用 yum 命令安装

```shell
sudo yum install docker-ce
```

### 或者使用 Docker 官方脚本安装

```shell
curl -fsSl https://get.docker.com -o get-docker.sh

sudo sh get-docker.sh --mirror Aliyun
```

### 启动 Docker CE

```shell
sudo systemctl enable docker #设置开机启动
sudo systemctl start docker
```

### 建立 docker 用户组

只用 root 用户和 docker 用户组的用户才能使用 docker 命令

```shell
sudo groupadd docker #docker用户组
sudo usermod -aG docker $USER
```

### 测试 Docker 是否正确安装

```shell
docker run hello-world
```

### CentOS 卸载 Docker

删除 docker 安装包

```shell
sudo yum remove docker-ce
```

删除 docker 镜像

```shell
sudo rm -rf /var/lib/docker
```

## Docker 命令

### 获取镜像

```shell
docker pull [option] [docker registry address[:port]/]repositoryName[:tag]

docker pull --help  #docker pull 的帮助命令 

docker pull ubuntu:16.04 #示例，默认从官方仓库中获取
```

### 运行镜像

```shell
docker run -it --rm ubuntu:16.04 bash
```

-it：两个参数，-i 和 -t，交互式操作 和 终端

--rm：说明容器退出后将其删除

bash：交互式 shell 使用 bash，可以使用  exit 退出容器

### 列出镜像

```shell
docker image ls #列出镜像
docker system df #查看镜像、容器、数据卷所占用的空间
docker image ls -f dangling=true #查看 仓库名、标签均为<none> 的镜像称为虚悬(dangling)镜像
docker image prune # 删除虚悬镜像
```

### 删除本地镜像

```shell
docker image rm [option] <image1> [<image2> ...]
docker image rm $(docker image ls -q ubuntu) #批量删除所有仓库名为 ubuntu 的镜像
docker image rm $(docker image ls -q -f before=ubuntu:16.04) # 删除所有在 ubuntu:16.04 之前的镜像
```

### 容器操作

#### 启动容器

新建并启动容器

```shell
docker run ubuntu:16.04 /bin/echo 'Hello world'
```

启动已终止的容器

```shell
docker container start 

#或者
docker start
```

启动一个 bash 终端，允许用户进行交互

```shell
docker run -t -i ubuntu:16.04 /bin/bash
```

-t 让 docker 分配一个伪终端并绑定到容器的标准输入上，-i 则让容器的标准输入保持打开

#### 后台运行

```shell
docker run -d hello-world #使用 -d 参数
```

#### 停止运行的容器

```shell
docker container stop #终止一个运行中的容器
docker container ls -a #查看终止状态的容器
docker container start # 重新启动终止状态的容器
docker container restart #终止运行中的容器，再重新启动
```

#### 进入容器

使用 `docker exec` 命令可进入到后台运行的容器中，添加 -i 和 -t 参数可以看到命令提示符

```shell
docker exec -it <container id> /bin/bash
```

#### 导出和导入容器

导出本地容器

```shell
docker export <container-id> > export-file-name.tar
```

导入容器

```shell
cat export-file-name.tar | docker import - <image-user>/<image-name>:<image-version> #从容器快照文件中再导入为镜像
docker import http://study.github.com/image.taz example/imagerepo #通过指定 URL 或某个目录来导入
```

#### 删除容器

```shell
docker container rm ubuntu:16.04 #删除处于终止状态的容器
docker container rm -f ubuntu:16.04 #添加 -f 参数，删除运行中的容器。docker 会发送 SIGKILL 信号给容器
```

清理所有处于终止状态的容器

```shell
docker container ls -a #查看所有已创建的容器
docker container prune #清理所有处于终止状态的容器
```

## Dockerfile 定制镜像

镜像的定制实际上就是定制每一层所添加的配置、文件。我们可以把每一层的修改、安装、构建、操作的命令都写入一个脚本，这个脚本就是 Dockerfile

### 以 nginx 为示例：

1. 新建 Dockerfile 文件

```shell
mkdir mynginx
cd mynginx
touch Dockerfile
```

2. 添加 Dockerfile 内容

```bash
FROM nginx #指定基础镜像
RUN echo '<h1>Hello, Docker</h1>' > /usr/share/nginx/html/index.html
```

FROM 指定基础镜像。定制镜像是以一个镜像为基础，在其上进行定制。基础进项是必须指定的。

除基础镜像外，Docker 还存在一个特殊的镜像，scratch，是一个虚拟的概念，并不存在。以该 scratch 为基础镜像的话，意味着不以任何镜像为基础，所有的指令将作为镜像的第一层开始存在

RUN 执行命令，有两种格式

shell 格式：`RUN <command>`

```shell
RUN echo '<h1>Hello, world</h1>' > /usr/share/nginx/html/index.html
```

exec 格式：`RUN ["execuable file", "param1", "param2"]`

```shell
RUN tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1
RUN make -C /usr/src/redis
RUN make -C /usr/src/redis install
```

3. 构建镜像

在 Dockerfile 文件所在目录执行

```shell
docker build -t nginx:v3
docker image ls #查看所有镜像
```

### Dockerfile 指令详解

##### COPY 复制文件

格式：

```shell
COPY <source-path>...<target-path>
COPY ["<source-paht1>", ... , "<target-path>"]
```

COPY 指令将从构建上下文目录中"源路径"的文件/目录复制到新的一层的镜像内的"目标路径"位置，如

````shell
COPY package.json /usr/src/app
COPY home* /mydir/
COPY hom?.txt /mydir/
````

##### ADD 更高级的复制文件

在 COPY 的基础上增加了一些功能。

尽可能的使用 COPY，其语意明确，ADD 则包含了更复杂的功能。最适合 ADD 的场合是需要自动解压缩的场合

可以在所有的文件复制命令中使用 COPY，仅在需要自动解压缩的场合使用 ADD

##### CMD 容器启动命令

CMD 指令的格式和 RUN 相似，有两种格式：

```shell
CMD <command> #shell格式
CMD ["可执行文件", "参数1", "参数2"...] #exec格式
CMD ["参数1", "参数2"...] #参数列表格式。在制定了 ENTRYPOINT 指令后，用 CMD 指定具体的参数
```

Docker 不是虚拟机，容器就是进程。CMD 指定就是用于指定默认的容器主进程启动命令的

##### ENTRYPOINT 入口点

目的和 CMD 一样，都是在制定容器启动程序及参数

##### ENV 设置环境变量

```shell
ENV <key> <value>
ENV <key1>=<value1> <key2>=<value2> ...
```

如：

```shell
ENV VERSION=1.0 DEBUG=ON NAME="HAPPY FEET"
$VERSION #使用环境变量
```

##### ARG 构建参数

与 ENV 一样都是设置环境变量，但其构建的环境变量在将来容器运行时是不会存在这些环境变量的

```shell
ARG <参数名>[=<默认值>]
```

##### VOLUME 定义匿名卷

```shell
VOLUME ["<路径1>", "<路径2>"...]
VOLUME <路径>
```

##### EXPOSE 声明端口

```
EXPOSE <端口1> [<端口2>...]
```

声明运行时容器提供服务端口

##### WORKDIR 指定工作目录

```shell
WORKDIR <工作目录路径>
```

每一个 RUN 都是启动一个容器、执行命令、然后提交存储层文件变更，和 shell 执行命令完全不同

如果要改变之后命令所在的各层的工作目录的位置，应该先使用 WORKDIR 指令变更工作目录

##### USER 指定当前用户

```shell
USER <用户名>
```

USER 改变之后各层的执行 RUN、CMD 以及 ENTRYPOINT 这类命令的身份，如

```shell
RUN groupadd -r redis && useradd -r -g redis redis
USER redis
RUN ["redis-server"]
```

##### HEALTHCHECK 健康检查

```shell
HEALTHCHECK [option] CMD <command> #设置检查容易健康状况的命令
HEALTHCHECK NONE	#如果基础镜像有健康检查命令，可以屏蔽掉其健康检查指令
```

##### ONBUILD 为他人做嫁衣

```shell
ONBUILD <其他指令>
```

特殊的指令，其后面跟的是其他指令。这些指令只有当以当前镜像为基础镜像，去构建下一级镜像的时候才会被执行

##### docker save 和 docker load

将镜像保存为一个 tar 文件，传输到其他位置，然后在加载进来。建议使用 Docker Registry。

## 搭建私有仓库

### 安装运行 docker-registry

通过获取官方 registry 镜像来运行。仓库会默认被创建在 /var/lib/registry 目录下，可以通过 -v 参数指定本地存放路径

```shell
docker run --name registry -d -p 5000:5000 --restart=always -v /opt/data/registry:/var/lib/registry registry
```

### 上传、搜索、下载 镜像

先使用 docker tag 来标记一个镜像，将其推送到仓库

```shell
docker image ls #查看本地镜像
docker tag session-web:latest 127.0.0.1:5000/session-web.latest #标记镜像 tag
docker push 127.0.0.1:5000/session-web:latest #上传镜像
```

tag 镜像的格式为：`docker tag IMAGE[:TAG] [REGISTRY_HOST[:REGISTRY_PORT]/]REPOSITORY[:TAG]`

再下载此镜像

```shell
curl 127.0.0.1:5000/v2/_catalog #如果看到 {"repostories":["session-web"]}，表明镜像已被成功上传
docker image rm 127.0.0.1:5000/session-web:latest #先删除已有镜像，再尝试下载
docker pull 127.0.0.1:5000/session-web:latest #尝试下载
```

若不使用 127.0.0.1 作为仓库地址，使用其他内网ip地址作为仓库地址，会出现无法成功推送镜像的情况。

这是因为 Docker 默认不允许非 HTTPS 方式推送镜像。可以修改 Docker 配置选项取消此限制

使用 systemd 的系统(如 Ubuntu16.04+，Debian 8+，centos7)，在 /etc/docker/daemon.json 中写入以下内容(文件不存在则新建此文件)

```json
{
	"registry-mirror":[
    "https://registry.docker-cn.com"
  ],
  "insecure-registries":[
    "192.168.100.150:5000"
  ]
}
```

