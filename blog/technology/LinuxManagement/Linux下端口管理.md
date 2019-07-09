> `#`代表注释。 默认采用centos的环境。

### **查询**

- 查看某个服务的默认端口（可以将服务名称替换为端口号进行筛选查看）: <br>
```$ grep 服务名称 /etc/services``` <br>
```$ getent services 服务名称```   # 相对更精确一些 <br>

- 查看进程和对应的端口：<br>
```netstat``` 命令 <br>
```$ netstat -lanp```  #查看详细的端口和进程，链接状态。 <br>
组合使用:<br>
查询指定的服务进程的PID:
```$ ps -ef | grep 服务名```  <br>
查询该PID所占用的端口：
```$ netstat -anpt | grep 刚才查询到的PID```

- 查看端口链接状态：<br>
```$ lsof -i``` #查看所有的网络链接，可以看到端口。 <br>
```$ lsof -i :22```  #查看谁正在使用22端口。 <br>

- 第三方工具 <br>
**[whatportis](https://github.com/ncrocfer/whatportis)**：  <br>
```$ pip install whatportis```  <br>
第一次查询的时候，需要在线自动导入数据库。 <br>
```$ whatportis ssh```   # 查询ssh服务的端口 <br>
体验相对好，展示的内容清晰，但是这是第三方工具，如果对安全有严格的要求，那就要进行安全审核以后再部署安装。详细的使用方法，可以查看帮助手册。 <br>

### **管理**
- 端口的开启 <br>
通过开启服务的方式，开启某个端口的使用:`service 服务名称 start` <br>
通过防火墙的管控，开启某个端口的使用:`firewall-cmd` 命令<br>

- 端口的关闭 <br>
通过关闭服务的方式，关闭某个端口的使用:
`service 服务名称 stop `<br>
通过防火墙的管控，关闭某个端口的使用:`firewall-cmd` 命令<br>
通过清理某个进程来关闭端口：<br>
```kill -9 进程的PID``` <br>
```kill -9 进程名字```<br>