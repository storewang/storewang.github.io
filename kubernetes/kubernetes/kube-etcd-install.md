## etcd安装
1. 进入工作目录 `cd /opt/k8s/work/`
2. 解压 `tar -xvf etcd-v3.3.10-linux-amd64.tar.gz`
3. 拷贝执行文件到目录
```shell
cd etcd-v3.3.10-linux-amd64/
cp etcd etcdctl /opt/k8s/etcd/bin/
```