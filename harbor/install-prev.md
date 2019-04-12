## 安装前准备
1. 配置域名映射,在 /etc/hosts中加入:
```
192.168.255.166 harbor.docker.com
```
2. 创建工作目录 **mkdir -p /opt/soft**
3. 下载harbor二进制文件到工作目录:**harbor-offline-installer-v1.7.5.tgz**,并且解压:```tar -xzvf harbor-offline-installer-v1.7.5.tgz```
4. 进入harbor二进制目录: **cd /opt/soft/harbor** 修改配置文件:**harbor.cfg**
5. harbor.cfg修改的内容
```
// harbor主机的域名
hostname = harbor.docker.com                   
// 证书文件配置
ssl_cert = /opt/cert/harbor.docker.com.crt
ssl_cert_key = /opt/cert/harbor.docker.com.key
secretkey_path = /opt
// 数据库root的密码
db_password = root123
// harbor 后台页面登录的用户密码,用户名为admin（后台管理员）
harbor_admin_password = abc123
```
6. 执行: ```sh ./prepare```