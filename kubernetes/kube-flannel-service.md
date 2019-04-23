## flanneld服务配置 `/usr/lib/systemd/system/flanneld.service`
```
[Unit]
Description=Flanneld overlay address etcd agent
After=network-online.target network.target
Before=docker.service
 
[Service]
Type=notify
EnvironmentFile=/opt/k8s/kubernetes/cfg/flanneld
ExecStart=/opt/k8s/kubernetes/bin/flanneld --ip-masq \$FLANNEL_OPTIONS 
ExecStartPost=/opt/k8s/kubernetes/bin/mk-docker-opts.sh -k DOCKER_NETWORK_OPTIONS -d /run/flannel/subnet.env
Restart=on-failure
 
[Install]
WantedBy=multi-user.target
```