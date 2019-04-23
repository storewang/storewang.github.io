## kube-master安装
1. 进入工作目录 `cd /opt/k8s/work/`
2. 解压 `tar -zxvf kubernetes-server-linux-amd64.tar.gz`
3. 拷贝执行文件到目录
```shell
cd kubernetes_bin
cp kube-scheduler kube-apiserver kube-controller-manager kubectl /opt/k8s/kubernetes/bin/
```
## 部署kube-apiserver组件
1. 创建TLS Bootstrapping Token `cd ${workdir}/`
``` shell
cat << EOF | tee token.csv
34aadc883a29a7fe4c2e5af40899912c,kubelet-bootstrap,10001,"system:kubelet-bootstrap"
EOF

cp token.csv /opt/k8s/kubernetes/cfg/

```