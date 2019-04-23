## etcd安装
1. 进入工作目录 `cd /opt/k8s/work/`
2. 解压 `tar -xvf flannel-v0.10.0-linux-amd64.tar.gz`
3. 拷贝执行文件到目录
```shell
mv flanneld mk-docker-opts.sh /opt/k8s/kubernetes/bin/
```