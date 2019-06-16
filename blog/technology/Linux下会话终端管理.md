Linux下终端会话管理工具：
- 基础环境：centos7 64bit，python3，pip <br>
安装pip : 
```
$ curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
$ python3 get-pip.py
```
&ensp;&ensp;&ensp;&ensp;或者:
```$ yum install python-pip``` 
<br>


- 终端录制：<br>
**TermRecord** : 可以对当前终端的会话操作进行录制，录制包含了文件的编辑内容，完整的操作动作都进行了录制，但是不会显示操作者使用了什么按键，录制过程，会产生一些缓存文件，可以指定录制输出的文件的名字和文件类型，可以指定输出的路径。<br>
安装 : ```$ pip install TermRecord```  <br>
使用 : <br>
&ensp;&ensp;录制 ```$ TermRecord -o /tmp/test.html``` <br>
&ensp;&ensp;暂停退出录制 ```exit``` <br>
生成的test.html文件，这个文件可以放到任何地方，用任何浏览器打开，然后查看刚才录制的操作。 <br>
<br>

&ensp;&ensp;&ensp;&ensp;**asciinema** : 录制当前终端的会话，包括文件编辑的内容，录制输出的内容，可以在线查看，动态展示，可以直接复制录制到的内容，无论是命令还是编辑的文件内容。可以发布到网上给大家直接分享。 <br>
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;[安装](https://asciinema.org/docs/installation) : ```# yum install asciinema -y```  <br>
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;[使用](https://asciinema.org/docs/usage) : ```# asciinema rec```   # 开启录制，录制结束以后，输入exit或者Ctrl + D结束录制，录制以后，可以选择按enter键，上传到网上，分享给其他人，可以指定分享，也可以公开反向，也可以按Ctrl + C保存在本地，只分享给指定的人。<br>
<br>


- 会话管理：<br>
screen : <br>
teleport : 可以进行会话录制管理，可以通过web界面的方式，管理多个物理节点，对多个物理节点进行会话录制，当会话接入的时候，录制就开始，录制的时候，是通过操作触发录制，当没有操作的时候，录制是暂停的，区分免费社区版本，收费的pro版本，功能有差异，支持多个平台，受限于国内网络速度，下载的时候，速度会比较慢。<br>
安装 : 
```
# wget https://get.gravitational.com/teleport-v3.2.6-linux-amd64-bin.tar.gz
# 
```