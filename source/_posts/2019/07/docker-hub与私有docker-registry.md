---
title: docker-hub与私有docker-registry
date: 2019-07-10 15:39:33
categories:
  - docker
tags:
  - docker
---
docker hub 使用与私有docker registry搭建。
<!-- more -->
## 私有docker registry使用
```
// 使用docker运行一个docker registry
$ docker run --name registry -d -p 5000:5000 --restart=always -v /opt/data/registry:/var/lib/registry registry

// 向私有仓库push一个镜像
$ docker push <imageName>

// 查看私有仓库中的镜像
127.0.0.1:5000/v2/_catalog

// 从私有仓库拉取镜像
$ docker pull 127.0.0.1:5000/<imageName>
```

在/etc/docker/daemon.json添加（注意是修改普通docker服务器而不是docker-registry服务器上的文件）, 来允许其他主机推送docker镜像到本机
```
{
	"insecure-registries": [
		"registryIp:port"
	]
}
```

{% asset_img sample.png %}
