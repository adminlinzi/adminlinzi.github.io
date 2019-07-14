**需求**: 服务器管理中，为了在网卡管理部分更加省事一些，需要把网卡名字改的符合实际生产需求，
有些客户场景会有定制网卡名字的需求。特别是大客户或者特殊的网络供应商。

**理论分析**: <br>
https://www.cnblogs.com/zyd112/p/8143464.html <br>
http://benjr.tw/93340<br>

**操作配置**：
```
# 其他无关网卡配置文件记得删除。
[root@localhost ~]# cat /etc/sysconfig/network-scripts/ifcfg-eth0 
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="dhcp"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="eth0"  #我一开始没有修改这里，居然生效了
UUID="83beade7-a104-4472-8360-bb4623b31334"
DEVICE="eth0"  #修改这里就能生效
ONBOOT="yes"

[root@localhost ~]# cat /etc/sysconfig/grub 
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="resume=UUID=10fe91c0-4d6a-49c9-9a61-b774700b0d21 rhgb net.ifnames=0 biosdevname=0 quiet"   # net.ifnames=0 biosdevname=0  是添加的内容，其他都是自带的。
GRUB_DISABLE_RECOVERY="true"

[root@localhost ~]# grub2-mkconfig -o /boot/grub2/grub.cfg 

[root@localhost ~]# reboot

```