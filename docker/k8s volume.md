# k8s volume

volume 类型：

## emptyDir

emptyDir Volume 对容器来说是持久的。

当容器崩溃，但是 Pod 未从节点删除时，Volume 是存在的；当 Pod 从节点删除时， Volume 内容也会被删除。

## hostPath

挂载节点的文件路径到容器中，允许挂载路径、文件、Socket、ChareDevice、BlockDevice 。

hostPath 生命周期与节点一致。在 host 上创建的文件或者文件夹，只有 root 拥有写权限，需要以 root 的权限运行容器。

## local

## 参考链接

- [官方文档 volumes](https://kubernetes.io/docs/concepts/storage/volumes/)
