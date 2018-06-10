# Docker

## 命令行

清理容器

```
docker rm $(docker ps -qa --filter status=exited)
```

清理状态为dingling的镜像

```
docker rmi $(docker images -f "dangling=true" -q)
```