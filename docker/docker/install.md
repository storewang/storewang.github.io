## docker离线安装
> 测试使用的离线包：centos7--docker-18.06.1-ce

1. 创建工作目录 `mkdir -p /opt/softs`
2. 下载docker二进制文件到工作目录 `docker-18.06.1-ce.tgz` ,并且解压: `tar -xzvf docker-18.06.1-ce.tgz`
3. 拷贝docker执行文件到 /usr/local/bin目录下
```
cp /opt/softs/docker/docker* /usr/local/bin/
```
