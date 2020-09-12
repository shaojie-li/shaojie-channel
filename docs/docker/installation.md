---
id: installation
title: 安装Docker
sidebar_label: 安装Docker
---

> 以CentOS为例，[传送门](https://docs.docker.com/engine/install/centos/)
    
1.删除老的docker版本

```shell
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

2.安装docker基本环境，需要的安装包
```shell
yum install -y yum-utils
```

3.设置镜像的仓库
```shell
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```
上面的是国外的，非常慢。使用阿里国内源安装docker
```shell
yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

4.安装Docker引擎（ce为社区版，ee为企业版）
```shell 
# 先更新软件包的索引
yum makecache fast

# 正式安装
yum install docker-ce docker-ce-cli containerd.io

# 安装指定版本
yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
```

5.启动Docker
```shell
systemctl start docker
```

6.验证Docer安装以及启动成功
```shell
# 验证是否安装成功
docker version

# 启动hello-world镜像，测试Docker是否启动
docker run hello-world

# 查看安装的所有镜像
docker images
```

> Docker镜像安装流程原理
![-w994](assets/docker/15957379173128.jpg)


> Docker底层原理
Docker是一个client-sever结构的系统，Docker的守护进程运行在主机上，通过Socket从客户端访问，DockerServer接收到Docker-Client的指令，就会执行这个命令
