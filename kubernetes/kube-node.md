## 一键安装脚本 install_etcd.sh

```shell
#!/usr/bin/bash
echo "－－－－－－设置需要用到的变量－－－－－－－－－"
export workdir="/opt/k8s/work"

export node_h="192.168.255.152" // 对应不同结点的ip

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
cp kubectl kubelet kube-proxy /opt/k8s/kubernetes/bin/

echo "－－－－－－部署kube-node组件－－－－－－－－－"
cd ${workdir}/

echo "－－－－－－创建kubelet配置文件－－－－－－－－－"

cat << EOF | tee kubelet
KUBELET_OPTS="--logtostderr=true \
--v=4 \
--hostname-override=${node_h} \
--kubeconfig=/opt/k8s/kubernetes/cfg/kubelet.kubeconfig \
--bootstrap-kubeconfig=/opt/k8s/kubernetes/cfg/bootstrap.kubeconfig \
--config=/opt/k8s/kubernetes/cfg/kubelet.config \
--cert-dir=/opt/k8s/kubernetes/ssl \
--pod-infra-container-image=registry.cn-hangzhou.aliyuncs.com/google-containers/pause-amd64:3.0"
EOF

echo "－－－－－－创建kubeconfig配置文件－－－－－－－－－"
cat << EOF | tee kubelet.config
kind: KubeletConfiguration
apiVersion: kubelet.config.k8s.io/v1beta1
address: ${node_h}
port: 10250
readOnlyPort: 10255
cgroupDriver: cgroupfs
clusterDNS: ["10.254.0.10"]
clusterDomain: cluster.local.
failSwapOn: false
authentication:
  anonymous:
    enabled: true
EOF

echo "－－－－－－创建kube-proxy配置文件－－－－－－－－－"
cat << EOF | tee kube-proxy
KUBE_PROXY_OPTS="--logtostderr=true \
--v=4 \
--hostname-override=${node_h} \
--cluster-cidr=10.254.0.0/16 \
--kubeconfig=/opt/k8s/kubernetes/cfg/kube-proxy.kubeconfig"
EOF

cp kubelet kubelet.config kube-proxy /opt/k8s/kubernetes/cfg/

echo "－－－－－－创建kubelet bootstrapping kubeconfig －－－－－－－－－"
cd /opt/k8s/kubernetes/cfg

export BOOTSTRAP_TOKEN=34aadc883a29a7fe4c2e5af40899912c
export KUBE_APISERVER="https://192.168.255.173:6443"

echo "－－－－－－设置集群参数 －－－－－－－－－"
kubectl config set-cluster kubernetes --certificate-authority=/opt/k8s/kubernetes/ssl/ca.pem --embed-certs=true --server=${KUBE_APISERVER} --kubeconfig=bootstrap.kubeconfig

echo "－－－－－－设置客户端认证参数 －－－－－－－－－"
kubectl config set-credentials kubelet-bootstrap --token=${BOOTSTRAP_TOKEN} --kubeconfig=bootstrap.kubeconfig

echo "－－－－－－设置上下文参数 －－－－－－－－－"
kubectl config set-context default --cluster=kubernetes --user=kubelet-bootstrap --kubeconfig=bootstrap.kubeconfig

kubectl config use-context default --kubeconfig=bootstrap.kubeconfig
echo "－－－－－－创建kube-proxy kubeconfig文件－－－－－－－－－"
kubectl config set-cluster kubernetes --certificate-authority=/opt/k8s/kubernetes/ssl/ca.pem --embed-certs=true --server=${KUBE_APISERVER} --kubeconfig=kube-proxy.kubeconfig

kubectl config set-credentials kube-proxy --client-certificate=/opt/k8s/kubernetes/ssl/kube-proxy.pem --client-key=/opt/k8s/kubernetes/ssl/kube-proxy-key.pem --embed-certs=true --kubeconfig=kube-proxy.kubeconfig 

kubectl config set-context default --cluster=kubernetes --user=kube-proxy --kubeconfig=kube-proxy.kubeconfig

kubectl config use-context default --kubeconfig=kube-proxy.kubeconfig 
cd ${workdir}/

echo "－－－－－－创建kubelet.service  systemd配置文件－－－－－－－－－"

cat << EOF | tee kubelet.service  
[Unit]
Description=Kubernetes Kubelet
After=docker.service
Requires=docker.service
 
[Service]
EnvironmentFile=/opt/k8s/kubernetes/cfg/kubelet
ExecStart=/opt/k8s/kubernetes/bin/kubelet \$KUBELET_OPTS
Restart=on-failure
KillMode=process
 
[Install]
WantedBy=multi-user.target
EOF

echo "－－－－－－创建kube-proxy.service  systemd配置文件－－－－－－－－－"

cat << EOF | tee kube-proxy.service  
[Unit]
Description=Kubernetes Proxy
After=network.target
 
[Service]
EnvironmentFile=-/opt/k8s/kubernetes/cfg/kube-proxy
ExecStart=/opt/k8s/kubernetes/bin/kube-proxy \$KUBE_PROXY_OPTS
Restart=on-failure
 
[Install]
WantedBy=multi-user.target
EOF

```