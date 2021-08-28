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

