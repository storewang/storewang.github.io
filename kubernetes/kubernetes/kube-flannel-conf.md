## flanneld服务启动参数配置文件:`/opt/k8s/kubernetes/cfg/flanneld`

```
FLANNEL_OPTIONS="--etcd-endpoints=https://192.168.255.173:2379,https://192.168.255.152:2379,https://192.168.255.166:2379 -etcd-cafile=/opt/k8s/etcd/ssl/ca.pem -etcd-certfile=/opt/k8s/etcd/ssl/server.pem -etc
d-keyfile=/opt/k8s/etcd/ssl/server-key.pem -etcd-prefix=/k8s/network"
```