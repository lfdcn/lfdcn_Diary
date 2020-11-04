
# frps.service与frpc.service的设置
## 两个文件的推荐配置

### frps.service

> ExecStart的路径可以随便修改，只要文件路径对就行

```shell
$ cat frps.service
[Unit]
Description=Frp Server Service
After=network.target

[Service]
Type=simple
User=root
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/frp/frps -c /usr/local/frp/frps.ini

[Install]
WantedBy=multi-user.target
```

### frpc.service

> 同frps.service

```shell
[Unit]
Description=Frp Client Service
After=network.target

[Service]
Type=simple
User=root
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/frp/frpc -c /usr/local/frp/frpc.ini
ExecReload=/usr/local/frp/frpc reload -c /usr/local/frp/frpc.ini

[Install]
WantedBy=multi-user.target
```

