---
title: "jenkins自动化构建部署"
date: 2020-06-18 02:00:00
categories:
  - 容器
  - docker
tags:
  - 容器
  - docker
  - 部署
thumbnailImagePosition: left
thumbnailImage: https://gitee.com/zzf35/cloudimg/raw/master/img/20200622193619.png
---

自动化构建部署。CI/CD

<!--more-->



>大纲：·
1、jenkins插件的安装
  &nbsp; &nbsp;  1.1 全局工具的配置
  &nbsp; &nbsp;  1.2 jenkins插件配置
2、开始项目的简单配置
  &nbsp; &nbsp;  2.1 配置服务器的登陆用户
  &nbsp; &nbsp;  2.2 开始进行项目的配置

##1. jenkins插件的安装
&nbsp; &nbsp;  **jenkins的安装可以参考另一篇文章：**https://www.jianshu.com/p/835986f64cf1

####1.1  全局工具配置(jdk1.8 + maven + docker)
&nbsp; &nbsp;  点击：系统管理 --> 全局工具配置
&nbsp; &nbsp;  大家根据自己的实际情况配置即可
&nbsp; &nbsp;  由于本人前面使用的是数据卷的形式，所以将相关的软件cp到jenkins_data数据卷进行安装。
```
[root@tag _data]# docker volume inspect jenkins_data
[
    {
        "CreatedAt": "2019-03-20T12:54:52+08:00",
        "Driver": "local",
        "Labels": {
            "com.docker.compose.project": "jenkins",
            "com.docker.compose.version": "1.23.0dev",
            "com.docker.compose.volume": "data"
        },
        "Mountpoint": "/var/lib/docker/volumes/jenkins_data/_data",
        "Name": "jenkins_data",
        "Options": null,
        "Scope": "local"
    }
]
```
&nbsp; &nbsp;  *可以直接将文件复制到挂载点(Mountpoint)：/var/lib/docker/volumes/jenkins_data/_data*
```
[root@tag _data]# mkdir -p /var/lib/docker/volumes/jenkins_data/_data/soft
[root@tag soft]# pwd
/var/lib/docker/volumes/jenkins_data/_data/soft
[root@tag soft]# ls
apache-maven-3.5.0-bin.tar.gz  docker-18.06.0-ce.tgz  git-2.9.5.tar.gz  jdk-8u152-linux-x64.tar.gz
[root@tag soft]# tar -zxvf apache-maven-3.5.0-bin.tar.gz 
[root@tag soft]# tar -zxvf docker-18.06.0-ce.tgz
[root@tag soft]# tar -zxvf jdk-8u152-linux-x64.tar.gz
[root@tag soft]# ls
apache-maven-3.5.0  apache-maven-3.5.0-bin.tar.gz  docker  docker-18.06.0-ce.tgz  git-2.9.5.tar.gz  jdk1.8.0_152  jdk-8u152-linux-x64.tar.gz
```
配置示例如下：
![image.png](https://gitee.com/zzf35/cloudimg/raw/master/img/20200628171457.jpg)
![image.png](https://gitee.com/zzf35/cloudimg/raw/master/img/20200628171506.jpg)

####1.2  jenkins插件配置
>&nbsp; &nbsp;  **主要安装的是：**
&nbsp; &nbsp;  1. **Maven Integration：**新建job时有maven项目可以选择；
&nbsp; &nbsp;  2. **Deploy to container：**将war包部署到tomcat所在的服务器上；
&nbsp; &nbsp;  3. **Publish Over SSH：**通过ssh推送文件，并可以执行shell命令；

![插件安装.png](https://gitee.com/zzf35/cloudimg/raw/master/img/20200628163310.jpg)

![Maven Integration安装.png](https://gitee.com/zzf35/cloudimg/raw/master/img/20200628163321.jpg)

![Deploy to container.png](https://gitee.com/zzf35/cloudimg/raw/master/img/20200628163329.jpg)

![Publish Over SSH.png](https://gitee.com/zzf35/cloudimg/raw/master/img/20200628163355.jpg)
##2. 开始项目的简单配置
#### 2.1 配置服务器的登陆用户
>**在设置里增加所要部署的服务器的ssh连接方式**

直接看图操作

![image.png](https://gitee.com/zzf35/cloudimg/raw/master/img/20200628163345.jpg)

![image.png](https://gitee.com/zzf35/cloudimg/raw/master/img/20200628163355.jpg)
###**这里可以配置多台不一样密码的服务器，自己慢慢去琢磨把！！！**

#### 2.2 开始进行项目的配置

![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200628171734.jpg)

![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200628171742.jpg)



![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200628171751.jpg)



![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200628171801.jpg)



![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200628171811.jpg)

![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200628171819.jpg)

![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200628171832.jpg)

![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200628171841.jpg)

![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200628171850.jpg)



##ok，到这里就部署完成了，这就是一个简单的流水线的部署。
**当然了，jenkins核心部署并不是这样的形势，但是作为一个入门或者平常的开发使用，对于博主来说目前是足够了，等有时间了再去琢磨另一种流水线的部署。**