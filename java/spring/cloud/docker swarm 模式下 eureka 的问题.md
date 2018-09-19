# docker swarm 模式下 eureka 的问题

## 问题描述

通过 docker swarm 模式部署三个微服务：

- eureka 注册中心
- service A
- service B

service A 对外暴露端口，service B 依赖与 service A 的服务，通过 feign 进行服务调用。

部署之后 service B 访问不到 service A 。

## 原因

进入 service A 通过 ifconfig 查看，service A 存在 4 个网络接口：

```
eth0      Link encap:Ethernet  HWaddr 02:42:0A:FF:00:25  
          inet addr:10.255.0.37  Bcast:10.255.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1450  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

eth1      Link encap:Ethernet  HWaddr 02:42:0A:00:06:09  
          inet addr:10.0.6.9  Bcast:10.0.6.255  Mask:255.255.255.0
          UP BROADCAST RUNNING MULTICAST  MTU:1450  Metric:1
          RX packets:30 errors:0 dropped:0 overruns:0 frame:0
          TX packets:22 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:6439 (6.2 KiB)  TX bytes:2316 (2.2 KiB)

eth2      Link encap:Ethernet  HWaddr 02:42:AC:13:00:03  
          inet addr:172.19.0.3  Bcast:172.19.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:43 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:5235 (5.1 KiB)  TX bytes:0 (0.0 B)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
```

注册到 eureka 的是 eth0 的 ip 地址，在 service B 中通过访问不到 service A 的 ip 地址。

## 解决方案

### 方案 1

- service A 不要对外暴露端口
- 添加 zuul 网关，通过网关访问 service A 的服务

### 方案 2

在 service A application.yml 中，指定网段：

```
spring:
  cloud:
    inetutils:
      preferred-networks:
        - 10.0

eureka:
  instance:
    preferIpAddress: true
```

通过 docker swarm 部署时，就会选 `10.0.` 网段的 ip 地址注册到 eureka 。

**存在的问题**

如果没有 `10.0.` 网段的 ip 地址可选，会注册 `127.0.0.1` 到 eureka 。

## 参考链接

- [Cannot find a way to configure Eureka client with Docker swarm mode](https://github.com/spring-cloud/spring-cloud-netflix/issues/1820)
