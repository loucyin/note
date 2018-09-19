# docker ffmpeg 使用 nvidia gpu 加速

使用 NVIDIA 硬件加速 ffmpeg ，需要对 ffmpeg 进行编译安装。

## 安装 nvidia docker

参考 [nvidia-docker QuickStart](https://github.com/NVIDIA/nvidia-docker)

## 安装 nvidia 驱动

- 下载驱动安装包进行安装
- 安装 cuda，会自动安装 nvidia 驱动

  [cuda 下载安装](https://developer.nvidia.com/cuda-downloads)，根据提示选择相应的版本，进行安装。

## 生成 docker 镜像

libx264 代码下载：

```
git clone http://git.videolan.org/git/x264.git
```

Dockerfile:

```
FROM nvidia/cuda:9.2-devel-ubuntu16.04

COPY FFmpeg /root/FFmpeg
COPY x264 /root/x264

RUN apt-get update \
    # 安装工具
    && apt-get install -y curl \
    && apt-get install -y pkg-config \
    && cd /root \
    # 下载代码
    && curl -O https://www.nasm.us/pub/nasm/releasebuilds/2.13.03/nasm-2.13.03.tar.gz \
    && tar -xzvf nasm-2.13.03.tar.gz \
    #编译安装 nasm
    && cd /root/nasm-2.13.03 \
    && ./configure \
    && make \
    && make install \
    # 编译安装 x264
    && cd /root/x264 \
    && ./configure --enable-shared \
    && make -j 10 \
    && make install \
    # 编译安装 ffmpeg
    && cd /root/FFmpeg \
    && ./configure  --enable-gpl --enable-nvenc --enable-cuda --enable-cuvid --enable-nonfree --enable-libnpp --enable-libx264 --extra-cflags=-I/usr/local/cuda/include --extra-ldflags=-L/usr/local/cuda/lib64 \
    && make -j 10 \
    && make install
```

**注意** :<br>
libx264 编译，需要 nasm 2.13 以上版本，Ubuntu 16.04 软件仓库中的版本太低，需要编译安装 nasm 2.13。

## docker 中运行

**注意**

- 需要指定 runtime
- 需要将 nvidia 驱动挂载到镜像中

```
docker run --runtime=nvidia -it -v /usr/lib/nvidia-396:/usr/local/nvidia/lib myffmpeg
```

## 修改 docker 的默认 RUNTIME

修改 /etc/docker/daemon.json

```json
{
    "default-runtime": "nvidia",
    "runtimes": {
        "nvidia": {
            "path": "nvidia-container-runtime",
            "runtimeArgs": []
        }
    }
}
```

修改完成后，重启 docker

```
systemctl daemon-reload
systemctl restart docker
```

## 参考连接

- [ffmpeg 使用 nvidia](https://developer.nvidia.com/ffmpeg)
- [nvidia-docker](https://github.com/NVIDIA/nvidia-docker)
- [cuda dockerfile](https://gitlab.com/nvidia/cuda)
- [cuda 下载安装](https://developer.nvidia.com/cuda-downloads)
