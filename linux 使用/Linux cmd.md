# 常用命令

## 文件权限

### chmod

```
sudo chmod 755 file
```

数子 755 为三位八进制数，从左至右分别代表所有者、组用户、所有用户的权限。<br>
一位八进制可以由三位二进制数组成，[000] 由左至右分别表示读、写、执行权限。<br>
755 就代表，所有者具有读、写、执行权限，组用户和其他用户具有读、执行权限。

### 安装eclipse的坑

从官网下载新版eclipse，解压，执行命令 ./eclipse 打开，无论更改什么设置，要么卡死机，要么卡注销。最后使用 sudo ./eclipse 竟然打开了。 原因应该是文件权限的问题。

## 查看系统信息

- 查看内存和交换区使用量：

```
free -m
```

- 查看磁盘信息

  ```
  fdisk -l
  df -l
  df -lh
  ```
- 查看 cpu

```
lscpu
```

## 修改环境变量后使其马上生效

```
source /etc/profile
source ~/.bashrc
```

## 查看文件夹大小

```
du -h
```

## 远程连接Linux，断开连接后程序继续运行

```
nohup
```

## 进程的前后台切换

```
jobs
bg %1
fg %1
```

## ssh 登录记录

- 查看
```
last
```

- 清空
```
echo >/var/log/wtmp 
```


## 参考链接

- [linux 查看系统信息命令(比较全)](http://blog.csdn.net/lhf_tiger/article/details/7102753)
- [Linux 查看系统硬件信息(实例详解)](http://www.cnblogs.com/ggjucheng/archive/2013/01/14/2859613.html)
- [Linux man命令的使用方法](http://www.cnblogs.com/hnrainll/archive/2011/09/06/2168604.html)
- [远程连接Linux，如何使程序断开连接后继续运行](http://blog.csdn.net/lyjcn/article/details/52780555)
