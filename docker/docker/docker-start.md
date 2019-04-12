## 启动docker,并设置docker开机启动
1. 使配置文件生效　  `systemctl daemon-reload`
2. 启动docker　     `systemctl start docker`
3. 设置docker开机启动`systemctl enable docker`
4. 查看docker版本　　`docker version`