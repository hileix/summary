# 创建自定义 service

所有的 service 都在 `/etc/systemd/system` 目录下。

下面以创建一个监听 nodejs (egg.js)应用为例。

假设该应用名为 `patient-nodejs`:

1. 首先创建 `patient-nodejs.service` 文件:

```shell
touch patient-nodejs.service
```

2. 使用 vim 打开 `patinet-nodejs.service`，填入以下内容:

```servcie
[Unit]
Description=patient-nodejs-service

[Service]
Type=forking
WorkingDirectory=/home/wwwroot/wx.sxivf.com/patient-nodejs
ExecStart=/usr/bin/npm run start
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=patient-nodejs
User=root

[Install]
WantedBy=multi-user.target
```

- 使用 service 管理应用，可以实现崩溃重启。

3. 运行以下命令让下次重启开机时会启动服务:

```shell
systemctl enable patient-nodejs.service
```

4. 启动服务:

```shell
systemctl start patient-nodejs.service
```
