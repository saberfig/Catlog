- [用户的创建与授权](#用户的创建与授权)
    - [创建用户](#创建用户)
    - [授权](#授权)
    - [撤销用户权限](#撤销用户权限)
    - [删除用户](#删除用户)
- [MySQL执行外部sql脚本文件](#MySQL执行外部sql脚本文件)
- [MySQL修改密码的4种方法](#MySQL修改密码的4种方法)
- [远程连接MySQL数据库](#远程连接MySQL数据库)

# 用户的创建与授权
> 摘抄自https://www.jianshu.com/p/d7b9c468f20d

## 创建用户
```sql
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
```
### 说明
* username：用户名
* host：用户在哪个主机上可以登录，本地用户用localhost，如果想从任意远程主机登录，使用通配符'%'
* password：密码，可以为空
### 例子
```sql
CREATE USER 'dog'@'localhost' IDENTIFIED BY '123456';
CREATE USER 'pig'@'192.168.1.101_' IDENDIFIED BY '123456';
CREATE USER 'pig'@'%' IDENTIFIED BY '123456';
CREATE USER 'pig'@'%' IDENTIFIED BY '';
CREATE USER 'pig'@'%';
```

## 授权
```sql
GRANT privileges ON databasename.tablename TO 'username'@'host';
```
说明
* privileges：用户的操作权限，如`SELECT`，`INSERT`，`UPDATE`等，授予所有权限使用`ALL`

例子
```sql
GRANT SELECT, INSERT ON test.user TO 'pig'@'%';
GRANT ALL ON *.* TO 'pig'@'%';
```
注意
用以上命令授权的用户不能给其他用户授权，如果想要授权，用以下命令
```sql
GRANT privileges ON databasename.tablename TO 'username'@'host' WITH GRANT OPTION;
```

## 撤销用户权限
```sql
REVOKE privilege ON databasename.tablename FROM 'username'@'host';
```
例子
```sql
REVOKE SELECT ON *.* FROM 'pig'@'%';
```
注意
撤销权限所在的`databasename.tablename`必须要和授权的时候一致。  
具体信息可用命令`SHOW GRANTS FOR 'pig'@'%';查看`

## 删除用户
```sql
DROP USER 'username'@'host'
```

# MySQL执行外部sql脚本文件
```sql
source /home/my.sql
```

# MySQL修改密码的4种方法
## 方法1：用SET PASSWORD命令
```sql
SET PASSWORD FOR 用户名@localhost=PASSWORD('新密码');
--例子
SET PASSWORD FOR root@localhost=PASSWORD('123');
```

## 方法2：用mysqladmin
```shell
shell> mysqladmin -u用户名 -p旧密码 password 新密码
shell> mysqladmin -uroot -p123456 password 123 #例子
```

## 方法3：用UPDATE直接编辑user表
```sql
USE mysql;
UPDATE user SET password=PASSWORD('123') WHERE user='root' and host='localhost';
FLUSH PRIVILEGES;
```
## 方法4：忘记root密码时修改密码
1.关闭MySQL服务  
2.执行`mysqld -skip-grant-tables`(跳过权限表认证)，或者在my.ini种添加  
3.登录mysql
4.修改密码
```sql
UPDATE user SET password=PASSWORD(“123”) WHERE user='root';
flush privileges;
```

# 远程连接MySQL数据库
## 分配权限
MySQL默认是只允许root账户在localhost登录，因此需要修改配置
```sql
-- 查看权限(user是用户，host是允许登录的ip，'%'为所有)
SELECT host,user FROM mysql.user;
-- 授权
GRANT ALL privileges ON *.* TO username@host IDENTIFIED BY 'mypassword'; -- 或者
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION; -- host表示授权ip
-- 刷新配置
FLUSH PRIVILEGES;
```
## 登录
```shell
shell> mysql -u用户名 -p -h 服务器IP地址 -P 服务器端口号 -D 数据库名
#> # 单独-p时，回车输密码，-p如果跟密码，不能有空格，如-p123456
```
## 注意
my.ini bind-address问题
> 查看my.ini是否有bind-address资源，如果有`注释`或者`删除`  

防火墙问题
> 需要服务器关闭防火墙或开放3306端口