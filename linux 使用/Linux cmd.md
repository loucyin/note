# 常用命令

## 软件包格式
- deb格式
可以直接点击安装，命令行：sudo dpkg -i xxxx.deb
有些安装包，依赖关系不存在时，可以使用：sudo apt-get -f install
- rpm格式
需要通过alien转换
安装alien: sudo apt-get install alien
转换为deb:sudo alien xxxx.rpm
安装deb: sudo dpkg -i xxxx.deb  

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

## `nohup command > file 2>&1 &`

- nohup命令：如果你正在运行一个进程，而且你觉得在退出帐户时该进程还不会结束，那么可以使用nohup命令。该命令可以在你退出帐户/关闭终端之后继续运行相应的进程；
- `2>&1`: `&1` 更准确的说应该是文件描述符 1，其中0 表示键盘输入，1 表示屏幕输出，2表示错误输出 。`2>&1`是将标准出错重定向到标准输出；
- `&`: 表示命令在后台执行。


## 参考链接

- [linux 查看系统信息命令(比较全)](http://blog.csdn.net/lhf_tiger/article/details/7102753)
- [Linux 查看系统硬件信息(实例详解)](http://www.cnblogs.com/ggjucheng/archive/2013/01/14/2859613.html)
- [Linux man命令的使用方法](http://www.cnblogs.com/hnrainll/archive/2011/09/06/2168604.html)
- [远程连接Linux，如何使程序断开连接后继续运行](http://blog.csdn.net/lyjcn/article/details/52780555)
- [nohup /dev/null 2>&1 含义详解](http://blog.csdn.net/u010889390/article/details/50575345)
