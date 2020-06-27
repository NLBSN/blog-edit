---
title: "Linux系统双网卡绑定配置"
date: 2020-06-27 11:00:00
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

Linux 系统双网卡绑定配置

<!--more-->

**系统版本**

```
[root@ ~]# cat /etc/redhat-release 
CentOS release 6.8 (Final)
[root@ ~]# uname -r
2.6.32-642.6.1.el6.x86_64
```

**网卡说明**

> eth0   192.168.1.8（服务器外网卡）
> eth1
> eth2
> 两块服务器网卡(内网)

**关闭防火墙**

```
[root@ ~]# /etc/init.d/iptables stop
[root@ ~]#  chkconfig iptables off
```


**关闭selinux**

```
[root@ ~]#setenforce 0  
[root@ ~]#sed -i ‘s/SELINUX=enforcing/SELINUX=disabled/‘ /etc/selinux/config
```


**禁用NetworkManager**

```
[root@ ~]# /etc/init.d/NetworkManager stop
Stopping NetworkManager daemon:           [  OK  ]
[root@ ~]# chkconfig NetworkManager off
[root@ ~]# /etc/init.d/network restart
```

**编辑eth1网卡**

```
[root@ ~]# cd /etc/sysconfig/network-scripts/
[root@ network-scripts\]# cat >ifcfg-eth1 <<EOF
DEVICE=eth1
ONBOOT=yes
BOOTPROTO=none
USERCTL=no
MASTER=bind0
EOF
```


**编辑eth2网卡**

```
[root@ network-scripts]# cat >ifcfg-eth2 <<EOF        
DEVICE=eth2
ONBOOT=yes
BOOTPROTO=none
USERCTL=no
MASTER=bind0
EOF
```

**编辑bind0网卡**

```
[root@ network-scripts]# cat >ifcfg-bind0 <<EOF
DEVICE=bind0
TYPE=Ethernet
ONBOOT=yes
BOOTPROTO=none
IPADDR=10.0.0.8
NETMASK=255.255.255.0
GATEWAY=10.0.0.254
IPV6INIT=no
USERCTL=no
EOF
```

**配置bond参数**

```
[root@ network-scripts]# cat >/etc/modprobe.conf <<EOF
alias bind0 bonding
options bind0 miimon=100 mode=6
EOF
```


加入开机自启动(/etc/rc.local)

```
[root@ network-scripts]# cat >>/etc/rc.local <<EOF
ifenslave bind0 eth1 eth2
EOF
```


重启网络服务

```
[root@LVS-2 network-scripts]# service network restart

Shutting down interface eth0:                              [  OK  ]
Shutting down interface eth1:                              [  OK  ]
Shutting down interface eth2:                              [  OK  ]
Shutting down loopback interface:                       [  OK  ]
Bringing up loopback interface:                            [  OK  ]
Bringing up interface bind0:  WARNING: Deprecated config file /etc/modprobe.conf, all config files belong into /etc/modprobe.d/.
WARNING: Deprecated config file /etc/modprobe.conf, all config files belong into /etc/modprobe.d/.
Determining if ip address 10.0.0.8 is already in use for device bind0..[  OK  ]
Bringing up interface eth0:  Determining if ip address 192.168.1.8 is already in use for device eth0...                                                    [  OK  ]
Bringing up interface eth1:  RTNETLINK answers: File exists         [  OK  ]
Bringing up interface eth2:  RTNETLINK answers: File exists  [  OK  ]
```


配置使绑定立即生效

```
[root@LVS-2 network-scripts]# ifenslave bind0 eth1 eth2
```


测试联通

```
[root@LVS-2 network-scripts]# ping 10.0.0.8

PING 10.0.0.8 (10.0.0.8) 56(84) bytes of data.

64 bytes from 10.0.0.8: icmp_seq=1 ttl=64 time=0.089 ms

64 bytes from 10.0.0.8: icmp_seq=2 ttl=64 time=0.046 ms

^C

--- 10.0.0.8 ping statistics ---

2 packets transmitted, 2 received, 0% packet loss, time 1921ms

rtt min/avg/max/mdev = 0.046/0.067/0.089/0.023 ms
```


此时会发现系统多一个网卡

```
[root@LVS-2 network-scripts]# ifconfig bind0

bind0 

Link encap:Ethernet  HWaddr 00:0C:29:CC:9B:5 

inet addr:10.0.0.8  Bcast:10.0.0.255  Mask:255.255.255.0

inet6 addr: fe80::20c:29ff:fecc:9b55/64 Scope:LinkUP BROADCAST RUNNING MASTER MULTICAST  MTU:1500 Metric:1

RX packets:151 errors:0 dropped:0 overruns:0 frame:0

TX packets:3 errors:0 dropped:0 overruns:0 carrier:0

collisions:0 txqueuelen:0 

RX bytes:11826 (11.5 KiB)  TX bytes:258 (258.0 b)
```

> 如有错误或其它问题，欢迎小伙伴留言评论、指正。如有帮助，欢迎点赞+转发分享。
>
> 欢迎大家关注民工哥的公众号：民工哥技术之路
>
> 转载自：https://segmentfault.com/a/1190000023033452
>
> 本作品系 原创 ， [采用《署名-非商业性使用-禁止演绎 4.0 国际》许可协议](https://creativecommons.org/licenses/by-nc-nd/4.0/)

