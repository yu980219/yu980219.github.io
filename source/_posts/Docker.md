---
title: Docker基础
tags: [docker]
categories:
  - 运维
index_img: /img/docker.png
date: 2021-02-02 21:45:54
---
# Docker

> 弱小和无知不是生存的障碍，傲慢才是。

## 一、Docker概述

### 1.1 Docker为什么出现？

一款产品： 开发–上线 两套环境！应用环境，应用配置！（运维）

环境配置是十分的麻烦，每一个机器都要部署环境(集群Redis、ES、Hadoop…) !费事费力。而且也不能跨平台

于是就想：发布一个项目( jar + (Redis MySQL JDK ES) )，项目能不能带上环境安装打包！

传统：开发jar，运维来做！

现在：Docker提出了解决方案！开发打包部署上线，一套流程做完！

安卓流程：java — apk —发布（应用商店）一 张三使用apk一安装即可用！

docker流程： java-jar（环境） — 打包项目帯上环境（镜像） — ( Docker仓库：商店）–下载我们发布的镜像—直接运行即可

![在这里插入图片描述](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411240258306.png)

Docker的思想就来自于集装箱！

JRE – 多个应用(端口冲突) – 原来都是交叉的！

隔离：Docker核心思想！打包装箱！每个箱子是互相隔离的。

Docker通过隔离机制，可以将服务器利用到极致

本质：所有的技术都是因为出现了一些问题，我们需要去解决，才去学习

### 1.2 Docker的历史

2010年，几个搞IT的年轻人，在美国成立了一家公司`dotCloud`

做一些paas的云计算服务，LXC有关的容器技术。

他们将自己的技术（容器化技术）命名为Docker。

Docker刚刚诞生的时候，没有引起行业的注意。dotCcloud，就活不下去。

2013年，Docker**开源**了！

Docker越来越多的人发现了Docker的优点！火了！Docker每个月都会更新一个版本。

2013年4月9日，Docker1.0发布。

Docker为什么这么火？十分的轻巧。

在容器技术出来之前，我们都是使用虚拟机技术。

虚拟机：在windows中装一个Vmware，通过这个软件我们可以虚拟出来一台或者多台电脑，十分笨重。

虚拟机也是属于虚拟化技术，Docker容器技术，也是一种虚拟化技术。

```
vm：linux centos原生镜像（一个电脑！）隔离：需要开启多个虚拟机，几个G，几分钟启动。
docker：隔离，镜像（最核心的环境 4m + jdk + mysql）十分的小巧，运行镜像就可以了，几个M，KB，秒级启动。
```

> 聊聊docker：

Docker是基于Go语言开发的，开源项目。

- 官网：https://www.docker.com/

![docker官网](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230418000159864.png)

- 文档地址：https://docs.docker.com/ Docker的文档是超级详细的！

- 仓库地址：https://hub.docker.com/ git命令在这里都可以使用

### 1.3 Docker能干嘛

> 之前的虚拟机技术

![之前的虚拟机技术](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230418002123031.png)

虚拟机技术缺点：

1. 资源占用十分多
2. 冗余步骤非常多
3. 启动很慢

> 容器化技术

![容器化技术](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230418002340896.png)

容器化技术不是模拟的一个完整的操作系统

比较Docker和虚拟机技术的不同：

- 传统虚拟机，虚拟出一个硬件，运行一个完整的操作系统，然后在这个系统上安装和运行软件。
- 容器内的应用直接运行在宿主机的内核中，容器是没有自己的内核的，也没有虚拟我们的硬件，所以就轻便了。
- 每个容器间是互相隔离的，每个容器内都有一个属于自己的文件系统，互不影响。

> DevOps（开发、运维）

**1. 应用更快速的交付和部署**

传统：一堆帮助文档，安装程序。

Docker：打包镜像，发布测试，一键运行。

**2. 更便捷的升级和扩缩容**

使用了Docker之后，我们部署应用就和搭积木一样。

项目打包为一个镜像，扩展。

**3. 更简单的系统运维**

在容器化之后，我们的开发，测试环境都是高度一致的。

**4. 更高效的计算资源利用**

1核2G的服务器，可以同时运行几十个Tomcat。

Docker是内核级别的虚拟机化，可以在一个物理机上运行很多的容器实例。

服务器的性能可以被压榨到极致。

## 二、Docker安装

### 2.1 Docker的基本组成

![Docker架构图](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411240259585.png)
**镜像（image)**：

docker镜像就好比一个模板，可以通过这个模板来创建容器服务，tomcat镜像===>run===>tomcat01容器（提供服务器），可通过镜像创建多个容器。

**容器（container）**：

Docker利用容器技术，独立运行一个或者一组应用，通过镜像来创建的。

启动、停止、删除、基本命令。

目前就可以把这个容器理解为一个简易的linux系统。

**仓库（repository）**：

存放镜像的地方

仓库分为公有仓库和私有仓库

DockerHub（默认是国外的）、阿里云都有容器服务器（配置镜像加速）

-----

*PS：镜像就像APP安装包，容器就像APP应用*

### 2.2 安装Docker

> 环境准备：Linux环境
>
> 环境查看：

```shell
# 系统内核是3.10以上的
[root@master ~]# uname -r
3.10.0-1160.el7.x86_64
```

```shell
# 系统版本
[root@master ~]# cat /etc/os-release
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

> 安装

帮助文档：

```shell
# 1.卸载旧版本
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
                  
# 2.需要的安装包
yum install -y yum-utils

# 3.设置镜像的仓库
# 默认是国外的，不推荐
yum-config-manager \ 
	--add-repo \ 	
	https://download.docker.com/linux/centos/docker-ce.repo
# 推荐使用国内的	（这里手敲代码然后复制网站）
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 4.更新yum软件包索引 
yum makecache fast

# 5.安装docker相关的内容 docker-ce 社区版 ee 是企业版
yum install docker-ce docker-ce-cli containerd.io

# 6、启动docker
systemctl start docker

# 7、使用 docker version 查看是否安装成功
docker version
```

![docker version](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230418010541255.png)

```shell
# 测试
docker run hello-world
```

![docker run hello-world](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230418010937780.png)

```shell
# 查看下载的这个 hello-world 镜像
docker images
```

![docker images](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230418011058247.png)

卸载docker

```shell
# 1.卸载依赖
yum remove docker-ce docker-ce-cli containerd.io

# 2.删除资源
rm -rf /var/lib/docker

# /var/lib/docker：docker的默认工作路径
```

### 2.3 阿里云镜像加速

阿里云镜像加速：https://www.anquanclub.cn/6132.html

Docker配置国内镜像源加速教程：https://www.runoob.com/docker/docker-mirror-acceleration.html（建议使用阿里云镜像加速）

*高可用的镜像配置*：

```json
{
    "registry-mirrors": [
        "https://bjtzu1jb.mirror.aliyuncs.com",
        "http://f1361db2.m.daocloud.io",
        "https://hub-mirror.c.163.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://reg-mirror.qiniu.com",
        "https://dockerhub.azk8s.cn",
        "https://registry.docker-cn.com"
    ]
}
```

![配置阿里云镜像后的docker](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230420002709854.png)

### 2.4 回顾Hello World流程

![run的运行流程图](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230419234350846.png)

### 2.5 底层原理

**Docker是怎么工作的？**

Docker是一个 Client - Server 结构的系统，Docker 的守护进程运行在主机上。通过 Socket 从客户端访问！

Docker-Server 接收到 Docker-Client 的指令，就会执行这个命令！

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230419234649418.png)

**Docker 为什么比 VM 快？**

1. Docker 有着比虚拟机更少的抽象层
2. Docker 利用的是宿主机的内核，VM 需要的是 Guest OS

![VM和Docker的区别](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411240300955.png)

所以说，新建一个容器的时候，docker 不需要像虚拟机一样重新加载一个操作系统内核，**避免引导和加载操作系统内核**。虚拟机是加载 Guset OS , 分钟级别的，而docker是利用宿主机的操作系统，省略了这个复杂的过程，秒级的，因此新建一个docker容器只需要几秒钟。

|            | Docker容器      | LXC         | VM         |
| ---------- | --------------- | ----------- | ---------- |
| 虚拟化类型 | OS虚拟化        | OS虚拟化    | 硬件虚拟化 |
| 性能       | =物理机性能     | =物理机性能 | 5%-20%损耗 |
| 隔离性     | NS隔离          | NS隔离      | 强         |
| QoS        | Cgroup弱        | Cgroup弱    | 强         |
| 安全性     | 中              | 差          | 强         |
| GuestOS    | 只支持Linux+win | 只支持Linux | 全部       |
| 可迁移性   | 强              | 弱          | 强         |

## 三、Docker的常用命令

### 3.1 帮助命令

```shell
docker version	# 显示docker的版本信息。
docker info		# 显示docker的系统信息，包括镜像和容器的数量
docker --help	# 帮助命令
```

帮助文档的地址：https://docs.docker.com/engine/reference/commandline/

### 3.2 镜像命令

- **docker images** 查看所有本地的主机上的镜像

```shell
[root@master ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    feb5d9fea6a5   19 months ago   13.3kB

# 解释
REPOSITORY 		# 镜像的仓库源 
TAG 			# 镜像的标签 
IMAGE ID 		# 镜像的id 
CREATED 		# 镜像的创建时间 
SIZE 			# 镜像的大小

# 可选项 Options:
	-a, --all      # 列出所有的镜像
	-q, --quiet    # 只显示镜像的id
```

- **docker search** 搜索镜像

```shell
[root@master ~]# docker search mysql
NAME                            DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                           MySQL is a widely used, open-source relation…   14050     [OK] 
mariadb                         MariaDB Server is a high performing open sou…   5367      [OK] 

# 可选项
--filter=STARS=3000 搜索出来的镜像就是STARS大于3000的

[root@master ~]# docker search mysql --filter=STARS=8000
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql     MySQL is a widely used, open-source relation…   14050     [OK]
```

- **docker pull** 下载镜像

```shell
# 下载镜像 docker pull 镜像名[:tag]		（tag是版本）
[root@master ~]# docker pull mysql
Using default tag: latest	# 如果不写tag,默认就是latest最新版
latest: Pulling from library/mysql
72a69066d2fe: Pull complete	# 分层下载
93619dbc5b36: Pull complete
99da31dd6142: Pull complete
626033c43d70: Pull complete
37d5d7efb64e: Pull complete
ac563158d721: Pull complete
d2ba16033dad: Pull complete
688ba7d5c01a: Pull complete
00e060b6d11d: Pull complete
1c04857f594f: Pull complete
4d7cfa90e6ea: Pull complete
e0431212d27d: Pull complete
Digest: sha256:e9027fe4d91c0153429607251656806cc784e914937271037f7738bd5b8e7709
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest


# 两条命令等价
docker pull mysql
docker.io/library/mysql:latest

# 指定版本下载
docker pull mysql:5.7
```

下载完mysql lastest版本再下载5.7版本会出现 Already exists，这是两个版本冲突的包，如果本地已经有了，就不必再次下载了，这是docker的分层思想。

![docker pull mysql:5.7](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230420005418921.png)

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230420005626787.png)

- **docker rmi** 删除镜像

```shell
docker rmi -f 镜像id                # 删除指定的镜像
docker rmi -f 镜像id 镜像id 镜像id   # 删除多个镜像
docker rmi -f $(docker images -aq)  # 删除全部镜像
```

### 3.3 容器命令

**说明：我们有了镜像才可以创建容器，linux，下载一个 centos 镜像来测试学习**

```shell
docker pull centos
```

```shell
docker run [可选参数] image

# 参数说明
--name = "Name"    容器名字  tomcat01，tomcat02,用来区分容器
-d                 后台方式运行
-it                使用交互方式运行，进入容器查看内容
-p                 指定容器的端口 -p 8080：8080
    -p ip:主机端口：容器端口
    -p 主机端口：容器端口(常用)
    -p 容器端口
    容器端口
-P                 随机指定端口
```
- 测试，启动并进入容器

```shell
# 测试，启动并进入容器
[root@master ~]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
centos       latest    5d0da3dc9764   19 months ago   231MB

[root@master ~]# docker run -it centos /bin/bash

# 查看容器内的centos，基础版本，很多命令都是不完善的！
[root@af0d062b3221 /]# ls
bin  etc   lib    lost+found  mnt  proc  run   srv  tmp  var dev  home  lib64  media       opt  root  sbin  sys  usr
```
- **从容器中退回主机**

```shell
[root@af0d062b3221 /]# exit
exit
[root@master ~]# ls
anaconda-ks.cfg  initial-setup-ks.cfg
```

- **列出所有的运行的容器**

```shell
# docker ps 命令
       # 列出当前正在运行的容器
  -a   # 列出当前正在运行的容器+带出历史运行过的容器
  -n=? # 显示最近创建的容器	？代表个数
  -q   # 只显示容器的编号
```

![docker ps](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230420011029449.png)

- **退出容器**

```
exit            # 直接容器停止并退出
Ctrl + P + Q    # 容器不停止退出
```

- **删除容器**

```shell
docker rm 容器id                  # 删除指定容器，不能删除正在运行的容器，如果要强制删除 rm -f	（f是force）
docker rm -f $(docker ps -aq)    # 删除所有的容器
docker ps -aq|xargs docker rm    # 删除所有的容器
```

- **启动和停止容器的操作**

```shell
docker start 容器id        # 启动容器
docker restart 容器id      # 重启容器
docker stop 容器id         # 停止当前正在运行的容器
docker kill 容器id         # 强制停止当前容器
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230420012014388.png)

### 3.4 常用其它命令

- **后台启动容器**

```shell
# 命令 docker run -d 镜像名
docker run -d centos

# 问题docker ps, 发现 centos 停止了

# 常见的坑, docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
# 如nginx，容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230420224011278.png)

- **查看日志**

```shell
# 自己编写一段shell脚本
[root@master ~]# docker run  -d centos /bin/sh -c "while true;do echo 666;sleep 1;done"

# 显示日志
-t                # 显示日志加时间
-f				  # 保留打印窗口，持续打印
--tail number     # 要显示的最后的日志条数

da3008d5641cad5839f4a354bd933c0cbd5363aba11050b31ccebdf473754396
[root@master ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
da3008d5641c   centos    "/bin/sh -c 'while t…"   8 seconds ago   Up 8 seconds             boring_pare
[root@master ~]# docker logs -tf --tail 10 da3008d5641c
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230420225009481.png)

- **查看容器中的进程信息**

```shell
# 命令 docker top 容器id
[root@master ~]# docker top da3008d5641c
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                14853               14834               0                   22:49               ?                   00:00:00            /bin/sh -c while true;do echo 666;sleep 1;done
root                15155               14853               0                   22:52               ?                   00:00:00            /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
```

- **查看镜像源数据**

```shell
# docker inspect 容器ID
[root@master ~]# docker inspect da3008d5641c
[
    {
        "Id": "da3008d5641cad5839f4a354bd933c0cbd5363aba11050b31ccebdf473754396",
        "Created": "2023-04-20T14:49:20.020151013Z",	# 容器创建时间
        "Path": "/bin/sh",
        "Args": [	# 传递的参数
            "-c",
            "while true;do echo 666;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 14853,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2023-04-20T14:49:20.496770061Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:5d0da3dc976460b72c77d94c8a1ad043720b0416bfc16c52c45d4847e53fadb6", # 从哪个镜像过来的
        "ResolvConfPath": "/var/lib/docker/containers/da3008d5641cad5839f4a354bd933c0cbd5363aba11050b31ccebdf473754396/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/da3008d5641cad5839f4a354bd933c0cbd5363aba11050b31ccebdf473754396/hostname",
        "HostsPath": "/var/lib/docker/containers/da3008d5641cad5839f4a354bd933c0cbd5363aba11050b31ccebdf473754396/hosts",
        "LogPath": "/var/lib/docker/containers/da3008d5641cad5839f4a354bd933c0cbd5363aba11050b31ccebdf473754396/da3008d5641cad5839f4a354bd933c0cbd5363aba11050b31ccebdf473754396-json.log",
        "Name": "/boring_pare",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {	# 主机的配置
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
            "ConsoleSize": [
                34,
                133
            ],
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
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
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": [],
            "BlkioDeviceWriteBps": [],
            "BlkioDeviceReadIOps": [],
            "BlkioDeviceWriteIOps": [],
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
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
                "LowerDir": "/var/lib/docker/overlay2/c596744e1c8bb435f4c9162c9d90723e1edc3293ba2c4e27f5ccfdba3eb1b719-init/diff:/var/lib/docker/overlay2/beaebc6f6613189b207a39e4be551dc60d5d3a096e9612b32e92ca4868fc9edd/diff",
                "MergedDir": "/var/lib/docker/overlay2/c596744e1c8bb435f4c9162c9d90723e1edc3293ba2c4e27f5ccfdba3eb1b719/merged",
                "UpperDir": "/var/lib/docker/overlay2/c596744e1c8bb435f4c9162c9d90723e1edc3293ba2c4e27f5ccfdba3eb1b719/diff",
                "WorkDir": "/var/lib/docker/overlay2/c596744e1c8bb435f4c9162c9d90723e1edc3293ba2c4e27f5ccfdba3eb1b719/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],	# 挂载
        "Config": {
            "Hostname": "da3008d5641c",
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
                "while true;do echo 666;sleep 1;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20210915",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {	# 网络配置
            "Bridge": "",
            "SandboxID": "0b38c1107b4c23d89525c9baad9a0dc5691910e1b4b2e4bea989dd4f50142316",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/0b38c1107b4c",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "ef9d43cda8b318ad2890785fae450a8dff6c19fa05c0bc0586338f62cbabd1f4",
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
                    "NetworkID": "3aa02576742d371bf5cdea1d676c799a7f9d3ac976870b882993dd10280324f0",
                    "EndpointID": "ef9d43cda8b318ad2890785fae450a8dff6c19fa05c0bc0586338f62cbabd1f4",
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

- **进入当前正在运行的容器**

```shell
# 我们的容器通常都是使用后台方式运行的，若要进入容器，需要修改一些配置

# 命令
docker exec -it 容器id baseShell

# 测试
[root@master ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
da3008d5641c   centos    "/bin/sh -c 'while t…"   12 minutes ago   Up 12 minutes             boring_pare
[root@master ~]# docker exec -it da3008d5641c /bin/bash
[root@da3008d5641c /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@da3008d5641c /]# ps -ef
UID         PID   PPID  C STIME TTY          TIME CMD
root          1      0  0 14:49 ?        00:00:00 /bin/sh -c while true;do echo 666;sleep 1;done
root        752      0  0 15:01 pts/0    00:00:00 /bin/bash
root        808      1  0 15:02 ?        00:00:00 /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
root        809    752  0 15:02 pts/0    00:00:00 ps -ef

# 方式二
docker attach 容器ID

# 测试
[root@master ~]# docker attach da3008d5641c
666
666

# docker exec        # 进入容器后开启一个新的终端，可以在里面操作(常用)
# docker attach      # 进入容器正在执行的终端，不会启动新的进程
```

- **从容器内拷贝到主机上**

```shell
# 命令
docker cp [r] 容器id :容器内路径 目的地主机路径
# 参数r : 递归拷贝

# 启动容器
[root@master home]# docker start 0a179a4eb26e
0a179a4eb26e

[root@master home]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
0a179a4eb26e   centos    "/bin/bash"   2 minutes ago   Up 3 seconds             practical_wozniak

# 进入docker容器内部
[root@master home]# docker attach 0a179a4eb26e
[root@0a179a4eb26e /]# cd /home/
[root@0a179a4eb26e home]# ls

# 在容器内新建一个文件
[root@0a179a4eb26e home]# touch test.java
[root@0a179a4eb26e home]# exit
exit

[root@master home]# docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS                     PORTS     NAMES
0a179a4eb26e   centos    "/bin/bash"   3 minutes ago   Exited (0) 4 seconds ago             practical_wozniak

# 将文件拷贝出来到主机上
[root@master home]# docker cp 0a179a4eb26e:/home/test.java /home
Successfully copied 1.536kB to /home
[root@master home]# ls
data  pflm  test.java  xiaoyu
```

### 3.5 小结

![docker命令图](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411240300869.png)

```shell
attach 		Attach local standard input, output, and error streams to a running container	# 当前 shell 下 attach 连接指定运行镜像 
build 		Build an image from a Dockerfile	# 通过 Dockerfile 定制镜像 
commit 		Create a new image from a container's changes	# 提交当前容器为新的镜像 
cp 			Copy files/folders between a container and the local filesystem	# 从容器中拷贝指定文件或者目录到宿主机中 
create 		Create a new container	# 创建一个新的容器，同run，但不启动容器 
diff 		Inspect changes to files or directories on a container's filesystem	# 查看docker容器变化 
events 		Get real time events from the server	# 从 docker 服务获取容器实时事件
exec 		Run a command in a running container	# 在已存在的容器上运行命令
export 		Export a container's filesystem as a tar archive	# 导出容器的内容作为一个 tar 归档文件[对应import] 
history 	Show the history of an image	# 显示一个镜像形成历史 
images 		List images	# 列出系统当前的镜像 
import 		Import the contents from a tarball to create a filesystem image	# 从tar包中导入内容创建一个新的文件系统映像[对应export] 
info 		Display system-wide information	# 显示系统相关信息 
inspect 	Return low-level information on Docker objects	# 查看容器详细信息 
kill 		Kill one or more running containers	# kill 指定 docker 容器 
load 		Load an image from a tar archive or STDIN	# 从一个 tar 包加载一个镜像[对应save] 
login 		Log in to a Docker registry	# 注册或登录一个 docker 源服务器
logout 		Log out from a Docker registry	# 从当前 Docker registry 退出
logs 		Fetch the logs of a container	# 输出当前容器日志信息
pause 		Pause all processes within one or more containers	# 暂停容器
port 		List port mappings or a specific mapping for the container	# 查看映射端口对应的容器内部源端口
ps 			List containers	# 列出容器列表
pull 		Pull an image or a repository from a registry	# 从docker镜像源服务器拉取指定镜像或者库镜像
push 		Push an image or a repository to a registry	# 推送指定镜像或者库镜像至docker源服务器
rename 		Rename a container	# 
restart 	Restart one or more containers	# 重启运行的容器
rm 			Remove one or more containers	# 移除一个或者多个容器
rmi 		Remove one or more images	# 移除一个或多个镜像[无容器使用该镜像才可删除，否则需删除相关容器才可继续或 -f 强制删除]
run 		Run a command in a new container	# 创建一个新的容器并运行一个命令
save 		Save one or more images to a tar archive (streamed to STDOUT by default)	# 保存一个镜像为一个 tar 包[对应load]
search 		Search the Docker Hub for images	# 在 docker hub 中搜索镜像
start 		Start one or more stopped containers	# 启动容器
stats 		Display a live stream of container(s) resource usage statistics
stop 		Stop one or more running containers	# 停止容器
tag 		Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE	# 给源中镜像打标签
top 		Display the running processes of a container	# 查看容器中运行的进程信息
unpause 	Unpause all processes within one or more containers	# 取消暂停容器
update 		Update configuration of one or more containers 
version 	Show the Docker version information	# 查看 docker 版本号
wait 		Block until one or more containers stop, then print their exit codes	# 截取容器停止时的退出状态值
```

### 3.6 作业练习

#### Docker 安装 Nginx

```shell
# 1、搜索镜像
[root@master home]# docker search nginx
NAME                                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
nginx                                             Official build of Nginx.                        18408     [OK]
unit                                              Official build of NGINX Unit: a polyglot app…   0         [OK]
bitnami/nginx                                     Bitnami nginx Docker Image                      158                  [OK]
bitnami/nginx-ingress-controller                  Bitnami Docker Image for NGINX Ingress Contr…   25                   [OK]
ubuntu/nginx                                      Nginx, a high-performance reverse proxy & we…   84
kasmweb/nginx                                     An Nginx image based off nginx:alpine and in…   4
rancher/nginx-ingress-controller                                                                  11
rancher/nginx-ingress-controller-defaultbackend                                                   2
bitnami/nginx-exporter                                                                            3
rancher/nginx                                                                                     2
rapidfort/nginx-ib                                RapidFort optimized, hardened image for NGIN…   0
rapidfort/nginx                                   RapidFort optimized, hardened image for NGINX   3
vmware/nginx-photon                                                                               1
bitnami/nginx-ldap-auth-daemon                                                                    3
rapidfort/nginx-official                          RapidFort optimized, hardened image for NGIN…   1
vmware/nginx                                                                                      2
rancher/nginx-conf                                                                                0
linuxserver/nginx                                 An Nginx container, brought to you by LinuxS…   192
privatebin/nginx-fpm-alpine                       PrivateBin running on an Nginx, php-fpm & Al…   72                   [OK]
bitnami/nginx-intel                                                                               1
rancher/nginx-ssl                                                                                 0
circleci/nginx                                    This image is for internal use                  2
continuumio/nginx-ingress-ws                                                                      0
rancher/nginx-ingress-controller-amd64                                                            0
webdevops/nginx                                   Nginx container                                 11                   [OK]

# 2、下载镜像 pull
[root@master home]# docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
a2abf6c4d29d: Pull complete
a9edb18cadd1: Pull complete
589b7251471a: Pull complete
186b1aaa4aa6: Pull complete
b4df32aa5a72: Pull complete
a0bcbecc962e: Pull complete
Digest: sha256:0d17b565c37bcbd895e9d92315a05c1c3c9a29f762b011a10c54a66cd53c9b31
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest


[root@master home]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
nginx        latest    605c77e624dd   15 months ago   141MB
centos       latest    5d0da3dc9764   19 months ago   231MB

# -d 后台运行
# --name 给容器命名
# -p 宿主机，容器内部端口
[root@master home]# docker run -d --name nginx01 -p 3344:80 nginx
6ba58e0cadf81d4c807d42946f70ce867ef140a3a0337658154583c1b93a9066
docker[root@master home]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                   NAMES
6ba58e0cadf8   nginx     "/docker-entrypoint.…"   5 seconds ago   Up 2 seconds   0.0.0.0:3344->80/tcp, :::3344->80/tcp   nginx01

[root@master home]# curl localhost:3344

# 进入容器
[root@master home]# docker exec -it nginx01 /bin/bash
root@6ba58e0cadf8:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@6ba58e0cadf8:/# cd /etc/nginx/
root@6ba58e0cadf8:/etc/nginx# ls
conf.d  fastcgi_params  mime.types  modules  nginx.conf  scgi_params  uwsgi_params
root@6ba58e0cadf8:/etc/nginx#
```

端口暴露的概念

![端口暴露](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230420234237227.png)

可以在公网访问了

![在外网访问nginx](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230420234333825.png)

停止容器

```shell
[root@master home]# docker stop 6ba58e0cadf8
6ba58e0cadf8
```

外网无法访问

![外网无法访问](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230420235128398.png)

**思考问题：**我们每次改动 nginx 配置文件，都需要进入容器内部吗？那会十分的麻烦。我要是可以在容器外部提供一个映射路径，达到在容器外修改文件名，容器内部就可以自动修改。 `-v` 数据卷 技术可以解决！

#### Docker 安装 Tomcat

```shell
# 官方使用
docker run -it --rm tomcat:9.0

# 我们之前的启动都是后台，停止了容器之后，容器还是可以查到
# --rm 一般用来测试，用完即删

# 我们正常操作，下载启动
docker pull tomcat

# 启动运行
docker run -d -p 3355:8080 --name tomcat01 tomcat
```

外网访问没有问题，但此时的tomcat是不完整的。

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230421001109021.png)

```shell
# 进入容器
[root@master home]# docker exec -it tomcat01 /bin/bash

root@ae782d777533:/usr/local/tomcat# ls
BUILDING.txt     LICENSE  README.md      RUNNING.txt  conf  logs            temp     webapps.dist
CONTRIBUTING.md  NOTICE   RELEASE-NOTES  bin          lib   native-jni-lib  webapps  work

root@ae782d777533:/usr/local/tomcat# cd webapps
root@ae782d777533:/usr/local/tomcat/webapps# ls
root@ae782d777533:/usr/local/tomcat/webapps#
```

发现问题：

1、linux命令少了	 

2、webapps文件夹为空 

原因：阿里云镜像（阉割版），它为保证最小镜像，将不必要的都剔除了→保证最小可运行环境！

解决：把webapps.dist文件下的内容复制到webapps文件夹下，再次访问。

```shell
root@ae782d777533:/usr/local/tomcat# cd webapps.dist/
root@ae782d777533:/usr/local/tomcat/webapps.dist# ls
ROOT  docs  examples  host-manager  manager
root@ae782d777533:/usr/local/tomcat/webapps.dist# cd ..
root@ae782d777533:/usr/local/tomcat# cp -r webapps.dist/* webapps
root@ae782d777533:/usr/local/tomcat# cd webapps
root@ae782d777533:/usr/local/tomcat/webapps# ls
ROOT  docs  examples  host-manager  manager
root@ae782d777533:/usr/local/tomcat/webapps#
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230421001604839.png)

**思考问题：**我们以后要部署项目，都需要进入容器内部，十分的麻烦。我要是可以在容器外部放置项目，能自动同步到内部就好了。

#### 部署ES+Kibana

难点：

1. ES暴露的端口很多
2. ES十分的耗内存
3. ES的数据一般需要放置到安全目录（挂载）

```shell
# --net somenetwork 是 网络配置，后续再使用

# 下载启动elasticsearch（Docker一步搞定）
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2

# ES非常耗内存，所以上一步启动了ES之后，linux就非常的卡。
# 使用命令 docker stats 查看CPU的状态
```

![ES占用1.2个G的内存](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230504235128633.png)

```shell
# 测试ES是否成功

[root@MiWiFi-R4A-srv ~]# curl localhost:9200
{
  "name" : "d48893932faf",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "6kuxlnPAQFy-hGcKXYBgiw",
  "version" : {
    "number" : "7.6.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "ef48eb35cf30adf4db14086e8aabd07ef6fb113f",
    "build_date" : "2020-03-26T06:34:37.794943Z",
    "build_snapshot" : false,
    "lucene_version" : "8.4.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

```shell
# 将ES停掉，重新分配内存，修改配置文件 -e 环境配置修改
docker run -d --name elasticsearch02 -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2

# 再次查看内存占用
```

![ES占用150M内存](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230504235938487.png)

<p id="2">作业：使用kibana连接es？思考网络如何才能连接。</p>

![如何让kibana连接ES？](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230505000254838.png)

### 3.7 可视化

- **portainer**（先用这个，不常用）

  介绍：Docker 图形化界面管理工具！提供一个后台面板供我们操作！

```shell
# 运行如下命令即可 打开可视化服务
docker run -d -p 8088:9000 \--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

访问测试：从外网访问8088，http://ip:8088/，第一次进入时间比较久，因为它要把所有的数据给挂载出来。

![登陆界面](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230422201924364.png)

给它设置一个密码就可以使用了

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230422202020730.png)

选择本地的：Local，进入之后的面板如下

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230422202148113.png)

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230422202243513.png)

可视化面板平时不会使用，大家自己测试玩玩即可。

## 四、Docker镜像讲解

### 4.1 镜像是什么

- 镜像是一种轻量级、可执行的独立软件保，用来打包软件运行环境和基于运行环境开发的软件，他包含运行某 个软件所需的所有内容，包括代码、运行时库、环境变量和配置文件。

- 将所有的应用和环境，直接打包为docker镜像，就可以直接运行。

  如何得到镜像：

  - 从远处仓库下载
  - 他人拷贝过来
  - 自己制作一个镜像 DockerFile

### 4.2 Docker 镜像加载原理

> UnionFs（联合文件系统）

- 我们下载的时候看到一层层的下载就是这个。
- UnionFs（联合文件系统）：Union文件系统（UnionFs）是一种分层、轻量级并且高性能的文件系统，他支 持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下（ unite several directories into a single virtual filesystem)。Union文件系统是 Docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像
- 特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系 统叠加起来，这样最终的文件系统会包含所有底层的文件和目录。

> Docker镜像加载原理

- docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统就是 UnionFS。
- bootfs（boot file system）主要包含 bootloader 和 Kernel，bootloader 主要是引导加载 kernel，Linux 刚启动时会加 bootfs 文件系统，在 Docker 镜像的最底层是 boots。这一层与我们典型的 Linux/Unix 系统是一样的，包含 boot 加載器和内核。当 boot 加载完成之后整个内核就都在内存中了，此时 内存的使用权已由 bootfs 转交给内核，此时系统也会卸载 bootfs。
- rootfs（root file system)，在 bootfs 之上。包含的就是典型 Linux 系统中 的 /dev，/proc，/bin。/etc 等标准目录和文件。 rootfs 就是各种不同的操作系统发行版，比如 Ubuntu，Centos等等。

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230422202846356.png)

平时我们安装进虚拟机的CentOS都是好几个G，为什么Docker这里才200M？

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230422203318596.png)

对于个精简的OS，rootfs 可以很小，只需要包合最基本的命令，工具和程序库就可以了，因为底层直接用 Host 的 kernel，自己只需要提供 rootfs 就可以了。由此可见对于不同的 Linux 发行版，boots基本是一致 的，rootfs 会有差別，因此不同的发行版可以公用 bootfs.

虚拟机是分钟级别，容器是秒级！

### 4.3 分层理解

我们下载一个镜像，注意观察下载的日志输出，可以看到是一层层的在下载

第一层显示 Already exists，已经存在，是基本层

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230422203520499.png)

> 思考：为什么Docker镜像要采用这种分层的结构呢？

最大的好处就是资源共享！比如有多个镜像都从相同的 Base 镜像构建而来，那么宿主机只需在磁盘上保留一份base镜像，同时内存中也只需要加载一份base镜像，这样就可以为所有的容器服务了，而且镜像的每一层都可以被共享。

查看镜像分层的方式可以通过`docker image inspect [镜像名]` 命令：docker image inspect redis:latest

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230422203945998.png)

> 理解

- 所有的 Docker镜像都起始于一个基础镜像层，当进行修改或增加新的内容时，就会在当前镜像层之上，创建新的镜像层。
- 举一个简单的例子，假如基于 Ubuntu Linux16.04 创建一个新的镜像，这就是新镜像的第一层；如果在该镜像中添加 Python 包，就会在基础镜像层之上创建第二个镜像层；如果继续添加一个安全补丁，就会创建第三个镜像层
- 该像当前已经包含3个镜像层，如下图所示（这只是一个用于演示的很简单的例子）。

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411240301065.png)

再添加额外镜像层的同时，镜像始终保持是当前所有镜像的组合，（理解这一点非常重要）如下图，每个镜像层包含 3 个文件，而镜像包含了来自两个镜像层的 6 个文件。

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411240303155.png)

上图中的镜像层跟之前图中的略有区別，主要目的是便于展示文件

下图中展示了一个稍微复杂的三层镜像，在外部看来整个镜像只有6个文件，这是因为最上层中的文件7是文件5的一个更新版

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411240301597.png)

这种情况下，上层镜像层中的文件覆盖了底层镜像层中的文件。这样就使得文件的更新版本作为一个新镜像层添加到镜像当中。

Docker通过存储引擎（新版本采用快照机制）的方式来实现镜像层堆栈，并保证多镜像层对外展示为统一的文件系统。

Linux上可用的存储引擎有AUFS、 Overlay2、 Device Mapper、Btrfs以及ZFS。顾名思义，每种存储引擎都基于 Linux中对应的文件系统或者块设备技术，并且每种存储引擎都有其独有的性能特点。

Docker在 Windows上仅支持 windowsfilter 一种存储引擎，该引擎基于NTFS文件系统之上实现了分层和CoW(写时复制)。

下图展示了与系统显示相同的三层镜像。所有镜像层堆叠并合并，对外提供统一的视图。

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411240301823.png)

> 特点

Docker 镜像都是只读的，当容器启动时，一个新的可写层加载到镜像的顶部！

这一层就是我们通常说的容器层，容器之下的都叫镜像层！

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411240301443.png)

如何提交一个自己的镜像？

### 4.4 commit镜像

```shell
docker commit 提交容器成为一个新的副本

# 命令和git原理类似
docker commit -m="描述信息" -a="作者" 容器id 目标镜像名:[TAG]
```

实战测试

```shell
# 1、启动一个默认的tomcat
docker run -d -p 8080:8080 tomcat
# 2、发现这个默认的tomcat 是没有webapps应用，官方的镜像默认webapps下面是没有文件的！
docker exec -it 容器id /bin/bash
# 3、拷贝文件进去
cp -r webapps.dist/* webapps
# 4、将操作过的容器通过commit调教为一个镜像！我们以后就使用我们修改过的镜像即可，这就是我们自己的一个修改的镜像。
docker commit -m="描述信息" -a="作者" 容器id 目标镜像名:[TAG]
docker commit -a="kuangshen" -m="add webapps app" 容器id tomcat02:1.0
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230422211313150.png)

如果你想要保存当前容器的状态，就可以通过commit来提交，获得一个镜像，就好比使用VM的时候的快照。

到这里才算是入门Docker。

## 五、容器数据卷

### 5.1 什么是容器数据卷

**docker 的理念回顾**

将应用和环境打包成一个镜像！

数据？如果数据都在容器中，那么我们容器删除，数据就会丢失！需求：数据可以持久化

MySQL，容器删除了，删库跑路！需求：MySQL 数据可以存储在本地

容器之间可以有一个数据共享的技术！Docker 容器中产生的数据，同步到本地

这就是卷技术！目录的挂载，将我们容器内的目录，挂载到 Linux 上面

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230424235758499.png)

**总结一句话：容器的持久化和同步操作！容器间也是可以数据共享的！**

### 5.2 使用数据卷

> 方式一：直接使用命令来挂载 -v

```shell
docker run -it -v 主机目录：容器内目录

# 测试
[root@master home]# docker run -it -v /home/ceshi:/home centos /bin/bash

# 启动起来的时候我们可以通过 docker inspect 容器ID 查看
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230425000622550.png)

*注：这是双向绑定*

**测试文件的同步**

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230425001055458.png)

再来测试

1. 停止容器

2. 宿主机修改文件

3. 启动容器

4. 容器内的数据依旧是同步的

好处：我们以后修改只需要在本地修改即可，容器内会自动同步！

### 5.3 实战：安装MySQL

<p id="1">思考：MySQL的数据持久化的问题</p>

```shell
# 获取mysql镜像  
docker pull mysql:5.7 
# 运行容器,需要做数据挂载 
# 安装启动mysql，需要配置密码的，这是要注意点！（去docker hub上找官方命令）
# 参考官网	-e 表示配置，昨天配置过网络限速，这里是配置密码
hub docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

#启动
-d 后台运行 
-p 端口映射 
-v 卷挂载 （可以挂载多个）
-e 环境配置 
-- name 容器名字 
docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7 

# 启动成功之后，我们在本地使用sqlyog来测试一下 
# sqlyog--连接到服务器的3310--和容器内的3306映射
```

![测试MySQL连接](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230426232130573.png)

在本地测试创建一个数据，查看一下我们映射的路径是否ok

新建一个数据库后，对应的主机内的`/home/mysql/data`也出现了test数据库的文件

![创建数据库](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506021323231.png)

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230426232301801.png)

假设我们将容器删除

```
docker rm -f mysql01
docker ps
docker ps -a
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230426232344260.png)

发现，我们挂载到本地的数据卷依旧没有丢失，这就实现了容器数据持久化功能。

### 5.4 具名和匿名挂载

#### 匿名挂载

```shell
-P 不指定路径
-v 容器内路径! 
# 这里没指定容器外路径
docker run -d -P --name nginx01 -v /etc/nginx nginx

# 查看所有的volume（卷）的情况 
docker volume ls

# 这种就是匿名挂载，我们在 -v 只写了容器内的路径，没有写容器外的路径！
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230426232629027.png)

#### 具名挂载

```shell
# 具名挂载 
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx 
docker volume ls  

# 通过 -v 卷名：容器内路径 
# 查看一下这个卷
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230426232751918.png)

所有的docker容器内的卷，没有指定目录的情况下都是在 `/var/lib/docker/volumes/[xxx]/_data` 下

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230426232833258.png)

我们通过具名挂载可以方便的找到我们的一个卷，大多数情况使用`具名挂载`

**如何确定是具名挂载还是匿名挂载，还是指定路径挂载？**

```shell
-v 容器内路径			#匿名挂载
-v 卷名：容器内路径		#具名挂载	（和指定路径挂载没关系，具名是指定卷名）
-v /宿主机路径：容器内路径 #指定路径挂载 此时 docker volume ls 是查看不到的
```

**扩展：**

```shell
# 通过 -v 容器内路径： ro rw 改变读写权限
ro -> readonly 只读
rw -> readwrite 可读可写

# 一旦设置了容器权限，容器对我们挂载出来的内容就有限定了
docker run -d -P --name nginx02 -v juming:/etc/nginx:ro nginx
docker run -d -P --name nginx02 -v juming:/etc/nginx:rw nginx

# ro 只要看到ro就说明这个路径只能通过宿主机来操作，容器内部是无法操作！默认的是rw
```

### 5.5 初识Dockerfile

> 第二种挂载方式

Dockerfile 就是用来构建 docker 镜像的构建文件！相当于命令脚本。先体验一下。

通过这个脚本可以生成镜像。镜像是一层一层的，脚本的一个个的命令，每个命令都是一层

```shell
# 创建一个dockerfile文件，名字可以随便 建议Dockerfile，这里 vim dockerfile1
# 文件中的内容 指令(大写) 参数
# 在dockerfile1中编写以下内容
FROM centos 

VOLUME ["volume01","volume02"] 

CMD echo "----end----" 
CMD /bin/bash 

# 这里的每个命令，就是镜像的一层！
```

`docker build -f /home/docker-test-volume/dockerfile1 -t kuangshen/centos:1.0 .`最后面要加上 “`.`”

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230426235006489.png)

`docker images`，这里就是我们自己创建的镜像

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230426235049773.png)

启动一下自己写的容器

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230426235311860.png)

这个卷和外部一定有一个同步的目录。且这种方式属于匿名挂载

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230426235527585.png)

我们现在容器中的卷内创建一个文件`container.txt`

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230426235750691.png)

不要退出容器，在容器外查看

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230426235832457.png)

可以看到挂载的卷的位置

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230426235926575.png)

进入路径，可以看到同步过来的文件。

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230427000102175.png)

测试一下刚才的文件是否同步出去了：在主机的挂载目录下也能找到刚才创建的文件，说明同步成功了

这种方式使用的十分多，因为我们通常会构建自己的镜像！

假设构建镜像时候没有挂载卷，要手动镜像挂载 `-v 卷名：容器内路径`！

### 5.6 数据卷容器

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230427000336179.png)

```shell
# 启动三个容器

# 1
docker run -it --name docker01 kuangshen/centos:1.0
# 2 使用--volumes-from 继承docker01，使docker01的数据卷同步到docker02中
docker run -it --name docker02 --volumes-from docker01 kuangshen/centos:1.0
# 3 略，和docker02的模式一样
```

进入docker01的数据卷创建一个文件

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230427001101874.png)

再进入docker02，会发现docker01创建的内容同步到了docker02上

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230427001316058.png)

```shell
docker run -it --name docker02 --volumes-from docker01 kuangshen/centos:1.0

# docker02 = Son
# --volumes-from = extends
# docker01 = Father

# 相当于docker02继承了docker01，只要通过--volumes-from就可以实现数据的共享
```

Q：如果我们把docker01停掉，docker02和docker03的数据还在不在？

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230427001654541.png)

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230427001745416.png)

[回顾第三小节](#1)：多个MySQL实现数据共享

```shell
docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7

docker run -d -p 3307:3306 -e MYSQL_ROOT_PASSWORD=123456 --name mysql02 --volumes-from mysql01  mysql:5.7

# 这个时候，可以实现两个容器数据同步！
```

**总结：**

1. 容器之间配置信息的传递，数据卷的生命周期一直持续到没有容器使用为止。
2. 但是一旦你持久化到了本地（-v），这个时候，本地的数据是一直不会被删除的

## 六、DockerFile

### 6.1 DockerFile介绍

dockerfile 是用来构建docker镜像的文件！是一个命令参数脚本

构建步骤：

1. 编写一个 dockerfile 文件

2. docker build 构建成为一个镜像

3. docker run 运行镜像

4. docker push 发布镜像（DockerHub 、阿里云仓库）

看下官方是怎么做的？

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230427003322784.png)

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230427003440913.png)

很多官方镜像都是基础包，很多功能没有，我们通常会自己搭建自己的镜像！（可能一百个人有一百个需求，我们可能要求镜像里除了centos，还要有jdk、mysql）

官方既然可以制作镜像，那我们也可以！

### 6.2 DockerFile 构建过程

基础知识：

1. 每个保留关键字（指令）都是必须是大写字母
2. 执行从上到下顺序
3. `#` 表示注释
4. 每一个指令都会创建提交一个新的镜像层，并提交！

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411240302497.png)

Dockerfile 是面向开发的，我们以后要发布项目，做镜像，就需要编写 dockerfile 文件，这个文件十分简单！

Docker 镜像逐渐成企业交付的标准，必须要掌握！

- **DockerFile**：构建文件，定义了一切的步骤。相当于源代码
- **DockerImages**：通过DockerFile构建生成的镜像。是最终发布和运行的产品。
- **Docker 容器**：容器就是镜像运行起来提供服务。
- **DockerFile –> DockerImages –> Docker 容器**：代码–类--类创建出来的对象

### 6.3 DockerFile的指令

```shell
FROM 		# 基础镜像，一切从这里开始构建 
MAINTAINER  # 镜像是谁写的， 姓名+邮箱 
LABEL		# 现在推荐写 LABEL，代替上面的那个
RUN 		# 镜像构建的时候需要运行的命令 
ADD 		# 步骤：如要加一个tomcat镜像，就是加这个tomcat压缩包！1.添加内容 2.添加同目录 
WORKDIR	    # 镜像的工作目录 
VOLUME 		# 挂载的目录 
EXPOSE 		# 暴露端口配置 
CMD 		# 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代。 
ENTRYPOINT  # 指定这个容器启动的时候要运行的命令，可以追加命令 
ONBUILD 	# 当构建一个被继承的 DockerFile 时，这时就会运行ONBUILD的指令，触发别的指令。
COPY 		# 类似ADD，将我们文件拷贝到镜像中 
ENV 		# 构建的时候设置环境变量！
```

![image-20241124030215925](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411240302994.png)

### 6.4 实战测试

Docker Hub 中 99% 的镜像都是从这个基础镜像过来的：`FROM scratch`，然后配置需要的软件和配置 来进行的构建

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230429221635547.png)

> 创建一个自己的 centos

```dockerfile
# 1.编写Dockerfile的文件
FROM centos:7
MAINTAINER yu<952450841@qq.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "----end----"
CMD /bin/bash

# 2.通过这个文件构建镜像
# 命令 docker build -f 文件路径 -t 镜像名:[tag] .
docker build -f mydockerfile-centos -t mycentos:0.1 .

# 成功后会显示如下信息：
 => exporting to image                                                                                          3.2s
 => => exporting layers                                                                                         3.2s
 => => writing image sha256:27a4dfc2485bddc6bef7b3b98e112398680f4fcd7224f5545b0e2d99487c5dfb                    0.0s
 => => naming to docker.io/library/mycentos:0.1  

# 3、测试运行
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230429223156157.png)

**对比：**

**之前的原生的 centos**

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230429223500600.png)

**我们自己制作的镜像**

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230429223640835.png)

我们可以列出本地进行的变更历史

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230429223908389.png)

我们平时拿到一个镜像，就可以研究一下它是怎么做的了

> CMD 和 ENTRYPOINT 的区别

```shell
CMD 		# 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代。 
ENTRYPOINT  # 指定这个容器启动的时候要运行的命令，可以追加命令 
```

**1. 测试CMD**

```shell
# 编写dockerfile文件
[root@MiWiFi-R4A-srv dockerfile]# vim dockerfile-cmd-test
FROM centos:7
CMD ["ls","-a"]

# 构建镜像
[root@MiWiFi-R4A-srv dockerfile]# docker build -f dockerfile-cmd-test -t cmdtest

# 运行镜像 发现 ls -a 命令生效了
[root@MiWiFi-R4A-srv dockerfile]# docker run d53776618e7c
.
..
.dockerenv
anaconda-post.log
bin
dev
...


# 想追加一个命令 -l 成为 ls -al
[root@MiWiFi-R4A-srv dockerfile]# docker run d53776618e7c -l
docker: Error response from daemon: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "-l": executable file not found in $PATH: unknown.
ERRO[0000] error waiting for container:
```

- 错误原因：cmd 的情况下，`-l` 替换了 `CMD ["ls","-a"]`。而 `-l` 不是命令，所以会报错。
- 解决方案：写出完整的命令：`ls -al`。如下

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230429224800096.png)

**2. 测试ENTRYPOINT**

```shell
# 编写dockerfile文件
[root@MiWiFi-R4A-srv dockerfile]# vim dockerfile-entrypoint-test
FROM centos:7
ENTRYPOINT ["ls","-a"]

# 构建镜像
[root@MiWiFi-R4A-srv dockerfile]# docker build -f dockerfile-entrypoint-test -t entrypoint-test:0.1 .

# 将命令-l直接添加在run的后面。会发现也能正常显示
[root@MiWiFi-R4A-srv dockerfile]# docker run 878051c15b29 -l
total 0
drwxr-xr-x.   1 root root   6 Apr 29 14:52 .
drwxr-xr-x.   1 root root   6 Apr 29 14:52 ..
-rwxr-xr-x.   1 root root   0 Apr 29 14:52 .dockerenv
lrwxrwxrwx.   1 root root   7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x.   5 root root 340 Apr 29 14:52 dev
drwxr-xr-x.   1 root root  66 Apr 29 14:52 etc
drwxr-xr-x.   2 root root   6 Nov  3  2020 home
lrwxrwxrwx.   1 root root   7 Nov  3  2020 lib -> usr/lib
lrwxrwxrwx.   1 root root   9 Nov  3  2020 lib64 -> usr/lib64
drwx------.   2 root root   6 Sep 15  2021 lost+found
drwxr-xr-x.   2 root root   6 Nov  3  2020 media
drwxr-xr-x.   2 root root   6 Nov  3  2020 mnt
drwxr-xr-x.   2 root root   6 Nov  3  2020 opt
dr-xr-xr-x. 140 root root   0 Apr 29 14:52 proc
dr-xr-x---.   2 root root 162 Sep 15  2021 root
drwxr-xr-x.  11 root root 163 Sep 15  2021 run
lrwxrwxrwx.   1 root root   8 Nov  3  2020 sbin -> usr/sbin
drwxr-xr-x.   2 root root   6 Nov  3  2020 srv
dr-xr-xr-x.  13 root root   0 Apr 26 15:07 sys
drwxrwxrwt.   7 root root 171 Sep 15  2021 tmp
drwxr-xr-x.  12 root root 144 Sep 15  2021 usr
drwxr-xr-x.  20 root root 262 Sep 15  2021 var

```

Dockerfile中很多命令都十分的相似，我们需要了解它们的区别，我们最好的学习就是对比它们然后测试效果

### 6.5 实战：Tomcat镜像

1. 准备镜像文件

   准备tomcat压缩包 和 jdk压缩包到当前目录，编写好README 。

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230429235638667.png)

2. 编写 dokerfile 文件，使用官方命名 `Dockerfile`。这样的话，build 时就会自动寻找这个文件，就不用加 -f 指定文件![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230430000914710.png)

   ```dockerfile
   FROM centos:7
   MAINTAINER yu<952450841@qq.com>
   
   COPY readme.txt /usr/local/readme.txt
   
   ADD apache-tomcat-9.0.74.tar.gz /usr/local/
   ADD jdk-8u181-linux-x64.tar.gz /usr/local/
   
   RUN yum -y install vim
   
   ENV MYPATH /usr/local
   
   WORKDIR $MYPATH
   
   ENV JAVA_HOME /usr/local/jdk1.8.0_181
   ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
   ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.74
   ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.74
   ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
   
   EXPOSE 8080
   
   CMD /usr/local/apache-tomcat-9.0.74/bin/startup.sh && tail -F /url/local/apache-tomcat-9.0.74/bin/logs/catalina.out
   ```

3. 构建镜像

   ```shell
   docker build -t diytomcat:1.0 .
   
   # 构建成功
   
    => exporting to image                                                                                          4.3s
    => => exporting layers                                                                                         4.3s
    => => writing image sha256:a0e7e27b3b2e663ad65aab69df1e43d31dae1cb70bd2a384fe0cd33ec983d14b                    0.0s
    => => naming to docker.io/library/diytomcat:1.0   
   ```

4. 启动镜像

   ```shell
   [root@MiWiFi-R4A-srv docker-tomcat]# docker run -d -p 9090:8080 --name yutomcat -v /home/dockerfile/docker-tomcat/test:/usr/local/apache-tomcat-9.0.74/webapps/test -v /home/dockerfile/docker-tomcat/tomcatlogs/:/usr/local/apache-tomcat-9.0.74/logs diytomcat:1.0
   
   # 显示容器ID
   fc38aba8b1e73f3acd5827d30c5656fa67f8cb7d2f0ae1b95965426cdeaea994
   ```

   进入镜像

   ```shell
   [root@MiWiFi-R4A-srv docker-tomcat]# docker exec -it fc38aba8b1e7 /bin/bash
   ```

   ![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230430002420954.png)

5. 访问测试

   ```shell
   # 访问挂载出来的端口
   [root@MiWiFi-R4A-srv docker-tomcat]# curl localhost:9090
   ```

   curl没问题

   ![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230430002512164.png)

   在外网查看

   ![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230430002621510.png)

6. 发布项目（由于做了卷挂载，我们直接在本地编写项目就可以发布了！）

   进入我们挂载的宿主机目录，创建WEB-INF目录。

   ```shell
   [root@MiWiFi-R4A-srv docker-tomcat]# cd test
   [root@MiWiFi-R4A-srv test]# pwd
   /home/dockerfile/docker-tomcat/test
   [root@MiWiFi-R4A-srv test]# mkdir WEB-INF
   ```

   进入WEB-INF目录，创建web.xml文件

   ```shell
   [root@MiWiFi-R4A-srv test]# cd WEB-INF/
   [root@MiWiFi-R4A-srv WEB-INF]# vim web.xml
   ```

   ```xml
   <web-app version="3.0" xmlns="http://java.sun.com/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
           http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd">
           
   </web-app>
   ```

   退到上一级，创建index.jsp文件，WEB-INF文件夹和index.jsp同级

   ```shell
   [root@MiWiFi-R4A-srv WEB-INF]# cd ..
   [root@MiWiFi-R4A-srv test]# vim index.jsp
   ```

   ```jsp
   <html>
   <head><title>Hello World</title></head>
   <body>
   Hello World!<br/>
   <%
   System.out.println("----my test web logs----");
   %>
   </body>
   </html>
   ```

   访问成功！

   ![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230430004234961.png)

   查看日志：发现打印了日志，说明日志的目录也挂载成功了。

   ```shell
   [root@MiWiFi-R4A-srv docker-tomcat]# cd tomcatlogs/
   [root@MiWiFi-R4A-srv tomcatlogs]# tail -f catalina.out
   ```

   ![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230430004513679.png)

   我们以后开发的步骤：需要掌握Dockerfile的编写！我们之后的一切都是使用docker镜像来发布运行！

### 6.6 发布自己的镜像

> 把镜像发布到DockerHub

1. 地址 `https://hub.docker.com/`，注册登录

2. 确定这个账号可以登陆。

3. 在我们服务器上提交自己的镜像。

   ```shell
   [root@MiWiFi-R4A-srv tomcatlogs]# docker login --help
   
   Usage:  docker login [OPTIONS] [SERVER]
   
   Log in to a registry.
   If no server is specified, the default is defined by the daemon.
   
   Options:
     -p, --password string   Password
         --password-stdin    Take the password from stdin
     -u, --username string   Username
   ```

   ```shell
   [root@MiWiFi-R4A-srv tomcatlogs]# docker login -u xiaoyu0219
   Password:
   WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
   Configure a credential helper to remove this warning. See
   https://docs.docker.com/engine/reference/commandline/login/#credentials-store
   
   Login Succeeded
   ```

4. 提交镜像

   ```shell
   # 首先需要用tag命令，把镜像打个tag
   # 这里要严格按照推送规范来：docker tag [imageID] 用户名/镜像名:版本号
   [root@MiWiFi-R4A-srv tomcatlogs]# docker tag a0e7e27b3b2e xiaoyu0219/diytomcat:1.0
   
   # 推送镜像的规范是：docker push 注册用户名/镜像名:版本号
   [root@MiWiFi-R4A-srv tomcatlogs]# docker push xiaoyu0219/diytomcat:1.0
   
   The push refers to repository [docker.io/xiaoyu0219/diytomcat]
   5f70bf18a086: Pushing  1.024kB
   9515394e7596: Pushing [=====================>                             ]  110.2MB/259.4MB
   44c0d4e884e0: Preparing
   
   # 最后推送成功
   ```

   *PS：阿里云容器镜像的推送参考官方*

### 6.7 小结

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/202411240302484.png)

镜像打包（save）成为一个tar压缩包，可以发送压缩包给别人（load）。

## 七、Docker网络

### 7.1 理解Docker0

清空所有环境，方便我们测试。

```shell
docker rm -f $(docker ps -aq)
docker rmi -f $(docker images -aq)
```

<p id=3>测试：三个网络</p>

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230505003033891.png)

**问题： docker 是如果处理容器网络访问的？**[回顾之前提出的问题](#2)

假设一个tomcat容器要访问mysql容器

```shell
# 测试 运行一个 tomcat
docker run -d -P --name tomcat01 tomcat:7.0

# 查看容器的内部网络地址（命令后追加 ip addr）
docker exec -it tomcat01 ip addr
```

发现容器启动的时候 会得到一个`eth0@if59`的IP地址，这个地址是docker分配的

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230505002211865.png)

思考： Linux 能不能ping通容器内部？-- 可以

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230505002241236.png)

> 原理

1. 我们每启动一个docker容器，docker就会给docker容器分配一个IP，我们只要安装了docker就会有一个网卡-docker0，（桥接模式），使用的技术是veth-pair技术！

   第一次执行ip addr，[只有三个网卡](#3)。

   再次执行ip addr，发现多了一个网卡（刚刚启动的tomcat容器）。

   ![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230505003128692.png)

2. 再启动一个tomcat02，继续测试ip addr，发现又多了一对网卡

   ```shell
   docker run -d -P --name tomcat02 tomcat:7.0
   ```

   ![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230505003347923.png)

   ```shell
   # 进入容器查看内部地址
   docker exec -it tomcat02 ip addr
   ```

   ![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230505003601861.png)

   内部是 **60**: eth0@if**61**

   外部是 **61**: vethbde694b@if**60**

   所以是一对。

   > 我们发现这个容器带来网卡，都是一对对的。
   > veth-pair 就是一对的虚拟设备接口，他们都是成对出现的，一端连着协议，一端彼此相连。
   > 正因为有这个特性，利用 veth-pair 充当一个桥梁，连接各种虚拟网络设备的。
   > OpenStac，Docker容器之间的连接，OVS的连接，都是使用veth-pair技术。

3. 我们来测试下 tomcat01 和 tomcat02 是否可以ping通

   ![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230505004246689.png)

**结论：容器和容器之间是可以 ping 通的**

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230505004932984.png)

结论：tomcat01和tomcat02是共用的一个路由器—docker0

所有的容器不指定网络的情况下，都是 docker0 路由的，docker会给我们的容器分配一个默认的可用 ip

> 小结

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230505005954501.png)

Docker中所有网络接口都是虚拟的，虚拟的转发效率高（内网传递文件）

只要容器删除，对应的一对网桥就没了！

### 7.2 --link

> 思考一个场景：我们编写了一个微服务，访问数据库MySQL，但容器分配的数据库ip换掉了，database url=ip: 如何做到项目不重启，但是数据ip换了，我们希望可以处理这个问题。也就是可以通过名字来进行访问容器。
>
> 如果做到这个，就可以实现高可用了！

```shell
# 启动两个tomcat容器 tomcat01和tomcat02
docker run -d -P --name tomcat01 tomcat:7.0
docker run -d -P --name tomcat02 tomcat:7.0

# 让tomcat02 ping tomcat01
docker exec -it tomcat02 ping tomcat01
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506001400534.png)

发现ping不通，如何可以解决呢？

```shell
# 启动tomcat03，让其和tomcat02使用 --link 连通
docker run -d -P --name tomcat03 --link tomcat02 tomcat:7.0

# 再使用 tomcat03 去 ping tomcat02
docker exec -it tomcat03 ping tomcat02
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506001527341.png)

发现可以连通。

反向可以连通吗？使用tomcat02 ping tomcat03

```shell
[root@MiWiFi-R4A-srv ~]# docker exec -it tomcat02 ping tomcat03
ping: tomcat03: No address associated with hostname

# 发现并不可以ping通
```

**探究：inspect命令**

原理：

```shell
# 查看tomcat03的 /etc/hosts 文件
docker exec -it tomcat03 cat /etc/hosts
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506002754526.png)

本质探究：--link就是我们在hosts配置中增加了一个 172.18.0.3 tomcat02，而tomcat02中的hosts文件没有（可以后续手动添加）。

也就是在hosts中增加了一层映射

现在使用Docker已经不建议使用–link了！

自定义网络，不适用docker0！

docker0问题：不支持容器名连接访问

### 7.3 自定义网络

> 查看所有的docker网络：docker netework ls

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506003028687.png)

> 网络模式

- bridge ：桥接 docker（默认，我们自己也是用bridge模式）
- none ：不配置网络，一般不用
- host ：和宿主机共享网络
- container ：容器网络连通（用得少！局限很大）

> 测试

清空所有的环境：docker rm -f $(docker ps -aq)

让我们的虚拟机只有原来的三个网络

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506003611758.png)

```shell
# 我们直接启动的命令 --net bridge,而这个就是我们的docker0 
# bridge就是docker0 
docker run -d -P --name tomcat01 tomcat:7.0
# 相当于↓
docker run -d -P --name tomcat01 --net bridge tomcat:7.0

# docker0，特点：默认，域名不能访问。 --link可以打通连接，但是很麻烦！

# 我们可以 自定义一个网络
# subnet 是子网，gateway 是路由，mynet是网络的名称，16代表最多支持IP范围 192.168.00 ~ 192.168.255.255
docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet

# 查看网络
docker network ls
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506004127804.png)

这个就是我们创建的自定义网络：docker network inspect mynet

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506004434484.png)

```shell
# 再次使用我们自定义的网络创建两个tomcat
docker run -d -P --name tomcat-net-01 --net mynet tomcat:7.0
docker run -d -P --name tomcat-net-02 --net mynet tomcat:7.0

# 查看网络状态
docker network inspect mynet
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506004926928.png)

```shell
# 再次测试ping连接
docker exec -it tomcat-net-01 ping 192.168.0.3
# 二者等价↓ 不再需要--link
docker exec -it tomcat-net-01 ping tomcat-net-02
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506005131601.png)

我们自定义的网络docker帮我们维护好了对应的关系，推荐我们平时这样使用网络！

**好处：**

redis - 不同的集群使用不同的网络，保证集群是安全和健康的

mysql - 不同的集群使用不同的网络，保证集群是安全和健康的

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506005503051.png)

### 7.4 网络连通

我们现在的情况是这样的：

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506010035376.png)

`tomcat01` 可以ping通`tomcat-net-01` 吗？

```shell
# 基于上一小节的内容，再在docker0网络中启动两个tomcat
docker run -d -P --name tomcat01 tomcat:7.0
docker run -d -P --name tomcat02 tomcat:7.0
```

查看现在所有的容器，docker0和mynet中各有两个tomcat容器

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506010229220.png)

```shell
# 用docker0中的tomcat01 去ping mynet中的tomcat-net-01
docker exec -it tomcat01 ping tomcat-net-01
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506010337301.png)

发现不通

> 测试：打通 tomcat01 到 mynet

```shell
# 使用 connect 命令，打通网络
docker network connect mynet tomcat01

# 查看mynet网络情况
docker network inspect mynet
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506010729703.png)

连通之后，将docker0中的tomcat01 和 mynet网络打通，直接放到了mynet网络下。

官方的名称就叫做：一个容器，两个IP地址。

例如：阿里云服务器的公网IP和私网IP

```shell
# 再次测试 tomcat01 ping tomcat-net-01
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506011012030.png)

网络打通了，但由于tomcat02并没有打通mynet，所以tomcat02依旧是不行的

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506011052390.png)

**结论：假设要跨网络操作别人，就需要使用docker network connect 连通！**

### 7.5 实战：部署Redis集群

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506014100764.png)

清空所有的环境：docker rm -f $(docker ps -aq)

```shell
# 创建 redis docker网络
docker network create redis --subnet 172.38.0.0/16

# 为6个redis创建配置文件（直接在命令行键入以下内容即可）
for port in $(seq 1 6); \
do \
mkdir -p /mydata/redis/node-${port}/conf
touch /mydata/redis/node-${port}/conf/redis.conf
cat << EOF > /mydata/redis/node-${port}/conf/redis.conf
port 6379
bind 0.0.0.0
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.38.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done

# 进入 /mydata/redis 路径可以看见6个节点，在6个节点下，分别有每个节点自己的redis配置
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506011955593.png)

```shell
# 通过脚本运行6个redis（直接在命令行键入以下内容即可）
for port in $(seq 1 6); \
do \
docker run -p 637${port}:6379 -p 1637${port}:16379 --name redis-${port} \
-v /mydata/redis/node-${port}/data:/data \
-v /mydata/redis/node-${port}/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.1${port} redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf; \
done

# 进入其中一个容器
docker exec -it redis-1 /bin/sh

# 在容器内执行创建集群命令
redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379 --cluster-replicas 1
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506012649960.png)

集群配置完毕

> 测试

```shell
# 创建集群完毕后，在redis-1中，开启redis交互
redis-cli -c
```

**查看集群信息：cluster info**

集群数量为3

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506013454345.png)

**查看节点状态：cluster nodes**

全部节点都是健康的，三个master，三个slave

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506013531664.png)

**执行命令：set a b**

kv被存放在了13节点上，也就是redis-3上

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506013631883.png)

**停止容器 redis-3：docker stop redis-3**

**执行命令：get a**

在14上发现了key a的值

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506013811037.png)

**再次查看节点状态：cluster nodes**

13容器宕机了，节点发生了故障转移。

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230506013856589.png)

## 八、Docker整合

### 8.1 SpringBoot微服务打包Docker镜像

1. 准备好一个springboot项目：myfavorites-0.0.1-SNAPSHOT.jar，拷贝到linux中。

2. 在同级文件夹下，创建dockerfile文件

   vim Dockerfile

   ```dockerfile
   FROM java:8
   VOLUME /tmp
   ADD myfavorites-0.0.1-SNAPSHOT.jar springboot.jar
   EXPOSE 8080
   ENTRYPOINT ["java","-jar","/springboot.jar"]
   ```

   保存并退出。

   > FROM：表示基础镜像，即运行环境
   >
   > VOLUME：/tmp创建/tmp目录并持久化到Docker数据文件及，因为SpringBoot使用的内嵌Tomcat容器默认使用/Tmp作为工作目录
   >
   > ADD：拷贝文件并且重命名（ADD myfavorites-0.0.1-SNAPSHOT.jar springboot.jar 将应用jar包复制到/springboot.jar）
   >
   > EXPOSE：并不是真正的发布端口，这个只是容器部署人员与监理image的人员之间的交流，即监理image的人员告诉容器部署人员容器应该映射哪个端口给外界
   >
   > ENTRYPOINT：容器启动时运行的命令，相当于我们在命令行中输入 java -jar xxx.jar

3. 构建镜像

   ```shell
   docker build -t springboot .
   ```

4. 完成容器的创建

   上一步结束后，docker images 就可以发现 该镜像，基于该镜像，完成容器的创建。

   把所有容器全部停掉：docker stop $(docker ps -a -q)

   运行容器（这里可以运行两个，暴露不同的端口号）：

   docker run -d --name springboot-8080 -p 8080:8080 --rm springboot

   docker run -d --name springboot-8081 -p 8081:8080 --rm springboot

5. 访问页面

   ![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230519013039430.png)

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230519013205117.png)

8080和8081都能访问成功。

### 8.2 IDEA集成Docker实现镜像打包一键部署

Idea集成docker实现镜像打包一键部署

#### 1）Docker开启远程访问

```shell
#修改该Docker服务文件
vi /lib/systemd/system/docker.service
#修改ExecStart这行
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock
```

> 将文件内的 ExecStart注释。 新增如上行。
> #ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375-H unix:///var/run/docker.sock

```shell
#重新加载配置文件
systemctl daemon-reload
#重启服务
systemctl restart docker.service
#查看端口是否开启
netstat -nlpt #如果找不到netstat命令，可进行安装。yum install net-tools
#直接curl看是否生效
curl http://127.0.0.1:2375/info
```

#### 2）IDEA安装Docker插件

> 打开Idea，从File->Settings->Plugins->Install JetBrains plugin进入插件安装界面，
>
> 在搜索框中输入docker，可以看到Docker integration，点击右边的Install按钮进行安装。
>
> 安装后重启Idea。

#### 3）IDEA配置docker

配置docker，连接到远程docker服务。

从File->Settings->Build,Execution,Deployment->Docker打开配置界面

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230519015408579.png)

开放防火墙端口命令：

```shell
# 查看已经开放的端口
firewall-cmd --list-ports
# 开放 2375
firewall-cmd --zone=public --add-port=2375/tcp --permanent
# 重启防火墙
firewall-cmd --reload
# 再次查看 2375 是否已经开放
firewall-cmd --list-ports
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230519015610707.png)

对着镜像右键，进行create创建容器操作

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230519020103781.png)

启动后，再次访问测试接口

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230519020300382.png)

#### 4）docker-maven-plugin

传统过程中，打包、部署、等。

而在持续集成过程中，项目工程一般使用 Maven 编译打包，然后生成镜像，通过镜像上线，能够大大提供上线效率，同时能够快速动态扩容，快速回滚，着实很方便。docker-maven-plugin 插件就是为了帮助我们在Maven工程中，通过简单的配置，自动生成镜像并推送到仓库中。

**pom.xml**

```xml
<properties>
    <docker.image.prefix>guoweixin</docker.image.prefix>
</properties>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>1.0.0</version>
        </plugin>
        <plugin>
            <groupId>com.spotify</groupId>
            <artifactId>docker-maven-plugin</artifactId>
            <version>1.0.0</version>
            <configuration>
                <!-- 镜像名称 guoweixin/exam-->
                <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
                <!--指定标签-->
                <imageTags>
                    <imageTag>latest</imageTag>
                </imageTags>
                <!-- 基础镜像jdk 1.8-->
                <baseImage>java</baseImage>
                <!-- 制作者提供本人信息 -->
                <maintainer>guoweixin guoweixin@aliyun.com</maintainer>
                <!--切换到/ROOT目录 -->
                <workdir>/ROOT</workdir>
                <cmd>["java", "-version"]</cmd>
                <entryPoint>["java", "-jar", "${project.build.finalName}.jar"]</entryPoint>
                <!-- 指定 Dockerfile 路径
<dockerDirectory>${project.basedir}/src/main/docker</dockerDirectory>
-->
                <!--指定远程 docker api地址-->
                <dockerHost>http://192.168.20.135:2375</dockerHost>
                <!-- 这里是复制 jar 包到 docker 容器指定目录配置 -->
                <resources>
                    <resource>
                        <targetPath>/ROOT</targetPath>
                        <!--用于指定需要复制的根目录，${project.build.directory}表示target目录-->
                        <directory>${project.build.directory}</directory>
                        <!--用于指定需要复制的文件。${project.build.finalName}.jar指的是打包后的jar包文件。-->
                        <include>${project.build.finalName}.jar</include>
                    </resource>
                </resources>
            </configuration>
        </plugin>
    </plugins>
</build>
```

**Dockerfile**

如上用docker-maven插件 自动生成如下文件：

```dockerfile
FROM java
MAINTAINER guoweixin guoweixin@aliyun.com
WORKDIR /ROOT
ADD /ROOT/qfnj-0.0.1-SNAPSHOT.jar /ROOT/
ENTRYPOINT ["java", "-jar", "qfnj-0.0.1-SNAPSHOT.jar"]
CMD ["java", "-version"]
```

#### 5）执行命令

对项目进行 打包。并构建镜像 到Docker 上。

```cmd
mvn clean package docker:build
```

#### 6）IDEA操作Docker

#### 7）扩展配置

绑定Docker 命令到 Maven 各个阶段

> 我们可以绑定 Docker 命令到 Maven 各个阶段。
>
> 我们可以把 Docker 分为 build、tag、push，然后分别绑定 Maven 的 package、deploy 阶段。
>
> 我们只需要执行 mvn deploy 就可以完成整个 build、tag、push操作了，当我们执行 mvn build 就只完成build、tag 操作。

```xml
<executions>
    <!--当执行mvn package 时，执行： mvn clean package docker:build -->
    <execution>
        <id>build-image</id>
        <phase>package</phase>
        <goals>
            <goal>build</goal>
        </goals>
    </execution>
    <!--当执行mvn package 时，会对镜像进行 标签设定-->
    <execution>
        <id>tag-image</id>
        <phase>package</phase>
        <goals>
            <goal>tag</goal>
        </goals>
        <configuration>
            <image>${docker.image.prefix}/${project.artifactId}:latest</image>
            <newName>docker.io/${docker.image.prefix}/${project.artifactId}:${project.version}</newName>
        </configuration>
    </execution>
    <execution>
        <id>push-image</id>
        <phase>deploy</phase>
        <goals>
            <goal>push</goal>
        </goals>
        <configuration>
            <imageName>docker.io/${docker.image.prefix}/${project.artifactId}:${project.version}</imageName>
        </configuration>
    </execution>
</executions>
```

总结：

当我们执行 `mvn package` 时，执行 build、tag 操作，

当执行 `mvn deploy` 时，执行build、tag、push 操作。

如果我们想跳过 docker 某个过程时，只需要：

- `-DskipDockerBuild` 跳过 build 镜像
- `-DskipDockerTag` 跳过 tag 镜像
- `-DskipDockerPush` 跳过 push 镜像
- `-DskipDocker` 跳过整个阶段

例如：我们想执行 package 时，跳过 tag 过程，那么就需要 `mvn package -DskipDockerTag`

### 8.3 Idea整合Docker CA加密认证

前面提到的配置是允许所有人都可以访问的，因为docker默认是root权限的，把2375端口暴露在外面，意味着别人随时都可以提取到你服务器的root权限，是很容易被黑客黑的，因此,docker官方推荐使用加密的tcp连接，以Https的方式与客户端建立连接。

官方示例Demo：

https://docs.docker.com/engine/security/https/#create-a-ca-server-and-client-keys-with-openssl

#### Docker认证命令配置

##### 1）创建ca文件夹，存放CA私钥和公钥

```shell
mkdir -p /usr/local/ca 
cd /usr/local/ca/
```

##### 2）生成CA私钥和公钥

```shell
openssl genrsa -aes256 -out ca-key.pem 4096
```

输入密码时，输入两次相同的密码，并且后面会用到。

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230522025233486.png)

##### 3）依次输入密码、国家、省、市、组织名称、邮箱等

```shell
openssl req -new -x509 -days 365 -key ca-key.pem -sha256 -out ca.pem
```

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230522024709823.png)

现在已经有了CA，接下来创建一个服务器密钥和证书签名请求(CSR)。确保“公用名”与你用来连接到Docker的主机名匹配。

##### 4）生成server-key.pem

```shell
openssl genrsa -out server-key.pem 4096
```

##### 5）CA来签署公钥

由于TLS连接可以通过IP地址和DNS名称进行，所以在创建证书时需要指定IP地址。例如，允许使用10.10.10.20 和127.0.0.1进行连接: ]

$Host换成你自己服务器外网的IP或者域名

```shell
openssl req -subj "/CN=$HOST" -sha256 -new -key server-key.pem -out server.csr
 比如
openssl req -subj "/CN=192.168.20.135" -sha256 -new -key server-key.pem -out server.csr 
 或
openssl req -subj "/CN=www.javaqf.com" -sha256 -new -key server-key.pem -out server.csr
```

本地是局域网： 

```shell
openssl req -subj "/CN=192.168.31.212" -sha256 -new -key server-key.pem -out server.csr
```

##### 6）配置白名单

1 允许指定ip可以连接到服务器的docker，可以配置ip，用逗号分隔开。

2 因为已经是ssl连接，所以我推荐配置0.0.0.0,也就是所有ip都可以连接(但只有拥有证书的才可以连接成功)，这样配置好之后公司其他人也可以使用。

```shell
如果填写的是ip地址 命令如下 echo subjectAltName = IP:$HOST,IP:0.0.0.0 >> extfile.cnf 
如果填写的是域名 命令如下 echo subjectAltName = DNS:$HOST,IP:0.0.0.0 >> extfile.cnf
```

上面的$Host依旧是你服务器外网的IP或者域名，请自行替换。

```
echo subjectAltName = IP:192.168.31.212,IP:0.0.0.0 >> extfile.cnf
```

##### 7）执行命令

将Docker守护程序密钥的扩展使用属性设置为仅用于服务器身份验证:

```shell
echo extendedKeyUsage = serverAuth >> extfile.cnf
```

##### 8）生成签名证书

```shell
openssl x509 -req -days 365 -sha256 -in server.csr -CA ca.pem -CAkey ca-key.pem \
-CAcreateserial -out server-cert.pem -extfile extfile.cnf
```

##### 9）生成客户端的key.pem

```shell
openssl genrsa -out key.pem 4096 
openssl req -subj '/CN=client' -new -key key.pem -out client.csr
```

##### 10）要使密钥适合客户端身份验证

创建扩展配置文件:

```shell
echo extendedKeyUsage = clientAuth >> extfile.cnf 
echo extendedKeyUsage = clientAuth > extfile-client.cnf
```

##### 11）现在，生成签名证书

```shell
openssl x509 -req -days 365 -sha256 -in client.csr -CA ca.pem -CAkey ca-key.pem \
-CAcreateserial -out cert.pem -extfile extfile-client.cnf
```

生成cert.pem,需要输入前面设置的密码

##### 12）删除不需要的文件，两个证书签名请求

生成cert.pem和server-cert之后。您可以安全地删除两个证书签名请求和扩展配置文件:

```shell
rm -v client.csr server.csr extfile.cnf extfile-client.cnf
```

##### 13）可修改权限

要保护您的密钥免受意外损坏，请删除其写入权限。要使它们只能被您读取，更改文件模式

```shell
chmod -v 0400 ca-key.pem key.pem server-key.pem
```

证书可以是对外可读的，删除写入权限以防止意外损坏

```shell
chmod -v 0444 ca.pem server-cert.pem cert.pem
```

##### 14）归集服务器证书

```shell
cp server-*.pem /etc/docker/ 
cp ca.pem /etc/docker/
```

##### 15）修改Docker配置

使Docker守护程序仅接受来自提供CA信任的证书的客户端的连接

```shell
vim /lib/systemd/system/docker.service 
将
ExecStart=/usr/bin/dockerd 
替换为： 
ExecStart=/usr/bin/dockerd --tlsverify --tlscacert=/usr/local/ca/ca.pem --tlscert=/usr/local/ca/server-cert.pem --tlskey=/usr/local/ca/server-key.pem -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock
```

##### 16）重新加载daemon并重启docker

```shell
systemctl daemon-reload 
systemctl restart docker
```

##### 17）开放2375端口

```shell
/sbin/iptables -I INPUT -p tcp --dport 2375 -j ACCEPT
```

##### 18）重启docker

```shell
systemctl restart docker
```

#### IDEA操作Docker

##### 1）保存相关客户端的pem文件到本地

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230522030158213.png)

2）IDEA CA配置

![](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230522030617839.png)

PS：如果配置package一键部署，需要把pom中的http改为https

![image-20230522030736946](https://raw.githubusercontent.com/yu980219/image-host/master/hexo/image-20230522030736946.png)

## 九、Docker Compose

### **思考**

前面我们使用 Docker 的时候，定义 Dockerfile 文件，然后使用 docker build、docker run -d --name -p等命令操作容器。然而微服务架构的应用系统一般包含若干个微服务，每个微服务一般都会部署多个实例，如果每个微服务都要手动启停，那么效率之低，维护量之大可想而知

**使用 Docker Compose 可以轻松、高效的管理容器，它是一个用于定义和运行多容器 Docker 的应用程序工具**

### **简介**

`Docker Compose` 是 Docker 官方编排（Orchestration）项目之一，**负责快速的部署分布式应用**。

### 描述

`Compose` 项目是 Docker 官方的开源项目，负责实现对 Docker 容器集群的快速编排。从功能上看，跟 `OpenStack`中的 `Heat` 十分类似。

其代码目前在 https://github.com/docker/compose 上开源。

`Compose` 定位是 「定义和运行多个 Docker 容器的应用（Defining and running multi-container Docker applications）」，其前身是开源项目 Fig。

通过第一部分中的介绍，我们知道使用一个 Dockerfile 模板文件，可以让用户很方便的定义一个单独的应用容器。然而，在日常工作中，经常会碰到需要多个容器相互配合来完成某项任务的情况。例如要实现一个 Web 项目，除了 Web 服务容器本身，往往还需要再加上后端的数据库服务容器，甚至还包括负载均衡容器等。

`Compose` 恰好满足了这样的需求。它允许用户通过一个单独的 `docker-compose.yml` 模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）。

`Compose` 中有两个重要的概念：

- 服务 ( `service` )：一个应用的容器，实际上可以包括若干运行相同镜像的容器实例。
- 项目 ( `project` )：由一组关联的应用容器组成的一个完整业务单元，在 `docker-compose.yml` 文件中定义。

`Compose` 的默认管理对象是项目，通过子命令对项目中的一组容器进行便捷地生命周期管理。

`Compose` 项目由 Python 编写，实现上调用了 Docker 服务提供的 API 来对容器进行管理。因此，只要所操作的平台支持 Docker API，就可以在其上利用 `Compose` 来进行编排管理。

### Docker Compose使用

#### 术语

> 首先介绍几个术语。
>
> - 服务 ( service )：一个应用容器，实际上可以运行多个相同镜像的实例。
> - 项目 ( project )：由一组关联的应用容器组成的一个完整业务单元。
>
> 可见，一个项目可以由多个服务（容器）关联而成， Compose 面向项目进行管理。

#### Docker-compose创建容器

> 通过一个单独的 docker-compose.yml 模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）。

##### 示例：

**用compose的方式创建3个springboot服务**

```shell
#1 管理文件夹，创建相应的目录
[root@MiWiFi-R4A-srv ~]# mkdir -p /opt/docker-boot-cluster
[root@MiWiFi-R4A-srv ~]# cd /opt/docker-boot-cluster/
```

#2 在如上目录中 编写创建 `docker-compose.yml`配置文件

编写 docker-compose.yml 文件，这个是 Compose 使用的主模板文件。

```yml
version: '3.1'
services:
  boot-8080:
    restart: always
    image: yu/myfavorites
    container_name: boot-8080
    ports:
      - 8080:8080
  boot-8081:
    restart: always
    image: yu/myfavorites
    container_name: boot-8081
    ports:
      - 8081:8080
  boot-8082:
    restart: always
    image: yu/myfavorites
    container_name: boot-8082
    ports:
      - 8082:8080
```

```shell
#3 启动(执行命令创建容器)
docker-compose up -d
```

#默认执行的文件名：docker-compose.yml(且需要在当前上下文路径中) 。如果说文件名不是默认的需要使用下面的指令：

```shell
docker-compose -f 文件名.后缀名 up –d
```

##### **测试：**

分别访问8080，8081，8082端口的接口，调用成功。

#### Docker-compose 常用命令

> build 构建或重建服务
> help 命令帮助
> kill 杀掉容器
> logs 显示容器的输出内容
> port 打印绑定的开放端口
> ps 显示容器
> pull 拉取服务镜像
> restart 重启服务
> rm 删除停止的容器
> run 运行一个一次性命令
> scale 设置服务的容器数目
> start 开启服务
> stop 停止服务
> up 创建并启动容器
> down

#### 常用命令示例：

> docker-compose up -d nginx 构建启动nignx容器
> docker-compose exec nginx bash 登录到nginx容器中
> docker-compose down 删除所有nginx容器,镜像
> docker-compose ps 显示所有容器
> docker-compose restart nginx 重新启动nginx容器
> docker-compose run --no-deps --rm php-fpm php -v 在php-fpm中不启动关联容器，并容器执行php -v 执行完成后删除容器
> docker-compose build nginx 构建镜像 。
> docker-compose build --no-cache nginx 不带缓存的构建。
> docker-compose logs nginx 查看nginx的日志
> docker-compose logs -f nginx 查看nginx的实时日志
> docker-compose config -q 验证（docker-compose.yml）文件配置，当配置正确时，不输出任何内容，当文件配置错误，输出错误信息。
> docker-compose events --json nginx 以json的形式输出nginx的docker日志
> docker-compose pause nginx 暂停nignx容器
> docker-compose unpause nginx 恢复ningx容器
> docker-compose rm nginx 删除容器（删除前必须关闭容器）
> docker-compose stop nginx 停止nignx容器
> docker-compose start nginx 启动nignx容器
