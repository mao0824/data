# ElasticSearch7.6

## 概述

Elaticsearch，简称ES，ES是一个开源的高拓展的`分布式全文检索工具`，他可以近乎实时的存储和检索数据。本身扩展性很好，可以扩展到上百台服务器，处理PB级别的数据。ES是用Java开发并使用Lucene作为核心来实现所有索引和搜索的功能，但是它的目的是通过简单的`RESTful API`来隐藏Lucene的复杂性，从而让全文搜索变得简单。

## 下载和安装

官网：https://www.elastic.co/

使用的最低要求JDK1.8

Java开发，ES的版本和我们之后对应的Java核心jar包版本对应

### Windows下安装

解压后就可以使用

![](https://raw.githubusercontent.com/mao0824/pictureBed/master/home/ES.png)

```
bin  启动文件
config  配置文件
	log4j2.properties  日志配置文件
	jvm.options  java虚拟机相关配置
	elasticsearch.yml  ES的配置文件
lib  相关jar包
modules  功能模块
plugins  插件 
```

启动:

![](https://raw.githubusercontent.com/mao0824/pictureBed/master/home/ES%E5%90%AF%E5%8A%A8%E6%88%90%E5%8A%9F.png)

启动测试：

```
{
  "name" : "MM-WIN",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "D3IhtAOoSVuScSrlQqmiPw",
  "version" : {
    "number" : "7.16.2",
    "build_flavor" : "default",
    "build_type" : "zip",
    "build_hash" : "2b937c44140b6559905130a8650c64dbd0879cfb",
    "build_date" : "2021-12-18T19:42:46.604893745Z",
    "build_snapshot" : false,
    "lucene_version" : "8.10.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

### 安装可视化界面

es head插件

需要有Node.js环境，谷歌插件Elasticsearch Head 可以直接使用



