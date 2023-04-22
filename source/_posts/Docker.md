# Docker

## Docker概述

### Docker为什么出现？

一款产品： 开发–上线 两套环境！应用环境，应用配置！（运维）

环境配置是十分的麻烦，每一个机器都要部署环境(集群Redis、ES、Hadoop…) !费事费力。而且也不能跨平台

于是就想：发布一个项目( jar + (Redis MySQL JDK ES) )，项目能不能带上环境安装打包！

传统：开发jar，运维来做！

现在：Docker提出了解决方案！开发打包部署上线，一套流程做完！

安卓流程：java — apk —发布（应用商店）一 张三使用apk一安装即可用！

docker流程： java-jar（环境） — 打包项目帯上环境（镜像） — ( Docker仓库：商店）–下载我们发布的镜像—直接运行即可

![在这里插入图片描述](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/c575979e284441b1bbb0ad13b754e7dc.png)

Docker的思想就来自于集装箱！

JRE – 多个应用(端口冲突) – 原来都是交叉的！

隔离：Docker核心思想！打包装箱！每个箱子是互相隔离的。

Docker通过隔离机制，可以将服务器利用到极致

本质：所有的技术都是因为出现了一些问题，我们需要去解决，才去学习

### Docker的历史

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

![docker官网](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230418000159864.png)

- 文档地址：https://docs.docker.com/ Docker的文档是超级详细的！

- 仓库地址：https://hub.docker.com/ git命令在这里都可以使用

### Docker能干嘛

> 之前的虚拟机技术

![image-20230418002123031](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230418002123031.png)

虚拟机技术缺点：

1. 资源占用十分多
2. 冗余步骤非常多
3. 启动很慢

> 容器化技术

![image-20230418002340896](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230418002340896.png)

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

## Docker安装

### Docker的基本组成

![Docker架构图](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/1591950504042.png)
**镜像（image）**：

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

### 安装Docker

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

![image-20230418010541255](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230418010541255.png)

```shell
# 测试
docker run hello-world
```

![image-20230418010937780](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230418010937780.png)

```shell
# 查看下载的这个 hello-world 镜像
docker images
```

![image-20230418011058247](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230418011058247.png)

卸载docker

```shell
# 1.卸载依赖
yum remove docker-ce docker-ce-cli containerd.io

# 2.删除资源
rm -rf /var/lib/docker

# /var/lib/docker：docker的默认工作路径
```

### 阿里云镜像加速

阿里云镜像加速：略

Docker配置国内镜像源加速教程：https://www.runoob.com/docker/docker-mirror-acceleration.html

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

![image-20230420002709854](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230420002709854.png)

### 回顾Hello World流程

![image-20230419234350846](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230419234350846.png)

### 底层原理

**Docker是怎么工作的？**

Docker是一个 Client - Server 结构的系统，Docker 的守护进程运行在主机上。通过 Socket 从客户端访问！

Docker-Server 接收到 Docker-Client 的指令，就会执行这个命令！

![image-20230419234649418](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230419234649418.png)

**Docker 为什么比 VM 快？**

1. Docker 有着比虚拟机更少的抽象层
2. Docker 利用的是宿主机的内核，VM 需要的是 Guest OS

![img](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/u=1974852863,3311841718&fm=253&fmt=auto&app=138&f=JPEG)

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

## Docker的常用命令

### 帮助命令

```shell
docker version	# 显示docker的版本信息。
docker info		# 显示docker的系统信息，包括镜像和容器的数量
docker --help	# 帮助命令
```

帮助文档的地址：https://docs.docker.com/engine/reference/commandline/

### 镜像命令

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

![image-20230420005418921](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230420005418921.png)

![image-20230420005626787](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230420005626787.png)

- **docker rmi** 删除镜像

```shell
docker rmi -f 镜像id                # 删除指定的镜像
docker rmi -f 镜像id 镜像id 镜像id   # 删除多个镜像
docker rmi -f $(docker images -aq)  # 删除全部镜像
```

### 容器命令

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

![image-20230420011029449](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230420011029449.png)

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

![image-20230420012014388](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230420012014388.png)

### 常用其它命令

- **后台启动容器**

```shell
# 命令 docker run -d 镜像名
docker run -d centos

# 问题docker ps, 发现 centos 停止了

# 常见的坑, docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
# 如nginx，容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了
```

![image-20230420224011278](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230420224011278.png)

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

![image-20230420225009481](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230420225009481.png)

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

### 小结

![img](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/2363254-20211002145236621-74451512.png)

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

### 作业练习

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

![image-20230420234237227](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230420234237227.png)

可以在公网访问了

![image-20230420234333825](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230420234333825.png)

停止容器

```shell
[root@master home]# docker stop 6ba58e0cadf8
6ba58e0cadf8
```

外网无法访问

![image-20230420235128398](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230420235128398.png)

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

![image-20230421001109021](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230421001109021.png)

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

![image-20230421001604839](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230421001604839.png)

**思考问题：**我们以后要部署项目，都需要进入容器内部，十分的麻烦。我要是可以在容器外部放置项目，能自动同步到内部就好了。

#### 部署ES+Kibana

略

### 可视化

- **portainer**（先用这个，不常用）

  介绍：Docker 图形化界面管理工具！提供一个后台面板供我们操作！

```shell
# 运行如下命令即可 打开可视化服务
docker run -d -p 8088:9000 \--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

访问测试：从外网访问8088，http://ip:8088/，第一次进入时间比较久，因为它要把所有的数据给挂载出来。

![登陆界面](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230422201924364.png)

给它设置一个密码就可以使用了

![image-20230422202020730](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230422202020730.png)

选择本地的：Local，进入之后的面板如下

![image-20230422202148113](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230422202148113.png)

![image-20230422202243513](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230422202243513.png)

可视化面板平时不会使用，大家自己测试玩玩即可。

## Docker镜像讲解

### 镜像是什么

- 镜像是一种轻量级、可执行的独立软件保，用来打包软件运行环境和基于运行环境开发的软件，他包含运行某 个软件所需的所有内容，包括代码、运行时库、环境变量和配置文件。

- 将所有的应用和环境，直接打包为docker镜像，就可以直接运行。

  如何得到镜像：

  - 从远处仓库下载
  - 他人拷贝过来
  - 自己制作一个镜像 DockerFile

### Docker 镜像加载原理

> UnionFs（联合文件系统）

- 我们下载的时候看到一层层的下载就是这个。
- UnionFs（联合文件系统）：Union文件系统（UnionFs）是一种分层、轻量级并且高性能的文件系统，他支 持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下（ unite several directories into a single virtual filesystem)。Union文件系统是 Docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像
- 特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系 统叠加起来，这样最终的文件系统会包含所有底层的文件和目录。

> Docker镜像加载原理

- docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统就是 UnionFS。
- bootfs（boot file system）主要包含 bootloader 和 Kernel，bootloader 主要是引导加载 kernel，Linux 刚启动时会加 bootfs 文件系统，在 Docker 镜像的最底层是 boots。这一层与我们典型的 Linux/Unix 系统是一样的，包含 boot 加載器和内核。当 boot 加载完成之后整个内核就都在内存中了，此时 内存的使用权已由 bootfs 转交给内核，此时系统也会卸载 bootfs。
- rootfs（root file system)，在 bootfs 之上。包含的就是典型 Linux 系统中 的 /dev，/proc，/bin。/etc 等标准目录和文件。 rootfs 就是各种不同的操作系统发行版，比如 Ubuntu，Centos等等。

![image-20230422202846356](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230422202846356.png)

平时我们安装进虚拟机的CentOS都是好几个G，为什么Docker这里才200M？

![image-20230422203318596](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230422203318596.png)

对于个精简的OS，rootfs 可以很小，只需要包合最基本的命令，工具和程序库就可以了，因为底层直接用 Host 的 kernel，自己只需要提供 rootfs 就可以了。由此可见对于不同的 Linux 发行版，boots基本是一致 的，rootfs 会有差別，因此不同的发行版可以公用 bootfs.

虚拟机是分钟级别，容器是秒级！

### 分层理解

我们下载一个镜像，注意观察下载的日志输出，可以看到是一层层的在下载

第一层显示 Already exists，已经存在，是基本层

![image-20230422203520499](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230422203520499.png)

> 思考：为什么Docker镜像要采用这种分层的结构呢？

最大的好处就是资源共享！比如有多个镜像都从相同的 Base 镜像构建而来，那么宿主机只需在磁盘上保留一份base镜像，同时内存中也只需要加载一份base镜像，这样就可以为所有的容器服务了，而且镜像的每一层都可以被共享。

查看镜像分层的方式可以通过`docker image inspect [镜像名]` 命令：docker image inspect redis:latest

![image-20230422203945998](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230422203945998.png)

> 理解

- 所有的 Docker镜像都起始于一个基础镜像层，当进行修改或增加新的内容时，就会在当前镜像层之上，创建新的镜像层。
- 举一个简单的例子，假如基于 Ubuntu Linux16.04 创建一个新的镜像，这就是新镜像的第一层；如果在该镜像中添加 Python 包，就会在基础镜像层之上创建第二个镜像层；如果继续添加一个安全补丁，就会创建第三个镜像层
- 该像当前已经包含3个镜像层，如下图所示（这只是一个用于演示的很简单的例子）。

![image-20201109091850084](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/f9eef1731785a9c7452ceaf45e0b0f79.png)

再添加额外镜像层的同时，镜像始终保持是当前所有镜像的组合，（理解这一点非常重要）如下图，每个镜像层包含 3 个文件，而镜像包含了来自两个镜像层的 6 个文件。

![image-20201109091955012](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/4558c75783b3a5aafb0b470a46265e0f.png)

上图中的镜像层跟之前图中的略有区別，主要目的是便于展示文件

下图中展示了一个稍微复杂的三层镜像，在外部看来整个镜像只有6个文件，这是因为最上层中的文件7是文件5的一个更新版

![image-20201109092028518](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/4f17993c4f3aba2dbe9675810292527f.png)

这种情况下，上层镜像层中的文件覆盖了底层镜像层中的文件。这样就使得文件的更新版本作为一个新镜像层添加到镜像当中。

Docker通过存储引擎（新版本采用快照机制）的方式来实现镜像层堆栈，并保证多镜像层对外展示为统一的文件系统。

Linux上可用的存储引擎有AUFS、 Overlay2、 Device Mapper、Btrfs以及ZFS。顾名思义，每种存储引擎都基于 Linux中对应的文件系统或者块设备技术，并且每种存储引擎都有其独有的性能特点。

Docker在 Windows上仅支持 windowsfilter 一种存储引擎，该引擎基于NTFS文件系统之上实现了分层和CoW(写时复制)。

下图展示了与系统显示相同的三层镜像。所有镜像层堆叠并合并，对外提供统一的视图。

![image-20201109092216584](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/207ddb28ff28e743cc14089c90af7241.png)

> 特点

Docker 镜像都是只读的，当容器启动时，一个新的可写层加载到镜像的顶部！

这一层就是我们通常说的容器层，容器之下的都叫镜像层！

![image-20201109093016813](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/9872d363fc277f3bba2c822efb2ac2a3.png)

如何提交一个自己的镜像？

### commit镜像

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

![image-20230422211313150](https://gitee.com/haktiong/picture-warehouse/raw/master/images/docker/image-20230422211313150.png)

如果你想要保存当前容器的状态，就可以通过commit来提交，获得一个镜像，就好比使用VM的时候的快照。

到这里才算是入门Docker。
