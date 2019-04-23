## kubelet服务启动参数配置文件:`/opt/k8s/kubernetes/cfg/kubelet`

```
KUBELET_OPTS="--logtostderr=true \
--v=4 \
--hostname-override=192.168.255.152 \   //node结点的ip
--kubeconfig=/opt/k8s/kubernetes/cfg/kubelet.kubeconfig \
--bootstrap-kubeconfig=/opt/k8s/kubernetes/cfg/bootstrap.kubeconfig \
--config=/opt/k8s/kubernetes/cfg/kubelet.config \
--cert-dir=/opt/k8s/kubernetes/ssl \
--pod-infra-container-image=registry.cn-hangzhou.aliyuncs.com/google-containers/pause-amd64:3.0"
```

## kubelet服务启动参数配置文件:`/opt/k8s/kubernetes/cfg/kubelet.config`
```
kind: KubeletConfiguration
apiVersion: kubelet.config.k8s.io/v1beta1
address: 192.168.255.152  // node结点的ip
port: 10250
readOnlyPort: 10255
cgroupDriver: cgroupfs
clusterDNS: ["10.254.0.10"]
clusterDomain: cluster.local.
failSwapOn: false
authentication:
  anonymous:
    enabled: true
```

## kube-proxy服务启动参数配置文件:`/opt/k8s/kubernetes/cfg/kube-proxy`
```
KUBE_PROXY_OPTS="--logtostderr=true \
--v=4 \
--hostname-override=192.168.255.152 \ // node结点的ip
--cluster-cidr=10.254.0.0/16 \
--kubeconfig=/opt/k8s/kubernetes/cfg/kube-proxy.kubeconfig"
```