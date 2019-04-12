## docker连接harbor
1. 创建存放证书文件的目录(docker): ```mkdir -p /etc/docker/certs.d/harbor.docker.com/```
2. 从harbor服务器上拷贝证书文件到docker客户端机器上

```
scp -r /opt/cert/ca.crt root@192.168.255.152:/etc/docker/certs.d/harbor.docker.com/
```

3. 现在可以在客户端机器上的docker可以登录harbor私有仓库了

```
docker login harbor.docker.com
```