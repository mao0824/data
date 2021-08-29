# Docker进阶

## 容器数据卷

但把应用和环境打包成镜像，如果数据都在容器中，那么删除容器数据就会丢失，所以需要数据持久化。

容器之间有一个数据共享技术，也就是容器数据卷技术，它可以将Docker容器产生的数据同步到宿主机，其实就是目录的挂载，将容器内的目录挂载到Linux上面。

总结：容器的持久化和同步操作！容器间也是可以同步共享的。

### 使用数据卷

方式一：直接使用命令来挂载

```shell
docker run -v 主机目录：容器内的目录

# 测试
        "Mounts": [  # 挂载 -v 卷
            {
                "Type": "bind",
                "Source": "/root/mysql5.7/data", # 主机内地址
                "Destination": "/var/lib/mysql", # docker容器内地址
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],
```

假设我们将容器删除后，挂载到宿主机的数据卷依旧没有丢失，这就实现了容器数据的持久化功能。

方式二：

通过编写dockerfile文件来挂载

```shell
# VOLUME 指定了临时文件目录为/tmp。
# 其效果是在主机 /var/lib/docker/volume 目录下创建了一个临时文件，并链接到容器的/tmp
VOLUME /tmp 
```

### 具名和匿名挂载

```shell
# 匿名挂载
-v 容器内路径
# 测试
docker run -d -P --name ngonx01 -v /etc/nginx nginx
# 查看卷
[root@zouma1 /]# docker volume ls
DRIVER    VOLUME NAME
local     9ffc47726753fe239a8b00109595b57b6c01294e8afdff734e67b5d48e9665a3

# 具名挂载
-v 卷名：容器内路径
docker run -d -P --name ngonx01 -v juan-nginx:/etc/nginx nginx
# 查看指定卷
[root@zouma1 /]# docker volume inspect beb24b67b2a0ce3a26e415cef6416f578beed190a383f0a7ee6cfb780e7f7f00
[
    {
        "CreatedAt": "2021-08-26T20:16:42+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/beb24b67b2a0ce3a26e415cef6416f578beed190a383f0a7ee6cfb780e7f7f00/_data", # 所有docker容器的卷，没有指定目录的情况下，放入/var/lib/docker/volumes
        "Name": "beb24b67b2a0ce3a26e415cef6416f578beed190a383f0a7ee6cfb780e7f7f00",
        "Options": null,
        "Scope": "local"
    }
]
```

**怎么区别匿名挂载、具名挂载和指定路径挂载**

```shell
# 匿名挂载
-v 容器内路径
# 具名挂载
-v 卷名：容器内路径
# 指定路径挂载
-v 宿主机路径：容器内路径
```

**拓展**

```shell
# 通过 -v 容器内路径：ro或者rw  改变读写权限
# ro readonly 可读
# rw readwrite 可读可写
# 一旦这个设置了容器权限，容器对我们挂载出来的内容就有了限定，默认的是rw，ro说明这个路径只能通过宿主机来# 操作，容器内部是不可以操作的
docker run -d -P --name ngonx01 -v juan-nginx:/etc/nginx:ro nginx
docker run -d -P --name ngonx01 -v juan-nginx:/etc/nginx:rw nginx
```

### 容器间数据挂载

```shell
 --volumes-from 
 # 拷贝的概念
```

**结论：**

容器之间配置信息的传递，数据卷容器的生命周期一直持续到没有容器使用为止

但是一旦持久化到了宿主机，这个时候，宿主机的数据是不会删除的

## DockerFile

### DockerFile简介

dockerfile是用来构建docker镜像的文件，就是命令参数脚本

**注意：**

每一个保留关键字（指令）都必须是大写字母

从上向下顺序执行

每一个指令都会创建提交一个新的镜像层，并提交

井号表示注释

![](https://raw.githubusercontent.com/mao0824/pictureBed/master/img/20210828181053.png)



dockerfile是面向开发的，我们以后要发布项目，做镜像，就要编写dockerfile文件

DockerFile：构建文件，定义了一切步骤，源代码

DockerImages：通过DockerFile构建生成的镜像，最终发布和运行的产品，原来是jar war

DockerContainer：容器就是就是镜像运行起来提供服务器

### DockerFile指令

|    指令    |                           指令介绍                           |
| :--------: | :----------------------------------------------------------: |
|    FROM    | DockerFile除了注释第一航必须是FROM，FROM后面镜像名称，代表要基于哪个基础镜像构建镜像 |
| MAINTAINER |              镜像维护者的信息，一般是姓名和邮箱              |
|    RUN     |                   镜像构建时需要运行的命令                   |
|    ADD     |               拷贝本机文件或者远程文件到镜像内               |
|    COPY    |                     拷贝本机文件到镜像内                     |
|   LABEL    |                        设置镜像的标签                        |
|    USER    |                运行RUN CMD ENTRYPOINT的用户名                |
|   VOLUME   |                      设置卷，挂载的目录                      |
| ENTRYPOINT |         指定一个容器启动时要运行的指令，可以追加命令         |
|    CMD     | 指定一个容器启动时要运行的指令，DockerFile中可以有多个CMD指令，但只有最后一个生效，CMD会被docker run之后的参数替换 |
|    ENV     |                指定环境变量，格式为key-value                 |
|    ARG     | 定义外部参数，构建镜像时可以使用build -arg<varname>=<value>的格式传递参数用于构建 |
|   EXPOSE   |            设置暴露的端口（容器运行时的服务端口）            |
|  WORKDIR   |    设置镜像的工作目录（RUN CMD ENTRYPOINT COPY ADD指令）     |
|  ONBUILD   |              创建子镜像时制定自动执行的操作指令              |
| STOPSIGNAL |                     设置容器的退出信号量                     |

**查看镜像历史信息**

```shell
 docker history [OPTIONS] IMAGE
 # 可选项
       --format string   Pretty-print images using a Go template
  -H, --human           Print sizes and dates in human readable format (default true)
      --no-trunc        Don't truncate output
  -q, --quiet           Only show image IDs
```

### 根据DockerFile创建镜像

```shell
 docker build [OPTIONS] PATH | URL | -
 # 可选项
       --add-host list           Add a custom host-to-IP mapping (host:ip)
      --build-arg list          Set build-time variables
      --cache-from strings      Images to consider as cache sources
      --cgroup-parent string    Optional parent cgroup for the container
      --compress                Compress the build context using gzip
      --cpu-period int          Limit the CPU CFS (Completely Fair Scheduler) period
      --cpu-quota int           Limit the CPU CFS (Completely Fair Scheduler) quota
  -c, --cpu-shares int          CPU shares (relative weight)
      --cpuset-cpus string      CPUs in which to allow execution (0-3, 0,1)
      --cpuset-mems string      MEMs in which to allow execution (0-3, 0,1)
      --disable-content-trust   Skip image verification (default true)
  -f, --file string             Name of the Dockerfile (Default is 'PATH/Dockerfile')
      --force-rm                Always remove intermediate containers
      --iidfile string          Write the image ID to the file
      --isolation string        Container isolation technology
      --label list              Set metadata for an image
  -m, --memory bytes            Memory limit
      --memory-swap bytes       Swap limit equal to memory plus swap: '-1' to enable unlimited swap
      --network string          Set the networking mode for the RUN instructions during build (default
                                "default")
      --no-cache                Do not use cache when building the image
      --pull                    Always attempt to pull a newer version of the image
  -q, --quiet                   Suppress the build output and print image ID on success
      --rm                      Remove intermediate containers after a successful build (default true)
      --security-opt strings    Security options
      --shm-size bytes          Size of /dev/shm
  -t, --tag list                Name and optionally a tag in the 'name:tag' format
      --target string           Set the target build stage to build.
      --ulimit ulimit           Ulimit options (default [])

```

### 发布镜像

**发布到到DockerHub**

```shell
# 登录Docker仓库
docker login [OPTIONS] [SERVER]
# 可选项
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username

# 登录完毕之后，提交镜像
 docker push [OPTIONS] NAME[:TAG]
# 可选项
  -a, --all-tags                Push all tagged images in the repository
      --disable-content-trust   Skip image signing (default true)
  -q, --quiet                   Suppress verbose output
```

**发布到阿里云容器服务**

登录阿里云

找到容器镜像服务

创建命名空间

创建容器镜像

![](https://raw.githubusercontent.com/mao0824/pictureBed/master/img/20210828225811.png)

命令拓展：

```shell
# 将镜像打成tar压缩包
docker save [OPTIONS] IMAGE [IMAGE...]
# 可选项
-o, --output string   Write to a file, instead of STDOUT

# 解压镜像tar包
docker load [OPTIONS]
# 可选项
  -i, --input string   Read from tar archive file, instead of STDIN
  -q, --quiet          Suppress the load output
```

## Docker网络

### 理解Docker0

```shell
# 查看所有网卡IP地址
ip addr
```

![](https://raw.githubusercontent.com/mao0824/pictureBed/master/img/20210829120709.png)

```shell
# 测试
docker run -d -P --name tomcat01 tomcat
# 查看容器内部ip地址
[root@zouma1 zouma]# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
94: eth0@if95: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:04 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.4/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft foreve     
# 启动容器之后，会得到一个 eth0@if95 ip地址，是docker分配的

# linux能不能ping通容器内部？
[root@zouma1 zouma]# ping 172.17.0.4
PING 172.17.0.4 (172.17.0.4) 56(84) bytes of data.
64 bytes from 172.17.0.4: icmp_seq=1 ttl=64 time=0.051 ms
64 bytes from 172.17.0.4: icmp_seq=2 ttl=64 time=0.059 ms
64 bytes from 172.17.0.4: icmp_seq=3 ttl=64 time=0.064 ms
```

我们每启动一个docker容器，docker就会给docker容器分配一个ip，我们只要安装了docker，就会有一个网卡docker0桥接模式，使用的技术是evth-pair技术。

发现容器带来的网卡都是一对对的。容器内查看94: eth0@if95，宿主机上查看95: veth16a2a71@if94。

**evth-pair 就是一对的虚拟设备接口，它们都是成对出现的，一段连着协议，一段彼此相连**，这是有这个特性，evth-pair充当一个桥梁。

容器之间是可以相互ping通的，容器之间通过一个路由器，docker0。

所有的容器不指定网络的情况下，都是docker0路由的，docker会给我们的容器分配一个默认的可用IP。

Docker中所有的网络接口都是虚拟的，虚拟的转发效率高。

### --link

链接两个容器相互通信，可以通过名字来访问容器

```shell
[root@zouma1 zouma]# docker run -d -P --name tomcat02 --link tomcat01 tomcat
29d0b6f2d62cb6a25157526b4d4ed51a339e6b4044cefde56c61f137d92ca8d0
[root@zouma1 zouma]# docker exec -it tomcat02 ping tomcat01
PING tomcat01 (172.17.0.4) 56(84) bytes of data.
64 bytes from tomcat01 (172.17.0.4): icmp_seq=1 ttl=64 time=0.133 ms
64 bytes from tomcat01 (172.17.0.4): icmp_seq=2 ttl=64 time=0.072 ms
64 bytes from tomcat01 (172.17.0.4): icmp_seq=3 ttl=64 time=0.073 ms
# 那反向可以ping通吗？答案是不可以 除非在tomcat01里面也配置一下
```

本质其实就是这个tomcat02在hosts文件里面配置了tomcat01的配置

```shell
[root@zouma1 zouma]# docker exec -it tomcat02 cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.17.0.4	tomcat01 87e419eed102
172.17.0.5	29d0b6f2d62c
```

不推荐使用--link。docker0不支持容器名连接访问，我们需要自定义网络。

### 自定义网络

```shell
# 查看所有的docker网络
docker network ls [OPTIONS]
# 可选项
  -f, --filter filter   # Provide filter values (e.g. 'driver=bridge')
      --format string   # Pretty-print networks using a Go template
      --no-trunc        # Do not truncate the output
  -q, --quiet           # Only display network IDs
# 测试
[root@zouma1 zouma]# docker network ls 
NETWORK ID     NAME      DRIVER    SCOPE
261351bf7a56   bridge    bridge    local
b770eb69310b   host      host      local
d85f23850873   none      null      local
```

**网络模式**

bridge：  桥接模式（默认）

none：  不配置网络

host：和宿主机共享网络

container：  容器内可以网络连通（用得少，局限性大）

```shell
docker network COMMAND
# 指令
  connect     # Connect a container to a network
  create      # Create a network
  disconnect  # Disconnect a container from a network
  inspect     # Display detailed information on one or more networks
  ls          # List networks
  prune       # Remove all unused networks
  rm          # Remove one or more networks
```

```shell
# --net bridge是默认的，两个效果一样
docker run -d -P --name tomcat01 tomcat
docker run -d -P --name tomcat01 --net bridge tomcat

# docker0特点：默认的，域名不能访问，--link可以打通连接但不建议使用，所以要自定义网络
```

```shell
# 创建网卡
docker network create [OPTIONS] NETWORK
# 可选项
      --attachable           # Enable manual container attachment
      --aux-address map      # Auxiliary IPv4 or IPv6 addresses used by Network driver (default map[])
      --config-from string   # The network from which to copy the configuration
      --config-only          # Create a configuration only network
  -d, --driver string        # Driver to manage the Network (default "bridge")管理网络的驱动程序
      --gateway strings      # IPv4 or IPv6 Gateway(网关) for the master subnet
      --ingress              # Create swarm routing-mesh network
      --internal             # Restrict external access to the network
      --ip-range strings     # Allocate container ip from a sub-range
      --ipam-driver string   # IP Address Management Driver (default "default")
      --ipam-opt map         # Set IPAM driver specific options (default map[])
      --ipv6                 # Enable IPv6 networking
      --label list           # Set metadata on a network
  -o, --opt map              # Set driver specific options (default map[])设置驱动程序的特定选项
      --scope string         # Control the network's scope
      --subnet strings       # Subnet（子网） in CIDR format that represents a network segment
      
# 自定义网络
# -d bridge
# --subnet 192.168.0.0/16   192.168.0.2-192.168.255.255
# --gateway 192.168.0.1
docker network create -d bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
# 测试
[root@zouma1 zouma]# docker network create -d bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
20c62ee2b6b1382a9f0ebb6c0e3c88ceae2ed5bf7cc9cf8b7cb95b360a060767
[root@zouma1 zouma]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
261351bf7a56   bridge    bridge    local
b770eb69310b   host      host      local
20c62ee2b6b1   mynet     bridge    local
d85f23850873   none      null      local
```

使用自定义网络运行容器测试

```shell
docker run -d -P --name tomcat-net-01 --net mynet tomcat
docker run -d -P --name tomcat-net-02 --net mynet tomcat
docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "20c62ee2b6b1382a9f0ebb6c0e3c88ceae2ed5bf7cc9cf8b7cb95b360a060767",
        "Created": "2021-08-29T18:18:12.824434229+08:00",
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
            "2b0607daa2b3941d83e338fe9d989d9944cc99e3be70140256988a6b78a5ecdf": {
                "Name": "tomcat-net-02",
                "EndpointID": "8e0fcd1593efe7fd04a106786cdd2cbe51463bf9261446095cc16e6880c2e739",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            },
            "90d310829339645bac5d88899f6a891a5887e58dd9fd4a70a3b991895ae1a135": {
                "Name": "tomcat-net-01",
                "EndpointID": "7497e729de39a96246ec56ea33faa85b150d36d4c0eb5f541f24e04f1089cddd",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
# 通过名称ping两个容器,发现是可以ping通的
[root@zouma1 zouma]# docker exec -it tomcat-net-01 ping tomcat-net-02
PING tomcat-net-02 (192.168.0.3) 56(84) bytes of data.
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.091 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.078 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=3 ttl=64 time=0.082 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=4 ttl=64 time=0.082 ms
```

自定义网络已经维护好了对应的关系，推荐使用这种

好处：

不同的集群使不同的网络，保证集群是安全和健康的，比如redis集群和mysql集群

### 网络连接

```shell
# 连接一个容器到网络
docker network connect [OPTIONS] NETWORK CONTAINER
# 可选项
      --alias strings           Add network-scoped alias for the container
      --driver-opt strings      driver options for the network
      --ip string               IPv4 address (e.g., 172.30.100.104)
      --ip6 string              IPv6 address (e.g., 2001:db8::33)
      --link list               Add link to another container
      --link-local-ip strings   Add a link-local address for the container
# 测试，打通tomcat-03 mynet
docker network connect mynet tomcat-03
docker network inspect mynet
# 查看信息后发现，mynet里面存在tomcat-03
 "Containers": {
            "2b0607daa2b3941d83e338fe9d989d9944cc99e3be70140256988a6b78a5ecdf": {
                "Name": "tomcat-net-02",
                "EndpointID": "8e0fcd1593efe7fd04a106786cdd2cbe51463bf9261446095cc16e6880c2e739",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            },
            "90d310829339645bac5d88899f6a891a5887e58dd9fd4a70a3b991895ae1a135": {
                "Name": "tomcat-net-01",
                "EndpointID": "7497e729de39a96246ec56ea33faa85b150d36d4c0eb5f541f24e04f1089cddd",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            },
            "c6c1c0eb955b0da558e77021e47756bb509c93c7c249f9832b890bc305aa241f": {
                "Name": "tomcat-03",
                "EndpointID": "054074528cead29c6fdc3891d772ef44428290f678d37353bbe0d06198817cd8",
                "MacAddress": "02:42:c0:a8:00:04",
                "IPv4Address": "192.168.0.4/16",
                "IPv6Address": ""
            }
        },
# 此时一个tomcat-03容器有两个ip
```

