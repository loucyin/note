## 下载 MySQL
[Ubuntu Linux 14.04 (x86, 64-bit), DEB Bundle](http://downloads.mysql.com/archives/community/)
## 解压，目录如下
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
## 设置 MySQL 密码
- 登陆
默认情况下，root用户是没有密码的，可以匿名登陆。
```
sudo mysql
```  
- 查看用户信息  
查看所有用户和密码信息  
5.7.6以后的版本：  
```
SELECT User, Host, HEX(authentication_string) FROM mysql.user;
```
5.7.6以前的版本：
```
SELECT User, Host, Password FROM mysql.user;
```
- 修改密码  
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
## ubuntu 16.04 安装 mysql
[How To Install Linux, Apache, MySQL, PHP (LAMP) stack on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-16-04) 
[2.10.4 Securing the Initial MySQL Accounts](http://dev.mysql.com/doc/refman/5.7/en/default-privileges.html)  
[mysql 5.7 设置root密码方法](http://my.oschina.net/zhailibao2010/blog/529887)  
[管理员 修改MySQL 5.7.9 新版本的root密码方法以及一些新变化整理](http://www.bubuko.com/infodetail-1339969.html)  
[B.5.3.2 How to Reset the Root Password](http://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)
[如何重设 MySQL 的 root 密码](http://www.ghostchina.com/how-to-reset-mysqls-root-password/)
## MySQL Workbench
[下载地址](http://downloads.mysql.com/archives/workbench/)
## MySQL 源码
[MySQL on GitHub](http://mysqlrelease.com/2014/09/mysql-on-github/)
