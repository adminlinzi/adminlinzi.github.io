**网卡bond类型**
**理论**
网卡绑定mode共有七种(0~6) bond0、bond1、bond2、bond3、bond4、bond5、bond6。
第一种模式：mod=0 ，即：(balance-rr) Round-robin policy（平衡抡循环策略） ,平衡负载模式，有自动备援，但需要”Switch”支援及设定（交换机配合）。
  特点：传输数据包顺序是依次传输（即：第1个包走eth0，下一个包就走eth1.一直循环下去，直到最后一个传输完毕），此模式提供负载平衡和容错能力；
如果一个连接或者会话的数据包从不同的接口发出的话，中途再经过不同的链路，在客户端很有可能会出现数据包无序到达的问题，而无序到达的数据包需要重新要求被发送，这样网络的吞吐量就会下降.
  如果想做成mode 0的负载均衡,仅仅设置这里options bond0 miimon=100 mode=0是不够的,与网卡相连的交换机必须做特殊配置（这两个端口应该采取聚合方式），
因为做bonding的这两块网卡是使用同一个MAC地址.从原理分析一下（bond运行在mode 0下）：
 mode 0下bond所绑定的网卡的IP都被修改成相同的mac地址，如果这些网卡都被接在同一个交换机，则交换机就出现同一个mac地址对应多个端口的问题。
正常情况下mac地址是全球唯一的，一个mac地址对应多个端口肯定使交换机迷惑了。所以 mode0下的bond如果连接到交换机，交换机这几个端口应该采取聚合方式（cisco称为 ethernetchannel，foundry称为portgroup，
华为是eth trunk）。
~~因为交换机做了聚合后，聚合下的几个端口也被捆绑成一个mac地址.所以，两个网卡接入不同的交换机即可。~~（这部分存在一些不清晰的点）这里也可以参考华为官方给出的标准方案参考。
 
第二种模式：mod=1，即： (active-backup) Active-backup policy（主-备份策略） ,自动备援模式，其中一条线若断线，其他线路将会自动备援。
  特点：只有一个设备处于活动状态，当一个宕掉另一个马上由备份转换为主设备。mac地址是外部可见得，
从外面看来，bond的MAC地址是唯一的，以避免switch(交换机)发生混乱。此模式只提供了容错能力；由此可见此算法的优点是可以提供高网络连接的可用性，但是它的资源利用率较低，
只有一个接口处于工作状态，在有 N 个网络接口的情况下，资源利用率为1/N
 
第三种模式：mod=2，即：(balance-xor) XOR policy（平衡策略）
  特点：基于指定的传输HASH策略传输数据包。缺省的策略是：(源MAC地址 XOR 目标MAC地址) % slave数量。其他的传输策略可以通过xmit_hash_policy选项指定，此模式提供负载平衡和容错能力
 
第四种模式：mod=3，即：broadcast（广播策略）
特点：在每个slave接口上传输每个数据包，此模式提供了容错能力
 
第五种模式：mod=4，即：(802.3ad) IEEE 802.3ad Dynamic link aggregation（IEEE 802.3ad 动态链接聚合）
特点：创建一个聚合组，它们共享同样的速率和双工设定。根据802.3ad规范将多个slave工作在同一个激活的聚合体下。
  外出流量的slave选举是基于传输hash策略，该策略可以通过xmit_hash_policy选项从缺省的XOR策略改变到其他策略。需要注意的是，
并不是所有的传输策略都是802.3ad适应的，尤其考虑到在802.3ad标准43.2.4章节提及的包乱序问题。不同的实现可能会有不同的适应性。
必要条件：
条件1：ethtool支持获取每个slave的速率和双工设定
条件2：switch(交换机)支持IEEE 802.3ad Dynamic link aggregation
条件3：大多数switch(交换机)需要经过特定配置才能支持802.3ad模式

第六种模式：mod=5，即：(balance-tlb) Adaptive transmit load balancing（适配器传输负载均衡）
特点：不需要任何特别的switch(交换机)支持的通道bonding。在每个slave上根据当前的负载（根据速度计算）分配外出流量。如果正在接收数据的slave出故障了，另一个slave接管失败的slave的MAC地址。
该模式的必要条件：ethtool支持获取每个slave的速率
 
第七种模式：mod=6，即：(balance-alb) Adaptive load balancing（适配器适应性负载均衡） ,平衡负载模式，有自动备援，不必”Switch”支援及设定。
  特点：该模式包含了balance-tlb模式，同时加上针对IPV4流量的接收负载均衡(receive load balance, rlb)，而且不需要任何switch(交换机)的支持。接收负载均衡是通过ARP协商实现的。
bonding驱动截获本机发送的ARP应答，并把源硬件地址改写为bond中某个slave的唯一硬件地址，从而使得不同的对端使用不同的硬件地址进行通信。
  来自服务器端的接收流量也会被均衡。当本机发送ARP请求时，bonding驱动把对端的IP信息从ARP包中复制并保存下来。
当ARP应答从对端到达时，bonding驱动把它的硬件地址提取出来，并发起一个ARP应答给bond中的某个slave。
使用ARP协商进行负载均衡的一个问题是：
每次广播ARP请求时都会使用bond的硬件地址，因此对端学习到这个硬件地址后，接收流量将会全部流向当前的slave。
这个问题可以通过给所有的对端发送更新 （ARP应答）来解决，应答中包含他们独一无二的硬件地址，从而导致流量重新分布。
当新的slave加入到bond中时，或者某个未激活的slave重新 激活时，接收流量也要重新分布。接收的负载被顺序地分布（round robin）在bond中最高速的slave上
当某个链路被重新接上，或者一个新的slave加入到bond中，接收流量在所有当前激活的slave中全部重新分配，通过使用指定的MAC地址给每个client发起ARP应答。

下面介绍的updelay参数必须被设置为某个大于等于switch(交换机)转发延时的值，从而保证发往对端的ARP应答不会被switch(交换机)阻截。
必要条件：
条件1：ethtool支持获取每个slave的速率；
条件2：底层驱动支持设置某个设备的硬件地址，从而使得总是有个slave(curr_active_slave)使用bond的硬件地址，同时保证每个bond中的slave都有一个唯一的硬件地址。如果curr_active_slave出故障，
它的硬件地址将会被新选出来的curr_active_slave接管。
其实mod=6与mod=0的区别：mod=6，先把eth0流量占满，再占eth1，….ethX；而mod=0的话，会发现2个口的流量都很稳定，基本一样的带宽。而mod=6，会发现第一个口流量很高，第2个口只占了小部分流量

**操作**

Linux网口绑定
不同的发行版，不同的版本，可以直接在系统官方文档支持上，查询到对应的操作指导，不建议死记硬背，如果系统改版了，最好始用官方给出的方案进行操作。
通过网口绑定(bond)技术,可以很容易实现网口冗余，负载均衡，从而达到高可用高可靠的目的。
测试环境如下：
centos6.5
2个物理网口分别是：eth0,eth1
绑定后的虚拟口是：bond0
服务器IP是：192.168.0.100
第一步，配置文件：
/etc/sysconfig/network-scripts/ifcfg-bond0
``` 
DEVICE=bond0
BOOTPROTO=none
ONBOOT=yes
IPADDR=192.168.0.100
NETMASK=255.255.255.0    #PREFIX=24 可以直接替代 NETMASK=255.255.255.0
NETWORK=192.168.0.0
BROADCAST=192.168.0.255   #BROADCAST广播地址
```

/etc/sysconfig/network-scripts/ifcfg-eth0
```
DEVICE=eth0
BOOTPROTO=none
MASTER=bond0
SLAVE=yes
```
 
/etc/sysconfig/network-scripts/ifcfg-eth1
```
DEVICE=eth1
BOOTPROTO=none
MASTER=bond0
SLAVE=yes
```

第二步，修改modprobe相关设定文件，并加载bonding模块：
1.创建一个加载bonding的文件，仅用于bond0，该bond使用的mode取决于配置文件定义。
/etc/modprobe.d/bonding.conf
```
alias bond0 bonding
options bonding mode=0 miimon=200
```
2.加载模块(重启系统后就不用手动再加载了)
modprobe bonding  # 这里的bonding指的是刚才配置的bonding.conf

3.确认模块是否加载成功：
lsmod | grep bonding
bonding 100065 0
第三步，重启一下网络，然后确认一下状况：
/etc/init.d/network restart
cat /proc/net/bonding/bond0

ifconfig | grep HWaddr
任意拔掉一根网线，然后再访问你的服务器，看网络是否还是通的。关掉对应的交换机端口也可以。

第四步，系统启动自动绑定、增加默认网关：
/etc/rc.d/rc.local
ifenslave bond0 eth0 eth1
route add default gw 192.168.0.1
#如可上网就不用增加路由，0.1地址按环境修改.

注意：前面只是2个网口绑定成一个bond0的情况，如果我们要设置多个bond口，比如物理网口eth0和eth1组成bond0，eth2和eth3组成bond1，
那么网口设置文件的设置方法和上面第1步讲的方法相同，只是/etc/modprobe.d/bonding.conf的设定就不能直接叠加内容。
正确的设置方法有2种：
- 第一种,多个bond口的模式设成相同的：
alias bond0 bonding
alias bond1 bonding
options bonding max_bonds=2 miimon=200 mode=1
- 第二种，不同的bond口的mode设成不一样：
alias bond0 bonding
options bond0 miimon=100 mode=1
install bond1 /sbin/modprobe bonding -o bond1 miimon=200 mode=0

简单的介绍一下上面在加载bonding模块的时候，options里的一些参数的含义：
- miimon 监视网络链接的频度，单位是毫秒，我们设置的是200毫秒。miimon是用来进行链路监测的。 比如:miimon=100，那么系统每100ms监测一次链路连接状态，如果有一条线路不通就转入另一条线路。
- bonding只能提供链路监测，即从主机到交换机的链路是否接通。如果只是交换机对外的链路down掉了，而交换机本身并没有故障，那么bonding会认为链路没有问题而继续使用。
- max_bonds 配置的bond口个数
- mode bond模式，主要有以下几种，在一般的实际应用中，0和1用的比较多.

除了上面的操作，还有更省事的配置bond的办法。
1、确定内核是否支持bonding
执行：cat /boot/config-3.0.13-0.27-default |grep -i bonding
例，执行：
cat /boot/config-2.6.32-431.el6.x86_64 |grep -i bonding
CONFIG_BONDING=m
CONFIG_BONDING为m表示支持bonding
配置文件根据自己实际环境，table键自动补齐。
2、确保网线已经连接好。
cd /etc/sysconfig/network-scripts
把原有的网卡配置文件mv成bak后缀的文件或者其他后缀的，总之让他不生效即可。可以直接mv到其他目录。
也可以直接删除，前提是，你以后想恢复文件的时候能自己找回一样的配置。
3、配置bond0文件
``` 
DEVICE=bond0
BOOTPROTO=static
NM_CONTROLLED=no
ONBOOT=yes
IPADDR=192.168.1.11
NETMASK=255.255.255.0   # 或者 PREFIX=24 也可以
GATEWAY=192.168.1.1
USERCTL=no
BONDING_OPTS="mode=1 miimon=100"
```
 
这里的mode是1，请注意，文件名字叫bond0，使用的是mode 1模式。
 
4、修改加入bond0的两个网卡的配置
```
DEVICE=eth6
BOOTPROTO=none
MASTER=bond0
SLAVE=yes
ONBOOT=yes
USERCTL=no
```
 
 ```
DEVICE=eth7
BOOTPROTO=none
MASTER=bond0
SLAVE=yes
ONBOOT=yes
USERCTL=no
```
根据自己实际情况操作，你的网卡未必叫这个，如果是rhel7系列的，你需要自己对网卡修改名字，并且配置永久生效，后面我单独写了网卡名称管理的部分。
 
5、关闭NetworkManager服务
service NetworkManager stop
service NetworkManager status   #查看验证
chkconfig NetworkManager off
chkconfig --list | grep NetworkManager  #查看验证
6、reboot
重启后查看配置情况
执行cat /proc/net/bonding/bond0
ifconfig bond0 #查看配置后状态。



---------------------------------------------------------------------------------------

网卡名称管理
理论：
服务器通常有多块网卡，有板载集成的网卡，也有插在PCIE插槽的网卡。
每个网卡的接口，都有一个逻辑上的名字，在系统上呈现出来，就是eth0之类的网口名字。

在centos7中，受到systemd的管理影响，需要biosdevname软件包提供管理功能，网卡命名规则如下：
``` 
Scheme 1: 如果从BIOS中能够取到可用的，板载网卡的索引号，则使用这个索引号命名，例如: eno1，如不能则尝试Scheme 2
Scheme 2: 如果从BIOS中能够取到可以用的，网卡所在的PCI-E热插拔插槽的索引号，则使用这个索引号命名，例如: ens1，如不能则尝试Scheme 3
Scheme 3：如果能拿到设备所连接的物理位置信息，则使用这个信息命名，例如:enp2s0，如不能则尝试Scheme 5
Scheme 5：传统的kernel命名方法，例如: eth0，这种命名方法的结果不可预知的，即可能第二块网卡对应eth0，第一块网卡对应eth1。

Scheme 4 使用网卡的MAC地址来命名，这个方法一般不使用。
```

命名规范：
**net.ifnames**命名规范为:设备类型+设备位置+数字
设备类型：
en 表示Ethernet
wl 表示WLAN
ww 表示无线广域网WWAN

|  format   | description  |
|  ----  | ----  |
| ```o<index>```  | on-board device index number ,集成设备索引号|
| ```s<slot>[f<functoin>][d<dev_id>]```  | hotplug slot index number,扩展槽的索引号  |
| ```x<MAC>``` | MAC address ,基于MAC进行命名|
| ```p<bus>s<slot>[f<function>][d<dev_id>]```| PCI geographical location ,PCI扩展总线 |
|```p<bus>s<slot>[f<function>][u<port>][...][c<config>][i<interface>] ```| USB port numberchain |



**biosdevname**的命名规范

|  device   | name  |
|  ----  | ----  |
| Embedded network interface(LOM) | ```em[1234....]``` |
| PCI card network interface | ```p<slot>p<ethernet port>``` |
| Virtual function | ```p<slot>p<ethernet port>_<virtual_interface>``` |

systemd中rule的实际执行顺序:
按照如下顺序执行udev的rule
1./usr/lib/udev/rules.d/60-net.rules
2./usr/lib/udev/rules.d/71-biosdevname.rules
3./lib/udev/rules.d/75-net-description.rules
4./usr/lib/udev/rules.d/80-net-name-slot.rules

``` 
60-net.rules 
使用/lib/udev/rename_device这个程序，去查询/etc/sysconfig/network-scripts/下所有以ifcfg-开头的文件
如果在ifcfg-xx中匹配到HWADDR=xx:xx:xx:xx:xx:xx参数的网卡接口
则选取DEVICE=yyyy中设置的名字作为网卡名称。

71-biosdevname.rules
如果系统中安装了biosdevname，且内核参数未指定biosdevname=0，且上一步没有重命名网卡，则按照biosdevname的命名规范，从BIOS中取相关信息来命名网卡。
主要是取SMBIOS中的type 9 (System Slot) 和 type 41 (Onboard Devices Extended Information)
不过要求SMBIOS的版本要高于2.6，且系统中要安装biosdevname程序。

75-net-description.rules
udev通过检查网卡信息，填写如下这些udev的属性值
ID_NET_NAME_ONBOARD
ID_NET_NAME_SLOT
ID_NET_NAME_PATH
ID_NET_NAME_MAC

80-net-name-slot.rules
如果在60-net.rules ，71-biosdevname.rules这两条规则中没有重命名网卡，且内核未指定net.ifnames=0参数
则udev依次尝试使用以下属性值来命名网卡，如果这些属性值都没有，则网卡不会被重命名。
ID_NET_NAME_ONBOARD
ID_NET_NAME_SLOT
ID_NET_NAME_PATH

上边的71-biosdevname.rules 是实际执行biosdevname的policy
75-net-description.rules和80-net-name-slot.rules实际执行Scheme 1,2,3

根据上述的过程，可见网卡命名受 biosdevname和net.ifnames这两个内核参数影响。
```

biosdevname/net.ifnames应用
这两个参数都可以在grub配置中提供，biosdevname=0是系统默认值（dell服务器默认是1），net.ifnames=1是系统默认值。

```
# 这个文件的路径没有找到
#vi /boot/grub/grub.conf
kernel /boot/vmlinuz biosdevname=1
initrd /boot/initrd.img
```

前面说的Scheme的策略顺序是系统默认的。
如系统BIOS符合要求，且系统中安装了biosdevname，且biosdevname=1启用，则biosdevname优先；
如果BIOS不符合biosdevname要求或biosdevname=0，则net.ifnames的规则优先。
如果用户自己定义了udev rule来修改内核设备名字，则用户规则优先。

内核参数组合使用的时候，其结果如下：
默认内核参数(biosdevname=0，net.ifnames=1):  网卡名 "enp5s2"
biosdevname=1，net.ifnames=0：网卡名 "em1"
biosdevname=0，net.ifnames=0：网卡名 "eth0"

操作

如果系统刚开始安装，且希望使用 eth0 这样的传统名称，需要在内核启动参数添加如下内容：
```net.ifnames=0 biosdevname=0```

cobbler profile 修改方法：
```
cobbler profile edit --name=Centos-7.3-x86_64 --kopts='net.ifnames=0 biosdevname=0'
```

如果系统已经安装，希望改成eth0这样的名称，那么执行如下操作需要：
1.修改grub2启动参数，在GRUB_CMDLINE_LINUX的中加上```net.ifnames=0 biosdevname=0```的参数
```
vi /etc/sysconfig/grub
GRUB_CMDLINE_LINUX=”rd.lvm.lv=vg0/swap vconsole.keymap=us crashkernel=auto vconsole.font=latarcyrheb-sun16 net.ifnames=0 biosdevname=0 rd.lvm.lv=vg0/usr rhgb quiet”
```

2.重新加载到启动中
```grub2-mkconfig -o /boot/grub2/grub.cfg```

3.重新对网卡配置文件进行命名（网卡文件全部重命名，顺便修改配置文件NAME、DEVICE的名称）
```mv /etc/sysconfig/network-scripts/ifcfg-enp0s3 /etc/sysconfig/network-scripts/ifcfg-eth0```
4.reboot重启生效

>参考文档：
https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Networking_Guide/ch-Consistent_Network_Device_Naming.html
https://bugzilla.redhat.com/show_bug.cgi?id=965718

