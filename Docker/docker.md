[TOC]

# Docker安装

## Docker基本组成

### **镜像（image）**

docker镜像好比一个模板，可以通过模板来创建容器服务，镜像==>run==>容器（提供服务容器），通过这个镜像可以创建多个容器（最终服务运行或者项目运行就是在容器中的）。

### **容器（container）**

Docker 利用容器技术，独立运行一个或者一组应用，通过镜像来创建；启动，停止，删除基本命令；目前就可以把这个容器理解为就是一个建议的linux系统。

### **仓库（repository）**

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

  # 更新yum软件包索引(centos没有fast选项)
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

```bash
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

> https://docs.docker.com/engine/install/centos/

## 容器生命周期管理

### docker run

> 创建一个新的容器并运行一个命令

**语法**

> ```bash
> docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
> ```

**OPTIONS说明**

> - **-a stdin:** 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；
> - **-d:** 后台运行容器，并返回容器ID；
> - **-i:** 以交互模式运行容器，通常与 -t 同时使用；
> - **-P:** 随机端口映射，容器内部端口**随机**映射到主机的端口
> - **-p:** 指定端口映射，格式为：**主机(宿主)端口:容器端口**
> - **-t:** 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
> - **--name="nginx-lb":** 为容器指定一个名称；
> - **--dns 8.8.8.8:** 指定容器使用的DNS服务器，默认和宿主一致；
> - **--dns-search example.com:** 指定容器DNS搜索域名，默认和宿主一致；
> - **-h "mars":** 指定容器的hostname；
> - **-e username="ritchie":** 设置环境变量；
> - **--env-file=[]:** 从指定文件读入环境变量；
> - **--cpuset="0-2" or --cpuset="0,1,2":** 绑定容器到指定CPU运行；
> - **-m :**设置容器使用内存最大值；
> - **--net="bridge":** 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型；
> - **--link=[]:** 添加链接到另一个容器；
> - **--expose=[]:** 开放一个端口或一组端口；
> - **--volume , -v:** 绑定一个卷



### docker start/stop/restart

> 启动一个或多个已经被停止的容器/停止一个运行中的容器/重启容器

**语法**

> ```
> docker start [OPTIONS] CONTAINER [CONTAINER...]
> docker stop [OPTIONS] CONTAINER [CONTAINER...]
> docker restart [OPTIONS] CONTAINER [CONTAINER...]
> ```



### docker kill

> 杀掉一个运行中的容器。

**语法**

> ```
> docker kill [OPTIONS] CONTAINER [CONTAINER...]
> ```

**OPTIONS说明**

> - **-s :**向容器发送一个信号



### docker rm

> 删除一个或多个容器

**语法**

> ```
> docker rm [OPTIONS] CONTAINER [CONTAINER...]
> ```

**OPTIONS说明：**

> - **-f :**通过 SIGKILL 信号强制删除一个运行中的容器。
> - **-l :**移除容器间的网络连接，而非容器本身。
> - **-v :**删除与容器关联的卷。



### docker pause/unpause

> 暂停容器中所有的进程/恢复容器中所有的进程

**语法**

> ```
> docker pause CONTAINER [CONTAINER...]
> docker unpause CONTAINER [CONTAINER...]
> ```



### docker create

> 创建一个新的容器但不启动它

**语法**

> ```
> docker create [OPTIONS] IMAGE [COMMAND] [ARG...]
> ```



### docker exec

> 在运行的容器中执行命令

**语法**

> ```
> docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
> ```

**OPTIONS说明：**

> - **-d :**分离模式: 在后台运行
> - **-i :**即使没有附加也保持STDIN 打开
> - **-t :**分配一个伪终端

## 容器操作

### docker ps

> 列出容器

**语法**

> ```
> docker ps [OPTIONS]
> ```

**OPTIONS说明：**

> - **-a :**显示所有的容器，包括未运行的。
>
> - **-f :**根据条件过滤显示的内容。
>
>   
>
> - **--format :**指定返回值的模板文件。
>
> - **-l :**显示最近创建的容器。
>
> - **-n :**列出最近创建的n个容器。
>
> - **--no-trunc :**不截断输出。
>
> - **-q :**静默模式，只显示容器编号。
>
> - **-s :**显示总的文件大小。



### docker inspect

> 获取容器/镜像的元数据

**语法**

> ```
> docker inspect [OPTIONS] NAME|ID [NAME|ID...]
> ```

**OPTIONS说明：**

> - **-f :**指定返回值的模板文件。
> - **-s :**显示总的文件大小。
> - **--type :**为指定类型返回JSON。



### docker top

> 查看容器中运行的进程信息，支持 ps 命令参数

**语法**

> ```
> docker top [OPTIONS] CONTAINER [ps OPTIONS]
> ```

tips:容器运行时不一定有/bin/bash终端来交互执行top命令，而且容器还不一定有top命令，可以使用docker top来实现查看container中正在运行的进程。



### docker attach

> 连接到正在运行中的容器

**语法**

> ```
> docker attach [OPTIONS] CONTAINER
> ```

**OPTIONS说明：**

> 要attach上去的容器必须正在运行，可以同时连接上同一个container来共享屏幕（与screen命令的attach类似）。官方文档中说attach后可以通过CTRL-C来detach，但实际上经过我的测试，如果container当前在运行bash，CTRL-C自然是当前行的输入，没有退出；如果container当前正在前台运行进程，如输出nginx的access.log日志，CTRL-C不仅会导致退出容器，而且还stop了。这不是我们想要的，detach的意思按理应该是脱离容器终端，但容器依然运行。好在attach是可以带上--sig-proxy=false来确保CTRL-D或CTRL-C不会关闭容器



### docker events

> 从服务器获取实时事件

**语法**

> ```
> docker events [OPTIONS]
> ```

**OPTIONS说明：**

> - **-f ：**根据条件过滤事件；
> - **--since ：**从指定的时间戳后显示所有事件;
> - **--until ：**流水时间显示到指定的时间为止；



### docker logs

> 获取容器的日志

**语法**

> ```
> docker logs [OPTIONS] CONTAINER
> ```

**OPTIONS说明：**

> - **-f :** 跟踪日志输出
> - **--since :**显示某个开始时间的所有日志
> - **-t :** 显示时间戳
> - **--tail :**仅列出最新N条容器日志



### docker wait

> 阻塞运行直到容器停止，然后打印出它的退出代码

**语法**

> ```
> docker wait [OPTIONS] CONTAINER [CONTAINER...]
> ```



### docker export

> 将文件系统作为一个tar归档文件导出到STDOUT

**语法**

> ```
> docker export [OPTIONS] CONTAINER
> ```

**OPTIONS说明：**

> - **-o :**将输入内容写到文件。



### docker port

> 列出指定的容器的端口映射，或者查找将PRIVATE_PORT NAT到面向公众的端口

**语法**

> ```
> docker port [OPTIONS] CONTAINER [PRIVATE_PORT[/PROTO]]
> ```



## 容器rootfs命令

### docker commit

> 从容器创建一个新的镜像

**语法**

> ```
> docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
> ```

**OPTIONS说明：**

> - **-a :**提交的镜像作者；
> - **-c :**使用Dockerfile指令来创建镜像；
> - **-m :**提交时的说明文字；
> - **-p :**在commit时，将容器暂停。



### docker cp

> 用于容器与主机之间的数据拷贝

**语法**

> ```
> docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
> docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH
> ```

**OPTIONS说明：**

> - **-L :**保持源目标中的链接



### **docker diff**

> 检查容器里文件结构的更改

**语法**

> ```
> docker diff [OPTIONS] CONTAINER
> ```



## **镜像仓库**

### docker login/logout

> 登陆到一个Docker镜像仓库，如果未指定镜像仓库地址，默认为官方仓库 Docker Hub/登出一个Docker镜像仓库，如果未指定镜像仓库地址，默认为官方仓库 Docker Hub

**语法**

> ```
> docker login [OPTIONS] [SERVER]
> docker login [OPTIONS] [SERVER]
> ```

**OPTIONS说明：**

> - **-u :**登陆的用户名
> - **-p :**登陆的密码



### docker pull

> 从镜像仓库中拉取或者更新指定镜像

**语法**

> ```
> docker pull [OPTIONS] NAME[:TAG|@DIGEST]
> ```

**OPTIONS说明：**

> - **-a :**拉取所有 tagged 镜像
> - **--disable-content-trust :**忽略镜像的校验,默认开启



### docker push

> 将本地的镜像上传到镜像仓库,要先登陆到镜像仓库

**语法**

> ```
> docker push [OPTIONS] NAME[:TAG]
> ```

**OPTIONS说明：**

> - **--disable-content-trust :**忽略镜像的校验,默认开启



### docker serach

> 从Docker Hub查找镜像

**语法**

> ```
> docker search [OPTIONS] TERM
> ```

**OPTIONS说明：**

> - **--automated :**只列出 automated build类型的镜像；
> - **--no-trunc :**显示完整的镜像描述；
> - **-f <过滤条件>:**列出收藏数不小于指定值的镜像。

## 本地镜像管理

### docker images

> 列出本地镜像

**语法**

> ```
> docker images [OPTIONS] [REPOSITORY[:TAG]]
> ```

**OPTIONS说明：**

> - **-a :**列出本地所有的镜像（含中间映像层，默认情况下，过滤掉中间映像层）；
> - **--digests :**显示镜像的摘要信息；
> - **-f :**显示满足条件的镜像；
> - **--format :**指定返回值的模板文件；
> - **--no-trunc :**显示完整的镜像信息；
> - **-q :**只显示镜像ID。



### docker rmi

>  删除本地一个或多个镜像

**语法**

> ```
> docker rmi [OPTIONS] IMAGE [IMAGE...]
> ```

**OPTIONS说明：**

> - **-f :**强制删除；
> - **--no-prune :**不移除该镜像的过程镜像，默认移除；



### docker tag

>  标记本地镜像，将其归入某一仓库

**语法**

> ```
> docker tag [OPTIONS] IMAGE[:TAG] [REGISTRYHOST/][USERNAME/]NAME[:TAG]
> ```



### docker build

> 命令用于使用 Dockerfile 创建镜像

**语法**

> ```
> docker build [OPTIONS] PATH | URL | -
> ```

**OPTIONS说明：**

> - **--build-arg=[] :**设置镜像创建时的变量；
> - **--cpu-shares :**设置 cpu 使用权重；
> - **--cpu-period :**限制 CPU CFS周期；
> - **--cpu-quota :**限制 CPU CFS配额；
> - **--cpuset-cpus :**指定使用的CPU id；
> - **--cpuset-mems :**指定使用的内存 id；
> - **--disable-content-trust :**忽略校验，默认开启；
> - **-f :**指定要使用的Dockerfile路径；
> - **--force-rm :**设置镜像过程中删除中间容器；
> - **--isolation :**使用容器隔离技术；
> - **--label=[] :**设置镜像使用的元数据；
> - **-m :**设置内存最大值；
> - **--memory-swap :**设置Swap的最大值为内存+swap，"-1"表示不限swap；
> - **--no-cache :**创建镜像的过程不使用缓存；
> - **--pull :**尝试去更新镜像的新版本；
> - **--quiet, -q :**安静模式，成功后只输出镜像 ID；
> - **--rm :**设置镜像成功后删除中间容器；
> - **--shm-size :**设置/dev/shm的大小，默认值是64M；
> - **--ulimit :**Ulimit配置。
> - **--squash :**将 Dockerfile 中所有的操作压缩为一层。
> - **--tag, -t:** 镜像的名字及标签，通常 name:tag 或者 name 格式；可以在一次构建中为一个镜像设置多个标签。
> - **--network:** 默认 default。在构建期间设置RUN指令的网络模式



### docker history

>  查看指定镜像的创建历史

**语法**

> ```
> docker history [OPTIONS] IMAGE
> ```

**OPTIONS说明：**

> - **-H :**以可读的格式打印镜像大小和日期，默认为true；
> - **--no-trunc :**显示完整的提交记录；
> - **-q :**仅列出提交记录ID。



### docker save

> 将指定镜像保存成 tar 归档文件

**语法**

> ```
> docker save [OPTIONS] IMAGE [IMAGE...]
> ```

**OPTIONS说明：**

> - **-o :**输出到的文件。



### docker load

> 导入使用 [docker save](https://www.runoob.com/docker/docker-save-command.html) 命令导出的镜像

**语法**

> ```
> docker load [OPTIONS]
> ```

**OPTIONS说明：**

> - **--input , -i :** 指定导入的文件，代替 STDIN。
> - **--quiet , -q :** 精简输出信息。



### docker import

>  从归档文件中创建镜像

**语法**

> ```
> docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]
> ```

**OPTIONS说明：**

> - **-c :**应用docker 指令创建镜像；
> - **-m :**提交时的说明文字；



## info|version

### docker info

> 显示 Docker 系统信息，包括镜像和容器数

**语法**

> ```
> docker info [OPTIONS]
> ```



### docker  version

> 显示 Docker 版本信息

**语法**

> ```
> docker version [OPTIONS]
> ```

**OPTIONS说明：**

> - **-f :**指定返回值的模板文件。





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

将环境和应用打包成一个镜像。

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

## 安装

1. 下载

   ```shell
   sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   ```

2. 授权可执行文件

   ```shell
   sudo chmod +x /usr/local/bin/docker-compose
   ```

## 体验

1. Create a directory for the project:

   ```shell
    mkdir composetest
    cd composetest
   ```

2. Create a file called `app.py` in your project directory and paste this in:

   ```python
   import time
   
   import redis
   from flask import Flask
   
   app = Flask(__name__)
   cache = redis.Redis(host='redis', port=6379)
   
   def get_hit_count():
       retries = 5
       while True:
           try:
               return cache.incr('hits')
           except redis.exceptions.ConnectionError as exc:
               if retries == 0:
                   raise exc
               retries -= 1
               time.sleep(0.5)
   
   @app.route('/')
   def hello():
       count = get_hit_count()
       return 'Hello World! I have been seen {} times.\n'.format(count)
   ```

3. Create another file called `requirements.txt` in your project directory and paste this in:

   ```
   flask
   redis
   ```

4. Create a Dockerfile：

   ```dockerfile
   # syntax=docker/dockerfile:1
   FROM python:3.7-alpine
   WORKDIR /code
   ENV FLASK_APP=app.py
   ENV FLASK_RUN_HOST=0.0.0.0
   RUN apk add --no-cache gcc musl-dev linux-headers
   COPY requirements.txt requirements.txt
   RUN pip install -r requirements.txt
   EXPOSE 5000
   COPY . .
   CMD ["flask", "run"]
   ```

5.  Define services in a Compose file:

   ```yaml
   version: "3.9"
   services:
     web:
       build: .
       ports:
         - "5000:5000"
     redis:
       image: "redis:alpine"
   ```

## Build and run your app with Compose

```shell
docker-compose up
```

## yaml编写规则

https://docs.docker.com/compose/compose-file/compose-file-v3/









# Docker Swarm



# CI/CD之Jenkins