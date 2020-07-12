---
title: "go-astiencoder简单使用记录"
date: 2020-06-29 12:00:00
categories:
  - golang
tags:
  - golang
  - cgo
  - ffmpeg
thumbnailImagePosition: left
thumbnailImage: https://gitee.com/zzf35/cloudimg/raw/master/img/20200622200041.jpg
---

​    使用ffmpeg的第三方视频编解码封装库：go-astiencoder

<!--more-->

​    [go-astiencoder](https://github.com/asticode/go-astiencoder)是用go编写的，并基于ffmpeg c绑定的开源的视频编码器。



# 使用步骤：

- 克隆go-astiencoder在github的源代码
- 下载arch linux上编译时所需要的依赖
- 编译相对应的ffmpeg的源代码
- 编译go-astiencoder的二进制可执行程序
- 测试

```shell
go get github.com/asticode/go-astiencoder/...

git clone https://gitee.com/zzf35/go-astiencoder.git

cd go-astiencoder

yay -S make gcc nasm yasm pkg-config

make install-ffmpeg

make build

make server

# 官方文档
sudo apt-get -y install autoconf automake build-essential libass-dev libfreetype6-dev libsdl1.2-dev libtheora-dev libtool libva-dev libvdpau-dev libvorbis-dev libxcb1-dev libxcb-shm0-dev libxcb-xfixes0-dev pkg-config texi2html zlib1g-dev

sudo apt install -y libavdevice-dev libavfilter-dev libswscale-dev libavcodec-dev libavformat-dev libswresample-dev libavutil-dev

sudo apt-get install yasm

export FFMPEG_ROOT=$HOME/ffmpeg
export CGO_LDFLAGS="-L$FFMPEG_ROOT/lib/ -lavcodec -lavformat -lavutil -lswscale -lswresample -lavdevice -lavfilter"
export CGO_CFLAGS="-I$FFMPEG_ROOT/include"
export LD_LIBRARY_PATH=$HOME/ffmpeg/lib



实际执行:
apt update
git clone https://gitee.com/zzf35/go-astiencoder.git
cd go-astiencoder/
vim Makefile
make install-ffmpeg
apt install -y nasm yasm
make build
make server


apt install ffmpeg










```



