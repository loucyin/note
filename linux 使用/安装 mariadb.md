# ubuntu 下安装 mariadb

根据参考链接下 Setting up MariaDB Repositories 选择系统、版本、镜像，会生成安装提示，复制安装提示，执行就可以完成 mariadb 安装。

## Access denied for user 'root'@'localhost'

- 使用 root 权限进入 mysql

```
sudo mysql
```

- 执行授权

```sql
grant all privileges on *.* to 'root'@'localhost' identified by 'password' with grant option;
```

## 参考链接
- [Setting up MariaDB Repositories](https://downloads.mariadb.org/mariadb/repositories/#mirror=tuna)
- [完美解决Access denied for user 'root'@'localhost'](http://www.centoscn.com/CentosBug/softbug/2015/1225/6573.html)
