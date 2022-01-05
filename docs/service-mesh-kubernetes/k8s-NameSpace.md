## Namespace

Namespace是对一组资源和对象的抽象集合，比如可以用来将系统内部的对象划分为不同的项目组或用户组。常见的pods, services, replication controllers和deployments等都是属于某一个 namespace 的（默认是default），而node, persistentVolumes等则不属于任何namespace。

Namespace常用来隔离不同的用户，比如 Kubernetes 自带的服务一般运行在 kube-system namespace中。

默认只隔离资源，不隔离网络

## Namespace操作

> kubectl可以通过–namespace或者-n选项指定namespace。如果不指定，默认为default。查看操作下,也可以通过设置–all-namespace=true来查看所有namespace下的资源；或者使用 `kubectl get pods -A`  可查看所有 namespace 下的 pod

### 查询

```bash
$ kubectl get namespaces
NAME          STATUS    AGE
default       Active    11d
kube-system   Active    11d
```

注意：namespace包含两种状态”Active”和”Terminating”。在namespace删除过程中，namespace状态被设置成”Terminating”。

### 创建

```
(1) 命令行直接创建
$ kubectl create namespace new-namespace
$ kubectl create ns new-namespace

(2) 通过文件创建
$ cat my-namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: new-namespace

$ kubectl create -f ./my-namespace.yaml
```

注意：命名空间名称满足正则表达式 `[a-z0-9]([-a-z0-9]*[a-z0-9])?`,最大长度为63位

### 删除

```
$ kubectl delete namespaces[ns] new-namespace

$ kubectl delete -f ./my-namespace.yaml
```

注意：

1. 删除一个namespace会自动删除所有属于该namespace的资源。
2. default和kube-system命名空间不可删除。