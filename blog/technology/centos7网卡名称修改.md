参考：https://blog.csdn.net/hzj_001/article/details/81587824
http://benjr.tw/93340

整理一下，区分操作，理论解析；

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
NAME="ens33"
UUID="83beade7-a104-4472-8360-bb4623b31334"
DEVICE="eth0"
ONBOOT="yes"

[root@localhost ~]# cat /etc/sysconfig/grub 
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="resume=UUID=10fe91c0-4d6a-49c9-9a61-b774700b0d21 rhgb net.ifnames=0 biosdevname=0 quiet"
GRUB_DISABLE_RECOVERY="true"

[root@localhost ~]# grub2-mkconfig -o /boot/grub2/grub.cfg 

[root@localhost ~]# reboot

