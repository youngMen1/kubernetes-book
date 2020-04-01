# Node

## 基本介绍

除了Master，kubernetes集群中的其他机器称为Node节点，Node节点才是kubernetes集群中的工作负载节点，每个Node都会被Master分配一些工作负载\(Docker容器\)。

## 怎么用

```
// 查看集群中有多少个Node
kubectl get nodes
// 查看某个Node的详细信息
kubectl describe node <node_name>
```



