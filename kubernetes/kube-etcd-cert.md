## etcd ca配置

1. 创建工作目录

```shell
mkdir /opt/k8s/etcd/{bin,cfg,ssl} -p
mkdir /opt/k8s/kubernetes/{bin,cfg,ssl} -p
```

2. etcd ca配置
  * 2.1 /opt/k8s/etcd/ssl/ca-config.json

    ```json
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
    ```
* 2.2 /opt/k8s/etcd/ssl/ca-csr.json
  ```json
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
  ```
* 2.3 生成etcd ca证书和私钥 初始化ca
```shell
cfssl gencert -initca ca-csr.json | cfssljson -bare ca
``` 
3. 生成etcd server证书
  * 3.1 etcd server证书配置
  ```json
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
  ```
* 3.2 生成server证书
```shell
cfssl gencert -ca=ca.pem -ca-key=ca-key.pem -config=ca-config.json -profile=etcd server-csr.json | cfssljson -bare server
``` 