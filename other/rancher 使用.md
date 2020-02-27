# rancher 使用

## 强删 removing 中的 pod

```sh
kubectl delete pod xxxx --namespace ns  --force --grace-period=0
```

## hostport 可以访问 nodeport 访问不了

```sh
iptables -P FORWARD ACCEPT
```
