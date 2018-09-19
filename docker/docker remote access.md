# docker remote access

官方给了两种方式:

- 修改 `docker.service`
- 修改 `daemon.json`

修改完成后需要重启 docker 服务：

```
sudo systemctl daemon-reload
sudo systemctl restart docker.service
```

## 修改 docker.service

CentOS 7 下 docker.service 路径 `/usr/lib/systemd/system/docker.service`，找到 `ExecStart` 添加参数如下：

```
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock
```

Ubuntu 16.04 下 docker.service 路径 `/lib/systemd/system/docker.service` :

```
ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2375
```

## 修改 daemon.json

默认情况下，`daemon.json` 文件不存在，需要自己创建，路径为 `/etc/docker/daemon.json` ：

```json
{
  "hosts": ["unix:///var/run/docker.sock", "tcp://0.0.0.0:2375"]
}
```

Ubuntu 16.04 `docker.service` 文件中 `ExecStart` 默认带有 -H 项。

```
ExecStart=/usr/bin/dockerd -H fd://
```

添加上面的 daemon.json 后，docker 服务启动不起来。 需要去掉 `docker.service` 文件中 `ExecStart` 的 -H 项。

**注意**

> 添加 `-H tcp://0.0.0.0:2375` 参数，会有安全隐患，任意 ip 地址都可以进行连接，只能用于开发测试环境。

## 客户端使用

设置客户端的环境变量：

```
export DOCKER_HOST=tcp://172.18.18.157:2375
```

客户端就可以访问远程的 docker 环境了。

## 参考链接

- [Post-installation steps for Linux](https://docs.docker.com/install/linux/linux-postinstall//)
- [Docker Daemon连接方式详解](https://www.jianshu.com/p/7ba1a93e6de4)
- [一个回车键黑掉一台服务器----使用Docker时不要这么懒啊喂](https://www.jianshu.com/p/74aa4723111b)
- [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/)
