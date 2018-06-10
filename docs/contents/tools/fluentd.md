# Fluentd

## Fluentd是什么

![image](http://docs.fluentd.org/images/fluentd-architecture.png)

Fluentd是一个开源的数据收集方案，用于统一数据的收集和消费，从而更好的使用和理解数据。

关键特性：

* 基于Json的结构化日志数据
* 可插拔的架构
* 最小化的资源损耗
* 内建可靠性

## Fluentd 配置

- source: 用于定义输入源
- match: 用于定义输出目的地
- filter: 用于定义event处理管道
- system: 用于定义系统范围内的配置
- label: 用于将内部路由的输出和过滤进行分组
- @include: 用于引入其他配置文件

## 常用的source类型

- http:
- forward:
- unix:
- debug_agent:
- tail

[http://docs.fluentd.org/articles/input-plugin-overview](http://docs.fluentd.org/articles/input-plugin-overview)

## 案例

- [基于Fluentd的日志告警](http://docs.fluentd.org/v0.12/articles/splunk-like-grep-and-alert-email)
- [使用Mongodb存储Apache日志](http://docs.fluentd.org/v0.12/articles/apache-to-mongodb) 

## 安装

install deb package

```
curl -L http://toolbelt.treasuredata.com/sh/install-ubuntu-trusty-td-agent2.sh | sh

```

start deamon

```
vagrant@vagrant-ubuntu-trusty-64:~$ sudo /etc/init.d/td-agent restart
Restarting td-agent:  * td-agent

```
check logs

```
vagrant@vagrant-ubuntu-trusty-64:~$ tail /var/log/td-agent/td-agent.log
    port 8888
  </source>
  <source>
    type debug_agent
    bind 127.0.0.1
    port 24230
  </source>
</ROOT>
2016-07-01 06:08:24 +0000 [info]: listening fluent socket on 0.0.0.0:24224
2016-07-01 06:08:24 +0000 [info]: listening dRuby uri="druby://127.0.0.1:24230" object="Engine"
```

check default td-agent.conf

```
/etc/td-agent/td-agent.conf

# HTTP input
# POST http://localhost:8888/<tag>?json=<json>
# POST http://localhost:8888/td.myapp.login?json={"user"%3A"me"}
# @see http://docs.fluentd.org/articles/in_http
<source>
  type http
  port 8888
</source>
```

test td-agent

```
vagrant@vagrant-ubuntu-trusty-64:~$ curl -X POST -d 'json={"json":"message"}' http://localhost:8888/debug.test
vagrant@vagrant-ubuntu-trusty-64:~$ tail /var/log/td-agent/td-agent.log
  </source>
  <source>
    type debug_agent
    bind 127.0.0.1
    port 24230
  </source>
</ROOT>
2016-07-01 06:08:24 +0000 [info]: listening fluent socket on 0.0.0.0:24224
2016-07-01 06:08:24 +0000 [info]: listening dRuby uri="druby://127.0.0.1:24230" object="Engine"
2016-07-01 06:13:06 +0000 debug.test: {"json":"message"}
```

## Docker容器张使用Fluentd作为log-driver

创建配置文件in_docker.conf

```
<source>
  type forward
  port 24224
  bind 0.0.0.0
</source>

<match *.*>
  type stdout
</match>
```

```
stop td-agent service

service td-agent stop

start td-agent process with in_docker.conf
```

```
vagrant@vagrant-ubuntu-trusty-64:~$ td-agent -c in_docker.conf
2016-07-01 08:22:16 +0000 [info]: reading config file path="in_docker.conf"
2016-07-01 08:22:16 +0000 [info]: starting fluentd-0.12.20
2016-07-01 08:22:16 +0000 [info]: gem 'fluent-mixin-config-placeholders' version '0.3.1'
2016-07-01 08:22:16 +0000 [info]: gem 'fluent-mixin-plaintextformatter' version '0.2.6'
2016-07-01 08:22:16 +0000 [info]: gem 'fluent-plugin-mongo' version '0.7.12'
2016-07-01 08:22:16 +0000 [info]: gem 'fluent-plugin-rewrite-tag-filter' version '1.5.4'
2016-07-01 08:22:16 +0000 [info]: gem 'fluent-plugin-s3' version '0.6.5'
2016-07-01 08:22:16 +0000 [info]: gem 'fluent-plugin-scribe' version '0.10.14'
2016-07-01 08:22:16 +0000 [info]: gem 'fluent-plugin-td' version '0.10.28'
2016-07-01 08:22:16 +0000 [info]: gem 'fluent-plugin-td-monitoring' version '0.2.2'
2016-07-01 08:22:16 +0000 [info]: gem 'fluent-plugin-webhdfs' version '0.4.1'
2016-07-01 08:22:16 +0000 [info]: gem 'fluentd' version '0.12.20'
2016-07-01 08:22:16 +0000 [info]: adding match pattern="*.*" type="stdout"
2016-07-01 08:22:16 +0000 [info]: adding source type="forward"
2016-07-01 08:22:16 +0000 [info]: using configuration file: <ROOT>
  <source>
    type forward
    port 24224
    bind 0.0.0.0
  </source>
  <match *.*>
    type stdout
  </match>
</ROOT>
2016-07-01 08:22:16 +0000 [info]: listening fluent socket on 0.0.0.0:24224

```

Docker和Fluented运行在同一主机的情况


```
docker run --log-driver=fluentd ubuntu echo "Hello Fluentd!"
```

检查fluentd日志

```
2016-07-01 08:22:16 +0000 [info]: using configuration file: <ROOT>
  <source>
    type forward
    port 24224
    bind 0.0.0.0
  </source>
  <match *.*>
    type stdout
  </match>
</ROOT>
2016-07-01 08:22:16 +0000 [info]: listening fluent socket on 0.0.0.0:24224
2016-07-01 08:25:12 +0000 docker.52260d5b5571: {"source":"stdout","log":"Hello Fluentd!","container_id":"52260d5b55719d314c1faeb3ff65be7b51a6492b339f577e48f2dfab0f2baf35","container_name":"/amazing_lamarr"}
```

跨主机收集日志数据

```
docker run --log-driver=fluentd --log-opt fluentd-address=192.168.50.101:24225 ubuntu echo "..."
```

使用自定义标签

```
docker run --log-driver=fluentd --log-opt fluentd-tag=docker.{{.ID}} ubuntu echo "..."
```

## 使用Elasticsearch存储日志数据

```
<source>
  type forward
  port 24224
  bind 0.0.0.0
</source>

<match *.*>
  type elasticsearch
  logstash_format true
  host "elasticsearch"
  port 9200
  flush_interval 5s
</match>
```

## 在Rancher中使用Fluentd

fluentd-address fluent-es.fluentd.rancher.internal

## 参考资料

- fluentd quick start: http://docs.fluentd.org/articles/quickstart#step1-installing-fluentd
- docker logging with fluentd:  http://www.fluentd.org/guides/recipes/docker-logging
- docker log driver fluentd https://docs.docker.com/engine/admin/logging/fluentd/
- fluentd config file:   http://docs.fluentd.org/articles/config-file
- collecting logs into elastic search and s3 http://www.fluentd.org/guides/recipes/elasticsearch-and-s3
- blog https://www.digitalocean.com/community/tutorials/how-to-centralize-your-docker-logs-with-fluentd-and-elasticsearch-on-ubuntu-14-04