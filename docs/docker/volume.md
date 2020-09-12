---
id: volume
title: 容器数据卷
sidebar_label: 容器数据卷
---

### 什么是容器数据卷
**docker的理念回顾**
将应用和环境打包成一个镜像。
数据？如果数据都在容器中，数据就会丢失！ --需求：数据可以持久化

Mysql，容器删了，删库跑路！--需求：Mysql数据可以存储在本地。
容器之间可以有一个数据可以共享的技术！docker容器中产生的数据，同步到本地。
这就是卷技术。目录到挂载，将我们容器内的目录，挂载到Linux上面。

**总结一句话：容器的持久化和同步操作。容器间也可以数据共享的**

### 使用数据卷
> 方式一：直接使用命令挂载 -v

```shell
docker run -it -v 主机目录:容器内目录

# 测试
[root@VM_0_13_centos home]# docker run -it -v /home/test:/home centos /bin/bash

# 启动起来之后我们可以通过 docker inspect 容器id 查看容器详细信息

# 再测试！
1、停止容器
2、宿主机上修改文件
3、启动容器
4、容器内的数据依旧是同步的
```
好处：我们以后修改只需要在本地修改即可，容器内会自动同步！

### 实战：安装Mysql
思考：Mysql的数据持久化问题

```shell
# 获取镜像
docker pull mysql:5.7

# 运行容器，需要做数据挂载

# 安装启动mysql，需要配置密码，这是要注意点！

# 官方测试，docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

# 启动自己本地的
-d 后台运行
-p 端口映射
-v 数据卷挂载
-e 环境配置
-- name 容器名称
[root@VM_0_13_centos /]# docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7

# 启动成功之后，用Navicat等可视化数据库工具连接上数据库

# 连接到服务器的3310端口，和容器内的3306映射，这个时候就可以连接上了！

# 在本地测试创建一个数据库，查看一下我们映射的路径是否ok!

# 假设将容器删除，挂载到本地上的数据卷依旧没有消失，从而实现了数据持久化功能！
```

### 具名挂载和匿名挂载
```shell
# 匿名挂载
-v 容器内路径
-P 大写P为随机映射端口
docker run -d -P --name nginx01 -v /etc/nginx nginx

# 查看所有 volume 的情况
[root@VM_0_13_centos /]# docker volume ls
DRIVER              VOLUME NAME
local               0ede9857f51c0b9855c9f8651514ae63e50dcf5da03b6c55defb70518c815b8f
local               890991f2a3f67b7d14a72e45ff1498926c560466a6de524c8a2c6959124db679

# 这里发现，这种就是匿名挂载，我们在 -v 只写了容器内的路径，没有写容器外的路径！

# 具名挂载
[root@VM_0_13_centos /]# docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx
DRIVER              VOLUME NAME
local               juming-nginx

# 通过 -v 卷名:容器内路径
# 查看一下这个卷
[root@VM_0_13_centos /]# docker volume inspect juming-nginx
[
    {
        "CreatedAt": "2020-09-06T22:00:12+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/juming-nginx/_data",
        "Name": "juming-nginx",
        "Options": null,
        "Scope": "local"
    }
]
```

所有容器内的卷，没有指定目录的情况下，都是在 `/var/lib/docker/volumes/xxxx/_data` 目录
我们通过具名挂载可以方便的找到我们的一个卷，大多数情况下使用的 `具名挂载`

```shell
# 如何确定是具名挂载还是匿名挂载，还是指定路径挂载？
-v 容器路径 # 匿名挂载
-v 卷名:容器内路径 # 具名挂载
-v /宿主机路径:容器内路径 # 指定路径挂载
```

拓展：
```shell
ro  readonly # 只读
rw  readwrite # 可读可写

# 一旦设置了容器权限，容器对我们挂载出来的内容就有限定了
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:rw nginx

# ro，只要看到ro就说明这个路径只能通过宿主机来操作，容器内部是无法操作的
```

### 初始Dockerfile
Dockerfile 就是用来构建 docker 镜像的构建文件！（命令脚本）
通过这个脚本可以生成镜像，镜像是一层一层的，脚本一个个的命令，每个命令都是一层！

```shell
# 创建一个dockerfile文件，名字可以随意，建议Dockerfile
# 文件中的内容 指令（大写） 参数
From centos

# volume01、volume02这两个为生成镜像的时候自动挂载（匿名挂载）的数据卷目录
# 这些卷和外部一定有个同步的目录
VOLUME ["volume01","volume02"]

CMD echo "----end----"
CMD /bin/bash

# 这里的每个命令，就是镜像的一层

# 启动一下自己写的容器
```
查看一下卷挂载的路径
![-w1265](assets/docker/15994029558072.jpg)
测试一下刚才的文件是否同步出去了？
这种方式未来使用得十分多，因为通常我们会构建自己的镜像。
假设构建镜像的时候没有挂载卷，要手动镜像挂载 -v 卷名:容器内路径

### 数据卷容器
多个mysql同步数据。

```shell
# 启动3个容器，通过我们刚才自己写的镜像启动
```

创建docker01容器
![-w1331](assets/docker/15994033974075.jpg)

```shell
# docker01为docker02的数据卷容器，docker02继承自docker01
# 实现两个数据卷volume01和volume02的数据同步
# 通过 --volumes-from 实现容器间的数据共享
docker run -it --name docker02 --volumes-from docker01 shaojie/centos

# 测试：可以删除docker01，查看一下docker02和docker03是否还可以访问这个文件
# 测试结果：依旧可以访问
```

多个mysql实现数据共享
```shell
[root@1caf4a098d35 volume01]# docker run -d -p 3310:3306 -v /etc/myqsl/conf.d -v /var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7

[root@1caf4a098d35 volume01]# docker run -d -p 3310:3306 -v /etc/myqsl/conf.d -v /var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql02 --volumes-from mysql01 mysql:5.7

# 这个时候，可以实现两个容器数据同步！
```

**结论**
容器之间配置信息的传递，数据卷容器的生命周期一直持续到没有容器使用为止
但是一旦你持久化到本地，这个时候，本地的数据是不会删除的
