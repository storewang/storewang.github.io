## -键安装脚本 install_etcd.sh
```shell
#!/usr/bin/bash
echo "－－－－－－设置需要用到的变量－－－－－－－－－"
export workdir="/opt/k8s/work"
export etcd_n="etcd01"
export etcd_h="192.168.255.173"
export node1_h="192.168.255.173"
export node2_h="192.168.255.152"
export node3_h="192.168.255.166"

## 创建工作目录
echo "－－－－－－创建工作目录－－－－－－－－－"
mkdir /opt/k8s/etcd/{bin,cfg,ssl} -p
mkdir /opt/k8s/kubernetes/{bin,cfg,ssl} -p

## etcd ca配置
echo "－－－－－－etcd ca配置－－－－－－－－－"
cd /opt/k8s/etcd/ssl/

cat << EOF | tee ca-config.json
{
  "signing": {
    "default": {
      "expiry": "87600h"
    },
    "profiles": {
      "etcd": {
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
    "CN": "etcd CA",
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "L": "Beijing",
            "ST": "Beijing"
        }
    ]
}
EOF

## etcd server证书
echo "－－－－－－etcd server证书－－－－－－－－－"
cat << EOF | tee server-csr.json
{
    "CN": "etcd",
    "hosts": [
    "192.168.255.173",
    "192.168.255.152",
    "192.168.255.166"
    ],
    "key": {
        "algo": "rsa",
        "size": 2048
    },
    "names": [
        {
            "C": "CN",
            "L": "Beijing",
            "ST": "Beijing"
        }
    ]
}
EOF

## 生成etcd ca证书和私钥 初始化ca
echo "－－－－－－生成etcd ca证书和私钥 初始化ca－－－－－－－－－"

cfssl gencert -initca ca-csr.json | cfssljson -bare ca

ls

echo "－－－－－－生成server证书－－－－－－－－－"
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=etcd server-csr.json | cfssljson -bare server

ls

echo "－－－－－－etcd安装－－－－－－－－－"
cd ${workdir}/

tar -xvf etcd-v3.3.10-linux-amd64.tar.gz
cd etcd-v3.3.10-linux-amd64/
cp etcd etcdctl /opt/k8s/etcd/bin/

cd ${workdir}/
echo "－－－－－－配置etcd主文件－－－－－－－－－"
cat << EOF | tee etcd.conf
#[Member]
ETCD_NAME="${etcd_n}"
ETCD_DATA_DIR="/data1/etcd"
ETCD_LISTEN_PEER_URLS="https://${etcd_h}:2380"
ETCD_LISTEN_CLIENT_URLS="https://${etcd_h}:2379"
#[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="https://${etcd_h}:2380"
ETCD_ADVERTISE_CLIENT_URLS="https://${etcd_h}:2379"
ETCD_INITIAL_CLUSTER="etcd01=https://${node1_h}:2380,etcd02=https://${node2_h}:2380,etcd03=https://${node3_h}:2380"
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
EOF

cp etcd.conf /opt/k8s/etcd/cfg/

echo "－－－－－－配置etcd启动文件－－－－－－－－－"
mkdir -p /data1/etcd

cat << EOF | tee etcd.service
[Unit]
Description=Etcd Server
After=network.target
After=network-online.target
Wants=network-online.target
[Service]
Type=notify
WorkingDirectory=/data1/etcd/
EnvironmentFile=-/k8s/etcd/cfg/etcd.conf
ExecStart=/bin/bash -c "GOMAXPROCS=$(nproc) /k8s/etcd/bin/etcd --name=\"\${ETCD_NAME}\" --data-dir=\"\${ETCD_DATA_DIR}\" --listen-client-urls=\"\${ETCD_LISTEN_CLIENT_URLS}\" --listen-peer-urls=\"\${ETCD_LISTEN_P
EER_URLS}\" --advertise-client-urls=\"\${ETCD_ADVERTISE_CLIENT_URLS}\" --initial-cluster-token=\"\${ETCD_INITIAL_CLUSTER_TOKEN}\" --initial-cluster=\"\${ETCD_INITIAL_CLUSTER}\" --initial-cluster-state=\"\${ETCD_
INITIAL_CLUSTER_STATE}\" --cert-file=\"\${ETCD_CERT_FILE}\" --key-file=\"\${ETCD_KEY_FILE}\" --trusted-ca-file=\"\${ETCD_TRUSTED_CA_FILE}\" --client-cert-auth=\"\${ETCD_CLIENT_CERT_AUTH}\" --peer-cert-file=\"\${E
TCD_PEER_CERT_FILE}\" --peer-key-file=\"\${ETCD_PEER_KEY_FILE}\" --peer-trusted-ca-file=\"\${ETCD_PEER_TRUSTED_CA_FILE}\" --peer-client-cert-auth=\"\${ETCD_PEER_CLIENT_CERT_AUTH}\""
Restart=on-failure
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
EOF
```