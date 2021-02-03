# 部署 DNS 扩展

本部分将部署 [DNS 扩展](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)，用于为集群内的应用提供服务发现。

## DNS 扩展

部属 `kube-dns` 群集扩展:

```bash
kubectl apply -f https://storage.googleapis.com/kubernetes-the-hard-way/coredns-1.7.0.yaml
```

输出为

```bash
serviceaccount/coredns created
clusterrole.rbac.authorization.k8s.io/system:coredns created
clusterrolebinding.rbac.authorization.k8s.io/system:coredns created
configmap/coredns created
deployment.apps/coredns created
service/kube-dns created
```

列出 `kube-dns` 部署的 Pod 列表:

```bash
kubectl get pods -l k8s-app=kube-dns -n kube-system
```

输出为

```bash
NAME                       READY   STATUS    RESTARTS   AGE
coredns-5677dc4cdb-d8rtv   1/1     Running   0          30s
coredns-5677dc4cdb-m8n69   1/1     Running   0          30s
```

## 验证

建立一个 `busybox` 部署:

```bash
kubectl run busybox --image=busybox --command -- sleep 3600
```

列出 `busybox` 部署的 Pod：

```bash
kubectl get pods -l run=busybox
```

输出为

```bash
NAME                       READY     STATUS    RESTARTS   AGE
busybox-2125412808-mt2vb   1/1       Running   0          15s
```

查询 `busybox` Pod 的全名:

```bash
POD_NAME=$(kubectl get pods -l run=busybox -o jsonpath="{.items[0].metadata.name}")
```

在 `busybox` Pod 中查询 DNS：

```bash
kubectl exec -ti $POD_NAME -- nslookup kubernetes
```

输出为

```bash
Server:    10.32.0.10
Address 1: 10.32.0.10 kube-dns.kube-system.svc.cluster.local

Name:      kubernetes
Address 1: 10.32.0.1 kubernetes.default.svc.cluster.local
```

下一步：[烟雾测试](13-smoke-test.md)。

