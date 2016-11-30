# CentOS 配置开机自启动

## 把启动程序的命令添加到/etc/rc.d/rc.local文件中

**CentOS 7 下面 rc.local 权限降低了，需要添加可执行权限**

```
chmod +x /etc/rc.d/rc.local
```

## chkconfig 命令

```
chkconfig --add sshd
chkconfig sshd on
```

## CentOS 7

```
systemctl start mysqld.service
systemctl status mysqld.service
systemctl enable mysqld.service
systemctl disable mysqld.service
```

## 参考链接

- [CentOS设置程序开机自启动的方法](http://www.cnblogs.com/xlmeng1988/archive/2013/05/22/3092447.html)
- [Centos7开机启动自己的脚本](http://www.jianshu.com/p/8a5d968afc7f)
