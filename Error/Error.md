# 4.1.3 配置网卡信息

## 问题描述

使用 `systemctl restart network` 后出现：

> [root@linuxprobe ~]# Restarting network (via systemctl): Job for network.service failed because the control process exited with error code.
> [root@linuxprobe ~]# See "systemctl status network.service" and "journalctl -xe" for details.

## 错误分析

配置文件的 MAC 地址与本机 MAC 地址不匹配

## 解决方法

### 获取本机 MAC 地址

`ip addr`查看 MAC 地址，可知本机 MAC 地址为 **00:16:3e:0e:6c:bf**

> [root@linuxprobe ~]# ip addr
> 1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
>     link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
>     inet 127.0.0.1/8 scope host lo
>        valid_lft forever preferred_lft forever
> 2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
>     link/ether 00:16:3e:0e:6c:bf brd ff:ff:ff:ff:ff:ff
>     inet 192.168.10.10/24 brd 192.168.10.255 scope global eth0
>        valid_lft forever preferred_lft forever
>     inet 172.17.3.183/18 brd 172.17.63.255 scope global dynamic eth0
>        valid_lft 315358909sec preferred_lft 315358909sec

### 编辑ifcfg-eno16777736

使用 `vim /etc/sysconfig/network-scripts/ifcfg-eno16777736` 编辑 ifcfg-eno16777736，将 HWADDR（如果没有就添加上）改成本机MAC地址，即 **00:16:3e:0e:6c:bf** ，编辑完毕后的 ifcfg-eno16777736 如下：

```
TYPE=Ethernet
BOOTPROTO=static
NAME=eno16777736
ONBOOT=yes
IPADDR=192.168.10.10
NETMASK=255.255.255.0
GATEWAY=192.168.10.1
DNS1=192.168.10.1
HWADDR=00:16:3e:0e:6c:bf
```

## 参考

[Job for network.service failed because the control process exited with error code问题](https://blog.csdn.net/dongfei2033/article/details/81124465)