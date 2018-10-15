# nvidia-docker 安装

## 安装 nvidia-driver

两种方式：
- 下载安装
- [安装 cuda](https://developer.nvidia.com/cuda-downloads)

## nvidia-docker 安装

- [安装 docker](https://docs.docker.com/install/linux/docker-ce/centos/)
- [安装 nvidia docker](https://github.com/NVIDIA/nvidia-docker)
- 编辑 `/etc/docker/daemon.json` 将默认的 runtime 设置为 nvidia

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

- 重启 docker service

```
sudo systemctl daemon-reload
sudo systemctl restart docker.service
```

## 参考连接

- [ffmpeg 使用 nvidia](https://developer.nvidia.com/ffmpeg)
- [nvidia-docker](https://github.com/NVIDIA/nvidia-docker)
- [cuda dockerfile](https://gitlab.com/nvidia/cuda)
- [cuda 下载安装](https://developer.nvidia.com/cuda-downloads)
- [cuda 驱动版本](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html)
