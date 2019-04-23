## apiserver systemd配置文件 `/usr/lib/systemd/system/kube-apiserver.service`
```shell
[Unit]
Description=Kubernetes API Server
Documentation=https://github.com/kubernetes/kubernetes

[Service]
EnvironmentFile=-/opt/k8s/kubernetes/cfg/kube-apiserver
ExecStart=/opt/k8s/kubernetes/bin/kube-apiserver $KUBE_APISERVER_OPTS
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
## kube-scheduler systemd配置文件 `/usr/lib/systemd/system/kube-scheduler.service`
```shell
[Unit]
Description=Kubernetes Scheduler
Documentation=https://github.com/kubernetes/kubernetes

[Service]
EnvironmentFile=-/opt/k8s/kubernetes/cfg/kube-scheduler
ExecStart=/opt/k8s/kubernetes/bin/kube-scheduler $KUBE_SCHEDULER_OPTS
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

## kube-scheduler systemd配置文件 `/usr/lib/systemd/system/kube-controller-manager.service`
```shell
[Unit]
Description=Kubernetes Controller Manager
Documentation=https://github.com/kubernetes/kubernetes
 
[Service]
EnvironmentFile=-/opt/k8s/kubernetes/cfg/kube-controller-manager
ExecStart=/opt/k8s/kubernetes/bin/kube-controller-manager $KUBE_CONTROLLER_MANAGER_OPTS
Restart=on-failure
 
[Install]
WantedBy=multi-user.target
```