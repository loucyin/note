# es 安装启动的几个问题

## 不能以 root 用户运行

1. 添加参数支持 root 用户

```
./elasticsearch -Des.insecure.allow.root=true
```

1. 新建用户运行

```sh
groupadd elsearch
useradd elsearch -g elsearch -p 123456
chown -R elsearch:elsearch elasticsearch
```

## vm.max_map_count

- 方案一

  ```sh
  sysctl -w vm.max_map_count=262144
  ```

  重启后失效。

- 方案二

  修改 /etc/sysctl.conf 加入一行 `vm.max_map_count=262144`

## max file descriptors

使用 elsearch 用户启动 es ，报错：

> max file descriptors [4096] for elasticsearch process is too low, increase to at least [65536]

解决方法：

- 切换到 root 用户，编辑 /etc/security/limits.conf
- 添加如下内容：

```
elsearch hard nofile 65536
elsearch soft nofile 65536
```
