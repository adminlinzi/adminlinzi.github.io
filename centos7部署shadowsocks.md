>参考:https://blog.51cto.com/zero01/2064660

[root@server ~]# curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py"  
[root@server ~]# python get-pip.py  
[root@server ~]# pip install --upgrade pip  
[root@server ~]# pip install shadowsocks  
[root@server ~]# vim /etc/shadowsocks.json  
```{
  "server": "0.0.0.0",
  "local_address": "127.0.0.1",
  "local_port": 1080,
  "port_password": {
    "8080": "填写密码",
    "8081": "填写密码"
  },
  "timeout": 600,
  "method": "aes-256-cfb"
}
```

备注：method为加密方法，可选aes-128-cfb, aes-192-cfb, aes-256-cfb, bf-cfb, cast5-cfb, des-cfb, rc4-md5, chacha20, salsa20, rc4, table
port_password为端口对应的密码，可使用密码生成工具生成一个随机密码

[root@server ~]# vim /etc/systemd/system/shadowsocks.service
```[Unit]
Description=Shadowsocks

[Service]
TimeoutStartSec=0
ExecStart=/usr/bin/ssserver -c /etc/shadowsocks.json

[Install]
WantedBy=multi-user.target
```

[root@server ~]# systemctl enable shadowsocks  
[root@server ~]# systemctl start shadowsocks  
[root@server ~]# firewall-cmd --zone=public --add-port=8080/tcp --permanent  
[root@server ~]# firewall-cmd --zone=public --add-port=8081/tcp --permanent  
[root@server ~]# firewall-cmd --reload
