## kubelet服务配置 `/usr/lib/systemd/system/kubelet.service`
```
[Unit]
Description=Kubernetes Kubelet
After=docker.service
Requires=docker.service
 
[Service]
EnvironmentFile=/opt/k8s/kubernetes/cfg/kubelet
ExecStart=/opt/k8s/kubernetes/bin/kubelet $KUBELET_OPTS
Restart=on-failure
KillMode=process
 
[Install]
WantedBy=multi-user.target
```
## kube-proxy服务配置 `/usr/lib/systemd/system/kube-proxy.service`
```
[Unit]
Description=Kubernetes Proxy
After=network.target
 
[Service]
EnvironmentFile=-/opt/k8s/kubernetes/cfg/kube-proxy
ExecStart=/opt/k8s/kubernetes/bin/kube-proxy $KUBE_PROXY_OPTS
Restart=on-failure
 
[Install]
WantedBy=multi-user.target
```