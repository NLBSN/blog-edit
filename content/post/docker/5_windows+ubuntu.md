---
title: "windows自带的linux子系统进行安装docker"
date: 2020-06-23
categories:
  - 容器
  - docker
tags:
  - 容器
  - docker
  - windows
thumbnailImagePosition: left
thumbnailImage: https://gitee.com/zzf35/cloudimg/raw/master/img/20200622193715.jpg

---

使用windows商店提供的ubuntu内置系统进行安装docker

<!--more-->

<!-- toc -->

> 主要分为几个步骤
>
> 1. 调整windows10的设置，运行内置的linux子系统
> 2. 从微软商店下载ubuntu子系统
> 3. ubuntu安装docker
> 4. 测试

# 一、windows设置更改

1. 将linux子系统的设置打开
   在如下图的位置，如果不知道，请自行百度。Telnet客户端也打开，方便之后做一些端口的排错。

   ![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200624202205.png)

   

2. 直接阅读官方文档

   - [适用于 Linux 的 Windows 子系统安装指南 (Windows 10)](https://docs.microsoft.com/zh-cn/windows/wsl/install-win10)

   - [更新 WSL 2 Linux 内核](https://docs.microsoft.com/zh-cn/windows/wsl/wsl2-kernel)
     我写这篇文章的时候，官方要求的最新的是
     ![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200624201611.png)

3. 安装ubuntu子系统
   推荐选择我图中所示的，当然了，你选择其他的也可以，无非就是试试错

   ![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200624202653.png)
   安装完毕之后，会在这里可以找得到：

   ![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200624202916.png)
   点击，打开即可

4. 在ubuntu中运行docker网上有很多的关于先下载windows版docker，利用它的服务，然后在ubuntu中执行命令的教程，看的我很是头大，哈哈。
   **这里给一个直接在ubuntu中安装docker的过程**
   注：自己的安装过程有点忘记了，这里放出了docker官方的---（到2020年6月25日左右，微软官方最新的linux内置子系统ubuntu是直接支持docker安装的，下面写的那个自己参考的博客应该是没啥用，但我懒得折腾，就不去重新去做试验了）
```
   $ sudo apt-get remove docker docker-engine docker.io containerd runc
   $ sudo apt-get update
   $ sudo apt-get install \
       apt-transport-https \
       ca-certificates \
       curl \
       gnupg-agent \
       software-properties-common
   $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   $ sudo apt-key fingerprint 0EBFCD88
   $ sudo add-apt-repository \
      "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) \
      stable"
   $ sudo apt-get update
   $ sudo apt-get install docker-ce docker-ce-cli containerd.io
```

自己的参考的是这个：

```
   $ wget https://download.docker.com/linux/ubuntu/dists/xenial/pool/stable/amd64/docker-ce_17.09.0~ce-0~ubuntu_amd64.deb -P /tmp/
   $ sudo dpkg -i /tmp/docker-ce_17.09.0~ce-0~ubuntu_amd64.deb
   $ sudo apt -y -f install
   $ sudo usermod -aG docker $USER
   $ sudo apt -y install cgroupfs-mount
   $ sudo cgroupfs-mount
   $ sudo service docker start
   $ sudo docker run --rm hello-world
```

参考自：https://blog.51cto.com/juestnow/2418932

5. 测试
    查看ubuntu的ip地址
   ![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200624205821.png)

   ```
   $ sudo docker run -p 8080:80 nginx
   ```

   本机浏览器访问：

   ```
   http://172.29.157.192:8080/
   ```

   ![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200624205925.png)
   
   **但是     但是     但是**       你会发现也是可以访问的。哈哈 自己去解密把
   
   ```
   http://localhost:8080/
   ```
   
   ![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200625204310.png)
   
   大功告成~~~

