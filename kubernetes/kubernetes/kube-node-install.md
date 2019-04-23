## etcd安装
1. 进入工作目录 `cd /opt/k8s/work/`
2. 解压 `tar -zxvf kubernetes-server-linux-amd64.tar.gz`
3. 拷贝执行文件到目录
```shell
cd kubernetes_bin
cp kubectl kubelet kube-proxy /opt/k8s/kubernetes/bin/
```