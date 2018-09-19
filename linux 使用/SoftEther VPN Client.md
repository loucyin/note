# SoftEther VPN Client 安装配置

## 安装

- 选择 vpn client [下载](http://www.softether-download.com/cn.aspx?product=softether)
- 解压，make

  ```
  cd vpnclient
  make
  ```

  出现选项后，选择 1 回车，需要选 3 次。

- 更改 vpnclien 的权限，添加执行权限

- 启动 vpnclient

  ```
  sudo ./vpnclien start
  ```

  因为要创建虚拟网卡，需要使用 root 权限启动，否则创建网卡会失败。

## 配置 vpn client

- 执行 `./vpncmd`

- 选择 2 ，回车

- 输入 localhost 回车，进入 VPN Client 界面

  ```
  gosun@PC-loucunyin:/opt/vpnclient$ ./vpncmd
  vpncmd 命令 - PacketiX VPN 命令行管理工具
  PacketiX VPN 命令行管理工具 (vpncmd 命令)
  Version 4.25 Build 9656   (Simplified_Chinese)
  Compiled 2018/01/15 09:33:22 by yagi at pc33
  Copyright (c) SoftEther Corporation. All Rights Reserved.

  通过使用 vpncmd 程序，可以取得以下成果。

  1\. VPN Server 或 VPN Bridge 的管理。
  2\. VPN Client 的管理。
  3\. 使用 VPN 工具 (证书创建和网络传输速度测试工具)

  选择 1, 2 或 3: 2

  指定的主机名或正在运行的目标 VPN Client 计算机的 IP 地址。
  如果不输入任何内容并且按下回车键，将连接到本地主机 (这台电脑)。
  目标 IP 地址的主机名:localhost

  连接到 VPN Client "localhost"。

  VPN Client>
  ```

### 相关配置命令

- 打开、关闭远程管理功能

  ```
  VPN Client>remoteenable
  RemoteEnable 命令 - 允许 VPN 客户服务的远程管理
  命令成功完成。

  VPN Client>remotedisable
  RemoteDisable 命令 - 禁止 VPN 客户服务的远程管理
  命令成功完成。
  ```

- 设定远程管理密码

  ```
  VPN Client>passwordset 123456
  PasswordSet 命令 - 为连接到 VPN 客户服务的密码的设定
  命令成功完成。
  ```

- 虚拟网卡相关命令

  ```
  VPN Client>nicdelete vpn
  NicDelete 命令 - 删除虚拟 LAN 卡
  命令成功完成。

  VPN Client>niccreate vpn
  NicCreate 命令 - 新的虚拟 LAN 卡的创建
  命令成功完成。

  VPN Client>niclist
  NicList 命令 - 获取虚拟 LAN 卡列表
  项目            |价值
  ----------------+----------------------------------------------
  虚拟网络适配器名|vpn
  状态            |已启用
  MAC 地址        |00AC50FD2899
  版本            |Version 4.25 Build 9656   (Simplified_Chinese)
  命令成功完成。
  ```

- account 相关命令

  1. 创建vpn连接（'BeiJing'是 vpn 连接的名称，'60.150.237.140.8888'是 VPN Server 的公网 IP 和映射的端口，'DEFAULT'是 VPN Server上的虚拟 HUB，'user1'是 VPN Server 上的用户，'VPN'是本地的虚拟网卡名称 ）
  2. 设置用户密码和密码认证方式（'Beijing'是刚刚创建的VPN连接名称，123456 是 VPN Server 上用户的密码。standard 是标准密码认证模式）
  3. 启动 VPN 连接
  4. 查看 VPN 连接
  5. 断开 VPN 连接
  6. 设置打开 vpnclien 自动连接
  7. 关闭自动连接

    ```
    VPN Client>accountcreate BeiJing /server:60.150.237.140:8888 /hub:DEFAULT /user:user1 /nic:vpn
    VPN Client>accountpasswordset BeiJing /password:123456 /type:standard
    VPN Client>accountconnect BeiJing
    VPN Client>accountlist
    VPN Client>accountdisconnect BeiJing
    VPN Client>accountstartupset BeiJing
    VPN Client>accountstartupremove BeiJing
    ```

    **注意：虚拟 hub 一定要设置正确**

上面的配置都保存在: `vpn_client.config` 文件中，可以直接修改文件，进行相关参数的修改。

## VPN 不起作用

- 通过 ifconfig 可以查看网卡中多了一个 vpn_vpn，但是没有 ipv4 地址，通过 dhclient 获取 IP 地址。

  ```
  sudo dhclient vpn_vpn
  ```

  网络都连不上了。

- 查看 route

  ```
  default         bfbfbefe.virtua 0.0.0.0         UG    0      0        0 vpn_vpn
  default         192.168.1.1     0.0.0.0         UG    100    0        0 enp2s0
  191.191.190.0   *               255.255.255.0   U     0      0        0 vpn_vpn
  192.168.1.0     *               255.255.255.0   U     100    0        0 enp2s0
  ```

  多了两条 vpn_vpn 路由，而 vpn_vpn default 路由就是导致不能联网的罪魁祸首，删除它 vpn 就可以用了。

- 做个启动脚本

  ```bash
  #!/bin/bash
  sudo /opt/vpnclient/vpnclient start
  # 睡 1 秒，等待 vpn_vpn 网卡生成
  sleep 1s
  sudo dhclient vpn_vpn
  # 再睡 1 秒，等待 vpn_vpn 获取 ip
  sleep 1s
  sudo route del default vpn_vpn
  ```

**路由配置**

由于默认路由使用的是本地网关，所以访问 VPN 其他网段的 ip 时，需要配置路由规则。

## 参考链接

- [PacketiX VPN Linux系统 安装教程](http://www.softether.cn/jiaocheng/linux.html)
- [How to create SoftEther connection on Ubuntu?](https://www.rapidvpn.com/setup-vpn-softether-ubuntu)
- [setting-up-softether-vpn-client](https://askubuntu.com/questions/666484/setting-up-softether-vpn-client)
