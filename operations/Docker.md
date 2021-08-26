# Docker

## Docker简介

Docker 是一个开源的应用容器引擎，基于 [Go 语言](https://www.runoob.com/go/go-tutorial.html) 并遵从 Apache2.0 协议开源。

Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

## Docker可以做什么

### 容器化技术

``容器化技术不是模拟的一个完整的操作系统``

Docker和虚拟机的不同：

- 传统虚拟机，虚拟出来一套硬件，运行一个完整的操作系统，然后在这个系统上安装和运行软件
- 容器内的应用直接运行在宿主机的内容，容器是没有自己内核的，也没有虚拟硬件，所以就很轻便
- 每个容器间是相互隔离的，每个容器内都有一个属于自己的文件系统，互不影响

### DevOps(开发、运维)

**应用更快速的交付和部署**

Docker：打报镜像发布测试，一键运行

**更便捷的升级和扩缩容**

**更简单的系统运维**

**更高效的计算资源利用**

Docker是内核级别的虚拟化，可以在一个物理机上运行很多的容器实例

## Docker架构

Docker 包括三个基本概念:

- **镜像（Image）**：Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:16.04 就包含了完整的一套 Ubuntu16.04 最小系统的 root 文件系统。
- **容器（Container）**：镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。
- **仓库（Repository）**：仓库可看成一个代码控制中心，用来保存镜像。

![](https://raw.githubusercontent.com/mao0824/pictureBed/master/img/20210822213508.png)

| 概念                   | 说明                                                         |
| :--------------------- | :----------------------------------------------------------- |
| Docker 镜像(Images)    | Docker 镜像是用于创建 Docker 容器的模板，比如 Ubuntu 系统。  |
| Docker 容器(Container) | 容器是独立运行的一个或一组应用，是镜像运行时的实体。         |
| Docker 客户端(Client)  | Docker 客户端通过命令行或者其他工具使用 Docker SDK (https://docs.docker.com/develop/sdk/) 与 Docker 的守护进程通信。 |
| Docker 主机(Host)      | 一个物理或者虚拟的机器用于执行 Docker 守护进程和容器。       |
| Docker Registry        | Docker 仓库用来保存镜像，可以理解为代码控制中的代码仓库。Docker Hub([https://hub.docker.com](https://hub.docker.com/)) 提供了庞大的镜像集合供使用。一个 Docker Registry 中可以包含多个仓库（Repository）；每个仓库可以包含多个标签（Tag）；每个标签对应一个镜像。通常，一个仓库会包含同一个软件不同版本的镜像，而标签就常用于对应该软件的各个版本。我们可以通过 **<仓库名>:<标签>** 的格式来指定具体是这个软件哪个版本的镜像。如果不给出标签，将以 **latest** 作为默认标签。 |
| Docker Machine         | Docker Machine是一个简化Docker安装的命令行工具，通过一个简单的命令行即可在相应的平台上安装Docker，比如VirtualBox、 Digital Ocean、Microsoft Azure。 |

## 安装Docker

系统内核

```shell
[zouma@zouma1 /]$ uname -r
4.18.0-147.5.1.el8_1.x86_64
```

系统版本

```shell
[zouma@zouma1 /]$ cat /etc/os-release 
NAME="CentOS Linux"
VERSION="8 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="8"
PLATFORM_ID="platform:el8"
PRETTY_NAME="CentOS Linux 8 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:8"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-8"
CENTOS_MANTISBT_PROJECT_VERSION="8"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="8"
```

 1、卸载旧的版本

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

2、需要的安装包

```shell
 sudo yum install -y yum-utils
```

3.设置镜像仓库

```shell
 sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
    
    #阿里云docker镜像仓库
 sudo yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

4.安装docker

```shell
# 更新yum软件包索引 Centos8没有fast参数
yum makecache fast
# ce社区版  ee企业版
sudo yum install docker-ce docker-ce-cli containerd.io
```

5.启动docker

```shell
sudo systemctl start docker
```

6.判断docker是否安装成功

```shell
docker version
```

注意：docker非root用户权限问题

```shell
sudo chmod a+rw /var/run/docker.sock
```

7.hello-word

```shell
docker run hello-word
```

出现 Hello from Docker! 表示安装成功

8.查看下载的hello-world镜像

```shell
docker images
```

出现：

```
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    d1165f221234   5 months ago   13.3kB
```

9.卸载docker

```shell
 # 卸载依赖
 sudo yum remove docker-ce docker-ce-cli containerd.io
 
 # 删除资源
 sudo rm -rf /var/lib/docker
 sudo rm -rf /var/lib/containerd
```

**阿里云镜像加速**

1. 登录阿里云找到镜像服务
2. 找到镜像加速地址
3. 通过修改daemon配置文件 ``/etc/docker/daemon.json`` 来使用加速器

```
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://rxw4srzm.mirror.aliyuncs.com"]
}
EOF

sudo systemctl daemon-reload
sudo systemctl restart docker
```

## Docker底层原理

**Docker是怎么工作的？**

Docker是一个Client-Server结构的系统，Docker的守护进程运行在主机上，通过Socket从客户端访问，Docker Server接收到Docker Client的指令，就会执行这个命令。



**Docker为什么比VM快？**

1. Docker有着比虚拟机更少的抽象层，**由于docker不需要Hypervisor实现硬件资源虚拟化,\**运行在docker容器上的程序直接使用的都是实际物理机的硬件资源\**。因此在CPU、内存利用率上docker将会在效率上有明显优势。**
2. **docker利用的是宿主机的内核,而不需要Guest OS**。因此,当新建一个 容器时,docker不需要和虚拟机一样重新加载一个操作系统内核。仍而避免引寻、加载操作系统内核返个比较费时费资源的过程,当新建一个虚拟机时,虚拟机软件需要加载GuestOS,返个新建过程是分钟级别的。**而docker由于直接利用宿主机的操作系统,则省略了返个过程,因此新建一个docker容器只需要几秒钟。**

![](https://raw.githubusercontent.com/mao0824/pictureBed/master/img/20210825164938.png)

## Docker的常用命令

### 帮助命令

```shell
# 显示docker的版本信息
docker version
# 显示docker的系统信息，包括镜像和容器的个数
docker info
# 万能信息
docker 命令 --help 
```

帮助文档地址：[Reference documentation | Docker Documentation](https://docs.docker.com/reference/)

### 镜像命令

```shell
# 查看所有本地主机上的镜像
docker images
# 结果及解释
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    d1165f221234   5 months ago   13.3kB

REPOSITORY    镜像的仓库源
TAG           镜像的标签
IMAGE ID      镜像的id
# 选项
  -a, --all             # Show all images (default hides intermediate images)
      --digests         # Show digests
  -f, --filter filter   # Filter output based on conditions provided
      --format string   # Pretty-print images using a Go template
      --no-trunc        # Don't truncate output
  -q, --quiet           # Only show image IDs

```

```shell
# 镜像搜索
docker search 镜像名称
# 结果及解释
[zouma@zouma1 ~]$ docker search mysql
NAME     DESCRIPTION       STARS       OFFICIAL       AUTOMATED
mysql    MySQL is a widely used, open-source relation…   11320     [OK]  

NAME           镜像仓库源的名称
DESCRIPTION    镜像的描述
OFFICIAL       是否 docker 官方发布
stars          类似 Github 里面的 star，表示点赞、喜欢的意思。
AUTOMATED      自动构建。
# 可选项
  -f, --filter filter   # Filter output based on conditions provided
      --format string   # Pretty-print search using a Go template
      --limit int       # Max number of search results (default 25)
      --no-trunc        # Don't truncate output
```

```shell
# 下载镜像
docker pull
# 结果及解释
[zouma@zouma1 ~]$ docker pull mysql:5.7
5.7: Pulling from library/mysql # 如果不写tag，默认是latest
e1acddbe380c: Pull complete # 分层下载，docker image的核心，联合文件系统
bed879327370: Pull complete 
03285f80bafd: Pull complete 
ccc17412a00a: Pull complete 
1f556ecc09d1: Pull complete 
adc5528e468d: Pull complete 
1afc286d5d53: Pull complete 
4d2d9261e3ad: Pull complete 
ac609d7b31f8: Pull complete 
53ee1339bc3a: Pull complete 
b0c0a831a707: Pull complete 
Digest: sha256:7cf2e7d7ff876f93c8601406a5aa17484e6623875e64e7acc71432ad8e0a3d7e
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7 # 真实地址
```

```shell
# 删除镜像
docker rmi
# 删除指定镜像
docker rmi -f 镜像id或镜像名称
# 删除多个镜像
docker rmi -f 镜像id或镜像名称 镜像id或镜像名称 镜像id或镜像名称
# 删除全部镜像
docker rmi -f $(docker images -aq)
```

### 容器命令

说明：有了镜像才可以创建容器，使用linux，下载Centos镜像来学习

```shell
# 下载centos镜像
docker pull centos
```

```shell
# 新建容器并启动
docker run [OPTIONS] image
# 常用可选项
--name="NAME" # 容器名字，用来区分容器
-d            # 后台运行方式
-it           # 使用交互方式运行，进入容器查看内容
-p            # 指定容器的端口
	-p ip：主机端口：容器端口
	-p 主机端口：容器端口（常用）
	-p 容器端口
	容器端口
-P            # 随机指定端口

# 新建容器后启动容器并进入容器
[zouma@zouma1 ~]$ docker run -it centos /bin/bash
[root@c03d9a990602 /]# 
```

```shell
# 查看运行的容器
docker ps

# 可选项
  -a, --all             # Show all containers (default shows just running) 会带出历史运行过的容器
  -f, --filter filter   # Filter output based on conditions provided
      --format string   # Pretty-print containers using a Go template
  -n, --last int        # Show n last created containers (includes all states) (default -1)
  -l, --latest          # Show the latest created container (includes all states)
      --no-trunc        # Don't truncate output
  -q, --quiet           # Only display container IDs
  -s, --size            # Display total file sizes
```

```shell
# 直接容器停止并退出
exit
# 容器不停止退出
Ctrl+p+q
```

```shell
# 删除容器，不能删除正在运行的容器
docker rm
# 删除指定容器，强制删除
docker rm -f 容器id或容器名称
# 删除多个容器，强制删除
docker rm -f 容器id或容器名称 容器id或容器名称 容器id或容器名称
# 删除全部容器，强制删除
docker rm -f $(docker ps -aq)
# 通过管道符,删除全部容器,并非强制删除
docker ps -a -q|xargs docker rm
```

```shell
# 启动容器
docker start 容器id
# 重启容器
docker restart 容器id
# 停止指定运行的容器
docker stop 容器id
# 强制停止指定运行的容器
docker kill 容器id
```

### 常用的其他命令

```shell
# 后台启动容器
docker run -d 镜像名称
# 结果
[zouma@zouma1 /]$ docker run -d centos
1c43ff1fe33ab5efb2cbe0edb3805ca30562e2a9f4260bfaa55585182b6fb745
[zouma@zouma1 /]$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
# 此时发现容器启动后停止了。
# 分析：docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用就会自动停止。比如nginx，容器启动后，发现自己没有提供服务，就会立刻停止，也就是没有程序了。
```

```shell
# 查看日志命令
docker logs [OPTIONS] CONTAINER
# 可选项
      --details        # Show extra details provided to logs
  -f, --follow         # Follow log output
      --since string   # Show logs since timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)
  -n, --tail string    # Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     # Show timestamps
      --until string   #Show logs before a timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)

# 测试
docker logs -f -t -n number CONTAINER 
# 发现没有日志，那就自己编写一段shell脚本产生日志
 docker run -d centos /bin/bash -c "while true;do echo duoxuexi;sleep 1;done"
# 查看日志
[zouma@zouma1 ~]$ docker logs -tf --tail 10 ac0d2a5b1970
2021-08-25T13:41:26.306233613Z duoxuexi
2021-08-25T13:41:27.308142738Z duoxuexi
2021-08-25T13:41:28.310013606Z duoxuexi
2021-08-25T13:41:29.311870554Z duoxuexi
2021-08-25T13:41:30.313790867Z duoxuexi
2021-08-25T13:41:31.315603335Z duoxuexi
2021-08-25T13:41:32.317546901Z duoxuexi
```

```shell
# 查看容器的进程
 docker top CONTAINER [ps OPTIONS]
# 测试
[zouma@zouma1 ~]$ docker top ac0d2a5b1970
UID      PID       PPID           C             STIME            TTY             TIME              CMD
root     14232     14214          0             21:39            ?              00:00:00            
root     15117     14232          0             21:52            ?              00:00:00           
```

```shell
# 返回有关docker的内部信息（元数据）
docker inspect [OPTIONS] NAME|ID [NAME|ID...]
# 可选项
  -f, --format string   # Format the output using the given Go template
  -s, --size            # Display total file sizes if the type is container
      --type string     # Return JSON for specified type
```

```shell
# 进入当前正在运行的容器
# 方式一
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
# 可选项
  -d, --detach               # Detached mode: run command in the background
      --detach-keys string   # Override the key sequence for detaching a container
  -e, --env list             # Set environment variables
      --env-file list        # Read in a file of environment variables
  -i, --interactive          # Keep STDIN open even if not attached
      --privileged           # Give extended privileges to the command
  -t, --tty                  # Allocate a pseudo-TTY
  -u, --user string          # Username or UID (format: <name|uid>[:<group|gid>])
  -w, --workdir string       # Working directory inside the container
# 测试
[zouma@zouma1 ~]$ docker exec -it ac0d2a5b1970 /bin/bash
[root@ac0d2a5b1970 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

# 方式二
docker attach [OPTIONS] CONTAINER
# 测试
[zouma@zouma1 ~]$ docker attach ac0d2a5b1970
duoxuexiduotigang
duoxuexiduotigang
duoxuexiduotigang

# 两种方式的区别
# docker exec 进入容器开启一个新的终端，可以在里面操作（常用），使用exit不会终止容器
# docker attach 进入容器正在容器正在执行的终端，不会开启新的进程
```

```shell
# 从docker容器里面拷贝文件到主机上
docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH
```

![](https://raw.githubusercontent.com/mao0824/pictureBed/master/img/20210826092925.png)

## 实战练习

### nginx

1. 搜索镜像

   建议去Docker hub上搜素，可以看到更多的信息

2. 下载镜像

   ```shell
   docker pull nginx 
   ```

3. 新建容器并启动

   ```shell
   docker run -d --name nginx01 -p 8100:80 nginx
   ```

4. 测试

   ```shell
   [root@zouma1 /]# curl localhost:8100
   <!DOCTYPE html>
   <html>
   <head>
   <title>Welcome to nginx!</title>
   <style>
       body {
           width: 35em;
           margin: 0 auto;
           font-family: Tahoma, Verdana, Arial, sans-serif;
       }
   </style>
   </head>
   <body>
   <h1>Welcome to nginx!</h1>
   <p>If you see this page, the nginx web server is successfully installed and
   working. Further configuration is required.</p>
   
   <p>For online documentation and support please refer to
   <a href="http://nginx.org/">nginx.org</a>.<br/>
   Commercial support is available at
   <a href="http://nginx.com/">nginx.com</a>.</p>
   
   <p><em>Thank you for using nginx.</em></p>
   </body>
   </html>
   ```

5. 进入容器

   ```shell
   [root@zouma1 /]# docker exec -it nginx01 /bin/bash
   root@7a5b5dbd7ad0:/# ls
   bin   dev		   docker-entrypoint.sh  home  lib64  mnt  proc  run   srv  tmp  var
   boot  docker-entrypoint.d  etc			 lib   media  opt  root  sbin  sys  usr
   root@7a5b5dbd7ad0:/# 
   ```

   思考：每次改动nginx的配置文件，都需要进入到容器内部十分的麻烦，是不是可以在容器的外部提供一个映射路径，可以到达容器并修改文件名，容器内部就可以自动修改？

   -v 数据卷    可以解决

## 可视化

### portainer

Docker图形化面板

### Rancher

## Docker镜像



