# 概述
> docker和 harbor 离线安装,并使用https进行通信

* 测试服务器两台

服务器类型|系统|域名|IP
--|:--:|:--:|--:
docker客户端|centOS7|client.docker.com|192.168.255.152
harbor|centOS7|harbor.docker.com|192.168.255.166

## 让非Root用户可以执行docker命令:
* 就是把用户添加进docker组中
1. 检查是否有docker组:
```
if [[ `cat /etc/group` =~ docker ]];then echo docker组已存在; else groupadd docker;fi
```
2. 加入docker组:
```
usermod -a -G docker wstore
```
3. 加入root组:
```
usermod -a -G root wstore
```
