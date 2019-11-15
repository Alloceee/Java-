## Docker

### 简介

Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从Apache2.0协议开源。
Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。
容器是完全使用沙箱机制，相互之间不会有任何接口,更重要的是容器性能开销极低。

### Docker能做什么

Docker可以解决虚拟机能够解决的问题，同时也能够解决虚拟机由于资源要求过高而无法解决的问题。Docker能处理的事情包括：

- 隔离应用依赖

- 创建应用镜像并进行复制

- 创建容易分发的即启即用的应用

- 允许实例简单、快速的扩展

- 测试应用并随后销毁它们

### Docker架构

| 组成      | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| Images    | Docker镜像，用于创建Docker容器的模板                         |
| Container | Docker容器，独立运行的一个或一组应用                         |
| Client    | Docker客户端，使用Docker Api与Docker的守护进行通信           |
| Host      | Docker主机，一个物理或者虚拟的机器用于执行Docker守护进程和容器 |
| Registry  | Docker仓库，用来保存镜像                                     |
| Machine   | 一个简化Docker安装的命令行工具，比如VirtualBox,Digital Ocean,Microsoft Azure |

![1568981516610](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1568981516610.png)

​	![1568981535933](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1568981535933.png)

三个部件：
镜像（Image）：Docker镜像类似虚拟机的快照，但更加轻量。

创建Docker镜像有几种方式，多数是在一个现有镜像基础上创建新镜像，因为几乎你需要的任何东西都有了公共镜像，包括所有主流Linux发行版。要创建一个镜像，你可以拿一个镜像，对他进行修改来创建它的镜像。实现前述目的的方式有两种：在一个文件中指定一个基础镜像及需要完成的修改；或通过“运行”一个镜像，对其进行修改并提交。不过方式各有优点，一般会使用文件来指定所做的变化。

容器（Container）：从镜像中创建容器，等同于从快照中创建虚拟机，不过更轻量。
仓库（Repository）

例如：
docker pull redis

docker run –d –-name redis redis

![1568981744589](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1568981744589.png)

docker run -d -name redis redis

使用redis镜像，docker run，实例化redis镜像，创建了一个名为redis的容器



### Docker的环境安装

安装： apt install docker.io

![1568983286549](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1568983286549.png)

### 运行第一个docker容器

service docker start 启动docker服务

docker run hello-world 运行镜像

```shell
root@simple:~# docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
1b930d010525: Pull complete
Digest: sha256:b8ba256769a0ac28dd126d584e0a2011cd2877f3f76e093a7ae560f2a5301c00
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

```

### Docker常规用法

#### docker操作

版本/信息     ——docker [version/info]

#### 容器操作

容器生命周期管理    ——docker  [run|start|stop|restart|kill|rm|pause|unpause]

容器操作运维   —— docker  [ps|inspect|exec|logs|export|import|port]

容器rootfs命令   —— docker  [commit|cp|diff]

#### 镜像操作

镜像管理   ——  docker  [images|rmi|tag|build|history|save|import]

#### 仓库管理

镜像仓库   ——  docker  [login|pull|push|search]

### 配置加速器

```shell
vi /etc/docker/daemon.json
```

```json
{
    "registry-mirrors":["https://registry.docker-cn.com"]
}
```

重启docker

```shell
server docker restart
```

### 容器的使用

进入容器

```shell
root@simple:~# docker run -it --name ub ubuntu /bin/bash
root@05ebc9dc4a5f:/#
```

退出容器但是不关闭:Ctrl+P+Q

```shell
root@05ebc9dc4a5f:/# root@simple:~#
```

exit:直接退出会关闭容器

保持一个前台进程，容器就不会退出-->main函数—-结束时，整个java进程也结束

前台进程结束，容器也结束

```shell
root@simple:~# docker run -dti --name bu ubuntu
3f0a1c47cc68ea1c1ee10b99c3a01f0c33846851fcac7f7c281847c8107ea4b7
```

### 镜像层级文件探究

#查看镜像组成

docker history ubuntu

```shell
root@simple:/# docker history ubuntu
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
2ca708c1c9cc        41 hours ago        /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B     
<missing>           41 hours ago        /bin/sh -c mkdir -p /run/systemd && echo 'do…   7B     
<missing>           41 hours ago        /bin/sh -c set -xe   && echo '#!/bin/sh' > /…   745B   
<missing>           41 hours ago        /bin/sh -c [ -z "$(apt-get indextargets)" ]     987kB  
<missing>           41 hours ago        /bin/sh -c #(nop) ADD file:288ac0434f65264f3…   63.2MB 

```

#### 镜像文件列表
docker info 查看配置Docker Root Dir=/var/lib/docker （默认）docker 镜像存放的路径，一般在image/overlay2/imagedb/content/sha256下

![1568995069239](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1568995069239.png)

#### 打开镜像的配置内容
cat f09fe80eb0e75e97b04b9dfb065ac3fda37a8fac0161f42fca1e6fe4d0977c80

----其中，history数组内，标识了镜像的历史记录（与history命令内容对应）
----rootfs的diff_ids中，对应了依赖使用中镜像层文件（history命令中size大于0的层）

![1568995253512](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1568995253512.png)

```shell
//镜像层
root@simple:/var/lib/docker/image/overlay2/layerdb/sha256# ls
0f3d9de16a216bfa5e2c2bd0e3c2ba83afec01a1b326d9f39a5ea7aecc112baf
2957e0a13c30e89650dd6c00644c04aa87ce516284c76a67c4b32cbb877de178
2db44bce66cde56fca25aeeb7d09dc924b748e3adfe58c9cc3eb2bd2f68a1b68
452d665d4efca3e6067c89a332c878437d250312719f9ea8fff8c0e350b6e471
5308e2e4a70bd4344383b8de54f8a52b62c41afb5caa16310326debd1499b748
9476758634326bb436208264d0541e9a0d42e4add35d00c2a7408f810223013d
a1aa3da2a80a775df55e880b094a1a8de19b919435ad0c71c29a0983d64e65db
af0b15c8625bb1938f1d7b17081031f649fd14e6b233688eea3c5483994a66a3
bd416bed302bc2f061a2f6848a565483a5f265932d2d4fa287ef511b7d1151c8
d6aec371927a9d4bfe4df4ee8e510624549fc08bc60871ce1f145997f49d4d37
dab02287e04c8b8207210b90b4056bd865fcfab91469f39a1654075f550c5592
```

```shell
root@simple:/var/lib/docker/image/overlay2/layerdb/sha256/0f3d9de16a216bfa5e2c2bd0e3c2ba83afec01a1b326d9f39a5ea7aecc112baf# ls
cache-id  diff  parent  size  tar-split.json.gz
```



### 镜像总结

-----本质是磁盘上一系列文件的集合

Docker的两大技术：    1、linux的容器方面技术      2、docker镜像技术

![1568993634729](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1568993634729.png)



---------初始挂载时读写层为空。

---------当需要修改镜像内的某个文件时，只对处于最上方的读写层进行了变动，不复写下层已有文件系统的内容，已有文件在只读层中的原始版本仍然存在，但会被读写层中的新版本文件所隐藏，当 docker commit 这个修改过的容器文件系统为一个新的镜像时，保存的内容仅为最上层读写文件系统中被更新过的文件。

---------联合挂载是用于将多个镜像层的文件系统挂载到一个挂载点来实现一个统一文件系统视图的途径，是下层存储驱动(aufs、overlay等) 实现分层合并的方式。

### 容器使用

----------------容器是独立运行的一个或一组应用，以及它们的运行态环境

![1568995343130](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1568995343130.png)

![1568995356939](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1568995356939.png)

容器 -----  读写层文件体系+镜像文件体系 ====》容器内部的操作系统文件环境

镜像文件 --- 只读的

```shell
root@simple:~# docker run -d --name tomcat -p 8080:8080 tomcat
```

-p 将容器的端口绑定到宿主机的端口，访问宿主机的端口即访问容器的端口

### 仓库

docker官方仓库： https://hub.docker.com

Docker  pull/search/login/push/tag
Tag: 标记本地镜像，将其归入某一仓库
Push: 推送镜像到仓库  --需要登陆
Search：在仓库中查询镜像 – 无法查询到tag版本
Pull： 下载镜像到本地
Login：登陆仓库

```shell
# docker tag imagename pluto72/registry1:tagname
# docker push pluto72/registry1:tagname
root@simple:/# docker tag hello-world pluto72/registry1:1.0
root@simple:/# docker push pluto72/registry1:1.0
The push refers to repository [docker.io/pluto72/registry1]
af0b15c8625b: Mounted from library/hello-world
1.0: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
root@simple:/#
```

### 私有仓库

-------使用registry镜像创建私有仓库

![1569050834727](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1569050834727.png)

```shell
# 查询本地仓库中内容
root@simple:/# curl http://47.107.131.246:5000/v2/_catalog
{"repositories":[]}
```

配置 vi  /etc/docker/daemon.json

```json
{
    "registry-mirrors":["https://registry.docker-cn.com"],
    "insecure-registries":["172.16.3.250:5000"]
}
```

不配置push到私有仓库采用https，配置之后采用http

### 数据管理

------Volume数据卷使用

数据卷让你不受容器生命周期影响进行数据持久化。它们表现为容器内的空间，但实际保存在容器之外，从而允许你在不影响数据的情况下销毁、重建、修改、丢弃容器。Docker允许你定义**应用**部分和**数据**部分，并提供工具让你可以将它们分开。使用Docker时必须做出的最大思维变化之一就是：==容器应该是短暂和一次性的==

卷是针对容器的，你可以使用同一个镜像创建多个容器并定义不同的卷。卷保存在运行Docker的宿主文件系统上，你可以指定卷存放的目录，或让Docker保存在默认位置。保存在其他类型文件系统上的都不是一个卷。

卷还可以用来在容器间共享数据。

```json
// docker inspect ubuntu
// mounts 表示挂载
"Mounts": [
            {
                "Type": "volume",
                "Name": "b6103a7496ef4605e85c320a73c0b01a0dfcb56c4bd18fe380964da728b5aeff",
                //宿主机对应的位置
                "Source": "/var/lib/docker/volumes/b6103a7496ef4605e85c320a73c0b01a0dfcb56c4bd18fe380964da728b5aeff/_data",
                //容器中挂载的位置
                "Destination": "/opt/data",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],

```

> 宿主机与容器内容同步

docker数据卷默认位置/var/lib/docker/volumes

![1569055112268](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1569055112268.png)

docker run -it --name centOs1 --volumes-from dataos centos

两个容器共享

#### 镜像的创建

commit

### 链接

链接是Docker的另一个重要部分。

容器启动时，将被分配一个随机的私有IP，其它容器可以使用这个IP地址与其进行通讯。这点非常重要，原因有二：一是它提供了容器间相互通信的渠道，二是容器将共享一个本地网络。我曾经碰到一个问题，在同一台机器上为两个客户启动两个elasticsearch容器，但保留集群名称为默认设置，结果这两台elasticsearch服务器立马变成了一个自主集群。注：限制容器间通讯是可行的，请阅读Docker的高级网络文档做进一步了解。

要开启容器间通讯，Docker允许你在创建一个新容器时引用其它现存容器，在你刚创建的容器里被引用的容器将获得一个（你指定的）别名。我们就说，这两个容器链接在了一起。&oq=链接是Docker的另一个重要部分。

容器启动时，将被分配一个随机的私有IP，其它容器可以使用这个IP地址与其进行通讯。这点非常重要，原因有二：一是它提供了容器间相互通信的渠道，二是容器将共享一个本地网络。我曾经碰到一个问题，在同一台机器上为两个客户启动两个elasticsearch容器，但保留集群名称为默认设置，结果这两台elasticsearch服务器立马变成了一个自主集群。注：限制容器间通讯是可行的，请阅读Docker的高级网络文档做进一步了解。

要开启容器间通讯，Docker允许你在创建一个新容器时引用其它现存容器，在你刚创建的容器里被引用的容器将获得一个（你指定的）别名。我们就说，这两个容器链接在了一起。

### Dockerfile

---------Dockerfile是一个文本格式的配置文件，用户可以使用Dockerfile快速创建自定义镜像

基础镜像、维护者信息、操作指令、容器CMD
dockerfile的指令分为两种：构建指令和设置指令。构建命令：用于构建镜像的时候执行的，不会在该镜像上的容器里执行。 
设置命令：用于设image的属性，将会在运行的容器里执行。

![1569055842716](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1569055842716.png)

run 就是增加一个镜像层

![1569056958218](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1569056958218.png)	

#### CMD  VS  ENTRYPOINT
 --------- cmd给出的是一个容器的默认的可执行体
 --------- entrypoint才是正统地用于定义容器启动以后的执行体的

##### CMD---多个cmd最后一个生效
-----shell用法： CMD echo "hello cmd!“
----- exec用法：CMD ["/bin/bash", "-c", "echo 'hello cmd!'"]
##### ENTRYPOINT---容器入口

-----shell用法（不接受参数，不推荐）： CMD ["p in cmd"]
			ENTRYPOINT echo
----- exec用法：CMD ["p in cmd"]
		ENTRYPOINT ["echo"]



Docker网络与存储
	docker的网络模型
	容器间的通信
	docker的数据卷使用
DockerCompose使用
	多容器组合使用的背景介绍
	Docker Compose 安装
	docker-compose 部署服务

