## 生成kubernets证书与私钥
1. 创建工作目录
```shell
mkdir /opt/k8s/etcd/{bin,cfg,ssl} -p
mkdir /opt/k8s/kubernetes/{bin,cfg,ssl} -p
```
2. kubernetes ca配置
  * 2.1 /opt/k8s/kubernetes/ssl/ca-config.json
    ```json
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
    ```
  * 2.2 /opt/k8s/kubernetes/ssl/ca-csr.json
    ```json
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
    ```
  * 2.3 kubernetes ca证书和私钥 初始化ca
    ```shell
    cfssl gencert -initca ca-csr.json | cfssljson -bare ca -
    ``` 
3. 生成apiserver server证书
  * 3.1 apiserver server证书配置:/opt/k8s/kubernetes/ssl/server-csr.json
    ```json
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
    ```
  * 3.2 生成apiserver server证书
    ```shell
    cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=kubernetes server-csr.json | cfssljson -bare server
    ``` 
4. 生成kube-proxy server证书
  * 4.1 kube-proxy server证书配置:/opt/k8s/kubernetes/ssl/kube-proxy-csr.json
    ```json
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
    ```
  * 4.2 生成kube-proxy server证书
    ```shell
        cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=kubernetes kube-proxy-csr.json | cfssljson -bare kube-proxy
    ``` 
