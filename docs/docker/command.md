---
id: command
title: Docker的常用命令
sidebar_label: Docker的常用命令
---

## 帮助命令

```shell
docker version # 显示Docker的版本信息
docker info # 显示Docker的系统信息，包括镜像和容器的数量
docker <命令> --help # 万能命令
```
命令参考官方文档，[传送门](https://docs.docker.com/reference/)

## 镜像命令

> **`docker images` 查看所有本地的主机上的镜像**

```shell
[root@VM_0_13_centos /]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mongo               latest              105a8b77784b        6 months ago        364MB
nginx               latest              c7460dfcab50        6 months ago        126MB
node                latest              f7756628c1ee        6 months ago        939MB
hello-world         latest              bf756fb1ae65        6 months ago        13.3kB

# 名词解释
REPOSITORY 镜像的仓库源
TAG        镜像的标签
IMAGE ID   镜像的id
CREATED    镜像的创建时间
SIZE       镜像的大小 

# 可选项命令参数
Options:
  -a, --all             # 累出所有的镜像
  -q, --quiet           # 只显示id
```

> **`docker search <image>` 远程搜索镜像**

```shell
[root@VM_0_13_centos /]# docker search mysql
NAME                              DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   9767                [OK]                
mariadb                           MariaDB is a community-developed fork of MyS…   3565                [OK]                
...

# 可选项命令，通过star数来过滤搜索结果
--filter=STARS=3000 # 搜索出来的结果为star数大于等于3000的
```

> **`docker pull <image>` 下载镜像**

```shell
[root@VM_0_13_centos /]# docker pull mysql
Using default tag: latest # 如果不写tag，默认为latest版本
latest: Pulling from library/mysql
6ec8c9369e08: Pull complete # 分层下载，docker image的核心，联合文件系统
177e5de89054: Pull complete 
ab6ccb86eb40: Pull complete 
e1ee78841235: Pull complete 
09cd86ccee56: Pull complete 
78bea0594a44: Pull complete 
caf5f529ae89: Pull complete 
cf0fc09f046d: Pull complete 
4ccd5b05a8f6: Pull complete 
76d29d8de5d4: Pull complete 
8077a91f5d16: Pull complete 
922753e827ec: Pull complete 
Digest: sha256:fb6a6a26111ba75f9e8487db639bc5721d4431beba4cd668a4e922b8f8b14acc # 签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest # 真实下载地址，docker pull mysql 等价 docker pull docker.io/library/mysql:latest

# 指定版本下载
docker pull mysql:5.7
```

> **`docker rmi -f` 删除镜像**

```shell
# 删除单个指定image id的镜像，也可以指定镜像名称
docker rmi -f 105a8b77784b 
# 删除多个指定镜像
docker rmi -f 105a8b77784b 105a8b77784c 105a8b77784d
# 删除所有镜像
docker rmi -f $(docker images -aq) 
```

## 容器命令
说明：有了镜像才可以创建容器。linux，下载一个 centos 镜像来测试学习

`docker pull centos`
**新建容器并启动**
```shell
docker run [可选参数] image

# 参数说明 
--name="NAME"   容器名字，tomcat1, tomcat2...，用来区分容器
-d              后台方式启动容器
-it             使用交互方式运行，进入容器查看内容
-p              指定容器端口 -p 8000:8080
    -p ip:主机端口:容器端口
    -p 主机端口:容器端口（主机端口映射到容器端口，比较常用）
    -p 容器端口
    容器端口
-P              大写P，随机指定端口

# 测试，启动并进入容器，使用bash命令进行交互
[root@VM_0_13_centos /]# docker run -it centos /bin/bash
[root@587456c2b9c2 /]# ls # 查看镜像内的centos，基础版本，很多命令都是不完善的
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@587456c2b9c2 /]# exit # 退出容器命令，退回到主机
```

**列出所有运行中的容器**
```shell
# docker ps 命令
     # 当前正在运行的容器
-a   # 列出当前正在运行的容器和历史运行过的容器
-n=? # 显示最近创建的容器?个容器
-q,-aq   # 只显示容器的编号

[root@VM_0_13_centos /]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@VM_0_13_centos /]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS                            PORTS               NAMES
a26db1e26b28        centos              "/bin/bash"         About a minute ago   Exited (127) About a minute ago                       cranky_lamport
587456c2b9c2        centos              "/bin/bash"         6 minutes ago        Exited (0) 2 minutes ago                              priceless_nightingale
fbcf080184ab        bf756fb1ae65        "/hello"            4 hours ago          Exited (0) 4 hours ago                                zealous_hodgkin
ae07a958fccf        bf756fb1ae65        "/hello"            4 hours ago          Exited (0) 4 hours ago                                quirky_swartz
```

**退出容器**
```shell
exit                # 直接容器停止并退出
control + P + Q     #容器不停止退出
```

**删除容器**
```shell
docker rm 容器id                     # 删除指定容器，不能删除运行中的容器，如果要强制删除，用 docker rm -f 容器id
docker rm -f $(docker ps -aq)       # 删除所有容器
docker ps -a -q|xargs docker rm     # 删除所有容器，等价于上
```

**启动和停止容器的操作**
```shell
docke 容器id       # 启动容器
docker restart 容器id     # 重启容器
docker stop 容器id        # 停止当前运行的容器
docker kill 容器id        # 强制停止容器
``` 

## 常用其他命令
### 后台启动容器
```shell
# 命令 docker run -d 容器名（以后台的方式运行）
# [root@VM_0_13_centos ~]# docker run -d centos

# 问题 docker ps，发现后台方式运行的 centos 容器停止了

# 常见的坑：docker 容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止

# nginx，容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了
```

### 查看日志
```shell
docker logs -f -t --tail 容器id，发现没有日志

# 自己编写一段shell脚本
"while true;do echo shaojie;sleep 1;done"

# [root@VM_0_13_centos ~]# docker ps
CONTAINER ID        IMAGE               
f3db5f35aa9d        centos

# 显示日志
-tf           # 显示日志
--tail number # 要显示日志条数
# [root@VM_0_13_centos ~]# docker logs -tf --tail 10 f3db5f35aa9d
```

### 查看容器中的进程信息
```shell
# 命令 docker top 容器id

# [root@VM_0_13_centos ~]# docker top c70f4fd064fd
UID                 PID                 PPID                C                   STIME               TTY                               
root                10980               10963               0                   22:02               ?                              
root                11509               10980               0                   22:04               ?                              
```

### 查看容器中的元数据
```shell
# 命令
docker inspect 容器id

# 测试
# [root@VM_0_13_centos ~]# docker inspect c70f4fd064fd
[
    {
        "Id": "c70f4fd064fd6c261597459649bd776e83fa4146673c270153eafbe780e3f2a7",
        "Created": "2020-08-02T14:02:12.188114755Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo shaojie;sleep 2;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 10980,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-08-02T14:02:12.461200166Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:831691599b88ad6cc2a4abbd0e89661a121aff14cfa289ad840fd3946f274f1f",
        "ResolvConfPath": "/var/lib/docker/containers/c70f4fd064fd6c261597459649bd776e83fa4146673c270153eafbe780e3f2a7/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/c70f4fd064fd6c261597459649bd776e83fa4146673c270153eafbe780e3f2a7/hostname",
        "HostsPath": "/var/lib/docker/containers/c70f4fd064fd6c261597459649bd776e83fa4146673c270153eafbe780e3f2a7/hosts",
        "LogPath": "/var/lib/docker/containers/c70f4fd064fd6c261597459649bd776e83fa4146673c270153eafbe780e3f2a7/c70f4fd064fd6c261597459649bd776e83fa4146673c270153eafbe780e3f2a7-json.log",
        "Name": "/magical_ellis",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Capabilities": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/7df960a4d3346c9cbc071dbbaa6ff1316a04a8b33552873bebb530da73198dd4-init/diff:/var/lib/docker/overlay2/a93f1a38b742c823248f1c275e81d14c56f3a7e24b1b2699b54cea004eaf7e36/diff",
                "MergedDir": "/var/lib/docker/overlay2/7df960a4d3346c9cbc071dbbaa6ff1316a04a8b33552873bebb530da73198dd4/merged",
                "UpperDir": "/var/lib/docker/overlay2/7df960a4d3346c9cbc071dbbaa6ff1316a04a8b33552873bebb530da73198dd4/diff",
                "WorkDir": "/var/lib/docker/overlay2/7df960a4d3346c9cbc071dbbaa6ff1316a04a8b33552873bebb530da73198dd4/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "c70f4fd064fd",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true;do echo shaojie;sleep 2;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20200611",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "2df80d736fee43830a6179633c873423ebb3b7fced859b81d201bcc4bad54b23",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/2df80d736fee",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "1e4eb718df045e8a1ef4c672f44e36ab00762d752baf292a1bbc67a69d5340e0",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "1d22455d914b9d4b24b12638341377c39bf91cbe5312d484010111e9105f7ce3",
                    "EndpointID": "1e4eb718df045e8a1ef4c672f44e36ab00762d752baf292a1bbc67a69d5340e0",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

### 进入当前正在运行的容器

```shell
我们通常容器都是使用后台方式运行的，需要进入容器，修改一些配置

# 命令
docker exec -it 容器id bashShell（默认的命令行工具，如：/bin/bash）

# 测试
# 方式一
# [root@VM_0_13_centos ~]# docker exec -it c70f4fd064fd /bin/bash
# [root@c70f4fd064fd /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
# [root@c70f4fd064fd /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 14:02 ?        00:00:00 /bin/sh -c while true;do echo shaojie;sleep 2;done
root       498     0  0 14:18 pts/0    00:00:00 /bin/bash
root       518     1  0 14:18 ?        00:00:00 /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 2
root       519   498  0 14:18 pts/0    00:00:00 ps -ef
# [root@c70f4fd064fd /]# ps
  PID TTY          TIME CMD
  498 pts/0    00:00:00 bash
  523 pts/0    00:00:00 ps
  
# 方式二
docker attach 容器id

# 测试
# [root@VM_0_13_centos ~]# docker attach c70f4fd064fd
正在执行当前的代码...


# docker exec   # 进入容器后，进入一个新的终端，可以在里面操作（常用）

# docker attach # 进入容器正在执行的终端，不会启动新的进程！
```

### 从容器内拷贝文件到主机上
```shell
docker cp 容器id:容器内路径 目的的主机路径

# 进入docker容器内部
[root@VM_0_13_centos home]# docker attach 253d664e054d
[root@253d664e054d /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@253d664e054d /]# cd home/
[root@253d664e054d home]# ls
# 在容器内部新建一个文件
[root@253d664e054d home]# touch shaojie_inner.java
[root@253d664e054d home]# ls
shaojie_inner.java
[root@253d664e054d home]# exit
exit
[root@VM_0_13_centos home]# ls
shaojie.java  webapp
[root@VM_0_13_centos home]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                       PORTS               NAMES
253d664e054d        centos              "/bin/bash"         2 minutes ago       Exited (0) 11 seconds ago                        exciting_dhawan
a6439a8bc34c        centos              "/bin/bash"         3 minutes ago       Exited (127) 2 minutes ago                       nice_sanderson
# 将这个文件拷贝出来到我们的主机上
[root@VM_0_13_centos home]# docker cp 253d664e054d:/home/shaojie_inner.java /home
[root@VM_0_13_centos home]# ls
shaojie_inner.java  shaojie.java  webapp
[root@VM_0_13_centos home]# rm -f shaojie.java
[root@VM_0_13_centos home]# ls
shaojie_inner.java  webapp

# 拷贝是一个收到过程，未来我们使用 -v 卷的技术，可以实现，自动同步（双向绑定）
```

### 作业练习
> Docker 安装 nginx

```shell
# 1、搜索镜像 docker search nginx，建议去docker搜索，可以看到帮助文档
# 2、下载镜像 docker pull nginx
# 3、运行测试
[root@VM_0_13_centos /]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              4bb46517cac3        3 weeks ago  
# -d 后台运行
# --name 给容器命名
# -p 宿主机端口，容器内端口，宿主机端口3344可以访问到nginx01容器里的80端口
[root@VM_0_13_centos /]# docker run -d --name nginx01 -p:3344:80 nginx
a1a0a9c1ffbea22d38f711548c4aa57fa3fd75b5557dd62bf5fd6d754e7fdd3e
[root@VM_0_13_centos /]# curl localhost:3344

# 4、进入容器
[root@VM_0_13_centos ~]# docker exec -it nginx01 /bin/bash
root@a1a0a9c1ffbe:/# ls
bin   docker-entrypoint.d   home   media  proc	sbin  tmp
boot  docker-entrypoint.sh  lib    mnt	  root	srv   usr
dev   etc		    lib64  opt	  run	sys   var
```

问题：每次改动nginx配置文件，都需要进入容器内部，十分麻烦，我们要是可以在容器外部提供一个映射路径，达到在容器外部修改文件，容器内部就可以自动修改。（使用数据卷）

> Docker 安装 tomcat

```shell
# 官方使用
docker run -it --rm tomcat:9.0

# 我们之前的启动都是后台，停止了容器之后，容器还是可以查到。 docker run -it --rm，一般用来测试，用完删除

# 下载启动
docker pull tomcat

# 启动运行
docker run -d -p 3355:8080 --name tomcat01 tomcat

# 进入容器
docker exec -it tomcat02 /bin/bash

# 发现问题
1、linux命令少了很多，阉割版
2、没有webapps。腾讯云镜像原因，默认是最小的镜像，所有不必要的都剔除。保证最小的可运行环境。
```
问题：每次改动nginx配置文件，都需要进入容器内部，十分麻烦，我们要是可以在容器外部提供一个映射路径，webapps，我们在外部放置项目，就自动同步到内部就好了！

>部署es + kibana

```shell
# es 暴露的端口很多
# es 十分的耗内存
# es 的数据一般需要放置到安全目录！挂载

# 启动 elasticsearch
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.9.1

# 启动了之后，linux很卡。docker stats 查看 cpu 的状态
# es是十分耗内存的
# 测试es是否成功
[root@VM_0_13_centos /]# curl localhost:9200
{
  "name" : "1d3e07980cae",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "ydi2YuagSeOnrKsRCNTh0w",
  "version" : {
    "number" : "7.9.1",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "083627f112ba94dffc1232e8b42b73492789ef91",
    "build_date" : "2020-09-01T21:22:21.964974Z",
    "build_snapshot" : false,
    "lucene_version" : "8.6.2",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
# 发现 es 占内存很多，关闭之后，增加内存的限制。修改配置文件 -e 环境配置修改
docker run -d --name elasticsearch03 -p 9200:9200 -p 9300:9300 -e ES_JAVA_OPTS="-Xms64m -Xmx512m" -e "discovery.type=single-node" elasticsearch:7.9.1
```
> 使用kibana链接es？思考网络如何才能连接过去！

### 可视化
* portainer（先用这个）

```shell
docker run -d -p 8088:9000 --restart=always -v /var/run/docker.sock --privileged=true portainer/portainer
```

* Rancher（CI/CD在用）

**什么事portainer**
Docker图形化管理工具！提供一个后台面板供我们操作！

```shell
# 安装 portainer
docker run -d -p 8088:9000 portainer/portainer
```