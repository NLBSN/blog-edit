---
title: "Linux下ffmpeg调用gpu计算的方案"
date: 2020-07-09 11:00:00
toc: true
tags:
  - linux
  - centos
categories:
  - linux
  - centos
thumbnailImagePosition: left
thumbnailImage: https://gitee.com/zzf35/cloudimg/raw/master/img/20200628112608.jpg

---

Linux平台显卡驱动的安装思路，以及调用ffmpeg时的一些思路。

<!--more-->

[TOC]

# 背景

​		服务器的使用环境是完全的离线环境，为了减少错误情况的发生，选择docker容器进行大部分安装。



​		在正常情况下，要通过ffmpeg调用gpu的的显卡进行转码计算等操作，需要在安装完显卡驱动后重新去编译ffmpeg的源码才可以进行调用。但是使用docker容器进行调用就没有这个限制。



​		文章将分一下几个步骤进行：

- 查询自己的显卡是否支持硬件编解码

- nvidia显卡驱动安装
- docker容器安装
- nvidia-docker插件安装
- DCGM监控显卡信息(了解即可)
- ffmpeg带nvidia驱动的容器调用



# 一、查询自己的显卡是否支持硬件编解码(如不支持，那么后面的就别看了)

查询自己的显卡是否支持gpu解码：[nvidia官网查询 链接](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix)

*查询发现我的mx150编解码都不支持，有点郁闷，但还是会把后面的过程走完。之后有了新的显卡，再去做文档修改*

**这个表格是对GPU硬件编码的支持列表**

![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200712211841.png)

**如果上面展开的列表中没有你的显卡，别急，在这个表格的后面有这么三个按钮，是对这个表格的补充，点击一下，又会展开一个列表，去这里寻找你的显卡型号吧**

![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200712211851.png)

**这个表格是对GPU硬件解码的支持列表**

![img](https://gitee.com/zzf35/cloudimg/raw/master/img/20200712211856.png)

备注：如果找不到你的显卡型号，也和上面一样的操作



# 二、nvidia显卡驱动安装

​		显卡驱动的安装大致分别以下几个步骤（具体版本的驱动安装再去查找相关的文档）：

- 确认显卡型号，下载对应的驱动
- 删除nvidia驱动，禁用自带的nouveau nvidia驱动，重启系统
- 安装下载的驱动
- 测试是否安装成功nvidia-smi/nvidia-settings

## 确认显卡型号，下载对应的驱动

下载对应的显卡驱动https://www.nvidia.cn/Download/index.aspx

## 删除nvidia驱动，禁用自带的nouveau nvidia驱动

参考方案：来自：https://github.com/dglt1/optimus-switch-gdm

```bash
#!/bin/sh

if [ "$(id -u)" != "0" ]; then
   echo "This script must be run as root" 1>&2
   exit 1
fi

####################################
# custom install script for GDM    #
# and following GPU BusID's        #
# intel iGPU BusID  00:02:0        #
# nvidia dGPU BusID  01:00:0       #
# chmod +x install.sh  first!      #
####################################

echo '##################################################################'
echo '# be sure you have all requirements BEFORE running this script  ##'
echo '# linux*-headers acpi_call-dkms xf86-video-intel git xorg-xrandr##'
echo '# ****installing in 5 sec... CTRL+C to abort****                ##'
echo '##################################################################'
sleep 6
echo ' '
echo '##################################################################'
echo '#errors about removing files can be ignored, i wrote this script##'
echo '#with the most common files in mind, you will not have all of   ##'
echo '#them, this is ok!                                              ##'
echo '##################################################################'
echo '## IF YOU HAVE ERRORS ABOUT COPYING FILES, SOMETHING IS WRONG   ##'
echo '## MAKE SURE THIS IS RUN WITH SUDO AND FROM DIRECTORY           ##'
echo '## /home/$USER/optimus-switch-gdm/  (this is very important!!!) ##'
echo '##################################################################'
sleep 5

echo ' '
echo 'Removing current nvidia prime setup if applicable......'
rm -rf /etc/X11/mhwd.d/nvidia.conf
rm -rf /etc/X11/mhwd.d/nvidia.conf.nvidia-xconfig-original
echo 'rm -rf /etc/X11/mhwd.d/nvidia.conf*'
rm -rf /etc/X11/xorg.conf.d/90-mhwd.conf
rm -rf /etc/X11/xorg.conf.d/nvidia.conf
rm -rf /etc/X11/xorg.conf.d/optimus.conf
rm -rf /etc/X11/xorg.conf.d/xorg.conf
rm -rf /etc/X11/xorg.conf.d/20-intel.conf
echo 'rm -rf /etc/X11/xorg.conf.d/90-mhwd.conf'
rm -rf /etc/modprobe.d/mhwd-gpu.conf
rm -rf /etc/modprobe.d/optimus.conf
rm -rf /etc/modprobe.d/nvidia.conf
echo 'rm -rf /etc/modprobe.d/mhwd-gpu.conf'
rm -rf /etc/modprobe.d/nvidia-drm.conf
rm -rf /etc/modprobe.d/nvidia.conf
echo 'rm -rf /etc/modprobe.d/nvidia*.conf'
rm -rf /etc/modules-load.d/mhwd-gpu.conf
echo 'rm -rf /etc/modules-load.d/mhwd-gpu.conf'
rm -rf /usr/local/bin/optimus.sh
rm -rf /usr/local/share/optimus.desktop
echo 'rm -rf /usr/local/share/optimus.desktop'
sleep 2

echo 'Copying contents of ~/optimus-switch-gdm/* to /etc/ .......'
mkdir /etc/switch/
cp -r * /etc/

sleep 2
echo 'Copying set-intel.sh and set-nvidia.sh to /usr/local/bin/'

cp /etc/switch/set-intel.sh /usr/local/bin/set-intel.sh

cp /etc/switch/set-nvidia.sh /usr/local/bin/set-nvidia.sh

cp /etc/switch/optimus.desktop /usr/local/share/optimus.desktop

sleep 1
echo 'Copying disable-nvidia.service to /etc/systemd/system/'
cp /etc/switch/intel/disable-nvidia.service /etc/systemd/system/disable-nvidia.service
chown root:root /etc/systemd/system/disable-nvidia.service
chmod 644 /etc/systemd/system/disable-nvidia.service

sleep 1
echo 'Creating symlinks ("file exists" errors can be ignored)....... '
ln -s /usr/local/share/optimus.desktop /usr/share/gdm/greeter/autostart/optimus.desktop
ln -s /usr/local/share/optimus.desktop /etc/xdg/autostart/optimus.desktop

sleep 1
echo 'Setting nvidia prime mode (sudo set-nvidia.sh).......'

cp /etc/switch/nvidia/nvidia-xorg.conf /etc/X11/xorg.conf.d/99-nvidia.conf
cp /etc/switch/nvidia/nvidia-modprobe.conf /etc/modprobe.d/99-nvidia.conf
cp /etc/switch/nvidia/nvidia-modules.conf /etc/modules-load.d/99-nvidia.conf
cp /etc/switch/nvidia/optimus.sh /usr/local/bin/optimus.sh

sleep 1
echo 'Setting permissions........'
chmod +x /usr/local/bin/set-intel.sh
chmod +x /usr/local/bin/set-nvidia.sh
chmod a+rx /usr/local/bin/optimus.sh
chmod a+rx /etc/switch/intel/no-optimus.sh
chmod a+rx /etc/switch/nvidia/optimus.sh
chmod +x /etc/switch/gpu_switch_check.sh
sleep 1
echo 'Currently boot mode is set to nvidia prime.'
echo 'you can switch to intel only mode with sudo set-intel.sh and reboot.'
sleep 1
echo 'Install finished!'
```



## 安装下载的驱动

研究后面的参数代表什么意思

```
./NVIDIA-Linux-x86_64-450.57.run --no-opengl-files --no-x-check --no-nouveau-check
```

## 测试是否安装成功nvidia-smi/nvidia-settings

**执行*nvidia-smi*命令，如果出现相关显卡的信息，则安装成功。**

```
~ >>> nvidia-smi                                                                                                                                              
Sat Jul 11 16:53:01 2020       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 440.82       Driver Version: 440.82       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce MX150       Off  | 00000000:01:00.0 Off |                  N/A |
| N/A   43C    P0    N/A /  N/A |      0MiB /  2002MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```

第一行：

- 版本信息

第二行：

- GPU：指第几块GPU显卡
- Fan：风扇转速
- NAME：GPU型号
- Temp：GPU温度
- Perf：GPU功率
- Persistence-M：是否持久化开启

备注：如有需要请自行百度



# 三、docker容器安装

直接二进制安装，但是记得安装相关的依赖包(直接挂镜像进行依赖包的安装)，参考：[docker容器安装部署](https://nlbsn.github.io/2020/06/docker%E5%AE%B9%E5%99%A8%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2/)

docker二进制版本下载地址：https://download.docker.com/linux/static/stable/



# 四、nvidia-docker插件安装

关于这一块的内容网上很多，但大部分都已经过时了，或者说是官方对这一块的改动太大。

2020年07月09日写本文章时，官方文档要求docker19.03的版本；原来官方的1版本和2版本都已经不适用了，所以最好还是自己看文档去了解。

## 官方文档的快速入门安装

### Ubuntu 16.04/18.04/20.04, Debian Jessie/Stretch/Buster

```
# Add the package repositories
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
sudo systemctl restart docker
```

### CentOS 7.X/8.X (docker-ce), RHEL 7.X/8.X (docker-ce), Amazon Linux 1/2

```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.repo | sudo tee /etc/yum.repos.d/nvidia-docker.repo

sudo yum install -y nvidia-container-toolkit
sudo systemctl restart docker
```



备注：cuda，cudnn等这些提供计算能力的框架/软件，由于在使用nvidia官方提供的最基础驱动镜像时已经安装，所以在物理机上并不需要再去安装这一块内容。

## nvidia官方文档提供的容器测试方法

```
#### Test nvidia-smi with the latest official CUDA image
docker run --gpus all nvidia/cuda:10.0-base nvidia-smi

# Start a GPU enabled container on two GPUs
docker run --gpus 2 nvidia/cuda:10.0-base nvidia-smi

# Starting a GPU enabled container on specific GPUs
docker run --gpus '"device=1,2"' nvidia/cuda:10.0-base nvidia-smi
docker run --gpus '"device=UUID-ABCDEF,1"' nvidia/cuda:10.0-base nvidia-smi

# Specifying a capability (graphics, compute, ...) for my container
# Note this is rarely if ever used this way
docker run --gpus all,capabilities=utility nvidia/cuda:10.0-base nvidia-smi
```



# 五、DCGM监控显卡信息(了解即可)

先把官方地址附上：https://github.com/NVIDIA/gpu-monitoring-tools         ---备注：记得原来还有一个单独的dcgm-exporter的仓库来着，但找了半天没找到



执行命令：

```bash
$ docker run -d --gpus all --rm -p 9400:9400 nvidia/dcgm-exporter:latest
$ curl localhost:9400/metrics
# HELP DCGM_FI_DEV_SM_CLOCK SM clock frequency (in MHz).
# TYPE DCGM_FI_DEV_SM_CLOCK gauge
# HELP DCGM_FI_DEV_MEM_CLOCK Memory clock frequency (in MHz).
# TYPE DCGM_FI_DEV_MEM_CLOCK gauge
# HELP DCGM_FI_DEV_MEMORY_TEMP Memory temperature (in C).
# TYPE DCGM_FI_DEV_MEMORY_TEMP gauge
...
DCGM_FI_DEV_SM_CLOCK{gpu="0", UUID="GPU-604ac76c-d9cf-fef3-62e9-d92044ab6e52"} 139
DCGM_FI_DEV_MEM_CLOCK{gpu="0", UUID="GPU-604ac76c-d9cf-fef3-62e9-d92044ab6e52"} 405
DCGM_FI_DEV_MEMORY_TEMP{gpu="0", UUID="GPU-604ac76c-d9cf-fef3-62e9-d92044ab6e52"} 9223372036854775794
...
```

备注：目前这个时间点，docker-compose还不支持gpu的调用参数，所以暂时只能用命令的形式。



# 六、ffmpeg带nvidia驱动的容器调用

https://hub.docker.com上面有提供的镜像，直接选取带显卡驱动的进行使用即可。

```
docker run -d --gpus all --rm --entrypoint='tail' jrottenberg/ffmpeg:4.1-nvidia -f /dev/null
```

通过ffprobe命令识别并输出视频信息

```
ffprobe -v error -show_streams -print_format json <input>
ffprobe -v error -show_streams -print_format json http://192.168.8.153:8888/4K-T-ara-Number9.mp4
```

可以看到一共返回了4个流，其中第0个是视频流，1是音频流，2和3是附加数据，没什么用
如果想指定分析视频流或音频流的话，可以加上参数-show_streams -v或-show_streams -a，这样就会只输出视频/音频流的分析结果。[参考链接](https://www.jianshu.com/p/59da3d350488)

```
ffmpeg -hwaccel cuvid -hwaccel_device 0 -c:v h264_cuvid -i <input> -c:v h264_nvenc -b:v 2048k -vf scale_npp=1280:-1 -y <output>

ffmpeg -hwaccel cuvid -hwaccel_device 1 -c:v h264_cuvid -i <input> -c:v h264_nvenc -b:v 2048k -vf scale_npp=1280:-1 -y <output>
```



## 解码经过系统内存

```bash
ffmpeg -c:v h264_cuvid -i input output.mkv
```

这种情况，解码器会将解码后的数据拷贝到系统内存。



## 完全硬件转码

```bash
ffmpeg -hwaccel cuvid -c:v h264_cuvid -i input -c:v h264_nvenc -preset slow output.mkv
```

加了 -hwaccel cuvid之后，这种情况完全通过显卡GPU完成。



## 部分硬件转码(好像是抄的官方的)

```
ffmpeg -c:v h264_cuvid -i input -c:v h264_nvenc -preset slow output.mkv
```

编码：

```bash
ffmpeg -h encoder=h264_nvenc
ffmpeg -h encoder=hevc_nvenc
ffmpeg -i input -c:v h264_nvenc -profile high444p -pixel_format yuv444p -preset default output.mp4
```

解码：

```
ffmpeg -hwaccel cuda -i input output

ffmpeg -c:v h264_cuvid -i input output

ffmpeg -hwaccel cuda -hwaccel_output_format cuda -i input -c:v h264_nvenc -preset slow output

ffmpeg -hwaccel_device 0 -hwaccel cuda -i input -vf scale_npp=-1:720 -c:v h264_nvenc -preset slow output.mkv
```

官方链接：https://trac.ffmpeg.org/wiki/HWAccelIntro

































