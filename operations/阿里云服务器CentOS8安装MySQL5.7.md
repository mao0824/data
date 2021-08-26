# 阿里云服务器CentOS8安装MySQL5.7

Centos8默认是安装MySQL8版本的，因为Centos8的AppStream仓库只包含MySQL8的包。



## 添加MySQL5.7存储库

关闭Centos8中MySQL默认的AppStream仓库：

```shell
sudo dnf remove @mysql
sudo dnf module reset mysql && sudo dnf module disable mysql
```

centos 8没有MySQL存储库，因此我们将使用centos 7存储库，创建一个新的存储库文件:

```shell
sudo vi /etc/yum.repos.d/mysql-community.repo
```

将下边的全部内容复制到新建的存储库文件中：

```
[mysql57-community]
name=MySQL 5.7 Community Server
baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/7/$basearch/
enabled=1
gpgcheck=0

[mysql-connectors-community]
name=MySQL Connectors Community
baseurl=http://repo.mysql.com/yum/mysql-connectors-community/el/7/$basearch/
enabled=1
gpgcheck=0

[mysql-tools-community]
name=MySQL Tools Community
baseurl=http://repo.mysql.com/yum/mysql-tools-community/el/7/$basearch/
enabled=1
gpgcheck=0
```

## 安装MySQL5.7

创建好仓库，输入以下命令就可以安装了

```shell
sudo dnf --enablerepo=mysql57-community install mysql-community-server
```

过程中输入Y



下载完成后检查版本---->出现版本号说明安装成功

```shell
rpm -qi mysql-community-server
```

或者 检查 mysql 源

```shell
yum repolist enabled | grep "mysql.*-community.*"
```

出现以下信息说明安装成功

```
mysql-connectors-community MySQL Connectors Community                       203
mysql-tools-community      MySQL Tools Community                            129
mysql57-community          MySQL 5.7 Community Server                       504
```



## MySQL配置

查看MySQL状态

```shell
systemctl status mysqld.service
```

启动MySQL

```shell
systemctl start mysqld.service
```

设置开机启动

```shell
systemctl enable mysqld.service
```

获取安装mysql后生成的临时密码，用于登录

```shell
grep 'temporary password' /var/log/mysqld.log
```

登录MySQL

```shell
mysql -uroot -p
```

修改密码

```shell
ALTER USER 'root'@'localhost' IDENTIFIED BY 'Password5';
```

注意：修改后的密码，注意必须包含大小写字母数字以及特殊字符并且长度不能少于8位,否则会报错



修改MySQL密码设置的规范

查看MySQL完整的初始密码规则，登入后执行以下命令：

```shell
SHOW VARIABLES LIKE 'validate_password%';
```

```
+--------------------------------------+-------+
| Variable_name                        | Value |
+--------------------------------------+-------+
| validate_password_check_user_name    | OFF   |
| validate_password_dictionary_file    |       |
| validate_password_length             | 8     |
| validate_password_mixed_case_count   | 1     |
| validate_password_number_count       | 1     |
| validate_password_policy             |MEDIUM |
| validate_password_special_char_count | 1     |
+--------------------------------------+-------+
```

密码的长度是由validate_password_length决定的,但是可以通过以下命令修改

```shell
set global validate_password_length=4;
```

validate_password_policy决定密码的验证策略,默认等级为MEDIUM(中等),可通过以下命令修改为LOW(低)

```shell
set global validate_password_policy=0;
```

修改之后就可以设置简单密码了

## 开启MySQL远程访问

默认MySQL的root用户只能在本机localhost上连接，不能远程进行连接，所以要开启

```shell
grant all privileges on *.* to 'root'@'%' identified by 'Password5' with grant option;
```

注意：%代表所有用户，如要开启某一个IP，用IP代替%

执行刷新任务

```shell
flush privileges;
```

## 开放防火墙端口：3306

启动防火墙

```shell
systemctl start firewalld
```

查看防火墙状态

```shell
systemctl status firewalld
```

开放端口

```shell
firewall-cmd --zone=public --add-port=3306/tcp --permanent
```

配置立即生效

```shell
firewall-cmd --reload
```

查看开放的端口列表

```shell
firewall-cmd --zone=public --list-ports
```

关闭端口

```shell
firewall-cmd --zone=public --remove-port=3306/tcp --permanent  
```

## 阿里云服务器添加安全组规则

阿里云ECS服务器需要添加安全组规则，开启3306端口才可以远程连接MySQL