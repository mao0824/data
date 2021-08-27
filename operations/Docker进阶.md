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
docker run -d -P --name ngonx01 -v /etc/nginx nginx


```



## Dockerfile

## Docker网络