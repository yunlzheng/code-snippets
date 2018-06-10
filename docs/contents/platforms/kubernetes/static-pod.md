# Static Pod

> 本文档定义了如果在Kubernetes中使用Static Pod

Static Pods是值通过kubectl daemon在特定节点上自动创建的Pods信息。Static Pods不由replica控制，而是由kubectl daemon自己监控，并且在crash后自动重启。

## 配置启动static pods支持

Static pods与特定节点相关，在需要支持的节点上修改kubelet启动命令

1. 添加启动参数，指定static pod清单文件目录

```
kubelet --pod-manifest-path=<the directory>
```

2, 添加yml到pod-manifest-path

```
apiVersion: v1
kind: Pod
metadata:
  name: static-web
  labels:
    role: myrole
spec:
  containers:
    - name: web
      image: nginx
      ports:
        - name: web
          containerPort: 80
          protocol: TCP
```

3. 重启Kubelet

```
systemctl restart kubelet
```

> --manifest-url=<URL> 从url下载json/yaml，在启动kubelet时自动启动

## Static Pods行为

当kubelet启动时，会自动启动在--pod-manifest-path= 或者 --manifest-url=定义的pods.

```
[joe@my-node1 ~] $ docker ps
CONTAINER ID IMAGE         COMMAND  CREATED        STATUS         PORTS     NAMES
f6d05272b57e nginx:latest  "nginx"  8 minutes ago  Up 8 minutes             k8s_web.6f802af4_static-web-fk-node1_default_67e24ed9466ba55986d120c867395f3c_378e5f3c
```

查看kube api我们可以看到一个mirror-pod被自动创建

```
[joe@my-master ~] $ kubectl get pods
NAME                       READY     STATUS    RESTARTS   AGE
static-web-my-node1        1/1       Running   0          2m
```

注意：
* 我们无法通过api删除该mirror-pod： kubectl delete pods static-web-my-node1
* 我们无法手动通过docker停止该容器,kubelet会自动重启该容器
* kubectl会自动扫描pod-manifest-path目录，并且创建或者移除Pods

## 参考资料

* https://kubernetes.io/docs/tasks/administer-cluster/static-pod/