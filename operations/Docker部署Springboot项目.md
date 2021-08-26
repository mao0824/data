# Docker部署Springboot+mysql

## Docker部署mysql5.7

一、创建数据、日志存放路径

```shell
mkdir -p ~/mysql5.7/{data,logs}
```

二、运行容器

```shell
docker run -d --name mysql5.7 \
           -v ~/mysql5.7/data:/var/lib/mysql \
           -e MYSQL_ROOT_PASSWORD=888888 \
           -p 3306:3306 \
           mysql:5.7
```

三、进入容器

```shell
docker exec -it mysql bash
```

四、登录mysql

五、开启远程服务并授权

```shell
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '888888' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EXIT;
```

六、大小写忽略

```shell
# 拷贝容器中的mysqld.cnf文件
docker cp mysql5.7:./etc/mysql/mysql.conf.d/mysqld.cnf \
          ~/mysql5.7/mysqld.cnf
# 修改mysqld.cnf文件，添加：
[mysqld] 
lower_case_table_names=1
# 拷贝修改后的mysqld.cnf文件到容器
docker cp ~/mysql5.7/mysqld.cnf \
          mysql5.7:./etc/mysql/mysql.conf.d/mysqld.cnf
```

七、重启容器

```shell
docker restart mysql5.7
```

八、查看日志

```shell
docker logs mysql5.7
```

## Docker部署Springboot项目

一、将SpringBoot项目打包

二、编写Dockerfile文件

```shell
touch dockerfile

# dockerfile 内容
# 基础镜像使用java
FROM java:8
# 作者
MAINTAINER zouma
# VOLUME 指定了临时文件目录为/tmp。
# 其效果是在主机 /var/lib/docker 目录下创建了一个临时文件，并链接到容器的/tmp
VOLUME /tmp 
# 将jar包添加到容器中并更名
ADD myBlog-0.0.1-SNAPSHOT.jar myBlog.jar 
# 运行jar包
RUN bash -c 'touch /myBlog.jar'
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/myBlog.jar"]
```

三、创建一个文件夹，把jar包和Dockerfile放里面

```shell
# 创建文件夹
mkdir docker
# 将dockerfile复制到docker文件夹中
cp -r /dockerfile /docker

# 查看你系统自带的软件包的信息，有filename：/user/bin/rz说明包是存在的
yum provides */rz
# 进行包安装
yum install -y lrzsz
# 上传
rz
```

四、制作镜像

```shell
docker build -t myblog .
# myblog是镜像名称
```

五、运行容器

```shell
 docker run -d -p 80:80 myblog
```

