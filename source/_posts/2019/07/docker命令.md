---
title: docker命令
date: 2019-07-10 10:36:58
categories:
  - docker
tags:
  - docker
---
docker命令
<!-- more -->
## 镜像命令
```
// 从docker registry拉取镜像
$ docker pull [option] [docker registry ip:port/] repository[:tag]

// 列出本地的镜像列表
$ docker image ls

// 删除本地镜像
$ docker image rm [option] <image1> [<image2>...] 

// 批量删除本地镜像
$ docker image rm $(docker image ls -q -f before=ubuntu:16.04)

// 查看镜像，容器，数据卷占用的空间
$ docker system df

// 显示虚悬镜像(repository和tag都为none的镜像)
$ docker image ls -f dangling=true

// 删除虚悬镜像
$ docker image prune

// 重命名镜像
$ docker tag <imageId> <newImageName>
```

## 容器命令
// docker run 命令及选项
$ docker run [option] repository:tag [commond] 

-d：后台运行
-i：交互式操作，让容器的标准输入打开
-t：打开一个伪终端，绑定到容器
--name：指定容器的名字
-p：-p ip:hostPort:containerPort[/udp], 将宿主机的端口绑定容器内端口
-P：-P，将宿主机49000～49900的随机端口映射到容器镜像EXPOSE中指定开放的端口
-v：指定宿主机的目录挂载容器镜像VOLUME指定的匿名卷，用于存储动态数据
--network：bridge桥接，host与宿主机一致的网络，none
--network-alias：为--network指定的网络取别名

// 进入到运行时的容器中
$ docker exec -it <container> /bin/bash

// 导出容器为镜像
$ docker export <container> > <exportName>

// 导入容器为镜像
$ docker import <exportName> <imageName>
$ cat <exportName> | docker import - <imageName>
$ docker import <url> <imageName>

// 启动容器并执行命令
$ docker run ubuntu:16.04 /bin/bash "hello world"

// 根据ubuntu:16.04来启动一个容器
$ docker run -it --rm ubuntu:16.04 /bin/bash

-it：这是两个参数，-i：交互式操作，让容器的标准输入保持打开，-t：打开一个终端绑定到容器上
--rm：容器退出后删除这个容器
bash：容器启动后执行的命令，这里是开启一个交互式的shell

// 终止容器
$ docker container stop <container>

// 查看终止状态的容器
$ docker container ls -a

// 启动已终止的容器
$ docker container start
$ docker start

// 重新启动正在运行的容器
$ docker container restart

// 删除容器
$ docker container rm <container>
$ docker rm -f <container>

// 删除所有处于终止状态的容器
$ docker container prune
