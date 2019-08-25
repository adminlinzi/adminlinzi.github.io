Linux下终端会话管理工具：
- 基础环境：centos7 64bit，python3，pip <br>
安装pip : 
```
$ curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
$ python3 get-pip.py
```
&ensp;&ensp;&ensp;&ensp;或者:
```$ yum install python-pip``` <br>

- 工具介绍（包含了系统自带和额外部署）<br>
**TermRecord** : 可以对当前终端的会话操作进行录制，录制包含了文件的编辑内容，完整的操作动作都进行了录制，但是不会显示操作者使用了什么按键，
录制过程，会产生一些缓存文件，可以指定录制输出的文件的名字和文件类型，可以指定输出的路径。<br>
安装 : ```$ pip install TermRecord```  <br>
使用 : <br>
&ensp;&ensp;录制 ```$ TermRecord -o /tmp/test.html``` <br>
&ensp;&ensp;暂停退出录制 ```exit``` <br>
生成的test.html文件，这个文件可以放到任何地方，用任何浏览器打开，然后查看刚才录制的操作。 <br>

&ensp;&ensp;&ensp;&ensp;**asciinema** : 录制当前终端的会话，包括文件编辑的内容，录制输出的内容，可以在线查看，动态展示，可以直接复制录制到的内容，
无论是命令还是编辑的文件内容。可以发布到网上给大家直接分享。 <br>
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;[安装](https://asciinema.org/docs/installation) : ```# yum install asciinema -y```  <br>
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;[使用](https://asciinema.org/docs/usage) : ```# asciinema rec```   # 开启录制，录制结束以后，
输入exit或者Ctrl + D结束录制，录制以后，可以选择按enter键，上传到网上，分享给其他人，可以指定分享，也可以公开反向，也可以按Ctrl + C保存在本地，
只分享给指定的人。<br>

&ensp;&ensp;&ensp;&ensp;**script** :  <br>
记录用户在终端的操作，可以将记录存储为指定的文件。```scriptreplay``` 命令可以堆记录进行回放。默认保存在执行```script``` 所在目录的typescript文件。可以直接```cat```查看。
这是主流Linux发行版自带的命令。无法录制文件编辑的过程，除非编辑前后查看文件，才能记录下文件的编辑前后的区别。<br>
```# script 文件A```  录制内容将输出到文件A。<br>
输入```exit``` 退出当前的录制。<br>
更多用法```scripte -h```
-a ,追加录制。可以追加录制到已经存在的录制文件中。<br>
-c, 追加要执行的命令。<br>

&ensp;&ensp;&ensp;&ensp;[**showterm**](http://showterm.io) : 整体与asciinema很像。需要额外配置ruby环境才能安装和使用。可以把录制的内容，镶嵌在网页中。<br>

**screen** : centos自带的命令，是一个很好用的会话管理器。可以用于保持某个操作，例如正在监控某个进程，但是担心ssh断开以后，回不到原有的会话，可以使用screen解决这个问题。<br>
创建会话名字为test01的会话 : ```# screen -S test01``` <br>
Ctrl + D 退出当前会话。 <br>
查看当前有哪些会话 ： ```# screen -ls``` 可以看到有会话ID。 <br>
恢复会话 : ```# screen -r 会话id或者会话名字``` <br>

&ensp;&ensp;&ensp;&ensp;[**teleport**](https://github.com/gravitational/teleport) : 开源堡垒机系统,可以进行会话录制管理，可以通过web界面的方式，管理多个物理节点，
对多个物理节点进行ssh会话录制，当会话接入的时候，录制就开始，录制的时候，是通过操作触发录制，当没有操作的时候，录制是暂停的，区分免费社区版本，收费的pro版本，
功能有差异，支持多个平台，可以部署在容器上，受限于国内网络速度，下载的时候，速度会比较慢。<br>
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;安装 : 
```
# wget https://get.gravitational.com/teleport-v3.2.6-linux-amd64-bin.tar.gz
# cd teleport/
# bash install
# teleport start
```
<br>当前部署测试中出现异常，添加用户以后，web界面访问不到，防火墙都关闭了，可以ssh，可以ping通，但是web界面访问不到。待排查。 <br>

&ensp;&ensp;&ensp;&ensp;[**reptyr**](https://github.com/nelhage/reptyr) :  <br>
可以对进程进行管理，将某个后台进程调用到前台可见，切换ssh会话也可以实现进程的管理。开发者自称，该工具比screenify效果更好。支持主流的Linux发行版本，支持freeBSD，但是在FreeBSD环境下，
有待完善。支持多种硬件平台。更多最新的细节，可以通过官方文档查看。<br>
```$ reptyr PID```  通过ssh进程的PID调用会话。所有的输入和输出将都从当前的终端进行。如果进程是被隐藏在后台的，进程的调用需要```bg```和```fg```来控制。```bg```查看当前放在后台的进程。
特别是输入了```Ctrl + Z  ``` 所隐藏的进程。``` fg``` 可以把距离当前时间最近的一个隐藏到后台的进程调用出来。也可以使用``` fg N```，调用第N个后台的进程，N必须是正整数。N代表了排在后台的
第N个进程，通过``` ps -a ```确认进程排在了第几个。<br>
``` jobs -l  ``` 也可以查看，还能看到对应的PID。<br>
```ps -a  ``` 也可以查看到进程名字和对应的PID。<br>
安装和查看使用帮助：
```
$ git clone https://github.com/nelhage/reptyr.git
$ cd reptyr/
$ yum  install  gcc -y
$ make && make install
$ reptyr --help
```
<br>
