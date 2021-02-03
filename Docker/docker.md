

# Docker安装

## Docker基本组成

**镜像（image）**：

docker镜像好比一个模板，可以通过模板来创建容器服务，镜像==>run==>容器（提供服务容器），通过这个镜像可以创建多个容器（最终服务运行或者项目运行就是在容器中的）。

**容器（container）**:

Docker 利用容器技术，独立运行一个或者一组应用，通过镜像来创建；启动，停止，删除基本命令；目前就可以把这个容器理解为就是一个建议的linux系统。

**仓库（repository）**:

仓库就是存放镜像的地方；分为公有和私有；Docker Hub（默认是国外）；可配置国内镜像。

## Docker安装

>环境查看

```shell
# 系统内核是3.10以上
[root@hecs-x-medium-2-linux-20200815230644 ~]# uname -r
3.10.0-1127.18.2.el7.x86_64

[root@hecs-x-medium-2-linux-20200815230644 ~]# cat /etc/os-release 
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

>安装

```shell
  # 1、卸载旧版本
  sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
  # 2、需要的安装包
  sudo yum install -y yum-utils
  # 3、设置镜像仓库
  sudo yum-config-manager \
      --add-repo \
      https://download.docker.com/linux/centos/docker-ce.repo # 默认是国外的
  # 4、安装docker相关的，docker-ce社区 docker-ee 企业版
  sudo yum install docker-ce docker-ce-cli containerd.io

  # 更新yum软件包索引
  yum makecache fast
 
  # 5、启动docker
  systemctl start docker
 
# 6、查看是否安装成功 
 [root@hecs-x-medium-2-linux-20200815230644 ~]# docker version
Client: Docker Engine - Community
 Version:           20.10.2
 API version:       1.41
 Go version:        go1.13.15
 Git commit:        2291f61
 Built:             Mon Dec 28 16:17:48 2020
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.2
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       8891c58
  Built:            Mon Dec 28 16:16:13 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.3
  GitCommit:        269548fa27e0089a8b8278fc4fc781d7f65a939b
 runc:
  Version:          1.0.0-rc92
  GitCommit:        ff819c7e9184c13b7c2607fe6c30ae19403a7aff
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
  
  # 卸载docker，卸载依赖，删除资源 
  sudo yum remove docker-ce docker-ce-cli containerd.io
  sudo rm -rf /var/lib/docker
  
  #docker的默认工作路径/var/lib/docker
```

## 阿里云镜像加速

```shell
# 配置加速
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://6aomei63.mirror.aliyuncs.com"]
}
EOF

sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 底层原理

**Docker是怎么工作的？**

Docker是一个C/S结构，Dokcer的守护进程运行在主机上，通过Socket从客户端访问；

DockerServer接受到命令，执行Client的命令。



# Docker常用命令

## Docker文档地址

https://docs.docker.com/engine/install/centos/

## 帮助命令

```shell
docker version # 显示docker的版本信息
docker info # 显示docker的系统信息，包括镜像和容器
docker command --help # 帮助命令
```

## 镜像命令

**docker images** 查看所有本地的主机上的镜像

```shell
[root@hecs-x-medium-2-linux-20200815230644 ~]# docker images
hello-world                       latest    bf756fb1ae65   12 months ago   13.3kB

# 可选项
-a, --all # 列出所有镜像
-q, --quiet # 值列出镜像的ID
```

**搜索镜像**

```shell
docker search
```

**下载镜像**

```shell
docker pull
```

**删除镜像**

```shell
docker rmi
```

## 容器命令

**新建容器并启动**

```shell
docker run [可选参数] image
# 参数说明
--name 容器名字 
-d 后台方式运行
-it 使用交互方式运行，进入容器查看内容
-p 指定容器端口 -p 8080:8080
-P 随机指定端口
```

**退出容器**

```shell
exit 容器停止
Ctrl + p + q 容器不停止
```

**删除容器**

```shell
docker rm 容器id			# 删除指定容器
docker rm -f $(docker ps -aq)		# 删除所有容器
```

**启动和停止容器操作**

```shell
docker start 容器id
docker restart 容器id
docker stop 容器id
docker kill 容器id
```

## 其它常用命令

**后台启动容器**

```shell
# 命令docker run -d 镜像名
# 常见坑，docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用
```

**查看日志**

```shell
docker logs
```

**查看容器进程信息**

```shell
docker top 容器id
```

**查看镜像元数据**

```shell
docker inspect 容器id
```

**进入当前正在运行的容器**

```shell
docker exec -it 容器id basheshell
docker attach 容器id
```

**从容器内拷贝文件到主机上**

```shell
docker cp 容器id:容器内路径 目的的主机路径
```

**查看images构建历史**

```shell
docker history 镜像id
```

## 小实践

部署**Nginx**

```shell
# 搜索镜像
# docker pull
# docker run
```

## 可视化

- portainer


```shell
docker run -d -p 8088:9000\
--ressart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```



# Docker镜像讲解

## **镜像是什么**

镜像是一种轻量级、可执行的独立软件包，用来打爆软件运行环境换和基于运行环境开发额软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量和配置文件。

所有的应用，直接打包docker镜像。

## Docker镜像加载原理

>UnionFS（联合文件系统）



>Docker镜像加载原理



## 分层理解



# 容器数据卷

## 什么是容器数据卷

**docker的理念回顾**

将环境和应用打爆成一个镜像。

数据持久化，容器之间有一个数据共享技术。

Docker容器中产生的数据，同步到本地。

目录的挂载，将容器内的目录，挂载到Linux中。

## 使用数据卷

> 方式一：直接使用命令来挂载  -v

```shell
docker run -v 主机目录:容器内目录
```

## 具名挂载和匿名挂载



# Dockerfile

## 	什么是Dockerfile

​		Dockerfile是一个包含用于组合映像的命令的文本文档。可以使用在命令行中调用任何命令。 Docker通过读取 `Dockerfile` 中的指令自动生成映像。

​		`docker build` 命令用于从Dockerfile构建映像。可以在 `docker build` 命令中使用 `-f` 标志指向文件系统中任何位置的Dockerfile。



## 	Dockerfile的基本结构

​		Dockerfile 一般分为四部分：基础镜像信息、维护者信息、镜像操作指令和容器启动时执行指令，’#’ 为 Dockerfile 中的注释。



## 	Dockerfile文件说明

​		Docker以从上到下的顺序运行Dockerfile的指令。为了指定基本映像，第一条指令必须是*FROM*。一个声明以`＃`字符开头则被视为注释。可以在Docker文件中		使用`RUN`，`CMD`，`FROM`，`EXPOSE`，`ENV`等指令。

### 		FROM

```shell
# 定基础镜像，必须为第一个命令。
格式：
　　FROM <image>
　　FROM <image>:<tag>
　　FROM <image>@<digest>
示例：
　　FROM mysql:5.6
注：
　　tag或digest是可选的，如果不使用这两个值时，会使用latest版本的基础镜像
```



### 		MAINTAINER

```shell
# 维护者信息。
格式：
    MAINTAINER <name>
示例：
    MAINTAINER Jasper Xu
    MAINTAINER sorex@163.com
    MAINTAINER Jasper Xu <sorex@163.com>
```



### 		RUN

```shell
# 构建镜像时执行的命令。
RUN用于在镜像容器中执行命令，其有以下两种命令执行方式：
shell执行
格式：
    RUN <command>
exec执行
格式：
    RUN ["executable", "param1", "param2"]
示例：
    RUN ["executable", "param1", "param2"]
    RUN apk update
    RUN ["/etc/execfile", "arg1", "arg1"]
注：
　　RUN指令创建的中间镜像会被缓存，并会在下次构建中使用。如果不想使用这些缓存镜像，可以在构建时指定--no-cache参数，如：docker build --no-cache
```



### 		ADD

```shell
# 将本地文件添加到容器，tar类型文件会自动解压（网络压缩资源不会自动被解压），可以访问网络资源，类似wget。
格式：
    ADD <src>... <dest>
    ADD ["<src>",... "<dest>"] 用于支持包含空格的路径
示例：
    ADD hom* /mydir/          # 添加所有以"hom"开头的文件
    ADD hom?.txt /mydir/      # ? 替代一个单字符,例如："home.txt"
    ADD test relativeDir/     # 添加 "test" 到 `WORKDIR`/relativeDir/
    ADD test /absoluteDir/    # 添加 "test" 到 /absoluteDir/
```



### 		COPY

```shell
# 功能类似ADD，但是不会自动解压文件，也不能访问网络资源。
```



### 		CMD

```shell
# 构建容器后调用，也就是在容器启动时才进行调用。
格式：
    CMD ["executable","param1","param2"] (执行可执行文件，优先)
    CMD ["param1","param2"] (设置了ENTRYPOINT，则直接调用ENTRYPOINT添加参数)
    CMD command param1 param2 (执行shell内部命令)
示例：
    CMD echo "This is a test." | wc -
    CMD ["/usr/bin/wc","--help"]
注：
 　　CMD不同于RUN，CMD用于指定在容器启动时所要执行的命令，而RUN用于指定镜像构建时所要执行的命令。
```



### 		ENTRYPOINT

```shell
# 配置容器，使其可执行。配合CMD可省去 `application` ，只使用参数。
格式：
    ENTRYPOINT ["executable", "param1", "param2"] (可执行文件, 优先)
    ENTRYPOINT command param1 param2 (shell内部命令)
示例：
    FROM ubuntu
    ENTRYPOINT ["top", "-b"]
    CMD ["-c"]
注：
　　　ENTRYPOINT与CMD非常类似，不同的是通过docker run执行的命令不会覆盖ENTRYPOINT，而docker run命令中指定的任何参数，都会被当做参数再次传递给ENTRYPOINT。Dockerfile中只允许有一个ENTRYPOINT命令，多指定时会覆盖前面的设置，而只执行最后的ENTRYPOINT指令。
```



### 		LABEL

```shell
# :用于为镜像添加元数据。
格式：
    LABEL <key>=<value> <key>=<value> <key>=<value> ...
示例：
　　LABEL version="1.0" description="这是一个Web服务器" by="IT笔录"
注：
　　使用LABEL指定元数据时，一条LABEL指定可以指定一或多条元数据，指定多条元数据时不同元数据之间通过空格分隔。推荐将所有的元数据通过一条LABEL指令指定，以免生成过多的中间镜像。
```

​		

### 		ENV

```shell
# 设置环境变量。
格式：
    ENV <key> <value>  #<key>之后的所有内容均会被视为其<value>的组成部分，因此，一次只能设置一个变量
    ENV <key>=<value> ...  #可以设置多个变量，每个变量为一个"<key>=<value>"的键值对，如果<key>中包含空格，可以使用\来进行转义，也可以通过""来进行标示；另外，反斜线也可以用于续行
示例：
    ENV myName John Doe
    ENV myDog Rex The Dog
    ENV myCat=fluffy
```



### 	 	EXPOSE

```shell
# 指定与外界交互的端口。
格式：
    EXPOSE <port> [<port>...]
示例：
    EXPOSE 80 443
    EXPOSE 8080
    EXPOSE 11211/tcp 11211/udp
注：
　　EXPOSE并不会让容器的端口访问到主机。要使其可访问，需要在docker run运行容器时通过-p来发布这些端口，或通过-P参数来发布EXPOSE导出的所有端口
```



### 		VOLUME

```shell
# 用于持久化目录。
格式：
    VOLUME ["/path/to/dir"]
示例：
    VOLUME ["/data"]
    VOLUME ["/var/www", "/var/log/apache2", "/etc/apache2"]
注：
　　一个卷可以存在于一个或多个容器的指定目录，该目录可以绕过联合文件系统，并具有以下功能：
　　
    1 卷可以容器间共享和重用
    2 容器并不一定要和其它容器共享卷
    3 修改卷后会立即生效
    4 对卷的修改不会对镜像产生影响
    5 卷会一直存在，直到没有任何容器在使用它
```



### 		WORKDIR

```shell
# 工作目录，类似于cd命令。
格式：
    WORKDIR /path/to/workdir
示例：
    WORKDIR /a  (这时工作目录为/a)
    WORKDIR b  (这时工作目录为/a/b)
    WORKDIR c  (这时工作目录为/a/b/c)
注：
　　通过WORKDIR设置工作目录后，Dockerfile中其后的命令RUN、CMD、ENTRYPOINT、ADD、COPY等命令都会在该目录下执行。在使用docker run运行容器时，可以通过-w参数覆盖构建时所设置的工作目录。
```



### 		USER

```shell
 # 指定运行容器时的用户名或 UID，后续的 RUN 也会使用指定用户。使用USER指定用户时，可以使用用户名、UID或GID，或是两者的组合。当服务不需要管理员权限时，可以通过该命令指定运行用户。并且可以在之前创建所需要的用户。
 格式:
　　USER user
　　USER user:group
　　USER uid
　　USER uid:gid
　　USER user:gid
　　USER uid:group

 示例：
　　USER www

 注：
　　使用USER指定用户后，Dockerfile中其后的命令RUN、CMD、ENTRYPOINT都将使用该用户。镜像构建完成后，通过docker run运行容器时，可以通过-u参数来覆盖所指定的用户。
```



### 		ARG

```shell
# 用于指定传递给构建运行时的变量。
格式：
    ARG <name>[=<default value>]
示例：
    ARG site
    ARG build_user=www
```



### 		ONBUILD

```shell
# 用于设置镜像触发器。
格式：
　　ONBUILD [INSTRUCTION]
示例：
　　ONBUILD ADD . /app/src
　　ONBUILD RUN /usr/local/bin/python-build --dir /app/src
注：
　　当所构建的镜像被用做其它镜像的基础镜像，该镜像中的触发器将会被钥触发
```



​		**总结**：

![d6b684f31fd83a36ca5b37c9dea5550](docker/d6b684f31fd83a36ca5b37c9dea5550.png)



## 实战：构建centos

> 官方centos7 dockerfile

```shell
FROM scratch
ADD centos-7-x86_64-docker.tar.xz /

LABEL \
    org.label-schema.schema-version="1.0" \
    org.label-schema.name="CentOS Base Image" \
    org.label-schema.vendor="CentOS" \
    org.label-schema.license="GPLv2" \
    org.label-schema.build-date="20201113" \
    org.opencontainers.image.title="CentOS Base Image" \
    org.opencontainers.image.vendor="CentOS" \
    org.opencontainers.image.licenses="GPL-2.0-only" \
    org.opencontainers.image.created="2020-11-13 00:00:00+00:00"

CMD ["/bin/bash"]
```



>自定义centos

```shell
# 编写dockerfile文件
FROM centos

MAINTAINER fanhuilin<fanhuilinl@163.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tooles

EXPOSE 80

CMD /bin/bash

# 进行镜像构建
docker build -f filename -t imagename:tags .
```



>CMD 和 ENTRYPOINT 的区别









# Docker 网络

## 理解网络Docker0

> ip addr 查看容器内部网络
>
> 1、本机地址
>
> 2、内网地址
>
> 3、docker0地址



## --link





## 自定义网络

>查看所有网络

```shell
[root@hecs-x-medium-2-linux-20200815230644 ~]# docker network ls
NETWORK ID     NAME          DRIVER    SCOPE
a182d6cca33e   bridge        bridge    local
800d8910ecb5   host          host      local
e46eeaf13378   none          null      local
dad4dcb55e5c   somenetwork   bridge    local
```



**网络模式**

bridge:桥接 docker(默认，自定义使用bridge)

none:不配置网络

host:和宿主机共享网络

contianer:容器内网络连通



**创建自定义网络**

```shell
docker network create --driver bridge --subnet 192.168.0.0/16 --gateway=192.168.0.1 mynet
```



## 网络连通

```shell
[root@hecs-x-medium-2-linux-20200815230644 ~]# docker network connect --help 

Usage:  docker network connect [OPTIONS] NETWORK CONTAINER

Connect a container to a network

Options:
      --alias strings           Add network-scoped alias for the container
      --driver-opt strings      driver options for the network
      --ip string               IPv4 address (e.g., 172.30.100.104)
      --ip6 string              IPv6 address (e.g., 2001:db8::33)
      --link list               Add link to another container
      --link-local-ip strings   Add a link-local address for the containe
```



# Docker Compose



# Docker Swarm



# CI/CD之Jenkins