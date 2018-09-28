# docker 安装

系统：elementary OS 0.4.1 Loki 基于 "Ubuntu 16.04.3 LTS" 构建。

## 官方教程

- 安装依赖包

  ```bash
  $ sudo apt-get install \
      apt-transport-https \
      ca-certificates \
      curl \
      software-properties-common
  ```

- 添加 GPG key

  ```bash
  $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
  $ sudo apt-key fingerprint 0EBFCD88
  ```

- 添加仓库

  ```bash
  $ sudo add-apt-repository \
     "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
     $(lsb_release -cs) \
     stable"
  ```

  这里掉坑里了：

  - 上面的链接中，拼接了系统版本名称 `$(lsb_release -cs)` ，得到的链接是错误的，执行 `sudo apt-get update` 命令报 404 错误；
  - ubuntu 16.04 的 codeName : xenial；
  - 当前系统的 codeName ：loki；
  - 使用 `xenial` 代替 `$(lsb_release -cs)` 解决。

  **删除上面添加的链接**

  - 刚才添加的链接在 `/etc/apt/sources.list` 文件中；
  - 编辑 `/etc/apt/sources.list` ，删除错误链接，解决 404 报错。

- 更新仓库、安装、运行 hello-world

  ```bash
  $ sudo apt-get update
  $ sudo apt-get install docker-ce
  $ sudo docker run hello-world
  ```

## 免 sudo

将当前用户加入到 docker 用户组中：

```
sudo gpasswd -a ${USER} docker
```

## 参考链接

- [Get Docker CE for Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
