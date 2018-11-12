# docker bridge 网络模式下 iptables 配置

## 背景

通过 docker 容器部署 nginx 服务，暴露 81 和 443 端口。nginx 容器启动命令：

```
docker run -idt -p 81:80 -p 443:443 nginx:alpine
```

nginx 容器 ifconfig:

```
eth0      Link encap:Ethernet  HWaddr 02:42:AC:13:00:02
          inet addr:172.19.0.2  Bcast:172.19.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:21081 errors:0 dropped:0 overruns:0 frame:0
          TX packets:19157 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:10481294 (9.9 MiB)  TX bytes:10533716 (10.0 MiB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```

## 网卡
docker 宿主机 ifconfig:

```
br-d062256f08bf Link encap:以太网  硬件地址 02:42:f1:f7:2a:05
          inet 地址:172.19.0.1  广播:172.19.255.255  掩码:255.255.0.0
          inet6 地址: fe80::42:f1ff:fef7:2a05/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  跃点数:1
          接收数据包:26935 错误:0 丢弃:0 过载:0 帧数:0
          发送数据包:29382 错误:0 丢弃:0 过载:0 载波:0
          碰撞:0 发送队列长度:0
          接收字节:14236296 (14.2 MB)  发送字节:14519417 (14.5 MB)

docker0   Link encap:以太网  硬件地址 02:42:76:d6:a6:41
          inet 地址:172.17.0.1  广播:172.17.255.255  掩码:255.255.0.0
          UP BROADCAST MULTICAST  MTU:1500  跃点数:1
          接收数据包:0 错误:0 丢弃:0 过载:0 帧数:0
          发送数据包:0 错误:0 丢弃:0 过载:0 载波:0
          碰撞:0 发送队列长度:0
          接收字节:0 (0.0 B)  发送字节:0 (0.0 B)

enx00e04c534458 Link encap:以太网  硬件地址 00:e0:4c:53:44:58
          inet 地址:172.18.18.155  广播:172.18.18.255  掩码:255.255.255.0
          inet6 地址: fe80::4eeb:990b:d571:f05d/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  跃点数:1
          接收数据包:333958 错误:22 丢弃:4 过载:4 帧数:28
          发送数据包:27341 错误:0 丢弃:0 过载:0 载波:0
          碰撞:0 发送队列长度:1000
          接收字节:27691717 (27.6 MB)  发送字节:9755402 (9.7 MB)
```

## nat 表中的配置

iptables -t nat -vL

```
Chain PREROUTING (policy ACCEPT 176 packets, 17100 bytes)
 pkts bytes target     prot opt in     out     source               destination
    2   120 DNAT       tcp  --  enx00e04c534458 any     anywhere             anywhere             tcp dpt:86 to:192.168.1.31:86
27095 1481K DOCKER     all  --  any    any     anywhere             anywhere             ADDRTYPE match dst-type LOCAL

Chain INPUT (policy ACCEPT 6 packets, 1176 bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain OUTPUT (policy ACCEPT 33 packets, 2276 bytes)
 pkts bytes target     prot opt in     out     source               destination
    2   144 DOCKER     all  --  any    any     anywhere            !127.0.0.0/8          ADDRTYPE match dst-type LOCAL

Chain POSTROUTING (policy ACCEPT 65 packets, 4112 bytes)
 pkts bytes target     prot opt in     out     source               destination
    2   120 MASQUERADE  all  --  any    enx00e04c534458  192.168.1.31         anywhere
    0     0 MASQUERADE  all  --  any    !docker0  172.17.0.0/16        anywhere
 1453 87204 MASQUERADE  all  --  any    !br-d062256f08bf  172.19.0.0/16        anywhere
    0     0 MASQUERADE  tcp  --  any    any     172.19.0.2           172.19.0.2           tcp dpt:https
    0     0 MASQUERADE  tcp  --  any    any     172.19.0.2           172.19.0.2           tcp dpt:http

Chain DOCKER (2 references)
 pkts bytes target     prot opt in     out     source               destination
    0     0 RETURN     all  --  docker0 any     anywhere             anywhere
    0     0 RETURN     all  --  br-d062256f08bf any     anywhere             anywhere
    2   100 DNAT       tcp  --  !br-d062256f08bf any     anywhere             anywhere             tcp dpt:snpp to:172.19.0.2:443
 1409 80956 DNAT       tcp  --  !br-d062256f08bf any     anywhere             anywhere             tcp dpt:81 to:172.19.0.2:80
```
docker 在 nat 表中，对 nginx 做了路由映射。

DOCKER 链将 443 端口 81 端口，映射到 nginx 容器中，在 PREROUTING 时映射，在 POSTROUTING 时进行伪装。


## filter 表中的配置

iptables -t filter -vL

```
Chain INPUT (policy DROP 376 packets, 34059 bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain FORWARD (policy ACCEPT 6 packets, 1506 bytes)
 pkts bytes target     prot opt in     out     source               destination
54780   28M DOCKER-USER  all  --  any    any     anywhere             anywhere
54780   28M DOCKER-ISOLATION-STAGE-1  all  --  any    any     anywhere             anywhere
    0     0 ACCEPT     all  --  any    docker0  anywhere             anywhere             ctstate RELATED,ESTABLISHED
    0     0 DOCKER     all  --  any    docker0  anywhere             anywhere
    0     0 ACCEPT     all  --  docker0 !docker0  anywhere             anywhere
    0     0 ACCEPT     all  --  docker0 docker0  anywhere             anywhere
26910   14M ACCEPT     all  --  any    br-d062256f08bf  anywhere             anywhere             ctstate RELATED,ESTABLISHED
 1764  102K DOCKER     all  --  any    br-d062256f08bf  anywhere             anywhere
26014   14M ACCEPT     all  --  br-d062256f08bf !br-d062256f08bf  anywhere             anywhere
    0     0 ACCEPT     all  --  br-d062256f08bf br-d062256f08bf  anywhere             anywhere

Chain OUTPUT (policy ACCEPT 1997 packets, 689K bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain DOCKER (2 references)
 pkts bytes target     prot opt in     out     source               destination
    2   100 ACCEPT     tcp  --  !br-d062256f08bf br-d062256f08bf  anywhere             172.19.0.2           tcp dpt:https
 1284 73452 ACCEPT     tcp  --  !br-d062256f08bf br-d062256f08bf  anywhere             172.19.0.2           tcp dpt:http

Chain DOCKER-ISOLATION-STAGE-1 (1 references)
 pkts bytes target     prot opt in     out     source               destination
    0     0 DOCKER-ISOLATION-STAGE-2  all  --  docker0 !docker0  anywhere             anywhere
26014   14M DOCKER-ISOLATION-STAGE-2  all  --  br-d062256f08bf !br-d062256f08bf  anywhere             anywhere
54780   28M RETURN     all  --  any    any     anywhere             anywhere

Chain DOCKER-ISOLATION-STAGE-2 (2 references)
 pkts bytes target     prot opt in     out     source               destination
    0     0 DROP       all  --  any    docker0  anywhere             anywhere
    0     0 DROP       all  --  any    br-d062256f08bf  anywhere             anywhere
26014   14M RETURN     all  --  any    any     anywhere             anywhere

Chain DOCKER-USER (1 references)
 pkts bytes target     prot opt in     out     source               destination
55025   28M RETURN     all  --  any    any     anywhere             anywhere
```

nginx 包的流转 ： PREROUTING -> FORWARD -> POSTROUTING 。所以 docker iptable 规则都配置到了 FORWARD 链中。

### DOCKER-USER 链

用于存放 docker 用户自定义规则。

### DOCKER-ISOLATION-STAGE-1 链

- DOCKER-ISOLATION-STAGE-1 把从网卡 br-d062256f08bf 到其他网卡的包映射到 DOCKER-ISOLATION-STAGE-2 。
- DOCKER-ISOLATION-STAGE-2 会过滤掉目标为 docker 网卡的包。

DOCKER-ISOLATION-STAGE-1 链的作用：

- 过滤掉来自 docker 网卡并且目标为 docker 网卡的包

  如 docker0 到 br-d062256f08bf 或者 br-d062256f08bf 到 docker0 的包。

- 放行从非 docker 网卡到 docker 网卡的包

  如 enx00e04c534458 到 br-d062256f08bf 的包

### DOCKER 链

放行来自其他网卡，到 docker 网卡 br-d062256f08bf 端口为 80 以及 443 的包。
