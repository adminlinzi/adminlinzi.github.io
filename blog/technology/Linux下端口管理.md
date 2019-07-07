> `#`代表注释。 默认采用centos的环境。

### **查询**

- 查看某个服务的默认端口（可以将服务名称替换为端口号进行筛选查看）: <br>
```$ grep 服务名称 /etc/services``` <br>
```$ getent services 服务名称```   # 相对更精确一些 <br>

- 第三方工具 <br>
**[whatportis](https://github.com/ncrocfer/whatportis)**：  <br>
```$ pip install whatportis```  <br>
第一次查询的时候，需要在线自动导入数据库。 <br>
```$ whatportis ssh```   # 查询ssh服务的端口 <br>
体验相对好，展示的内容清晰，但是这是第三方工具，如果对安全有严格的要求，那就要进行安全审核以后再部署安装。详细的使用方法，可以查看帮助手册。 <br>

### **管理**
端口的开启

端口的关闭

