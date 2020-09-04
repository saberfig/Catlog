
* [Linux命令](#Linux命令)
    * [systemctl命令](#systemctl命令)
    * [ip&ifconfig命令](#ip&ifconfig命令)
    * [dhclient命令](#dhclient命令)
    * [grep命令](#grep命令)
    * [防火墙相关命令](#防火墙相关命令)
    * [端口相关命令](#端口相关命令)
* [centOS安装后没有桌面的问题](#centOS安装后没有桌面的问题)
* [Linux配置静态IP地址](#Linux配置静态IP地址)
* [centOS安装MySQL5.7](#centOS安装MySQL5.7)



# Linux命令
## systemctl命令

## ip&ifconfig命令
```shell
shell> ip addr
```

## dhclient命令
```shell
shell> dhclient
```

## grep命令
## 防火墙相关命令
```shell
shell> service iptables status #查看防火墙状态   
shell>service iptables stop|start # 即时生效，重启后复原
shell>chkconfig iptables on|off # 永久性生效，重启后不会复原  
```
## 端口相关命令
```shell
shell> netstat -tlunp # 检查是否开启端口
shell> firewall-cmd --zone=public --add-port=3306/tcp --permanent # 开启端口
shell> firewall-cmd --reload


# centOS安装后没有桌面的问题
安装GNOME桌面
```shell
shell> yum groupinstall "GNOME Desktop"
```

安装成功后，执行下面的命令进入桌面
```shell
shell> start x
```

此时开机时还是默认进入命令界面，需要进行配置
```shell
shell> systemctl set-default grahical.target #默认进入桌面  
shell> systemctl set-default multi-user.target #默认进入命令行界面
```

相关配置文件在 /etc/systemd/system/default.target中





# Linux配置静态IP地址
通过命令自动分配IP地址
```shell
shell> dhclient
```
配置网卡文件，固定IP地址
```shell
shell> vi /etc/sysconfig/network-scripts/ifcfg-ens33

BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.31.100
NETMASK=255.255.255.0
GATEWAY=192.168.31.1
DNS=119.29.29.29

shell> systemctl restart network.service #重启网络
```

# centOS安装MySQL5.7
> 摘抄自https://www.jianshu.com/p/1dab9a4d0d5f

## 配置yum源
安装MySQL源
```shell
shell> wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm #下载  
shell> yum localinstall mysql57-community-release-el7-11.noarch.rpm # 安装MySQL源
```
用下面的命令检查MySQL源是否安装成功
```shell
shell> yum repolist enabled | grep "mysql.*-community.*"
```
## 安装MySQL
```shell
shell> yum install -y mysql-community-server
```
## 启动MySQL服务
```shell
shell> systemctl start mysqld
shell> systemctl status mysqld # 查看MySQL状态
```
## 设置开机启动
```shell
shell> systemctl enable mysqld
shell> systemctl daemon-reload #重载所有修改过的配置文件
```
## 修改root本地账户密码
MySQL安装完成后，默认密码在/var/log/mysqld.log文件中
```shell
shell> grep 'temporary password' /var/log/mysqld.log # 查看生成的密码
```
修改密码
```shell
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass!'; 
或者
mysql> set password for 'root'@'localhost'=password('MyNewPass!'); # 其他修改密码方式可看数据库部分
```
> mysql5.7默认安装了密码安全检查插件(validate_password)，默认密码检查策略要求密码必须包含：大小写字母，数字和特殊符号，并且长度不能少于8位

## 添加远程登录用户
默认只允许root账户在本地登录，如果要在其他机器上连接MySQL，必须<b>添加一个远程连接的账户</b>或者<s>修改root为允许远程连接</s>不推荐

添加一个允许远程连接的账户
```shell
mysql> GRANT ALL PRIVILEGES ON *.* TO 'zhangsan'@'%' IDENTIFIED BY 'Zhangsan2018!' WITH GRANT OPTION;
```
修改root为允许远程连接
```shell
mysql> use mysql;
mysql> UPDATE user SET Host='%' WHERE User='root';
mysql> flush privileges;
```
## 设置默认编码为utf-8
mysql安装后默认不支持中文，需要修改编码，修改/etc/my.cnf配置文件
```shell
[mysqld]
character-set-server=utf8
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
```
重启MySQL服务，查询编码
```shell
shell> systemctl restart mysqld
shell> mysql -uroot -p
mysql> show variables like 'character%';
```

## 默认配置文件路径
配置文件：/etc/my.cnf  
日志文件：/var/log/mysqld.log  
服务启动脚本：/usr/lib/systemd/system/mysqld.service
socket  
文件：/var/run/mysqld/mysqld.pid