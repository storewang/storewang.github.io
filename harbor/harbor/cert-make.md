## 生成证书文件
> 现有docker版本连接私有镜像仓库使用的是https协议进行连接

1. 创建存放证书文件的目录：**mkdir -p /opt/cert**
2. 进入证书文件的目录**cd /opt/cert**执行下面的命令：
```
1. openssl req  -newkey rsa:4096 -nodes -sha256 -keyout ca.key -x509 -days 365 -out ca.crt -subj "/C=CN/L=shanghai/O=wangxy/CN=harbor-registry"
2. openssl req -newkey rsa:4096 -nodes -sha256 -keyout harbor.docker.com.key -out server.csr -subj "/C=CN/L=shanghai/O=wangxy/CN=harbor.docker.com"
3. openssl x509 -req -days 365 -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out harbor.docker.com.crt
```
**最终会生成下面六个文件**
１. ca.crt
2. ca.key
3. ca.srl
4. harbor.docker.com.crt
5. harbor.docker.com.key
6. server.csr
