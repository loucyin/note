# rancher 使用

## 强删 removing 中的 pod

```sh
kubectl delete pod xxxx --namespace ns  --force --grace-period=0 
```