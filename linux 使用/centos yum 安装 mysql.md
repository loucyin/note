# CentOS yum 安装 MySQL

## 下载仓库

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

## 设置密码

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

## 参考链接

- [A Quick Guide to Using the MySQL Yum Repository](http://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/)
- [Download MySQL Yum Repository](http://dev.mysql.com/downloads/repo/yum/)
- [ERROR 1819 (HY000): Your password does not satisfy the current policy requirements](http://www.cnblogs.com/ivictor/p/5142809.html)
