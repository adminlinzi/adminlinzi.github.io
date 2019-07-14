网卡bond类型
理论

操作





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

