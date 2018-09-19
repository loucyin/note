# docker-compose 踩坑

docker-compose.yml 文件内容：

```yml
version: '3'

services:

  redis:
    ports:
      - 6379:6379
    image: redis

  webapp:
    image: my-demo/docker-test
    ports:
      - 12000:12000
    depends_on:
      - redis
```

webapp 镜像是自己生成的一个 docker 镜像，需要依赖 redis ，镜像内使用的 redis 地址是 ： `127.0.0.1:6379`。

## 时间不对

在服务中添加 `environment` 项，指定时区：

```
  redis:
    environment:
      - TZ=Asia/Shanghai
```

## redis 连不上

- 修改 webapp 镜像内使用的 redis 地址为 : `redis:6379`
- 修改 compse 文件

  ```yml
  version: '3'

  services:

    redis:
      environment:
        - TZ=Asia/Shanghai
        - DNSDOCK_NAME=redis
        - DNSDOCK_IMAGE=redis
      ports:
        - 6379:6379
      image: redis

    webapp:
      environment:
        - TZ=Asia/Shanghai
      image: my-demo/docker-test
      ports:
        - 12000:12000
      depends_on:
        - redis
  ```

## 自定义 docker-compose 创建的网络网段

- 局域网网段 172.18.18.0/24
- 启动后局域网访问不了
- route

  ```
  $ route
  内核 IP 路由表
  目标            网关            子网掩码        标志  跃点   引用  使用 接口
  default         192.168.1.1     0.0.0.0         UG    100    0        0 enp2s0
  172.17.0.0      *               255.255.0.0     U     0      0        0 docker0
  172.18.0.0      *               255.255.0.0     U     0      0        0 br-b0d9ccd4ae33
  192.168.1.0     *               255.255.255.0   U     100    0        0 enp2s0
  ```

  docker-compse 创建的网络和局域网网络冲突了。

- 添加 docker-compose 网络的默认配置

  ```yml
  networks:
    default:
      ipam:
        config:
          - subnet: 172.19.0.0/16
  ```

## 参考链接

- [替代Docker Compose实现容器双向联通的三种方法](http://www.dockone.io/article/929)
- [自定义 Docker-Compose创建的网络网段](https://www.xiaocaicai.com/2018/05/14/%E8%87%AA%E5%AE%9A%E4%B9%89-docker-compose%E5%88%9B%E5%BB%BA%E7%9A%84%E7%BD%91%E7%BB%9C%E7%BD%91%E6%AE%B5/)
