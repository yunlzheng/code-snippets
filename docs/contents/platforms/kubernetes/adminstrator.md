# System Adminstrator

## ETCD管理

```
export NODE_IPS="172.31.31.156 172.31.31.58 172.31.23.108"
export ETCD_ENDPOINTS=https://172.31.31.156:2379,https://172.31.31.58:2379,https://172.31.23.108:2379
```

### 备份ETCD集群

创建集群快照

```
ETCDCTL_API=3 etcdctl --endpoints=$ETCD_ENDPOINTS snapshot save snapshotdb
```

验证快照

```
$ ETCDCTL_API=3 etcdctl --write-out=table snapshot status snapshotdb
+----------+----------+------------+------------+
|   HASH   | REVISION | TOTAL KEYS | TOTAL SIZE |
+----------+----------+------------+------------+
| 70bf14f6 |   163619 |       2374 | 2.8 MB     |
+----------+----------+------------+------------+
```

恢复快照

```
ETCDCTL_API=3 etcdctl --endpoints=$ETCD_ENDPOINTS snapshot restore <filename>
```

对于启用secure的etcd集群

```
ETCDCTL_API=3 etcdctl \
--endpoints=$ETCD_ENDPOINTS \
--cacert=/etc/kubernetes/ssl/ca.pem \
--cert=/etc/etcd/ssl/etcd.pem \
--key=/etc/etcd/ssl/etcd-key.pem \
snapshot save snapshotdb
```

