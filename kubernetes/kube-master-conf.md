## Apiserver配置文件:`/opt/k8s/kubernetes/cfg/kube-apiserver`

```
KUBE_APISERVER_OPTS="--logtostderr=true \
--v=4 \
--etcd-servers=https://${node1_h}:2379,https://${node2_h}:2379,https://${node3_h}:2379 \
--bind-address=${node_h} \
--secure-port=6443 \
--advertise-address=${node_h} \
--allow-privileged=true \
--service-cluster-ip-range=10.254.0.0/16 \
--enable-admission-plugins=NamespaceLifecycle,LimitRanger,ServiceAccount,ResourceQuota,NodeRestriction \
--authorization-mode=RBAC,Node \
--enable-bootstrap-token-auth \
--token-auth-file=/opt/k8s/kubernetes/cfg/token.csv \
--service-node-port-range=30000-50000 \
--tls-cert-file=/opt/k8s/kubernetes/ssl/server.pem  \
--tls-private-key-file=/opt/k8s/kubernetes/ssl/server-key.pem \
--client-ca-file=/opt/k8s/kubernetes/ssl/ca.pem \
--service-account-key-file=/opt/k8s/kubernetes/ssl/ca-key.pem \
--etcd-cafile=/opt/k8s/etcd/ssl/ca.pem \
--etcd-certfile=/opt/k8s/etcd/ssl/server.pem \
--etcd-keyfile=/opt/k8s/etcd/ssl/server-key.pem"
```

## kube-scheduler配置文件: `/opt/k8s/kubernetes/cfg/kube-scheduler`
```shell
KUBE_SCHEDULER_OPTS="--logtostderr=true --v=4 --master=127.0.0.1:8080 --leader-elect"
```

## kube-controller-manager配置文件: `/opt/k8s/kubernetes/cfg/kube-controller-manager`
```shell
KUBE_CONTROLLER_MANAGER_OPTS="--logtostderr=true \
--v=4 \
--master=127.0.0.1:8080 \
--leader-elect=true \
--address=127.0.0.1 \
--service-cluster-ip-range=10.254.0.0/16 \
--cluster-name=kubernetes \
--cluster-signing-cert-file=/opt/k8s/kubernetes/ssl/ca.pem \
--cluster-signing-key-file=/opt/k8s/kubernetes/ssl/ca-key.pem  \
--root-ca-file=/opt/k8s/kubernetes/ssl/ca.pem \
--service-account-private-key-file=/opt/k8s/kubernetes/ssl/ca-key.pem"
```