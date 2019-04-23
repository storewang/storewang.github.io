## etcd服务启动参数配置文件:`/opt/k8s/etcd/cfg/etcd.conf`

```
#[Member]
ETCD_NAME="etcd01"        //第台机器修改成自己的名称，唯一
ETCD_DATA_DIR="/data1/etcd" //目录需要提前创建好
ETCD_LISTEN_PEER_URLS="https://192.168.255.173:2380"   //修改成当前结点机器对应的IP
ETCD_LISTEN_CLIENT_URLS="https://192.168.255.173:2379" //修改成当前结点机器对应的IP
#[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="https://192.168.255.173:2380" // 修改成当前结点机器对应的IP
ETCD_ADVERTISE_CLIENT_URLS="https://192.168.255.173:2379" // 修改成当前结点机器对应的IP
ETCD_INITIAL_CLUSTER="etcd01=https://192.168.255.173:2380,etcd02=https://192.168.255.152:2380,etcd03=https://192.168.255.166:2380"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_INITIAL_CLUSTER_STATE="new"
#[Security]
ETCD_CERT_FILE="/opt/k8s/etcd/ssl/server.pem"
ETCD_KEY_FILE="/opt/k8s/etcd/ssl/server-key.pem"
ETCD_TRUSTED_CA_FILE="/opt/k8s/etcd/ssl/ca.pem"
ETCD_CLIENT_CERT_AUTH="true"
ETCD_PEER_CERT_FILE="/opt/k8s/etcd/ssl/server.pem"
ETCD_PEER_KEY_FILE="/opt/k8s/etcd/ssl/server-key.pem"
ETCD_PEER_TRUSTED_CA_FILE="/opt/k8s/etcd/ssl/ca.pem"
ETCD_PEER_CLIENT_CERT_AUTH="true"
```