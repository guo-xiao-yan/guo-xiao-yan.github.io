---
layout: post
title:  "Docker部署SpringBoot项目"
date:   2023-03-12 15:33:07 +0800
categories: Docker SpringBoot
---

[CMSC5702分布式的作业](https://github.com/codeworm111/MovieHeaven)，做了个电影评分网站。
本文介绍工作最后一步：将项目打包成Docker镜像。

<!-- more -->

# Docker
## 干嘛的
像GitHub一样有一个社区，里面有大家发布的各种镜像。你可以直接把它们下载下来运行，像虚拟机似的，不用自己一点一点配置环境，就很方便。
## 为啥用它
我也可以把我的项目打个包一丢，这样别人（检查作业的TA）可以直接下载看看我们做了个什么鬼东西。
# 需要的镜像
## 下载
我们的项目用到了`mysql`和`redis`，所以先把这俩pull下来。

```shell
docker pull mysql
docker pull redis
```

懒得研究命令行就直接用[GUI](https://www.docker.com)，看着也顺眼。

![mysql&redis](/assets/images/deploy-springboot-project-with-docker/mysql&redis.png)

## 运行
### mysql
填好数据库的用户名密码

```shell
docker run --name mymysql -e MYSQL_ROOT_PASSWORD=JXZ1234@ -d -p 3306:3306  mysql
```

把本地的数据库文件导入进容器里

```shell
docker cp SqlGeneration.sql mymysql:/home/SqlGeneration.sql
```

进入刚才启动的容器

```shell
sudo docker exec -it mymysql bash
```

用刚才设的帐户密码登录mysql

```shell
mysql -uroot -pJXZ1234@
```

然后把数据库导入进去

```sql
source /home/SqlGeneration.sql
```

### redis

```shell
docker run --name myredis -p 6379:6379 -d redis redis-server --appendonly yes
```

# 准备工作
## Maven打包

```shell
mvn clean package -Dmaven.test.skip=true
```

## Dockerfile
在进入`target`文件夹，于`MovieHeaven-0.0.1-SNAPSHOT.jar`旁边再建一个名叫`Dockerfile`的文件，上书：

```dockerfile
#指定基础镜像，格式为：FROM image:tag 例如：FROM java:8   （8是版本1.8）
#这个java改到openjdk里了，没有就pull下
FROM openjdk:jdk

#维护者信息
MAINTAINER GUOXIAOYAN

#添加要加入的文件，与COPY命令的性质基本一致，但是ADD更强大
#可添加源路径，可自动解压压缩包，但解压功能和指定目标路径不可同时使用
#该命令在使用docker run -v时可不用，在后续有介绍
ADD MovieHeaven-0.0.1-SNAPSHOT.jar /opt/MovieHeaven/MovieHeaven.jar
#add命令会把config下的所有文件拷贝到容器中的/opt/test/config下,此条代码只用作添加文件夹的讲解示范，
#ADD config /opt/test/config/

#用于指定在容器启动时要执行的命令
CMD java -jar /opt/MovieHeaven/MovieHeaven.jar

#为构建镜像设置监听端口，使容器在运行时监听
EXPOSE 8083
```

# 构建与运行
## 把jar包build成docker镜像

```shell
docker build -t eblog .
```

## 启动容器
别忘了link上mysql与redis。

```shell
docker run -p 8083:8083 --name movieheaven  --link mymysql:emysql --link myredis:eredis -d movieheaven-image
```

这时候打开[127.0.0.1:8083/movie/index](http://127.0.0.1:8083/movie/index)就可以看到MovieHeaven主页啦，如果报错了可以查看日志：

```shell
docker logs -f eblog
```

# 参考
- [Docker快速部署springboot包含mysql、redis、rabbitmq](https://huangkongshenghugh.gitee.io/posts/docker%E5%BF%AB%E9%80%9F%E9%83%A8%E7%BD%B2springboot%E5%8C%85%E5%90%ABmysqlredisrabbitmq/)
- [20分钟学会Docker部署SpringBoot项目](https://www.bilibili.com/video/BV1dk4y1r7pi?p=1&vd_source=b7e4d17fd13ffa91c4da6d37c08a6c7c)
