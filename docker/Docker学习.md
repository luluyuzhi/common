

# Docker 入门学习

docker（容器化技术）

### docker和虚拟机技术的不同

- 传统虚拟机，虚拟出一台硬件环境，运行一个完整的操作系统，然后在这个系统上安装和运行软件
- 容器内的应用直接运行在  `宿主机`  的内核中的，容器是没有自己的内核的，也没有虚拟我们的硬件，所以就轻便
- 每个容器间是互相隔离，每个容器内都有一个属于自己的文件系统，互不影响、

> 举个例子：之前使用虚拟机，电脑装了3个hadoop节点的集群，电脑就开始卡了
>
> 使用容器后，你可能会装到30个（有点夸张了啊！）

> DevOps（开发、运维）

应用更快速的交付和部署、

**更快速的交付和部署**

传统：一堆帮助文档，安装程序

Docker：一键运行打包镜像发布测试，一键运行

**更便捷的升级和扩缩容**

使用了Docker之后，我们部署应用就和搭积木一样！！！

项目打包为一个镜像，扩展 服务器A！服务器B

**更简单的系统运维**

在容器化之后，我们的开发，测试环境都是高度一致的。（不会再出现在我的电脑上可以使用，再别人的电脑上就不可以使用了）

**更高效的计算资源利用：**

Docker是内核级别的虚拟化，可以再一个物理机上运行很多的容器实例！服务器的性能可以被压榨到极致。

## Docker安装

### Docker 的基本组成

![img](https://www.runoob.com/wp-content/uploads/2016/04/576507-docker1.png)

- **镜像**：Docker镜像（image），就相当于一个root文件系统。比如官方镜像ubuntu16.04就包含了完整的一套ubuntu16.04最下系统的root文件系统
- **容器(Container)**：镜像和容器的关系，就像是面向对象程序设计中的类和对象一样，镜像是静态的定义，容器是镜像运行时的实例。容器可以被创建、启动、停止、删除、暂定等等。
- **仓库（Repository）**：仓库可看成一个代码控制中心，用来保存镜像。

| 概念                        | 说明                                                         |
| --------------------------- | ------------------------------------------------------------ |
| **Docker镜像（Image）**     | Docker镜像是用于创建Docker容器的模板，比如Ubuntu系统。       |
| **Docker容器（Container）** | 容器是独立运行的一个或一组应用，是镜像运行时的实体           |
| **Docker客户端（Client）**  | Docker客服端通过命令或者其他工具Docker SDK 与 Docker的守护进程通信。 |
| **Docker主机（Host）**      | 一个物理或者虚拟的机器用于执行Docker守护进程和容器           |
| **Docker Registry**         | Docker仓库用来保存镜像，可以理解为代码控制中的代码仓库、<br />Docker Hub（https://hub.docker.com）提供了庞大的镜像集合供使用（但是在国内，我们需要**配置镜像加速**）<br />一个Docker Registry中可以包含多个仓库（Repository）；每个仓库可以包含多个标签（tag）；每个标签对应一个镜像。<br />通常，一个仓库会包含同一个软件不同版本的镜像，而标签就常用于对应软件的各个版本。我们可以通过**<仓库名>:<标签>**的格式来指定具体是这个软件哪个版本的镜像。如果不给出标签，将以latest作为默认标签 |
| **Docker Machine**          | Docker Machine是一个简化Docker安装的命令行工具，通过一个简单的命令即可在相应的平台上安装Docker。<br />比如VirtualBox。Digital Ocean、Microsoft Azure |

### 安装Docker

参考官方文档https://docs.docker.com/engine/install/centos/

在centos7下安装docker

####  卸载旧版

```shell
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

#### 安装yum yum-utils基础工具

```shell
 sudo yum install -y yum-utils
 sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo  -- 配置docker的yum源，由于是国外的地址，我们需要配合国内的镜像
    
 sudo yum install -y yum-utils
 sudo yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    
```

#### 更新yum索引

```shell
yum makecache fast
```

#### 安装Docker引擎

```shell
sudo yum install docker-ce docker-ce-cli containerd.io
```

#### 启动Docker服务

```shell
sudo systemctl start docker
```

#### 设置开机自启

```shell
sudo systemctl enable docker.service	
```

#### 设置docker镜像源

```shell
$ vim /etc/docker/daemon.json
{
  "registry-mirrors": ["https://iqckan08.mirror.aliyuncs.com"]
}
$ sudo systemctl daemon-reload 重载
$sudo systemctl restart docker 最后检查是否·成功
```

docker装成功之后，输入docker version 可以查看docker是否安装成功

```shell
[hadoop@hadoop102 ~]$ sudo docker version
Client: Docker Engine - Community
 Version:           20.10.7
 API version:       1.41
 Go version:        go1.13.15
 Git commit:        f0df350
 Built:             Wed Jun  2 11:58:10 2021
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.7
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       b0f5bc3
  Built:            Wed Jun  2 11:56:35 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.6
  GitCommit:        d71fcd7d8303cbf684402823e425e9dd2e99285d
 runc:
  Version:          1.0.0-rc95
  GitCommit:        b9ee9c6314599f1b4a7f497e1f1f856fe433d3b7
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

### Docker底层原理

#### Docker是怎么工作的？

Docker是一个Client-Server结构的系统，Docker的守护进程运行在主机上，通过Socket从客户端访问！

Docker-Server接收到Docker-Client的指令，就会执行这些指令![image-20210605185400973](C:\Users\LuYu\AppData\Roaming\Typora\typora-user-images\image-20210605185400973.png)

<img src="https://img-blog.csdnimg.cn/20200411132031597.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0ODkxMjk1,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" style="zoom:50%;" />

#### Docker为什么比VM快？

<img src="https://img1.baidu.com/it/u=3114630737,3916130790&fm=26&fmt=auto&gp=0.jpg" alt="img" style="zoom:150%;" />

1. Docker有着比虚拟机更少的抽象层
2. Docker利用的是宿主机的内核（kernel，只有一个）
   1. 而VM需要Guest OS。

所以说，新建一个容器的时候，，docker不需要像虚拟机一样重新加载一个操作系统内核，避免引导。虚拟机是加载Guest OS的，是分钟级别；而docker是利用宿主机的操作系统，省略了Guest OS那一层的加载，启动时秒级的。

**Host OS 与 Guest OS**

Host OS(主人操作系统）就是安装在你硬件设备上的系统，而Guest OS(客人操作系统）则是安装在虚拟机（VM）上面的系统。

例如你的电脑上的Windows系统就是作为Host OS,如果在你的电脑设备上拓展出一些虚拟机，并在虚拟机上安裝了 Windows XP，那么Windows XP 就叫做 Guest OS。



## Docker的常用命令

### 帮助命令

```shell
docker version			# 显示docker的版本信息
docker info			    # 显示docker的系统信息，包括镜像和容器的数量
docker 命令 --help	   # 帮助信息
```

帮助文档的地址：https://docs.docker.com/reference/

### 镜像命令

#### **docker images** 查看所有本地的主机上的镜像

```shell
# 1. docker images
[hadoop@hadoop102 ~]$ sudo docker images
[sudo] hadoop 的密码：
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    d1165f221234   3 months ago   13.3kB
#解释
REPOSITORY		镜像的仓库名
TAG				镜像的标签
IMAGE ID		镜像的ID
CREATED			镜像的创建时间
SIZE			镜像的大小
# 可选项
	-a，--all	#列出所有镜像
	-q，--quiet  # 只显示镜像的ID
# 测试
[hadoop@hadoop102 ~]$ sudo docker images -a
[sudo] hadoop 的密码：
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    d1165f221234   3 months ago   13.3kB
[hadoop@hadoop102 ~]$ sudo docker images -aq
d1165f221234
[hadoop@hadoop102 ~]$
```

#### **docker search**搜索镜像

```shell
[hadoop@hadoop102 ~]$ sudo docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   10957     [OK]
mariadb                           MariaDB Server is a high performing open sou…   4142      [OK]
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   814                  [OK]
percona                           Percona Server is a fork of the MySQL relati…   539       [OK]
centos/mysql-57-centos7           MySQL 5.7 SQL database server                   88
mysql/mysql-cluster               Experimental MySQL Cluster Docker images. Cr…   85
centurylink/mysql                 Image containing mysql. Optimized to be link…   59                   [OK]
bitnami/mysql                     Bitnami MySQL Docker Image                      52                   [OK]
databack/mysql-backup             Back up mysql databases to... anywhere!         43
deitch/mysql-backup               REPLACED! Please use http://hub.docker.com/r…   41                   [OK]

```

#### **docker pull[:tag]** 下载镜像

```shell
[hadoop@hadoop102 ~]$ sudo docker pull --help
[sudo] hadoop 的密码：

Usage:  docker pull [OPTIONS] NAME[:TAG|@DIGEST]

Pull an image or a repository from a registry

Options:
  -a, --all-tags                Download all tagged images in the repository
      --disable-content-trust   Skip image verification (default true)
      --platform string         Set platform if server is multi-platform capable
  -q, --quiet                   Suppress verbose output


# pull Mysql 镜像
[hadoop@hadoop102 ~]$ sudo docker pull mysql
Using default tag: latest			# 不指定tag， 默认最新版本
latest: Pulling from library/mysql
69692152171a: Downloading			# 分层下载，docker image的核心  联合文件系统
1651b0be3df3: Downloading
951da7386bc8: Downloading
0f86c95aa242: Waiting
37ba2d8bd4fe: Waiting
6d278bb05e94: Downloading
497efbd93a3e: Waiting
f7fddf10c2c2: Waiting
16415d159dfb: Waiting
0e530ffc6b73: Waiting
b0a4a1a77178: Waiting
cd90f92aa9ef: Waiting
latest: Pulling from library/mysql
69692152171a: Pull complete
1651b0be3df3: Pull complete
951da7386bc8: Pull complete
0f86c95aa242: Pull complete
37ba2d8bd4fe: Pull complete
6d278bb05e94: Pull complete
497efbd93a3e: Pull complete
f7fddf10c2c2: Pull complete
16415d159dfb: Pull complete
0e530ffc6b73: Pull complete
b0a4a1a77178: Pull complete
cd90f92aa9ef: Pull complete
Digest: sha256:d50098d7fcb25b1fcb24e2d3247cae3fc55815d64fec640dc395840f8fa80969	# 签名
Status: Downloaded newer image for mysql:lates
docker.io/library/mysql:latest	# 真实地址

# 说明
docker pull mysql
docker pull docker.io/library/mysql:latest 
# 上述这两条命令是等价的

```

#### **docker rmi imageID**:删除镜像、

```shell
[hadoop@hadoop102 ~]$ sudo docker rmi --help

Usage:  docker rmi [OPTIONS] IMAGE [IMAGE...]

Remove one or more images

Options:
  -f, --force      Force removal of the image
      --no-prune   Do not delete untagged parents
# 删除一个镜像
[hadoop@hadoop102 ~]$ sudo docker rmi -f d1165f221234
Untagged: hello-world:latest
Untagged: hello-world@sha256:5122f6204b6a3596e048758cabba3c46b1c937a46b5be6225b835d091b90e46c
Deleted: sha256:d1165f2212346b2bab48cb01c1e39ee8ad1be46b87873d9ca7a4e434980a7726

# 批量删除
sudo docker rmi -f 镜像ID1 镜像ID2 镜像ID3	# 删除多个镜像，中间使用空格分隔
sudo docker rmi -f `sudo docker images -aq` #删除全部镜像
sudo docker rmi -f $(sudo docker images -aq) #删除全部镜像
[hadoop@hadoop102 ~]$ sudo docker rmi -f `sudo docker images -aq`  # 查找出所有的images的ID
Untagged: mysql:5.7
Untagged: mysql@sha256:a682e3c78fc5bd941e9db080b4796c75f69a28a8cad65677c23f7a9f18ba21fa
Deleted: sha256:2c9028880e5814e8923c278d7e2059f9066d56608a21cd3f83a01e3337bacd68
Deleted: sha256:c49c5c776f1bc87cdfff451ef39ce16a1ef45829e10203f4d9a153a6889ec15e
Deleted: sha256:8345316eca77700e62470611446529113579712a787d356e5c8656a41c244aee
Deleted: sha256:8ae51b87111404bd3e3bde4115ea2fe3fd2bb2cf67158460423c361a24df156b
Deleted: sha256:9d5afda6f6dcf8dd59aef5c02099f1d3b3b0c9ae4f2bb7a61627613e8cdfe562
Untagged: mysql:latest
Untagged: mysql@sha256:d50098d7fcb25b1fcb24e2d3247cae3fc55815d64fec640dc395840f8fa80969
Deleted: sha256:c0cdc95609f1fc1daf2c7cae05ebd6adcf7b5c614b4f424949554a24012e3c09
Deleted: sha256:137bebc5ea278e61127e13cc7312fd83874cd19e392ee87252b532f0162fbd56
Deleted: sha256:7ed0de2ad4e43c97f58fa9e81fba73700167ef9f8a9970685059e0109749a56b
Deleted: sha256:9375660fbff871cd29c86b8643be60e376bfc96e99a3d7e8f93d74cd61500705
Deleted: sha256:d8a47065d005ac34d81017677330ce096eb5562eeb971e2db12b0e200fdb1cb6
Deleted: sha256:ca13c8ad9df5d824d5a259a927eaa6c04a60f022bc2abe8fc7866cf4b2b366f4
Deleted: sha256:7af1865d5c19316c3dc0829a2ee2b3a744ae756f7fec9c213d3afc5f1f6ed306
Deleted: sha256:f205c8f3c8aaa6376442b34c0c2062738461d37e0aa16ba021cd7e09c67213c2
Deleted: sha256:d690e8a8242cf13cbe98c5b2faffdd0fc7e6c4c13425b5da31de991aa1f89a76
Deleted: sha256:24efeee958e9f3d859fe15540e9296d5aaa6d3eb3b5f5494a2e8370608a4cfaa
Deleted: sha256:654f2ffede3bb536fd62d04c9c7b7826e890828bec92182634e38684959b2498
Deleted: sha256:de478a06eaa676052e665faa0b07d86a007f4b87cf82eb46a258742dc2d32260
Deleted: sha256:02c055ef67f5904019f43a41ea5f099996d8e7633749b6e606c400526b2c4b33
[hadoop@hadoop102 ~]$ sudo docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE

```

### 容器命令

#### 新建容器并启动：docker run

```shell
[hadoop@hadoop102 ~]$ sudo docker run --help

Usage:  docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Run a command in a new container

Options:
      --add-host list                  Add a custom host-to-IP mapping (host:ip)
  -a, --attach list                    Attach to STDIN, STDOUT or STDERR
      --blkio-weight uint16            Block IO (relative weight), between 10 and 1000, or 0 to disable (default 0)
      --blkio-weight-device list       Block IO weight (relative device weight) (default [])
      --cap-add list                   Add Linux capabilities
      --cap-drop list                  Drop Linux capabilities
      --cgroup-parent string           Optional parent cgroup for the container
      --cgroupns string                Cgroup namespace to use (host|private)
                                       'host':    Run the container in the Docker host's cgroup namespace
                                       'private': Run the container in its own private cgroup namespace
                                       '':        Use the cgroup namespace as configured by the
                                                  default-cgroupns-mode option on the daemon (default)
      --cidfile string                 Write the container ID to the file
      --cpu-period int                 Limit CPU CFS (Completely Fair Scheduler) period
      --cpu-quota int                  Limit CPU CFS (Completely Fair Scheduler) quota
      --cpu-rt-period int              Limit CPU real-time period in microseconds
      --cpu-rt-runtime int             Limit CPU real-time runtime in microseconds
  -c, --cpu-shares int                 CPU shares (relative weight)
      --cpus decimal                   Number of CPUs
      --cpuset-cpus string             CPUs in which to allow execution (0-3, 0,1)
      --cpuset-mems string             MEMs in which to allow execution (0-3, 0,1)
  -d, --detach                         Run container in background and print container ID
      --detach-keys string             Override the key sequence for detaching a container
      --device list                    Add a host device to the container
      --device-cgroup-rule list        Add a rule to the cgroup allowed devices list
      --device-read-bps list           Limit read rate (bytes per second) from a device (default [])
      --device-read-iops list          Limit read rate (IO per second) from a device (default [])
      --device-write-bps list          Limit write rate (bytes per second) to a device (default [])
      --device-write-iops list         Limit write rate (IO per second) to a device (default [])
      --disable-content-trust          Skip image verification (default true)
      --dns list                       Set custom DNS servers
      --dns-option list                Set DNS options
      --dns-search list                Set custom DNS search domains
      --domainname string              Container NIS domain name
      --entrypoint string              Overwrite the default ENTRYPOINT of the image
  -e, --env list                       Set environment variables
      --env-file list                  Read in a file of environment variables
      --expose list                    Expose a port or a range of ports
      --gpus gpu-request               GPU devices to add to the container ('all' to pass all GPUs)
      --group-add list                 Add additional groups to join
      --health-cmd string              Command to run to check health
      --health-interval duration       Time between running the check (ms|s|m|h) (default 0s)
      --health-retries int             Consecutive failures needed to report unhealthy
      --health-start-period duration   Start period for the container to initialize before starting health-retries countdown (ms|s|m|h) (default 0s)
      --health-timeout duration        Maximum time to allow one check to run (ms|s|m|h) (default 0s)
      --help                           Print usage
  -h, --hostname string                Container host name
      --init                           Run an init inside the container that forwards signals and reaps processes
  -i, --interactive                    Keep STDIN open even if not attached
      --ip string                      IPv4 address (e.g., 172.30.100.104)
      --ip6 string                     IPv6 address (e.g., 2001:db8::33)
      --ipc string                     IPC mode to use
      --isolation string               Container isolation technology
      --kernel-memory bytes            Kernel memory limit
  -l, --label list                     Set meta data on a container
      --label-file list                Read in a line delimited file of labels
      --link list                      Add link to another container
      --link-local-ip list             Container IPv4/IPv6 link-local addresses
      --log-driver string              Logging driver for the container
      --log-opt list                   Log driver options
      --mac-address string             Container MAC address (e.g., 92:d0:c6:0a:29:33)
  -m, --memory bytes                   Memory limit
      --memory-reservation bytes       Memory soft limit
      --memory-swap bytes              Swap limit equal to memory plus swap: '-1' to enable unlimited swap
      --memory-swappiness int          Tune container memory swappiness (0 to 100) (default -1)
      --mount mount                    Attach a filesystem mount to the container
      --name string                    Assign a name to the container
      --network network                Connect a container to a network
      --network-alias list             Add network-scoped alias for the container
      --no-healthcheck                 Disable any container-specified HEALTHCHECK
      --oom-kill-disable               Disable OOM Killer
      --oom-score-adj int              Tune host's OOM preferences (-1000 to 1000)
      --pid string                     PID namespace to use
      --pids-limit int                 Tune container pids limit (set -1 for unlimited)
      --platform string                Set platform if server is multi-platform capable
      --privileged                     Give extended privileges to this container
  -p, --publish list                   Publish a container's port(s) to the host
  -P, --publish-all                    Publish all exposed ports to random ports
      --pull string                    Pull image before running ("always"|"missing"|"never") (default "missing")
      --read-only                      Mount the container's root filesystem as read only
      --restart string                 Restart policy to apply when a container exits (default "no")
      --rm                             Automatically remove the container when it exits
      --runtime string                 Runtime to use for this container
      --security-opt list              Security Options
      --shm-size bytes                 Size of /dev/shm
      --sig-proxy                      Proxy received signals to the process (default true)
      --stop-signal string             Signal to stop a container (default "SIGTERM")
      --stop-timeout int               Timeout (in seconds) to stop a container
      --storage-opt list               Storage driver options for the container
      --sysctl map                     Sysctl options (default map[])
      --tmpfs list                     Mount a tmpfs directory
  -t, --tty                            Allocate a pseudo-TTY
      --ulimit ulimit                  Ulimit options (default [])
  -u, --user string                    Username or UID (format: <name|uid>[:<group|gid>])
      --userns string                  User namespace to use
      --uts string                     UTS namespace to use
  -v, --volume list                    Bind mount a volume
      --volume-driver string           Optional volume driver for the container
      --volumes-from list              Mount volumes from the specified container(s)
  -w, --workdir string                 Working directory inside the container
```

```shell
# docker run [可选参数] image   常用参数说明
--name="Name"			# 容器的名字，给容器起个名字，用来区分容器
-d						# 后台方式运行，类似于nohup
-it						# 使用交互式方式运行，进入容器查看内容
-p						# 指定容器的端口， -p 8080:8080
						-p ip:主机端口:容器端口
						-p 主机端口:容器端口（常用）
						-p 容器端口
						容器端口
-P						# 随机指定端口
# 测试使用，启动并进入容器  -i 交互式方式  -t 终端   /bin/bash 交互式方式
[hadoop@hadoop102 ~]$ sudo docker run -it centos /bin/bash
[root@713702cc6293 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

```

#### 退出容器：exit

exit退出容器后，容器就停止了

```shell
ctrl+p+q  退出后，容器不停止
```

#### 查看运行的容器：docker ps

```shell
# 查看正在运行的容器有哪些
docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

# docker ps
		# 不带任何选项，列出正在运行的容器
-a		# 列出正在运行的容器+历史运行过的容器
-a -n=? # 显示最近使用的？个容器
-q		# 只显示

# 查看曾经所有运行过的容器有哪些
docker pa -a
[hadoop@hadoop102 ~]$ sudo docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED          STATUS                      PORTS     NAMES
713702cc6293   centos         "/bin/bash"   23 seconds ago   Exited (0) 8 seconds ago              goofy_fermat
fa6d40911878   300e315adb2f   "/bin/bash"   34 minutes ago   Exited (0) 34 minutes ago             Centos_OS
06b202339143   d1165f221234   "/hello"      5 hours ago      Exited (0) 5 hours ago                jolly_perlman
4b43a5cdbedc   d1165f221234   "/hello"      5 hours ago      Exited (0) 5 hours ago                lucid_brahmagupta
```

#### 删除容器

```shell
docker rm 容器id				# 删除指定的容器 不能删除正在运行的容器
docker rm -f $(docker ps -aq)	# 删除所有的容器
docker ps -aq | xargs docker rm  # 利用linux的管道进行删除所有的容器
```

#### 启动和停止容器的操作

```shell
docker start 容器id
docker restart 容器id
docker stop 容器id
docker kill 容器id  # 强制杀掉容器
```

### 常用其他命令

#### 后台启动+容器

```shell
#命令 docker run -d 镜像名
[hadoop@hadoop102 ~]$ sudo docker run -d centos
6aeb3d547f3ecfdc38c27148fe59033fb7cd74b49acd61c2b236bc422e0b3d85
[hadoop@hadoop102 ~]$ sudo docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[hadoop@hadoop102 ~]$
# 问题：docker ps 发现 刚刚后台启动的容器停止了
# 常见的坑：docker 容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
# nginx，容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了。
```

#### 显示日志

```shell
[hadoop@hadoop102 ~]$ sudo docker logs --help

Usage:  docker logs [OPTIONS] CONTAINER

Fetch the logs of a container

Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output
      --since string   Show logs since timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)
  -n, --tail string    Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     Show timestamps
      --until string   Show logs before a timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)
```



```shell
# docker logs -tf --tail 10 容器id

# 创建一个容器，并执行一段shell脚本命令
[hadoop@hadoop102 ~]$ sudo docker run -d 300e315adb2f /bin/sh -c "while true;do echo luyu;sleep 1;done"
aac0df3b1f54b0ed7b637423d1800d363ebeb20901f5cb17b2d33597c3b3fe0d
[hadoop@hadoop102 ~]$ sudo docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS     NAMES
aac0df3b1f54   300e315adb2f   "/bin/sh -c 'while t…"   12 seconds ago   Up 11 seconds             upbeat_blackburn
# 查看容器aac0df3b1f54的日志
[hadoop@hadoop102 ~]$ sudo docker logs -ft --tail 10  aac0df3b1f54
2021-06-08T14:46:13.046146963Z luyu
2021-06-08T14:46:14.051677179Z luyu
2021-06-08T14:46:15.054572203Z luyu
2021-06-08T14:46:16.061439230Z luyu
2021-06-08T14:46:17.065692351Z luyu
2021-06-08T14:46:18.069007728Z luyu
2021-06-08T14:46:19.073717192Z luyu
2021-06-08T14:46:20.079255001Z luyu

```

#### 查看容器中的进程信息（top）

```shell
# docker top 容器id
[hadoop@hadoop102 ~]$ sudo docker top aac0df3b1f54
UID                 PID(父id)           PPID(进程id)         C                   STIME 
root                5604                5584                0                   22:56
root                5720                5604                0                   22:57
```

#### 查看镜像的元数据(inspect)

```shell
[hadoop@hadoop102 ~]$ sudo docker inspect --help
[sudo] hadoop 的密码：

Usage:  docker inspect [OPTIONS] NAME|ID [NAME|ID...]

Return low-level information on Docker objects

Options:
  -f, --format string   Format the output using the given Go template
  -s, --size            Display total file sizes if the type is container
      --type string     Return JSON for specified type
```

```shell
# 使用docker inspect 容器id 可以查看容器的元数据
[hadoop@hadoop102 ~]$ sudo docker inspect aac0df3b1f54
[
    {
        "Id": "aac0df3b1f54b0ed7b637423d1800d363ebeb20901f5cb17b2d33597c3b3fe0d", # id
        "Created": "2021-06-08T14:41:33.14773429Z", # 创建时间
        "Path": "/bin/sh",		# sh控制台
        "Args": [				# 传递的参数
            "-c",
            "while true;do echo luyu;sleep 1;done"
        ],
        "State": {				# 该容器的状态
            "Status": "exited",
            "Running": false,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 0,				# 父进程的id
            "ExitCode": 137,
            "Error": "",
            "StartedAt": "2021-06-08T14:56:40.814242482Z",
            "FinishedAt": "2021-06-08T15:17:58.965385381Z"
        },
        # 从哪个镜像创建的
        "Image": "sha256:300e315adb2f96afe5f0b2780b87f28ae95231fe3bdd1e16b9ba606307728f55",
        "ResolvConfPath": "/var/lib/docker/containers/aac0df3b1f54b0ed7b637423d1800d363ebeb20901f5cb17b2d33597c3b3fe0d/resolv.conf",  
        "HostnamePath": "/var/lib/docker/containers/aac0df3b1f54b0ed7b637423d1800d363ebeb20901f5cb17b2d33597c3b3fe0d/hostname",
        "HostsPath": "/var/lib/docker/containers/aac0df3b1f54b0ed7b637423d1800d363ebeb20901f5cb17b2d33597c3b3fe0d/hosts",
        "LogPath": "/var/lib/docker/containers/aac0df3b1f54b0ed7b637423d1800d363ebeb20901f5cb17b2d33597c3b3fe0d/aac0df3b1f54b0ed7b637423d1800d363ebeb2090
1f5cb17b2d33597c3b3fe0d-json.log",
        "Name": "/upbeat_blackburn",
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
                "LowerDir": "/var/lib/docker/overlay2/f9dc718957db90b4588e60aeced6060e6f0fc605521724e94513c0133f136914-init/diff:/var/lib/docker/overlay2
/ccb1f6f2f87f8be6415039fc26c9997728911e9792b6e13c11633ab40bfb4fdb/diff",
                "MergedDir": "/var/lib/docker/overlay2/f9dc718957db90b4588e60aeced6060e6f0fc605521724e94513c0133f136914/merged",
                "UpperDir": "/var/lib/docker/overlay2/f9dc718957db90b4588e60aeced6060e6f0fc605521724e94513c0133f136914/diff",
                "WorkDir": "/var/lib/docker/overlay2/f9dc718957db90b4588e60aeced6060e6f0fc605521724e94513c0133f136914/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {					# 该容器的配置信息
            "Hostname": "aac0df3b1f54",		# hostname
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [				# 环境变量
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true;do echo luyu;sleep 1;done"
            ],
            "Image": "300e315adb2f",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20201204",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {   # 关于网络的一些元数据
            "Bridge": "",
            "SandboxID": "f064098b6957297393ae82fffe032e3aa6d1295c5818e195d2221546e520ffa4",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/f064098b6957",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "",
            "Gateway": "",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "",
            "IPPrefixLen": 0,
            "IPv6Gateway": "",
            "MacAddress": "",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "69c5ae2719e4d97b668a69acd2d4e85e66cf6688825c768f8975d3a7eadfa1e8",
                    "EndpointID": "",
                    "Gateway": "",
                    "IPAddress": "",
                    "IPPrefixLen": 0,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "",
                    "DriverOpts": null
                }
            }
        }
    }
]

```

#### 进入当前正在运行的容器

```shell
docker exec -it 容器id /bin/bash     # 进入容器后开启一个新的终端，可以在里面操作（常用）
									# exit后不会停止当前容器

docker attach 容器id				   # 进入容器正在执行的终端，不会启动新的进程
									# exit后，会立即停止当前的容器


```

#### 从容器内拷贝文件到主机上（反向拷贝也是可以的）

```shell
docker cp 容器id:容器内的路径 主机目标路径            # 是在主机上完成的，而不是在容器内部
# 在home目录下创建File目录，
```

```shell
# 进入容器，并切换到home目录下创建hello.java文件
[hadoop@hadoop102 File]$ sudo docker exec -it e3a5ccb43497 /bin/bash
[root@e3a5ccb43497 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@e3a5ccb43497 /]# cd home/
[root@e3a5ccb43497 home]# vi hello.java
#退出容器，进入到主机中，进行文件拷贝
[hadoop@hadoop102 File]$ sudo docker cp e3a5ccb43497:/home/hello.java ~/File/
# 也可以直接拷贝目录
[hadoop@hadoop102 File]$ sudo docker cp e3a5ccb43497:/home ~/File/

#将文件拷贝出来到主机，这是一个手动的过程

# 未来我们使用 -v 卷的技术，可以实现  自动同步  容器内的home目录  与主机的home  连通，打通
```

### 常用命令小结

![img](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Finews.gtimg.com%2Fnewsapp_match%2F0%2F10379248343%2F0.jpg&refer=http%3A%2F%2Finews.gtimg.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626488404&t=24a45ef82f2f339faeffe9c9a29caea6)





![Docker关系命令图](https://img-blog.csdnimg.cn/202007171706264.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NlZV95b3VfWVU=,size_16,color_FFFFFF,t_70)





#### 容器生命周期管理

- [run](https://www.runoob.com/docker/docker-run-command.html)
- [start/stop/restart](https://www.runoob.com/docker/docker-start-stop-restart-command.html)
- [kill](https://www.runoob.com/docker/docker-kill-command.html)
- [rm](https://www.runoob.com/docker/docker-rm-command.html)
- [pause/unpause](https://www.runoob.com/docker/docker-pause-unpause-command.html)
- [create](https://www.runoob.com/docker/docker-create-command.html)
- [exec](https://www.runoob.com/docker/docker-exec-command.html)

#### 容器操作

- [ps](https://www.runoob.com/docker/docker-ps-command.html)
- [inspect](https://www.runoob.com/docker/docker-inspect-command.html)
- [top](https://www.runoob.com/docker/docker-top-command.html)
- [attach](https://www.runoob.com/docker/docker-attach-command.html)
- [events](https://www.runoob.com/docker/docker-events-command.html)
- [logs](https://www.runoob.com/docker/docker-logs-command.html)
- [wait](https://www.runoob.com/docker/docker-wait-command.html)
- [export](https://www.runoob.com/docker/docker-export-command.html)
- [port](https://www.runoob.com/docker/docker-port-command.html)

#### 容器rootfs命令

- [commit](https://www.runoob.com/docker/docker-commit-command.html)
- [cp](https://www.runoob.com/docker/docker-cp-command.html)
- [diff](https://www.runoob.com/docker/docker-diff-command.html)

#### 镜像仓库

- [login](https://www.runoob.com/docker/docker-login-command.html)
- [pull](https://www.runoob.com/docker/docker-pull-command.html)
- [push](https://www.runoob.com/docker/docker-push-command.html)
- [search](https://www.runoob.com/docker/docker-search-command.html)

#### 本地镜像管理

- [images](https://www.runoob.com/docker/docker-images-command.html)
- [rmi](https://www.runoob.com/docker/docker-rmi-command.html)
- [tag](https://www.runoob.com/docker/docker-tag-command.html)
- [build](https://www.runoob.com/docker/docker-build-command.html)
- [history](https://www.runoob.com/docker/docker-history-command.html)
- [save](https://www.runoob.com/docker/docker-save-command.html)
- [load](https://www.runoob.com/docker/docker-load-command.html)
- [import](https://www.runoob.com/docker/docker-import-command.html)

#### info|version

- [info](https://www.runoob.com/docker/docker-info-command.html)
- [version](https://www.runoob.com/docker/docker-version-command.html)



## Docker镜像讲解

### 镜像是什么？

镜像是一种轻量级，可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码，运行时、库、环境变量和配置文件。

所有的应用，直接打包docker镜像，就可以直接跑起来！

如何得到镜像：

- 从远程仓库下载
- 朋友拷贝给你
- 自己制作一个镜像DockerFile

### Docker镜像加载原理

> UnionFS（联合文件系统）

UnionFS（联合文件系统）：Union文件系统（UnionFS）是一种分层，轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂在到同一个虚拟文件系统。Union文件系统是Docker镜像的基础，镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录。

> Docker镜像加载原理

docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统称为UnionFS。

==bootfs==（boot file system）主要包含bootloader和kernel；bootloader主要是引导加载kernel，Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核，当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转将给内核，此时系统哦也会卸载bootfs。

==rootfs==在bootfs之上。包含的就是典型Linux系统中的/dev, /proc, /bin, /etc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu，Centos等等。

![Docker镜像的内部结构(四)](https://s4.51cto.com/images/blog/201711/27/462718f3cd35608c20481b33b09db020.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

对于一个精简的OS，rootfs可以很小，只需要包含最基本的命令，工具和程序库就可以了，因为底层直接用Host的kernel，自己只需要提供rootfs就可以了。由此可见对于不同的linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版可以共用bootfs。



### 镜像的分层

通过查看镜像的元数据信息，可以查看到镜像的分层信息。

```shell
$ docker inspect ubuntu
"RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:ccdbb80308cc5ef43b605ac28fac29c6a597f89f5a169bbedbb8dec29c987439",
                "sha256:63c99163f47292f80f9d24c5b475751dbad6dc795596e935c5c7f1c73dc08107",
                "sha256:2f140462f3bcf8cf3752461e27dfd4b3531f266fa10cda716166bd3a78a19103"
            ]
        }
# 可以看到，rootfs下面显示了镜像的分层信息。
```

**理解**

所有的Docker镜像都起始于一个基础镜像层，当进行修改或增加新的内容时，就会在当前镜像层之上，创建新的镜像层。举一个简单的例子，假如基于Ubuntu Linux16.04创建一个新的镜像，这就是新镜像的第一层；如果在该镜像中添加了Python包，就会在基础镜像层之上创建第二个镜像层；如果继续添加一个安全补丁，就会创建第三个镜像层。

再添加额外的镜像层的同时，镜像始终保持是当前所有镜像的组合，理解这一点非常重点。

> 特点

Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部！

这一层就是我们通常说的容器层，容器之下的都叫镜像层！

### Commit镜像

```shell
$ docker commit 提交容器称为一个新的副本
# 命令 和 git原理类似
$ docker commit -m="提交的描述信息" -a="作者" 容器id	目标镜像名:[TAG]
```

```shell
# 进入容器 ubuntu1
$ docker exec -it ubuntu1 /bin/bash 
root@53f02879b24f:/# ls
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
# home目录增加文件
root@53f02879b24f:/# ls home/
root@53f02879b24f:/# echo "hello world" > home/helloworld.txt
root@53f02879b24f:/# ls home/
helloworld.txt
root@53f02879b24f:/# exit
exit
# 提交当前容器，本地会新增一个镜像
didi@bogon ~$ docker commit -m="first commit" -a="yulu" 53f02879b24f myubuntu:0.1
sha256:cb1d9e8a66a511c5639a5d47a42a0f8949c6ac1e06a96cb9d665036e85609bc9
# 查看镜像
didi@bogon ~$ docker images
REPOSITORY                TAG       IMAGE ID       CREATED         SIZE
myubuntu                  0.1       cb1d9e8a66a5   8 seconds ago   72.7MB # 这就是我们修改后提交的镜像
ubuntu                    latest    7e0aa2d69a15   7 weeks ago     72.7MB
```

`如果你想保存当前容器的状态，就可以通过commit来提交，获得一个镜像，就好比VM中的快照`

# Docker进阶学习

## 容器数据卷

### 什么是容器数据卷

将应用和环境打包成一个镜像！

数据？如果数据都在容器中，那么我们容器删除之后，这些数据就会丢失！<font color=red>需求：数据可以持久化</font>

比如：我们在容器中安装了mysql，存储了一大堆重要的数据，当容器被删除之后，mysql中的数据就随之删除了（删库跑路）==需求：MySql数据可以存储在本地==

容器之间可以有一个数据共享的技术！Docker容器中产生的数据，同步到本地！

这就是卷技术！目录的挂载，将我们容器内的目录，挂在到Linux上面！

<font color=green>总结：卷技术可以将容器中的数据持久化和同步到主机上，方便我们对其容器内容的配置文件进行配置和数据备份;容器之间也可以共享数据</font>

### 使用数据卷（-v）

> 方式1：指定文件目录挂载（数据同步是双向的）
>
> 主机上被指定的目录应该不存在

```shell
$ docker run -it -v 主机目录:容器内目录
# 举个例子：将ubuntu2容器中的home目录挂载到主机的~/docker/data/unbuntu1/home上
$ docker run -it --name ubuntu2 -v ~/docker/data/ubuntu1/home:/home ubuntu

# 容器内的home目录
root@5bf887e2b9e0:/# ls home
# 容器外主机上的挂载目录
didi@bogon ~$ ls ~/docker/data/ubuntu1/home/

# 接下来在容器中添加一个hello.java文件
root@5bf887e2b9e0:/# echo 'System.out.println("hello world!");' > home/hello.java
root@5bf887e2b9e0:/# ls home
hello.java
# 查看主机上挂载的目录
didi@bogon ~$ cat docker/data/ubuntu1/home/hello.java 
System.out.println("hello world!");
# 通过docker inspect 容器id  查看容器的元数据信息
$ docker inspect 5bf887e2b9e0
"Mounts": [
            {
                "Type": "volume",				# 卷
                "Name": "ubuntu3_volume",		# 卷名
                "Source": "/var/lib/docker/volumes/ubuntu3_volume/_data",	# 主机挂载地址
                "Destination": "/home",																		# 容器内地址
                "Driver": "local",				
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            }
        ],
```

### 具名挂载 与 匿名挂载

具名挂载和匿名挂载都不需要指定主机的挂载路径。

<font color=green>不指定主机的挂载地址，但需要指定容器内需要挂载的地址，如果不存在，会自动创建该目录（文件）</font>

```shell
# 匿名挂载
didi@bogon ~$ docker run -d --name ubuntu4 -v /etc ubuntu
b79600ad36d982e88f18e70059dcb75c8b2ef1e0c646809fff0a526b86199ca7
didi@bogon ~$ docker volume ls
DRIVER    VOLUME NAME
local     c123ec3d50d8cc96802041d60e8a8fcefe03c9d82d23ab7a47a768217380507f  # 匿名卷，没有指定卷的名字
# 查看容器的元数据
$ docker inspect b79600ad36d982e88f18e70059dcb75c8b2ef1e0c646809fff0a526b86199ca7
"Mounts": [
            {
                "Type": "volume",
                "Name": "c123ec3d50d8cc96802041d60e8a8fcefe03c9d82d23ab7a47a768217380507f",
                "Source": "/var/lib/docker/volumes/c123ec3d50d8cc96802041d60e8a8fcefe03c9d82d23ab7a47a768217380507f/_data",
                "Destination": "/etc",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],
        
        
# 具名挂载
didi@bogon ~$ docker run -d --name ubuntu5 -v ubuntu5_volume:/etc ubuntu
37de88fb243838b931e797ceb271a1bd70e887fb8c399c8f22d70099af770cd2
didi@bogon ~$ docker volume ls
DRIVER    VOLUME NAME
local     ubuntu5_volume
didi@bogon ~$ docker inspect 37de88fb243838b931e797ceb271a1bd70e887fb8c399c8f22d70099af770cd2
"Mounts": [
            {
                "Type": "volume",
                "Name": "ubuntu5_volume",
                "Source": "/var/lib/docker/volumes/ubuntu5_volume/_data",
                "Destination": "/etc",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            }
        ],
# 查看卷的元数据信息
didi@bogon ~$ docker volume inspect ubuntu5_volume
[
    {
        "CreatedAt": "2021-06-16T14:12:33Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/ubuntu5_volume/_data",
        "Name": "ubuntu5_volume",
        "Options": null,
        "Scope": "local"
    }
]
# 挂载的时候，可以指定读写权限；ro：只读； rw：读写
```



DockerFile的方式也可以指定数据卷，将容器内的目录挂载到主机上（宿主机上）



### 数据卷容器（容器与容器之间共享数据卷）

```shell
# 数据卷选项   --volume-from
$ docker run -it --name docker2 --volume-from docker1 ubuntu  #  容器docker2 同步（继承）容器docker1的数据卷，docker1就是数据卷容器

```

==数据共享，如果数据卷容器停止了且删除了，你猜其他挂载这个容器的中的数据还会存在吗？？？？？  答案是肯定存在啊==

<font color=red>特别说明：</font>

> docker中的数据共享，数据卷技术是通过拷贝的技术来实现，如果其中挂载的容器被删除了，但是被挂载的目录因为复制了容器中的数据，所以在容器被删除之后，还是会存才相应的数据的。

**小结**

容器之间配置信息的传递，数据卷容器的生命周期一直持续到没有容器使用为止。

但是你一旦持久化到了本地，这个时候，本地的数据是不会删除的。

## DockerFile

### DockerFile介绍

dockerfile是用来构建docker镜像的文件！命令参数脚本！

**构建步骤：**

1. 编写一个dockerfile文件
2. 使用docker build 构建成为一个镜像
3. docker run 运行镜像
4. docker push 发布镜像（DockerHub、阿里云镜像仓库！）

> 很多官方镜像都是基础包，很多功能没有（常用的命令也可能没有），我们通常会自己搭建自己的镜像！

### DockerFile构建过程

**基础知识**

1. 每个保留关键字（指令）都必须是大写字母
2. 执行从上到下顺序执行
3. #：表示注释
4. 每一个指令都会创建提交一个新的镜像层，并提交

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fwww.liuhaihua.cn%2Fwp-content%2Fuploads%2F2016%2F08%2FMFB3Yjf.jpg&refer=http%3A%2F%2Fwww.liuhaihua.cn&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626488126&t=67aeecd77f39cae1d12bb367c661085f" alt="img" style="zoom:50%;" />

dockerfile是面向开发的，我们以后要发布项目，做镜像，就需要编写dockerfile文件，这个文件十分简单！

Docker镜像逐渐成为企业交付的标准，必须要掌握！

**三部曲**：开发、部署、运维

1. DockerFile：构建文件，定义了一切的步骤，源代码
2. DockerImages：通过DockerFile构建生成的镜像，最终发布和运行的产品
3. Docker容器：容器就是镜像运行起来提供服务器

### DockerFile指令

<img src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.dongcoder.com%2Fup%2Finfo%2F201912%2F20191201213114965392.png&refer=http%3A%2F%2Fimg.dongcoder.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626488249&t=cd0fff660b8407d3f0dbf318766d3a1f" alt="img" style="zoom:%;" />



```shell
FROM					# 基础镜像，一切从这里开始构建
MAINTAINER		# 镜像是谁写的， 姓名+邮箱
RUN						# 镜像构建的时候需要运行的命令
ADD						# 步骤，tomcat镜像，这个tomcat压缩包！   自动解压
WORKDIR				# 镜像的工作目录   进入容器的时候，默认的工作空间
VOLUME				# 挂载的目录
EXPOSE				# 保留端口配置
CMD						# 指定这个容器启动的时候要执行的命令。只有最后一个会生效，可被替代
ENTRYPOINT		# 指定这个容器启动的时候要运行的命令。可以追加命
ONBUILD				# 当构建一个被继承DockerFile 这个时候就会运行 ONBUILD 的指令。 触发指令
COPY					# 累计ADD，将文件拷贝到镜像中   仅仅拷贝，不解压
ENV						# 构建的时候设置环境变量！
```

![img](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fseo-1255598498.file.myqcloud.com%2Ffull%2F4455082961bc017cb7c87a3463bb326e021013ce.jpg&refer=http%3A%2F%2Fseo-1255598498.file.myqcloud.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1626488342&t=d082a7ddde61f3208268b222179c264f)

#### **构建DockerFile之前的说明**

> Docker 通过DockerFile构建镜像的时候，Docker的守护进程会将当前目录下的所有文件打包到镜像中；就像我们使用git add .  会将当前目录下的所有文件加入到暂存区。但是可以通过指定.ignore文件进行选择性忽略。
>
> Docker也支持类似的操作；
>
> 可以通过编写 `.dockerignore`文件来进行选择过滤掉一些我们不想打包到镜像中的文件，尽量保证镜像的精简。

#### Docker构建DockerFile命令

```shell
# 1. 通常我们会创建一个空的目录，这个目录下我们存放一些我们需要构建的文件，比如MySql，tomcat等的安装包， 还有一些必备的文件
# 2. 我们可以自定义DockerFile的文件名，但是如果直接定义成DockerFile，我们在执行build命令的时候，就不需要指定改文件的路径位置，否则需要通过-f选项执行具体的DockerFile文件的路径
$ docker build -f DockerFile_Path -t luyu/myapp:1.0.2 -t luyu/myapp:latest
-f: 指定DockerFile文件的路径
-t: 指定生成的image镜像的目标位置
```

#### DockerFile Centos官方样例

```shell
# 官方centos的DockerFile
FROM scratch
ADD centos-7-x86_64-docker.tar.xz /

LABEL \
    org.label-schema.schema-version="1.0" \
    org.label-schema.name="CentOS Base Image" \
    org.label-schema.vendor="CentOS" \
    org.label-schema.license="GPLv2" \
    org.label-schema.build-date="20201113" \
    org.opencontainers.image.title="CentOS Base Image" \
    org.opencontainers.image.vendor="CentOS" \
    org.opencontainers.image.licenses="GPL-2.0-only" \
    org.opencontainers.image.created="2020-11-13 00:00:00+00:00"

CMD ["/bin/bash"]
```

<font color=green>通常情况下，build DockerFile，都会指定这个要构建的镜像的bootfs的来源（换句话说，就是要指定这个镜像是从哪个基础镜像构建的, 也就是指定 **父镜像**）</font>

#### DockerFile中的关键字说明

虽然在dockerfile中支持关键字小写，但是为了区分参数，尽量保持关键字大写，参数小写

##### FROM

```dockerfile
FROM [--platform=<platform>] <image> [AS <name>]
FROM [--platform=<platform>] <image>[:<tag>] [AS <name>]
FROM [--platform=<platform>] <image>[@<digest>] [AS <name>]
```

###### ARG和FROM

虽然说DockerFile都是一FROM关键字开头的，但是可以在FROM前声明一个或者多个ARG指令。ARG指令可以定义变量，例如TAG信息

```dockerfile
ARG  CODE_VERSION=latest
FROM base:${CODE_VERSION}
CMD  /code/run-app

FROM extras:${CODE_VERSION}
CMD  /code/run-extras
```

==特别说明==

FROM在同一个DockerFile文件中可以出现一次或多次

当出现多次的时候，可以构建多个镜像；或者一个镜像作为另一个镜像的**父依赖**。可以通过AS关键字为FROM起一个别名

##### RUN

镜像构建的时时候运行指定命令

```shell
# 一般RUN 有两种格式运行命令   shell format  和  exec format
# 第一种方式可通过 \ 进行换行
$ RUN /bin/bash -c 'source $HOME/.bashrc; \
echo $HOME'
##########################
$ RUN ["/bin/bash", "-c", "echo hello"]
```

<font color=red>关于RUN关键字执行命令的时候的注意事项</font>

镜像构建的时候，是按照层级构建的。如果构建到当前层的时候，发现之前已经构建过相同的层，这时就会复用历史层，而不会再执行当前层了！

如果使用 `apt-get update` 与 `apt-get install`命令就需要特别注意，尽量将`apt-get update` 与 `apt-get install` 放到一个RUN层。

```dockerfile
RUN apt-get update && apt-get install -y \
    aufs-tools \
    automake \
    build-essential \
    curl \
    dpkg-sig \
    libcap-dev \
    libsqlite3-dev \
    mercurial \
    reprepro \
    ruby1.9.1 \
    ruby1.9.1-dev \
    s3cmd=1.1.* \
 && rm -rf /var/lib/apt/lists/*
```

如果不放到同一个RUN中，例如下面这段dockerfile

```dockerfile
# 刚开始的时候，我们通过以下命令，进行构建，但是还想安装nginx，
# syntax=docker/dockerfile:1
FROM ubuntu:18.04
RUN apt-get update
RUN apt-get install -y curl
# 又在右面添加
RUN apt-get update
RUN apt-get install -y curl nginx
```

> 上述两端命令，第二段命令中的 `apt-get update` 不会再执行了，因为在Docker的缓存看来，这一层之前已经构建过了，就复用了之前层的 `apt-get update`
>
> 这就有可能导致之前的update之后没有nginx，这就会导致`RUN apt-get install -y curl nginx`运行失败！
>
> 所及尽量保证在一层RUN中执行，避免这种情况的发生。

##### CMD 与 ENTRYPOINT

CMD 与 ENTRYPOINT关键字都可能指定以该DockerFile构建的镜像创建的容器在启动的时候要执行的命令。

区别在于：CMD指定的命令，会因为我们在执行`docker run [OPTIONS] IMAGE [COMMAND] [ARG...]`时人为指定的`COMMAND`要执行命令

```shell
# 创建一个DockerFile文件，末尾指定一个CMD ['ls']
$ cat DockerFile 
FROM	centos
RUN		echo "hello world";
CMD		['ls']
# 然后build dockerfile文件
$ docker build -f ./DockerFile -t luyu/firstimages:1.0.1 .
[+] Building 0.1s (6/6) FINISHED                                                                                                     
 => [internal] load build definition from DockerFile                                                                            0.0s
 => => transferring dockerfile: 91B                                                                                             0.0s
 => [internal] load .dockerignore                                                                                               0.0s
 => => transferring context: 2B                                                                                                 0.0s
 => [internal] load metadata for docker.io/library/centos:latest                                                                0.0s
 => [1/2] FROM docker.io/library/centos                                                                                         0.0s
 => CACHED [2/2] RUN  ECHO "hello world";                                                                                       0.0s
 => exporting to image                                                                                                          0.0s
 => => exporting layers                                                                                                         0.0s
 => => writing image sha256:c3e7f267d07123989d20e73d1772247cc1d7a83b068225f507f6f639d5e5ec70                                    0.0s
 => => naming to docker.io/luyu/firstimages:1.0.1                                                                               0.0s

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
# 后台启动一个该镜像的容器
$ docker run -d c3e7f267d07123989d20e73d1772247cc1d7a83b068225f507f6f639d5e5ec70
4fead54e8978e1a4be2f2457845d144ec428135b3367ccfc1a47ef9ab0ba49dc
# 查看该容器的日志
$ docker logs 4fead54e8978e1a4be2f2457845d144ec428135b3367ccfc1a47ef9ab0ba49dc
/bin/sh: [ls]: command not found   # 这行证明CMD内的指令已经执行了，但是提示没有找到ls命令

# 然后后台再启动一个容器。并指定要执行的命令  ls -al
$ docker run -d c3e7f267d07123989d20e73d1772247cc1d7a83b068225f507f6f639d5e5ec70 ls -al
a19557b0b538ba0cb75579d0e81d1fccf8d03053dff717bc670de0e6889ae467
# 再次查看容器日志
$ docker logs a19557b0b538ba0cb75579d0e81d1fccf8d03053dff717bc670de0e6889ae467
total 56
drwxr-xr-x   1 root root 4096 Jun 18 09:40 .
drwxr-xr-x   1 root root 4096 Jun 18 09:40 ..
-rwxr-xr-x   1 root root    0 Jun 18 09:40 .dockerenv
lrwxrwxrwx   1 root root    7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x   5 root root  340 Jun 18 09:40 dev
drwxr-xr-x   1 root root 4096 Jun 18 09:40 etc
drwxr-xr-x   2 root root 4096 Nov  3  2020 home
lrwxrwxrwx   1 root root    7 Nov  3  2020 lib -> usr/lib
lrwxrwxrwx   1 root root    9 Nov  3  2020 lib64 -> usr/lib64
drwx------   2 root root 4096 Dec  4  2020 lost+found
drwxr-xr-x   2 root root 4096 Nov  3  2020 media
drwxr-xr-x   2 root root 4096 Nov  3  2020 mnt
drwxr-xr-x   2 root root 4096 Nov  3  2020 opt
dr-xr-xr-x 185 root root    0 Jun 18 09:40 proc
dr-xr-x---   2 root root 4096 Dec  4  2020 root
drwxr-xr-x  11 root root 4096 Dec  4  2020 run
lrwxrwxrwx   1 root root    8 Nov  3  2020 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 Nov  3  2020 srv
dr-xr-xr-x  13 root root    0 Jun 18 09:40 sys
drwxrwxrwt   7 root root 4096 Dec  4  2020 tmp
drwxr-xr-x  12 root root 4096 Dec  4  2020 usr
drwxr-xr-x  20 root root 4096 Dec  4  2020 var
# 从上述结果可以看出，启动容器的时候， 人为指定的命令  覆盖了DockerFile文件中指定的  CMD命令
```

而**ENTRYPOINT**关键字指定的命令不会被覆盖，人为指定的命令会追加到后面



可以使用**ENTRYPOINT**作为镜像的主命令，然后**CMD**作为默认命令

例子：

本地有一个shell脚本

```shell
#!/bin/bash
set -e

if [ "$1" = 'postgres' ]; then
    chown -R postgres "$PGDATA"

    if [ -z "$(ls -A "$PGDATA")" ]; then
        gosu postgres initdb
    fi

    exec gosu postgres "$@"
fi

exec "$@"
```

复制脚本至容器内

```dockerfile
COPY ./docker-entrypoint.sh \
ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["postgres"]
```



##### EXPOSE

EXPOSE 可以人为的暴露容器的端口，方便在启动容器的时候，进行端口随机映射

##### ENV

ENV可以设置容器内部的环境变量，例如：

```dockerfile
ENV PATH=/usr/local/nginx/bin:$PATH  # 以确保CMD ["nginx"] 正常工作
```

##### ADD和COPY

ADD和COPY都可以将文件添加到容器内，虽然二者相似，但又有不同之处；

- COPY是单纯的将本地文件复制到容器内
- ADD条件本地文件时，如果是压缩包，会自动解压

```dockerfile
COPY requirements.txt /tmp/
RUN pip install --requirement /tmp/requirements.txt
COPY . /tmp/
```

<font color=red>注意：实际使用过程中</font>

<font color=blue>ADD相对于COPY来说，不透明，实际使用应该避免使用ADD关键</font>

​		<font color=red>替代方案：</font>

​						可以先COPY到容器内，然后通过RUN进行解压，或者通过`curl`或`wget`将压缩包下载到本地之后在进行解压

```dockerfile
COPY big.tar.xz /tmp/
RUN  tar -xJf /tmp/big.tar.xz -C /usr/src/things \
			&& make -C /usr/src/things all
```

对于不需要ADD tar自动提取功能的其他项目（文件、目录），应该始终使用 COPY

##### VOLUME

dockerfile中的`VOLUME`关键字可以挂载容器内目录到主机目录，通过匿名挂载的方式。可以通过`docker inspect 容器id`查看挂载到主机的实际目录在哪里

```dockerfile
FROM ubuntu
RUN mkdir /myvol
RUN echo "hello world" > /myvol/greeting
VOLUME /myvol  # 通过匿名挂载的方式挂载容器内   /myvol 目录
```























































## Docker网络

[Docker的四种网络模式](https://www.jianshu.com/p/22a7032bb7bd)

docker的网络模式支持4种：

1. host模式（仅主机模式）
2. container模式（用的少！局限性很大）
3. none模式：不配置网络
4. bridge模式（默认的模式）

```shell
$ docker network ls # 可以查看docker的所有网络信息
$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
37f1233b03fe   bridge    bridge    local
dddd641e9fee   host      host      local
1bd165c33f9d   none      null      local

```

```shell
$ docker network --help

Usage:  docker network COMMAND

Manage networks

Commands:
  connect     Connect a container to a network
  create      Create a network
  disconnect  Disconnect a container from a network
  inspect     Display detailed information on one or more networks
  ls          List networks
  prune       Remove all unused networks
  rm          Remove one or more networks

```





### 理解Docker0

清空所有环境

Docker是如何处理容器网络访问的?

#### bridge模式

<img src="https://upload-images.jianshu.io/upload_images/13618762-f1643a51d313a889.png?imageMogr2/auto-orient/strip|imageView2/2/w/1083/format/webp" alt="img" style="zoom:50%;" />



1. 先查看本机网卡信息

```shell
# 主机ip信息
[root@hadoop102 hadoop]# ip addr
# 自环地址
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:50:e3:01 brd ff:ff:ff:ff:ff:ff
    # ens33 网卡ip地址
    inet 192.168.70.102/24 brd 192.168.70.255 scope global noprefixroute ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fe50:e301/64 scope link 
       valid_lft forever preferred_lft forever
3: virbr0: <BROADCAST,MULTICAST> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 52:54:00:f2:8e:d9 brd ff:ff:ff:ff:ff:ff
4: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast master virbr0 state DOWN group default qlen 1000
    link/ether 52:54:00:f2:8e:d9 brd ff:ff:ff:ff:ff:ff
5: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:2f:c2:0c:74 brd ff:ff:ff:ff:ff:ff
    # docker0 网络
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:2fff:fec2:c74/64 scope link 
       valid_lft forever preferred_lft forever
# 容器内的ip地址信息
[root@hadoop102 hadoop]# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
# evth-pair  34--35
34: eth0@if35: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever

# 容器开启后，主机多出一块网卡   35 -- 34
35: veth35da31e@if34: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
    link/ether 0e:cc:1b:7b:3b:27 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::ccc:1bff:fe7b:3b27/64 scope link 
       valid_lft forever preferred_lft forever

```

> 我们发现，容器带来的网卡，都是成对出现的（成对儿的虚拟设备接口）
>
> veth-pair 就是一对的虚拟设备接口，它们都是成对儿出现的，一端连着协议，一端彼此相连
>
> 正因为这个特性，veth-pair充当一个桥梁，连接各种虚拟网络设备的
>
> docker 中的所有网络接口都是虚拟的，虚拟的转发效率高

**小总结**：容器都是公用一个路由器的，就是这个docke0，在bridge模式下也称之为桥



### 容器互联

#### --link（不推荐使用）

> 思考一个场景，我们编写一个微服务， database url=ip:端口号 ，项目重启时，ip地址就会变化，可不可以通过名字来访问容器的ip地址，这样就可以保证虽然ip地址在变化，但是名字一直不会变？

```shell
[root@hadoop102 hadoop]# docker exec -it tomcat02 ping tomcat01
ping: tomcat01: Name or service not known

# 通过 --link可以连通两个容器，进而通过容器名就可以相互ping同
$ docker run -d --name tomcat03 --link tomcat01 tomcat
11145388c943701718c7baa9c2b291a1c2f72b9845709fe098066d7bd3005038
$ docker exec -it tomcat03 ping tomcat01
PING tomcat01 (172.17.0.2) 56(84) bytes of data.
64 bytes from tomcat01 (172.17.0.2): icmp_seq=1 ttl=64 time=0.168 ms
64 bytes from tomcat01 (172.17.0.2): icmp_seq=2 ttl=64 time=0.061 ms
64 bytes from tomcat01 (172.17.0.2): icmp_seq=3 ttl=64 time=0.061 ms
# 反向ping 却ping不通
docker exec -it tomcat01 ping tomcat03
ping: tomcat03: Name or service not known
```

**底层探究**

--link 选项可以更改容器的hosts文件，将被链接的容器的ip与容器名字和 ip进行**映射**；但是这个过程是**单向的**

<font color=red>--link已经不推荐使用了</font>，可以通过自定义网络，不直接使用docker0.

例如docker0的问题： 不支持容器名链接访问



#### 自定义网络



```shell
# docker run 的时候，默认会添加一个 --net bridge的选项，这里的bridge就是创建的网络名
$ docker run -d --name tomcat01 --net bridge tomcat
# docker0特点： 默认、域名不能访问、 --link可以打通连接，但是笨重，不灵活
# 通过自定义网络，可实现容器的域名连接
# 接下来创建一个网络mynet
$ docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
bb4918b73768c514519787fe74308638384ea49d79f1cc55b9df330b5a36a0ad
$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
37f1233b03fe   bridge    bridge    local
dddd641e9fee   host      host      local
bb4918b73768   mynet     bridge    local # 自定义创建的网络，相当于我们自己创建了一个桥
1bd165c33f9d   none      null      local

# 启动两个容器，都指定相同的网络
[root@hadoop102 hadoop]$ docker run -d --name tomcat01 --net mynet tomcat
3dc2a40323a1272adca27ea381a1038a27a176dc3dbdc07ab7a1a03ed83ef5b7
[root@hadoop102 hadoop]$ docker run -d --name tomcat02 --net mynet tomcat
9c0be98e171d19eb4d1db49f11b8936a79f41d8aa91052b96572a917de668bdd
[root@hadoop102 hadoop]$ docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "bb4918b73768c514519787fe74308638384ea49d79f1cc55b9df330b5a36a0ad",
        "Created": "2021-06-19T10:54:35.743318807+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "3dc2a40323a1272adca27ea381a1038a27a176dc3dbdc07ab7a1a03ed83ef5b7": {
                "Name": "tomcat01",
                "EndpointID": "c76434e3d13f2b5b279404afa70b088eb5f054fb30701b14218d77fdf46f46c8",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            },
            "9c0be98e171d19eb4d1db49f11b8936a79f41d8aa91052b96572a917de668bdd": {
                "Name": "tomcat02",
                "EndpointID": "68b1c4c037eae586663d693447d49fe29736ebc0fcf1c89e1407c3c651a4d79f",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
# 使用自定义网络之后，不仅可以自动的能进行域名的映射，而且还能双向ping通
[root@hadoop102 hadoop]$ docker exec -it tomcat01 ping tomcat02
PING tomcat02 (192.168.0.3) 56(84) bytes of data.
64 bytes from tomcat02.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.172 ms
64 bytes from tomcat02.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.117 ms
64 bytes from tomcat02.mynet (192.168.0.3): icmp_seq=3 ttl=64 time=0.088 ms
^C
--- tomcat02 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 6ms
rtt min/avg/max/mdev = 0.088/0.125/0.172/0.037 ms
[root@hadoop102 hadoop]$ docker exec -it tomcat02 ping tomcat01
PING tomcat01 (192.168.0.2) 56(84) bytes of data.
64 bytes from tomcat01.mynet (192.168.0.2): icmp_seq=1 ttl=64 time=0.206 ms
64 bytes from tomcat01.mynet (192.168.0.2): icmp_seq=2 ttl=64 time=0.061 ms
64 bytes from tomcat01.mynet (192.168.0.2): icmp_seq=3 ttl=64 time=0.062 ms
^C
--- tomcat01 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 4ms
rtt min/avg/max/mdev = 0.061/0.109/0.206/0.069 ms

```

使用自定义网络的好处：

1. redis - 不同的集群使用不同的网络，保证集群是安全和健康的 
2. mysql - 不同的集群使用不同的网络，保证集群是安全和健康的



#### 网络连通

处于不同网桥下的两个容器如何连通？

   ![image-20210619121627000](C:\Users\LuYu\AppData\Roaming\Typora\typora-user-images\image-20210619121627000.png)

```shell
$ docker network --help

Usage:  docker network COMMAND

Manage networks

Commands:
  connect     Connect a container to a network  # 连接一个容器到一个网络
  create      Create a network
  disconnect  Disconnect a container from a network
  inspect     Display detailed information on one or more networks
  ls          List networks
  prune       Remove all unused networks
  rm          Remove one or more networks
# 用法
$ docker network connect --help

Usage:  docker network connect [OPTIONS] NETWORK CONTAINER

Connect a container to a network

Options:
      --alias strings           Add network-scoped alias for the container
      --driver-opt strings      driver options for the network
      --ip string               IPv4 address (e.g., 172.30.100.104)
      --ip6 string              IPv6 address (e.g., 2001:db8::33)
      --link list               Add link to another container
      --link-local-ip strings   Add a link-local address for the container
# 假设有两个不同网桥 docker0  和  mynet
# docker0上有一个容器 tomcat01   mynet上有一个容器tomcat-net-01
# 网络连通这两个容器
$ docker network connect mynet tomcat01
# 实质上是把容器tomcat01的网络信息添加到了网桥mynet中，因此这个过程是单向，tomcat01能ping通tomcat-net-01
# 但是tomcat-net-01 ping不通tomcat01

# 产生的效果就是：tomcat01这个容器会有两个ip地址，一个是公网ip地址，一个是内网IP地址
```





## Docker Compose

> 官方介绍

Compose使用一个用于**定义**和**运行**多个容器应用的工具

使用YAML文件配置应用的服务，通过这个配置文件可**实现一条命令**启动所有的**应用服务**

> 使用Compose的三个基础步骤

1. `DockerFile`，保证项目在任何地方都可以运行
2. 编写`docker-compose.yml`,
3.  启动项目`docker-compose up`

> 作用：基本的容器编排

Compose是Docker官方的开源项目。需要安装！

`DockerFile`让程序在任何地方运行。web服务，redis，mysql，nginx等多个容器,这么多容器一个一个手动的去run非常的麻烦，通过Compose我们就可以直接一条命令解决！！！

Compose: docker-compose.yml

```yaml
version: "3.9"  # optional since v1.27.0
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
      - logvolume01:/var/log
    links:
      - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

Compose：重要的概念

- 服务services，容器。应用。（web、redis、mysql）
- 项目project。一组关联的容器。博客、web、mysql

##### docker Compose安装

1. Run this command to download the current stable release of Docker Compose:

```shell
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

```

2. Apply executable permissions to the binary:

```shell
sudo chmod +x /usr/local/bin/docker-compose
```

3. ln软连接

```shell
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

4. 测试安装是否完成

```shell
$ docker-compose --version
docker-compose version 1.29.2, build 1110ad01
```



##### 测试案例

python应用：计数器； redis！

compose一般使用的步骤：

1. 创建应用文件 app.py
2. DockerFile（应用打包成镜像）
3. Docker-compose.yaml文件（定义整个服务，需要的环境、web、redis） 完整的上线服务
4. 启动compose项目（docker-compose up）

>  Step1:Setup

```shell
# 1.Create a directory for the project:
$ mkdir composetest
$ cd composetest
# 2.Create a file called app.py in your project directory and paste this in:
$ vim app.py

import time

import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)
# 3.Create another file called requirements.txt in your project directory and paste this in:
$ vim requirements.txt
flask
redis
```

> Step2: Create a Dockerfile

```dockerfile
# syntax=docker/dockerfile:1
FROM python:3.7-alpine
RUN sed -i -e 's/http:/https:/' /etc/apk/repositories
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run"]
```

> Step 3: Define services in a Compose file  docker-compose.yml

```yaml
version: "3.9"
services:
  web:
    build: .
    ports:
      - "5000:5000"
  redis:
    image: "redis:alpine"
```

> Step 4: Build and run your app with Compose  

```shell
$ docker-compose up
```

测试

```Tashell
[root@hadoop102 hadoop]# curl localhost:5000
Hello World! I have been seen 1 times.
[root@hadoop102 hadoop]# curl localhost:5000
Hello World! I have been seen 2 times.

[root@hadoop102 hadoop]# docker ps
CONTAINER ID   IMAGE             COMMAND                  CREATED         STATUS         PORTS                                       NAMES
feace8f427c9   redis:alpine      "docker-entrypoint.s…"   5 minutes ago   Up 5 minutes   6379/tcp                                    composetest_redis_1
68a9e5a7c4e4   composetest_web   "flask run"              5 minutes ago   Up 5 minutes   0.0.0.0:5000->5000/tcp, :::5000->5000/tcp   composetest_web_1

```



##### Yaml 规则

```yaml
# 3 层
version: ''  # 版本
services: # 服务
	服务1 web:
		images
		build
		network
	服务2 redis
		....
	服务3
# 其他配置
volumes:
networks:
configs:
		
```

用的时候，可以查看官方文档https://docs.docker.com/compose/compose-file/compose-file-v3/

##### 使用Compose一键部署WP博客

（WP、HEXO）

1. 创建wordpress目录并进入
2. 创建docker-compose.yml

```yaml
version: "3.9"
    
services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - wordpress_data:/var/www/html
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
volumes:
  db_data: {}
  wordpress_data: {}
```

3. docker-compose up 启动





## Docker Swarm















## CI/CD Jenkins 流水线！





企业实战

































