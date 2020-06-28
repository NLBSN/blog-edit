---
title: "博客安装的简单记录"
date: 2020-07-05 11:00:00
toc: true
tags:
  - 78
categories:
  - 78
thumbnailImagePosition: left
thumbnailImage: https://gitee.com/zzf35/cloudimg/raw/master/img/20200628112608.jpg
---

博客安装的简单思路。

<!--more-->

# 背景

# 环境

- hugo
- travis
- PicGo
- typora

# hugo

​    hugo使用golang进行开发的，直接一个可执行的二进制文件就可以运行，安装简单快速。



​    用helm快速生成博客所需要的模板，然后直接从github上面下载自己喜欢的主题，放到themes文件夹下面，就可以使用了



​    hugo常用命令：

```
hugo new site blog-name
hugo server
```

# travis

​    自动编译发布博客。    

​    在本地或者云端编写完markdown文档，travis会自己去github上面拉取源码，然后执行相对应的编译，发布等操作。    

​    部署：

- 先去github上面申请密钥
- https://travis-ci.org/网站上面进行相关的设置
- 在博客的源码目录下放置.travis.yml文件，配置各项参数



# PIcGo

一款图床的软件。主要用来将图片方便的上传到github，gitee上面，然后markdown文档里直接引用图片的链接。

下载地址：https://github.com/Molunerfinn/PicGo



# typora

文本编辑工具。





