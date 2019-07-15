---
title: 使用dockerfile构建镜像
date: 2019-07-10 11:51:58
categories:
  - docker
tags:
  - docker
---
使用dockerfile构建镜像
<!-- more -->
## FROM
选择基础镜像
```
FROM:scratch,虚拟基础镜像，空白
```
## RUN
执行命令
```
// shell格式
RUN echo "hello world"
// exec格式
RUN exec tar -zxvf redis.tar.gz -C /usr/src/redis --strip-components=1
```
每一个RUN都是启动一个容器、执行命令、然后提交存储层文件变更，所以每次执行一个命令后其上下文环境(比如当前所在目录)都会初始化.

## COPY
```
COPY <源路径>... <镜像内目标路径>
```

## ADD
在COPY基础上增加了一些功能
```
ADD <源路径>... <镜像内目标路径>
```
源路径可以是url，有自动解压缩的场合使用ADD，文件复制使用COPY

## CMD
当docker run时没有指定运行的命令时，CMD将会被执行
```
// shell格式
CMD <命令>
// exec格式
CMD [可执行文件, "参数1", "参数2"]
// 参数列表格式，在指定了ENTRYPOINT指令后，用CMD指定具体的参数
CMD ["参数1", "参数2"]
```
## ENTRYPOINT
通过docker run --entrypoint来指定
当指定了ENTRYPOINT后，CMD的含义就发生了改变，不再是直接运行其命令，而是将CMD的内容作为参数传给ENTRYPOINT指令，即<ENTRYPOINT> "<CMD>"

## ENV
设置环境变量, 后续的指令和运行时的应用，都可以直接用$NAME 来使用这个环境变量
```
ENV <key> <value>
ENV <key1>=<value1> <key2>=<value2>
```
支持的命令：ADD, COPY, ENV, EXPOSE, LABEL, USER, WORKDIR, VOLUME, STOPSIGNAL, ONBUILD

## ARG
构建参数，与ENV相同，但是容器运行时应用中无法使用
```
ARG <参数名>[=<默认值>]
```
默认值可以在构建命令docker build中用--build-arg <参数名>=<值>来覆盖

## VOLUME
定义匿名卷，动态数据不应写入容器存储层（将容器导出为镜像时，不需要这些动态数据），动态数据应写入到数据卷中，容器存储层应该是无状态的。
```
VOLUME /data
```
运行容器时可以覆盖这个挂载设置，比如：
```
$ docker run -d -v ./mydata:/data xxxx
```

## EXPOSE
```
EXPOSE <端口1> [<端口2>...]
```
声明容器内端口，这只是一个声明，在运行时并不会因为这个声明应用就会开启这个端口的服务,需要应用自己开启。
1. 帮助镜像使用者理解这个镜像服务的守护端口，方便配置映射
2. 在使用随机端口映射(docker run -P)时，会自动随机映射EXPOSE的端口

## WORKDIR
指定工作目录（或者称为当前目录），以后镜像各层的但前目录就被改为指定的目录，如该目录不存在，则WORKDIR会帮你建立该目录

## USER
指定工作用户，与WORKDIR类似，影响以后各层的构建过程。
这个用户必须是事先建立好的，否则无法切换。

## HEALTHCHECK 
```
// 设置检查容器健康状况的命令
HEALTHCHECK [选项] CMD <命令>
--interval=<间隔>：两次健康检查的间隔，默认30s
--timeout=<时长>：健康检查命令运行超时时间，如果超过这个时间，本次健康检查就被视为失败，默认30s
--retries=<次数>：当连续失败指定次数后，则容器被视为unhealthy，默认3次

// 如果基础镜像有健康检查指令，可以先屏蔽掉其健康检查指令
HEALTHCHECK NONE
```
## ONBUILD
```
ONBUILD <其他指令>
```
只有当以当前镜像为基础镜像，去构建下一级镜像的时候才会被执行。
ONBUILD时为了帮助别人定制自己而准备的

## RUN、CMD和ENTRYPOINT
- 使用 RUN 指令安装应用和软件包，构建镜像。
- 如果 Docker 镜像的用途是运行应用程序或服务，比如运行一个 MySQL，应该优先使用 Exec 格式的 ENTRYPOINT 指令。CMD 可为 ENTRYPOINT 提供额外的默认参数，同时可利用 docker run 命令行替换默认参数。
- 如果想为容器设置默认的启动命令，可使用 CMD 指令。用户可在 docker run 命令行中替换此默认命令。

## 其他构建镜像的方式
```
$ docker save nginx | gzip > nginx-latest.tat.gz
$ docker load -i nginx-latest.tar.gz
```
{% asset_img sample.png %}
