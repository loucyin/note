# ubuntu tips

## 挂载 samba 共享文件夹

- 安装 cifs-utils

  ```
  sudo apt-get install cifs-utils
  ```

- 挂载共享文件夹

  ```
  mount -t cifs -o username=用户名,password=密码 -l //ip地址/文件夹路径 挂载点
  ```

## samba client 使用

- 查看共享文件夹

  ```
  smbclient -L ip地址 -U username%password
  ```

- 进入 smbclient 环境

  ```
  smbclient //ip地址/文件夹路径 -U username%password
  ```

  在 smbclient 环境中，可以使用 cd 、lcd、get、megt、put、mput 等命令。

## ssh no matching cipher found

ssh 连接报错：

```
Unable to negotiate with 172.18.18.16 port 22: no matching cipher found. Their offer: arcfour
```

解决方案：

编辑 /etc/ssh/ssh_config，在文件最后加入

```
Ciphers 3des-cbc,blowfish-cbc,cast128-cbc,arcfour,arcfour128,arcfour256,aes128-cbc,aes192-cbc,aes256-cbc,rijndael-cbc@lysator.liu.se,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com,chacha20-poly1305@openssh.com
```

## 开机挂载硬盘

几个硬盘相关的常用命令：

- 查看硬盘分区

  ```
  sudo fdisk -l
  ```

- 查看硬盘剩余空间

  ```
  du -hl
  ```

- 查看硬盘文件系统类型 lable uuid

  ```
  sudo blkid
  ```

- 查看可用块设备信息 `lsblk`

  ```
  lsblk -f
  ```

开机挂载硬盘：

- 通过 `blkid` 命令查询到要挂在硬盘的 uuid
- 编辑 `/etc/fstab`

  ```
  # <file system>       <mount point>       <type>  <options> <dump>  <pass>
  UUID=48DE4301D3252FC4 /home/gosun/backup  ntfs    defaults  0       0
  ```

选项           | 含义
:----------- | :----------------------------------
file systems | 要挂载的分区或存储设备
dir          | <file systems="">的挂载位置</file>
type         | 要挂载设备或是分区的文件系统类型
options      | 挂载时使用的参数
dump         | dump 工具通过它决定何时作备份，0 表示忽略， 1 则进行备份
pass         | fsck 的检查顺序 0 不被检查 ，1 最高优先级 ，2 需要被检查

## 参考链接

- [原 scp 使用加密算法报错 unknown cipher type](https://blog.csdn.net/u010906068/article/details/41211605)
- [linux之fstab文件详解](https://blog.csdn.net/richerg85/article/details/17917129)
