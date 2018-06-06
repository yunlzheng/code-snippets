# Docker

## 命令行

Cleanup dingling images

```
docker rmi $(docker images -f "dangling=true" -q)
```