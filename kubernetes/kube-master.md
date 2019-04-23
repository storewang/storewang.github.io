## 一键安装脚本 `install_master.sh`
```shell
#!/usr/bin/bash
echo "－－－－－－设置需要用到的变量－－－－－－－－－"
export workdir="/opt/k8s/work"

export node_h="192.168.255.173"

export node1_h="192.168.255.173"
export node2_h="192.168.255.152"
export node3_h="192.168.255.166"

## 创建工作目录
echo "－－－－－－创建工作目录－－－－－－－－－"
mkdir /opt/k8s/etcd/{bin,cfg,ssl} -p
mkdir /opt/k8s/kubernetes/{bin,cfg,ssl} -p

## 生成kubernets证书与私钥
echo "－－－－－－制作kubernetes ca证书－－－－－－－－－"
cd /opt/k8s/kubernetes/ssl

cat << EOF | tee ca-config.json
{
  "signing": {
    "default": {
      "expiry": "87600h"
    },
    "profiles": {
      "kubernetes": {
         "expiry": "87600h",
         "usages": [
            "signing",
            "key encipherment",
            "server auth",
            "client auth"
        ]
      }
    }
  }
}
EOF
cat << EOF | tee ca-csr.json
{
    "CN": "kubernetes",
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "L": "Beijing",
            "ST": "Beijing",
            "O": "k8s",
            "OU": "System"
        }
    ]
}
EOF

cfssl gencert -initca ca-csr.json | cfssljson -bare ca -

ls

echo "－－－－－－制作apiserver证书－－－－－－－－－"
cat << EOF | tee server-csr.json
{
    "CN": "kubernetes",
    "hosts": [
      "10.254.0.1",
      "127.0.0.1",
      "192.168.255.173",
          "192.168.255.152",
          "192.168.255.166",
      "kubernetes",
      "kubernetes.default",
      "kubernetes.default.svc",
      "kubernetes.default.svc.cluster",
      "kubernetes.default.svc.cluster.local"
    ],
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "L": "Beijing",
            "ST": "Beijing",
            "O": "k8s",
            "OU": "System"
        }
    ]
}
EOF

cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=kubernetes server-csr.json | cfssljson -bare server

ls

echo "－－－－－－制作kube-proxy证书－－－－－－－－－"
cat << EOF | tee kube-proxy-csr.json
{
  "CN": "system:kube-proxy",
  "hosts": [],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "C": "CN",
      "L": "Beijing",
      "ST": "Beijing",
      "O": "k8s",
      "OU": "System"
    }
  ]
}
EOF

cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=kubernetes kube-proxy-csr.json | cfssljson -bare kube-proxy

ls
## 部署kubernetes server
cd ${workdir}/
echo "－－－－－－解压缩文件－－－－－－－－－"
## tar -zxvf kubernetes-server-linux-amd64.tar.gz
cd kubernetes_bin
cp kube-scheduler kube-apiserver kube-controller-manager kubectl /opt/k8s/kubernetes/bin/

echo "－－－－－－部署kube-apiserver组件 创建TLS Bootstrapping Token－－－－－－－－－"
cd ${workdir}/

cat << EOF | tee token.csv
34aadc883a29a7fe4c2e5af40899912c,kubelet-bootstrap,10001,"system:kubelet-bootstrap"
EOF

cp token.csv /opt/k8s/kubernetes/cfg/

echo "－－－－－－创建Apiserver配置文件－－－－－－－－－"
cat << EOF | tee kube-apiserver
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
EOF
echo "－－－－－－创建apiserver systemd配置文件－－－－－－－－－"
cat << EOF | tee kube-apiserver.service
[Unit]
Description=Kubernetes API Server
Documentation=https://github.com/kubernetes/kubernetes

[Service]
EnvironmentFile=-/opt/k8s/kubernetes/cfg/kube-apiserver
ExecStart=/opt/k8s/kubernetes/bin/kube-apiserver \$KUBE_APISERVER_OPTS
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

cp kube-apiserver /opt/k8s/kubernetes/cfg/

## 部署kube-scheduler组件 创建kube-scheduler配置文件
echo "－－－－－－创建kube-scheduler配置文件－－－－－－－－－"
cat << EOF | tee kube-scheduler
KUBE_SCHEDULER_OPTS="--logtostderr=true --v=4 --master=127.0.0.1:8080 --leader-elect"
EOF

echo "－－－－－－创建kube-scheduler systemd配置文件－－－－－－－－－"
cat << EOF | tee kube-scheduler.service
[Unit]
Description=Kubernetes Scheduler
Documentation=https://github.com/kubernetes/kubernetes

[Service]
EnvironmentFile=-/opt/k8s/kubernetes/cfg/kube-scheduler
ExecStart=/opt/k8s/kubernetes/bin/kube-scheduler \$KUBE_SCHEDULER_OPTS
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

cp kube-scheduler /opt/k8s/kubernetes/cfg/ 
echo "－－－－－－创建kube-controller-manager配置文件－－－－－－－－－"
cat << EOF | tee kube-controller-manager
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
EOF

echo "－－－－－－创建kube-controller-manager systemd配置文件－－－－－－－－－"
cat << EOF | tee kube-controller-manager.service 
[Unit]
Description=Kubernetes Controller Manager
Documentation=https://github.com/kubernetes/kubernetes
 
[Service]
EnvironmentFile=-/opt/k8s/kubernetes/cfg/kube-controller-manager
ExecStart=/opt/k8s/kubernetes/bin/kube-controller-manager \$KUBE_CONTROLLER_MANAGER_OPTS
Restart=on-failure
 
[Install]
WantedBy=multi-user.target
EOF
cp kube-controller-manager /opt/k8s/kubernetes/cfg/ 

ls -l /opt/k8s/kubernetes/cfg/

```