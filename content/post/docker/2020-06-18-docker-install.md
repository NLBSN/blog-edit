---
title: "docker容器安装部署"
date: 2020-06-18
categories:
  - 容器
  - docker
tags:
  - 容器
  - docker
  - 部署
thumbnailImagePosition: left
thumbnailImage: https://gitee.com/zzf35/cloudimg/raw/master/img/20200622193715.jpg
---

docker容器的在线以及二进制安装。以及一些简单的配置。

<!--more-->

<!-- toc -->

# 在线安装

卸载老版本的 docker 及其相关依赖

```
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine \
                  -y
```

删除旧的docker文件

```
sudo rm -rf /var/lib/docker
```

使用yum进行安装

\# step 1: 安装必要的一些系统工具

```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

安装仓库地址

```
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

查看仓库内可选的版本包

````
[root@DESKTOP-KQN1KKC ~]# yum list docker-ce --showduplicates | sort -r
 * updates: mirrors.huaweicloud.com
Loading mirror speeds from cached hostfile
Loaded plugins: fastestmirror
 * extras: mirrors.huaweicloud.com
docker-ce.x86_64            3:19.03.9-3.el7                     docker-ce-stable
docker-ce.x86_64            3:19.03.8-3.el7                     docker-ce-stable
docker-ce.x86_64            3:19.03.7-3.el7                     docker-ce-stable
docker-ce.x86_64            3:19.03.6-3.el7                     docker-ce-stable
docker-ce.x86_64            3:19.03.5-3.el7                     docker-ce-stable
docker-ce.x86_64            3:19.03.4-3.el7                     docker-ce-stable
docker-ce.x86_64            3:19.03.3-3.el7                     docker-ce-stable
docker-ce.x86_64            3:19.03.2-3.el7                     docker-ce-stable
docker-ce.x86_64            3:19.03.1-3.el7                     docker-ce-stable
docker-ce.x86_64            3:19.03.12-3.el7                    docker-ce-stable
docker-ce.x86_64            3:19.03.11-3.el7                    docker-ce-stable
docker-ce.x86_64            3:19.03.10-3.el7                    docker-ce-stable
docker-ce.x86_64            3:19.03.0-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.9-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.8-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.7-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.6-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.5-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.4-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.3-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.2-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.1-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.0-3.el7                     docker-ce-stable
docker-ce.x86_64            18.06.3.ce-3.el7                    docker-ce-stable
docker-ce.x86_64            18.06.2.ce-3.el7                    docker-ce-stable
docker-ce.x86_64            18.06.1.ce-3.el7                    docker-ce-stable
docker-ce.x86_64            18.06.0.ce-3.el7                    docker-ce-stable
docker-ce.x86_64            18.03.1.ce-1.el7.centos             docker-ce-stable
docker-ce.x86_64            18.03.0.ce-1.el7.centos             docker-ce-stable
docker-ce.x86_64            17.12.1.ce-1.el7.centos             docker-ce-stable
docker-ce.x86_64            17.12.0.ce-1.el7.centos             docker-ce-stable
docker-ce.x86_64            17.09.1.ce-1.el7.centos             docker-ce-stable
docker-ce.x86_64            17.09.0.ce-1.el7.centos             docker-ce-stable
docker-ce.x86_64            17.06.2.ce-1.el7.centos             docker-ce-stable
docker-ce.x86_64            17.06.1.ce-1.el7.centos             docker-ce-stable
docker-ce.x86_64            17.06.0.ce-1.el7.centos             docker-ce-stable
docker-ce.x86_64            17.03.3.ce-1.el7                    docker-ce-stable
docker-ce.x86_64            17.03.2.ce-1.el7.centos             docker-ce-stable
docker-ce.x86_64            17.03.1.ce-1.el7.centos             docker-ce-stable
docker-ce.x86_64            17.03.0.ce-1.el7.centos             docker-ce-stable
 * base: mirror.bit.edu.cn
Available Packages
[root@DESKTOP-KQN1KKC ~]#
````

选则其中一个包安装

默认安装：(以下命令二选一)

```
sudo yum install docker-ce
sudo yum install docker-ce docker-ce-cli containerd.io
```

指定版本安装：(以下命令二选一)

```
sudo yum install -y docker-ce-19.03.12
sudo yum install -y docker-ce-19.03.12 docker-ce-cli-19.03.12 containerd.io
```

## 安装docker-compose

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

docker-compose --version
```

# 离线安装

docker版本：docker-18.06.0-ce

>大纲：
一、为什么要采用二进制安装
二、二进制的安装包的下载
三、二进制安装包的安装步骤
四、小tips
&nbsp; &nbsp;  1、docker默认网卡网段的修改
&nbsp; &nbsp;  2、docker版本13和18的一个差别----文件挂载

## 一、为什么要采用二进制安装

&nbsp; &nbsp;  网上有很多的docker的安装教程，我也原来写过关于docker的安装(CSDN)，但最近在部署使用docker的过程中，发现有很多的配置还是需要学习并注意的。所以再写一次并方便自己日后进行补充。
&nbsp; &nbsp;  使用二进制部署的实际场景：
&nbsp; &nbsp;  &nbsp; &nbsp;  1、在open suse12的系统上安装部署过。(备注：docker官方只提供了在suse12上的docker ee的版本，没有docker ce的版本，虽然suse的仓库中存在docker的rpm安装包，博主也下载了，但实际安装时并未安装成功，而采用的二进制docker包部署上了)
&nbsp; &nbsp;  &nbsp; &nbsp;  2、在没有外网的环境中并且没有yum源时，部署过。
&nbsp; &nbsp;  &nbsp; &nbsp;  3、这篇文章从开始编写，到发布，拖得太久了，貌似脱了有一个月的时间，以后有文章最多不超过3天写完，导致要写的好多东西都忘记了。

## 二、二进制安装包的下载
&nbsp; &nbsp;  首先打开 https://www.docker.com/ 

![docker的主页](https://upload-images.jianshu.io/upload_images/7906353-d93401c76fe3bb6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/740)

![docker的文档](https://upload-images.jianshu.io/upload_images/7906353-8cf66a3540a281c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/740)

![docker二进制链接的获取](https://upload-images.jianshu.io/upload_images/7906353-0428c57fed5b264e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/740)

&nbsp; &nbsp;  &nbsp; &nbsp;  **docker二进制文件的下载链接： [https://download.docker.com/linux/static/stable/](https://download.docker.com/linux/static/stable/)**

## 三、二进制安装包的安装步骤
&nbsp; &nbsp;  模拟一个局域网，信息中心要求把相关的软件包和数据放到固定的路径的一个环境。
&nbsp; &nbsp;  安装非常的简单，就是把二进制的可执行包放到被要求的固定的路径下，然后将此路径加到系统的环境变量中，然后自己创建docker.service和daemon.json两个配置文件。
&nbsp; &nbsp;  **实际的部署过程**
&nbsp; &nbsp;  &nbsp; &nbsp;&nbsp; &nbsp;在这里将二进制的包放到/opt/docker路径下，数据放到/data/docker    
&nbsp; &nbsp;  **直接上操作步骤**

```
[root@tag home]# ls
docker-18.06.0-ce.tgz
[root@tag home]# tar -zxvf docker-18.06.0-ce.tgz -C /opt/
docker/
docker/dockerd
docker/docker-proxy
docker/docker-containerd
docker/docker-runc
docker/docker-init
docker/docker-containerd-shim
docker/docker
docker/docker-containerd-ctr
[root@tag home]# 
[root@tag home]# ls /opt/docker/
docker  docker-containerd  docker-containerd-ctr  docker-containerd-shim  dockerd  docker-init  docker-proxy  docker-runc 
[root@tag home]# 
[root@tag home]# cat > /etc/systemd/system/docker.service <<"EOF"
[Unit]
Description=Docker Application Container Engine
Documentation=http://docs.docker.io
 
[Service]
Environment="PATH=/opt/docker:/bin:/sbin:/usr/bin:/usr/sbin"
EnvironmentFile=-/run/flannel/docker
ExecStart=/opt/docker/dockerd \
         --graph /data/docker                          ###备注：这里就是docker数据存储的目录设置
         --log-level=error \
          $DOCKER_NETWORK_OPTIONS
ExecReload=/bin/kill -s HUP $MAINPID
Restart=on-failure
RestartSec=5
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
Delegate=yes
KillMode=process
 
[Install]
WantedBy=multi-user.target
EOF
[root@tag home]# 
[root@tag home]# mkdir -p  /etc/docker/
[root@tag home]# cat > /etc/docker/daemon.json <<EOF        #备注：外网拉取镜像时可用
{
    "registry-mirrors": ["https://hub-mirror.c.163.com", "https://docker.mirrors.ustc.edu.cn"],
    "max-concurrent-downloads": 20
}
EOF
[root@tag home]#  cat /etc/docker/daemon.json              #备注：使用内网仓库时可用
{
    "insecure-registries":["https://内网镜像仓库ip:5000"]
}
[root@tag home]#  
[root@tag home]#  systemctl daemon-reload
[root@tag home]#  systemctl enable docker 
[root@tag home]#  systemctl restart docker
[root@tag home]#  systemctl status docker
[root@tag home]#  ip a                                 #备注：查看是否多了一张docker0的网卡
```

## 四、小tips
####&nbsp; &nbsp;  **1、docker默认网卡网段的修改**
&nbsp; &nbsp;  **``这里提供两种修改方法：一种是新建一个网桥；另一种是直接修改docker0网卡的网段``**
#####&nbsp; &nbsp; 第一种：新建一个网桥

```
[root@tag home]#  
[root@tag home]#  systemctl stop docker
[root@tag home]#  iptables -t nat -F POSTROUTING            #查看一下防火墙的nat
[root@tag home]#  ip link set dev docker0 down                    # 删除docker默认网关配置
[root@tag home]#  ip addr del 172.17.0.1/16 dev docker0
[root@tag home]#  yum install -y bridge-utils                   #新建一张网卡
[root@tag home]#  brctl addbr bridge1
[root@tag home]#  ip addr add 172.18.2.1/24 dev bridge1       # 增加新的docker网关配置
[root@tag home]#  ip link set dev bridge1 up
[root@tag home]#  ip addr show bridge1
[root@tag home]#
[root@tag home]# cat > /etc/docker/docker-daemon.json <<EOF       #这里你也可以将上面的仓库地址加上
{
  "bridge": "bridge1"
}
EOF
[root@tag home]# 
[root@tag home]#  systemctl daemon-reload
[root@tag home]#  systemctl enable docker 
[root@tag home]#  systemctl restart docker
[root@tag home]#  systemctl status docker
[root@tag home]#  
```
#####&nbsp; &nbsp; 第二种：直接修改docker0网卡的网段
```
[root@tag home]#
[root@tag home]# cat > /etc/docker/daemon.json <<EOF       #这里你也可以将上面的仓库地址加上
{
    "bip": "179.2.0.1/16"
}
EOF
[root@tag home]# 
[root@tag home]#  systemctl daemon-reload
[root@tag home]#  systemctl enable docker 
[root@tag home]#  systemctl restart docker
[root@tag home]#  systemctl status docker
[root@tag home]#  
[root@tag home]#  iptables -t nat -L -n
[root@tag home]#  iptables -t nat -F POSTROUTING
[root@tag home]#  
```
####&nbsp; &nbsp;  **2、docker版本13和18的一个差别----文件挂载**
&nbsp; &nbsp;  这也是今天去客户那里安装才发现的。在把宿主机的目录挂载到容器内时，使用docker run方式启动容器时，需要添加参数docker run -itd --privileged ...，使用docker-compose方式启动时，需要添加参数privileged: true。
忘记要写什么了，记得时候再来补充

