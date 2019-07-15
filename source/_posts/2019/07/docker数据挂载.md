---
title: docker数据挂载
date: 2019-07-10 16:12:13
categories:
  - docker
tags:
  - docker
---
docker数据挂载的两种方式：数据卷、挂载主机目录
<!-- more -->
## docker数据管理
- 数据卷
- 挂载主机目录

### 数据卷
一个可供一个或多个容器使用的特殊目录，它绕过UFS，提供很多有用的特性：
- 数据卷可以在容器之间共享和重用
- 对数据卷的修改会立马生效
- 对数据卷的更新，不会影响镜像
- 数据卷默认会一直存在，即使容器被删除

两种挂载方式：-v和-mount

```
// 创建数据卷
$ docker volume create my-volume

// 查看所有数据卷
$ docker volumes

// 查看指定数据卷的信息
$ docker volume inspect my-volume

// 启动容器时挂载数据卷
$ docker run --mount source=my-volume,target=/webapp xxx
```

删除容器时不会默认删除挂载的数据卷，使用以下命令使删除容器时同时删除挂载的数据卷
```
$ docker rm -v xxx
```

没有被引用的数据卷可以使用以下命令删除
```
$ docker volume prune
```

{% asset_img sample.png %}
