# Linux 下 MySQL 安装

## Ubuntu MySQL 安装

### 下载 MySQL

[Ubuntu Linux 14.04 (x86, 64-bit), DEB Bundle](http://downloads.mysql.com/archives/community/)

### 解压，目录如下

```
mysql-common_5.7.11-1ubuntu14.04_amd64.deb
libmysqlclient20_5.7.11-1ubuntu14.04_amd64.deb
libmysqlclient-dev_5.7.11-1ubuntu14.04_amd64.deb
libmysqld-dev_5.7.11-1ubuntu14.04_amd64.deb
mysql-community-client_5.7.11-1ubuntu14.04_amd64.deb
mysql-client_5.7.11-1ubuntu14.04_amd64.deb
mysql-community-server_5.7.11-1ubuntu14.04_amd64.deb
mysql-community-source_5.7.11-1ubuntu14.04_amd64.deb
mysql-server_5.7.11-1ubuntu14.04_amd64.deb

mysql-community-test_5.7.11-1ubuntu14.04_amd64.deb
mysql-testsuite_5.7.11-1ubuntu14.04_amd64.deb
```

安装所有的包。

### 设置 MySQL 密码

- 登陆 默认情况下，root用户是没有密码的，可以匿名登陆。

  ```
  sudo mysql
  ```

- 查看用户信息<br>
  查看所有用户和密码信息<br>
  5.7.6以后的版本：

  ```
  SELECT User, Host, HEX(authentication_string) FROM mysql.user;
  ```

  5.7.6以前的版本：

  ```
  SELECT User, Host, Password FROM mysql.user;
  ```

- 修改密码<br>
  5.7.6以后的版本：

  ```
  ALTER USER user IDENTIFIED BY 'new_password';
  ```

  上面的指令貌似不起作用。下面这个可以用：

  ```
  update user set authentication_string = password('lcy2016') where user = 'root';
  ```

  5.7.6以前的版本：

  ```
  SET PASSWORD FOR user = PASSWORD('new_password');
  ```

## CentOS yum 安装 MySQL

### 下载仓库

- 下载 rpm

  ```
  wget http://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
  ```

- 安装 rpm

  ```
  rpm -Uvh platform-and-version-specific-package-name.rpm
  ```

- 查看版本

  ```
  yum repolist all | grep mysql
  yum repolist enabled | grep mysql
  ```

- 安装

  ```
  yum install mysql-community-server
  ```

### 设置密码

- mysql 的配置文件 ： /etc/my.cnf log 文件路径：

  ```
  log-error=/var/log/mysqld.log
  ```

- 从 log 中找到初始密码：

  ```
  cat /var/log/mysqld.log | grep password
  2016-11-29T04:38:29.248272Z 1 [Note] A temporary password is generated for root@localhost: pobozZpaY8:C
  ```

- 进入 Mysql 修改密码

```
use mysql;
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.

alter user 'root'@'localhost' identified by 'root'
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements

set global validate_password_policy=0;
set global validate_password_length=4;
alter user user() identified by 'root';
```

validate_password_policy有以下取值：

Policy      | Tests Performed
:---------- | :----------------------------------------------------------------------------
0 or LOW    | Length
1 or MEDIUM | Length; numeric, lowercase/uppercase, and special characters
2 or STRONG | Length; numeric, lowercase/uppercase, and special characters; dictionary file

MySQL 5.7.6 前后重置密码的方式不同。

## MySql 设置 root 密码 配置远程访问

- 创建用户

  ```
  CREATE USER 'username'@'host' IDENTIFIED BY 'password';
  ```

- 设置密码

  ```
  SET PASSWORD FOR 'username'@'host' = PASSWORD('newpassword');
  update user set password=password('newpassword') where user = 'username' and host = 'host';
  ```

- 删除用户

  ```
  DROP USER 'username'@'host';
  ```

- 授权

  ```
  GRANT privileges ON databasename.tablename TO 'username'@'host'
  ```

  说明:

  > privileges - 用户的操作权限,如SELECT , INSERT , UPDATE 等(详细列表见该文最后面). 如果要授予所的权限则使用ALL; databasename - 数据库名,tablename-表名,如果要授予该用户对所有数据库和表的相应操作权限则可用 _._ 表示

  ```
  GRANT SELECT, INSERT ON test.user TO 'pig'@'%';
  GRANT ALL ON *.* TO 'pig'@'%';
  GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'WITH GRANT OPTION;
  ```

- 创建一个 host 为 % 的用户

  ```
  CREATE USER 'username'@'%' IDENTIFIED BY 'password';
  ```

  远程访问的用户名为 username , 密码为 password。

- 操作完用户后执行

  ```
  flush privileges;
  ```

## 参考链接

### ubuntu

- [How To Install Linux, Apache, MySQL, PHP (LAMP) stack on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-16-04)
- [2.10.4 Securing the Initial MySQL Accounts](http://dev.mysql.com/doc/refman/5.7/en/default-privileges.html)
- [mysql 5.7 设置root密码方法](http://my.oschina.net/zhailibao2010/blog/529887)
- [管理员 修改MySQL 5.7.9 新版本的root密码方法以及一些新变化整理](http://www.bubuko.com/infodetail-1339969.html)
- [B.5.3.2 How to Reset the Root Password](http://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)
- [如何重设 MySQL 的 root 密码](http://www.ghostchina.com/how-to-reset-mysqls-root-password/)
- [MySQL Workbench 下载地址](http://downloads.mysql.com/archives/workbench/)
- [MySQL on GitHub](http://mysqlrelease.com/2014/09/mysql-on-github/)

### CentOS

- [A Quick Guide to Using the MySQL Yum Repository](http://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/)
- [Download MySQL Yum Repository](http://dev.mysql.com/downloads/repo/yum/)
- [ERROR 1819 (HY000): Your password does not satisfy the current policy requirements](http://www.cnblogs.com/ivictor/p/5142809.html)
