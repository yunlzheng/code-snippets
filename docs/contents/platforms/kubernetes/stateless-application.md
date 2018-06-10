# Stateless Application Manage

> Kubernetes对Stateless类型应用管理

## 部署应用

```
kubectl run t2nginx --image=nginx:1.7.9 --port=80
```

## 扩容应用

```
kubectl scale deployments/t2nginx --replicas=2
```

## 升级应用

```
kubectl set image deployment/t2nginx t2nginx=nginx:1.9.1
```

## 回滚应用

```
kubectl rollout history deployments/t2nginx

kubectl rollout status deployments/t2nginx

kubectl rollout undo deployments/t2nginx
```

## 暴露应用服务地址

```
kubectl expose deployment t2nginx --port=80 --target-port=80
```