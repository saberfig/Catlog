## systemctl命令

## centOS安装后没有桌面的问题
安装GNOME桌面
* yum groupinstall "GNOME Desktop"
安装成功后，执行下面的命令进入桌面
* start x
此时开机时还是默认进入命令界面，需要进行配置
* systemctl set-default grahical.target #默认进入桌面
* systemctl set-default multi-user.target #默认进入命令行界面
相关配置文件在 /etc/systemd/system/default.target中

## centOS查看网卡信息
* ip addr

## Linux分配IP地址
* dhclient

## centOS配置静态IP地址
配置网卡文件
* vi /etc/sysconfig/network-scripts/ifcfg-ens33
* BOOTPROTO=static
* ONBOOT=yes
* IPADDR=192.168.31.100
* NETMASK=255.255.255.0
* GATEWAY=192.168.31.1
* DNS=119.29.29.29
* systemctl restart network.service #重启网络

