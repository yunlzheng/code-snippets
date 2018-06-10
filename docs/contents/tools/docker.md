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

在持续集成中使用容器

```
docker run --rm -v `pwd`:/workspace -v /tmp/.m2/repository:/root/.m2/repository --workdir /workspace  maven:3-jdk-8 /bin/sh -c 'mvn clean package'
```

在持续集成中使用compose

```
build:
  command: sh -c 'mvn --help'
  image: maven:3-jdk8
  links: [mysql]
  volumes:
    - '.:/code'
    - '/tmp/.m2/repository:/root/.m2/repository'
  working_dir: /code
mysql:
  environment: {MYSQL_DATABASE: test, MYSQL_PASSWORD: test, MYSQL_ROOT_PASSWORD: test, MYSQL_USER: test}
  image: mysql:5.5
```

```
docker-compose run --rm build sh -c 'mvn clean package' && docker-compose stop && docker-compose rm -f
```