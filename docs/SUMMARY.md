# Summary

* [概述](README.md)

## 第一章 docker离线安装
* [1.1 二进制安装](docker/install.md)
* [1.2 docker启动](docker/start.md)
    * [1.2.1 docker.service](docker/docker-conf.md)
    * [1.2.2 开机启动设置](docker/docker-start.md)  

## 第二章 harbor离线安装
* [2.1 生成证书文件](harbor/cert-make.md)
* [2.2 harbor离线安装](harbor/install.md)
    * [2.2.1 安装前准备](harbor/install-prev.md)
    * [2.2.2 安装](harbor/install-do.md)
    * [2.2.2 docker连接harbor](harbor/dokcer-harbor.md)

## 第三章 etcd离线安装
* [3.1 环境说明](kubernetes/kube-env.md)
* [3.2 etcd离线安装](kubernetes/kube-etcd.md) 
    * [3.2.1 生成证书文件](kubernetes/kube-etcd-cert.md) 
    * [3.2.2 二进制安装](kubernetes/kube-etcd-install.md)
    * [3.2.3 etcd服务配置](kubernetes/kube-etcd-service.md)
    * [3.2.4 etcd服务参数配置](kubernetes/kube-etcd-conf.md)

## 第四章 kube-master离线安装
* [4.1 环境说明](kubernetes/kube-env.md)
* [4.2 master离线安装](kubernetes/kube-master.md) 
    * [4.2.1 生成证书文件](kubernetes/kube-master-cert.md) 
    * [4.2.2 二进制安装](kubernetes/kube-master-install.md)
    * [4.2.3 apiserver服务配置](kubernetes/kube-master-service.md)
    * [4.2.4 apiserver服务参数配置](kubernetes/kube-master-conf.md)

## 第五章 kube-node离线安装
* [5.1 环境说明](kubernetes/kube-env.md)
* [5.2 node离线安装](kubernetes/kube-node.md) 
    * [5.2.1 生成证书文件](kubernetes/kube-node-cert.md) 
    * [5.2.2 二进制安装](kubernetes/kube-node-install.md)
    * [5.2.3 kubelet服务配置](kubernetes/kube-node-service.md)
    * [5.2.4 kubelet服务参数配置](kubernetes/kube-node-conf.md)
* [5.3 flannel离线安装](kubernetes/kube-flannel.md) 
    * [5.3.1 二进制安装](kubernetes/kube-flannel-install.md)
    * [5.3.2 flannel服务配置](kubernetes/kube-flannel-service.md)
    * [5.3.3 flannel服务参数配置](kubernetes/kube-flannel-conf.md)            