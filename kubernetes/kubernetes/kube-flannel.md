## 一键安装脚本 install_flannel.sh 
```shell
#!/usr/bin/bash
echo "－－－－－－flannel安装 －－－－－－－－－"

tar -xvf flannel-v0.10.0-linux-amd64.tar.gz
mv flanneld mk-docker-opts.sh /opt/k8s/kubernetes/bin/
echo "－－－－－－配置flanneld －－－－－－－－－"
cat << EOF | tee flanneld
FLANNEL_OPTIONS="--etcd-endpoints=https://192.168.255.173:2379,https://192.168.255.152:2379,https://192.168.255.166:2379 -etcd-cafile=/opt/k8s/etcd/ssl/ca.pem -etcd-certfile=/opt/k8s/etcd/ssl/server.pem -etc
d-keyfile=/opt/k8s/etcd/ssl/server-key.pem -etcd-prefix=/k8s/network"
EOF

echo "－－－－－－flanneld systemd －－－－－－－－－"
cat << EOF | tee flanneld.service
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
EOF

cp flanneld /opt/k8s/kubernetes/cfg/
```