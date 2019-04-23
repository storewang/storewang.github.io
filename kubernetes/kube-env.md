# 概述
> etcd flannel kubernetes 离线安装,并使用https进行通信

* 测试服务器三台

服务器类型|系统|域名|IP|HOSTNAME
--|:--:|:--:|:--:|:--:
etcd1|centOS7|etcd1.docker.com|192.168.255.173|master1
etcd2|centOS7|etcd2.docker.com|192.168.255.152|node1
etcd3|centOS7|etcd2.docker.com|192.168.255.166|node2
flannel|centOS7|node1.docker.com|192.168.255.152|node1
flannel|centOS7|node2.docker.com|192.168.255.166|node2
kube-master|centOS7|master.docker.com|192.168.255.173|master1
kube-node1|centOS7|node1.docker.com|192.168.255.152|node1
kube-node2|centOS7|node2.docker.com|192.168.255.166|node2

* 工作目录 */opt/k8s/work*
#### etcd安装目录
1. 运行程序目录 `/opt/k8s/etcd/bin `
2. 服务启动参数配置文件存放目录 `/opt/k8s/etcd/cfg `
3. 证书文件存放目录 `/opt/k8s/etcd/ssl `
#### flannel安装目录
1. 运行程序目录 `/opt/k8s/kubernetes/bin `
2. 服务启动参数配置文件存放目录 `/opt/k8s/kubernetes/cfg `
3. 证书文件存放目录 `/opt/k8s/kubernetes/ssl `
#### kubernetes安装目录
1. 运行程序目录 `/opt/k8s/kubernetes/bin `
2. 服务启动参数配置文件存放目录 `/opt/k8s/kubernetes/cfg `
3. 证书文件存放目录 `/opt/k8s/kubernetes/ssl `

#### ssl工具安装
`把kubernetes_bin/cfssl,kubernetes_bin/cfssljson,kubernetes_bin/cfssl-certinfo上的可执行文件拷贝到/usr/local/bin目录下,并且添加可执行权限,chmod +x cfssl cfssljson cfssl-certinfo`

![soft-all](https://storewang.github.io/images/kube-soft.png "用到的软件")
