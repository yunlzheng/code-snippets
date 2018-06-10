# Network Policy

> Securing Kubernetes Cluster Networking

## 基本概念

Network Policy是用于控制和管理Pods之间的通讯，**NetworkPolicy**资源使用标签(labels)来选择Pods同时定义规则(rule)来规定哪些符合select规则的Pods可以访问该Pod。

支持Network Policy Provider:
* Calico
* Cillium
* Kube-router
* Romana
* Weave

NetworkPolicy的两个组成部分：
* Target pods: 定义那些Pod会被应用该网络策略，通过pod的标签进行选择
* Ingress Rules: 定义那些Pod可以连接目标Pod，通过标签podSelector或者命名空间选择namespaceSelector

> 注意： 当前spec.ingress.from[]自定定义的规则之间的关系是或者关系(or)

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default #定义当前policy启用的namespace
spec:
  podSelector: # Pod选择器选择应用该策略的Pod or {} 表示所有
    matchLabels: # matchLabels为空时，表示选择当前Namespace所有
      role: db   # 标签
  policyTypes: # 定义当前策略是否包含Ingress，Egress或者都包含
  - Ingress  # 定义访问入口策略
  - Egress   # 控制对外访问控制策略
  ingress:   # 网络访问控制白名单
  - from:
    - ipBlock: # 定义可访问IP地址白名单
        cidr: 172.17.0.0/16
        except:  # 除去
        - 172.17.1.0/24
    - namespaceSelector:  # 定义namespace白名单
        matchLabels:
          project: myproject
    - podSelector:  # 定义pod白名单
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 6379
  egress:  # 网络对外访问控制白名单
  - to:
    - ipBlock:
        cidr: 10.0.0.0/24
    ports:
    - protocol: TCP
      port: 5978
```

## 应用场景

### DENY all traffic to an application

> 拒绝所有其它Pod的访问

![image](http://7pn5d3.com1.z0.glb.clouddn.com/k8s/networkpolicy1.gif)

创建Deployment web,设置标签 app=web,env=prod,并且创建相应的服务暴露80端口

```
kubectl run web --image=nginx --labels app=web,env=prod --expose --port 80
```

验证web服务的可访问性

```
$ kubectl run --rm -i -t --image=alpine test-$RANDOM -- sh
/ # wget -qO- http://web
<!DOCTYPE html>
<html>
<head>
...
```

创建NetworkPolicy

```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: web-deny-all
spec:
  podSelector:
    matchLabels:
      app: web
      env: prod
```

```
$ kubectl apply -f web-deny-all.yaml
networkpolicy "access-nginx" created
```

验证web服务的可访问性

```
$ kubectl run --rm -i -t --image=alpine test-$RANDOM -- sh
/ # wget -qO- --timeout=2 http://web
wget: download timed out
```

### LIMIT traffic to an application

> 限制只满足条件的Pod访问目标Pod

![image](http://7pn5d3.com1.z0.glb.clouddn.com/k8s/networkpolicy2.gif)

创建服务

```
kubectl run apiserver --image=nginx --labels app=bookstore,role=api --expose --port 80
```

网络策略

```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: api-allow
spec:
  podSelector:
    matchLabels:
      app: bookstore
      role: api
  ingress:
  - from:
      - podSelector:
          matchLabels:
            app: bookstore
```

验证

```
$ kubectl run test-$RANDOM --rm -i -t --image=alpine -- sh
/ # wget -qO- --timeout=2 http://apiserver
wget: download timed out
```

```
$ kubectl run test-$RANDOM --rm -i -t --image=alpine --labels app=bookstore,role=frontend -- sh
/ # wget -qO- --timeout=2 http://apiserver
<!DOCTYPE html>
<html><head>
```

### DENY all non-whitelisted traffic in a namespace

![image](http://7pn5d3.com1.z0.glb.clouddn.com/k8s/networkpolicy3.gif)

网络策略

在default中的所有Pod拒绝所有请求，但是default中的Pod可以访问其他Namespace中的Pod：
* namespace: 定义当前policy在default命名空间启用
* podSelector： matchLabels为空，表示对被对所有Pod启用该策略
* Ingress：为空表示拒绝所有Pod的请求

```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: default-deny-all
  namespace: default
spec:
  podSelector:
    matchLabels:
```

### DENY all traffic from other namespaces

![image](http://7pn5d3.com1.z0.glb.clouddn.com/k8s/networkpolicy4.gif)

```
kubectl create namespace secondary

kubectl run web --namespace secondary --image=nginx \
    --labels=app=web --expose --port 80
```

网络策略

* namespace： 定义当前网络策略在secondary中启用
* podSelector: matchLabels为空表示对当前namespace中的所有Pod应用该策略
* ingress： from.podSelector为空，表示允许当前namespace中的所有Pod访问目标Pod, 未定义namespaceSelector表示拒绝所有其它namespace的请求

```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  namespace: secondary
  name: web-deny-other-namespaces
spec:
  podSelector:
    matchLabels:
  ingress:
  - from:
    - podSelector: {}
```

验证：

```
$ kubectl run test-$RANDOM --namespace=default --rm -i -t --image=alpine -- sh
/ # wget -qO- --timeout=2 http://web.secondary
wget: download timed out
```

```
$ kubectl run test-$RANDOM --namespace=secondary --rm -i -t --image=alpine -- sh
/ # wget -qO- --timeout=2 http://web.secondary
<!DOCTYPE html>
<html>
```

### ALLOW traffic from other namespaces

![image](http://7pn5d3.com1.z0.glb.clouddn.com/k8s/networkpolicy5.gif)

```
kubectl create namespace secondary

kubectl run web --image=nginx \
    --namespace secondary \
    --labels=app=web --expose --port 80
```

```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  namespace: secondary
  name: web-allow-all-namespaces
spec:
  podSelector:
    matchLabels:
      app: web
  ingress:
  - from:
    - namespaceSelector: {}
```

### ALLOW all traffic from a namespace

![image](http://7pn5d3.com1.z0.glb.clouddn.com/k8s/networkpolicy6.gif)

```
kubectl run web --image=nginx \
    --labels=app=web --expose --port 80
```

```
kubectl create namespace dev
kubectl label namespace/dev purpose=testing
```

```
kubectl create namespace prod
kubectl label namespace/prod purpose=production
```

网络策略

```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: web-allow-prod
spec:
  podSelector:
    matchLabels:
      app: web
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: production
```

### ALLOW traffic from external clients

![image](http://7pn5d3.com1.z0.glb.clouddn.com/k8s/networkpolicy8.gif)

```
kubectl run web --image=nginx \
    --labels=app=web --port 80

kubectl expose deployment/web --type=LoadBalancer
```

网络策略

```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: web-allow-external
spec:
  podSelector:
    matchLabels:
      app: web
  ingress:
  - from: []
```

如果要限制外部访问的端口添加一下配置:

```
 ingress:
  - ports:
    - port: 80
    from: []
```

### ALLOW traffic only to a port number of an application

![image](http://7pn5d3.com1.z0.glb.clouddn.com/k8s/networkpolicy9.gif)

```
kubectl run apiserver --image=ahmet/app-on-two-ports --labels=app=api
```

```
kubectl create service clusterip apiserver \
    --tcp 8001:8000 \
    --tcp 5001:5000
```

网络策略

```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: api-allow-5000
spec:
  podSelector:
    matchLabels:
      app: api
  ingress:
  - ports:
    - port: 5000
    from:
    - podSelector:
        matchLabels:
          role: monitoring
```