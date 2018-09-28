# docker 使用中遇到的坑

## 网络问题

背景：

- 局域网有两个网段 ： 192.168.1.0 ， 172.18.18.0
- 主机 A 在 192.168.1.0 网段，主机 B 在 172.18.18.0 网段

问题：

主机 A 装上 Docker ，运行容器，使用 bridge 模式进行端口映射，导致从主机 A 访问不到局域网内的主机 B 。

原因：

- docker 创建的网络与局域网创建的网络冲突；
- docker 创建网络时，从 172.17.0.0 网段开始创建，docker0 接口占用 172.17.0.0 网段；
- 当使用 bridg 模式进行端口映射时，docker 创建 docker_gwbridge 接口，占用了 172.18.0.0 网段；
- 172.18.0.0 网段的访问默认通过 docker_gwbridge 接口；

解决方案：

- 停止主机 A 上的容器，删除 docker_gwbridge ；
- 重新运行主机 A 上的容器，此时 docker_gwbridge 接口，占用了 172.19.0.0 网段。

## CentOS 7 firewall

- 主机 A CentOS 7.5 防火墙启用，作为 docker swarm manager，通过 swarm 模式部署一系列 Service；
- 主机 B 作为节点，加入 swarm 集群失败：

  ```
  Error response from daemon: rpc error: code = Unavailable desc = all SubConns are in TransientFailure, latest connection error: connection error: desc = "transport: Error while dialing dial tcp 172.18.18.138:2377: connect: no route to host"
  ```

  失败原因： 主机 A 防火墙没有关闭。

- 关闭 CentOS 防火墙，此时，Service 访问失败，需要重启 docker 服务。

  ```
  sudo systemctl stop firewalld
  sudo systemctl disable firewalld
  sudo systemctl restart docker.service
  ```

  原因：

  > firewall的底层是使用iptables进行数据过滤，建立在iptables之上，这可能会与 Docker 产生冲突。 当 firewalld 启动或者重启的时候，将会从 iptables 中移除 DOCKER 的规则，从而影响了 Docker 的正常工作。 当你使用的是 Systemd 的时候， firewalld 会在 Docker 之前启动，但是如果你在 Docker 启动之后再启动 或者重启 firewalld ，你就需要重启 Docker 进程了。

## 参考链接

- [installing-docker-centos-7](https://www.widuu.com/docker/installation/centos.html#installing-docker-centos-7)
