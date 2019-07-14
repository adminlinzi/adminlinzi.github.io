两种工作模式：新手模式和专家模式。
支持几乎所有的操作系统。
功能：
    修复分区表, 恢复已删除分区
    用FAT32备份表恢复启动扇区
    重建FAT12/FAT16/FAT32启动扇区
    修复FAT表
    重建NTFS启动扇区
    用备份表恢复NTFS启动扇区
    用MFT镜像表(MFT Mirror)修复MFT表
    查找ext2/ext3/ext4备份的SuperBlock
    从FAT,NTFS及ext2文件系统恢复删除文件
    从已删除的FAT,NTFS及ext2/ext3/ext4分区复制文件.

TestDisk 可恢复以下文件系统的丢失分区:
    BeFS ( BeOS )
    BSD disklabel ( FreeBSD/OpenBSD/NetBSD )
    CramFS, 压缩文件系统
    DOS/Windows FAT12, FAT16 和 FAT32
    Windows exFAT
    HFS, HFS+ 和 HFSX (Hierarchical File System)
    JFS (IBM's Journaled File System)
    Linux ext2, ext3 和ext4
    Linux LUKS 加密分区
    Linux RAID md 0.9/1.0/1.1/1.2
    RAID 1: 镜像(Mirror)
    RAID 4: 带容错的条带阵列
    RAID 5: 带分布式冗余信息的条带阵列
    RAID 6: 带分布式双冗余信息的条带阵列
    Linux Swap (版本1 和 2)
    LVM 和 LVM2, Linux逻辑卷管理器(Linux Logical Volume Manager)
    Mac partition map
    Novel NSS (Novell Storage Services)
    NTFS ( Windows NT/2000/XP/2003/Vista/2008 )
    ReiserFS 3.5, 3.6 和 4
    Sun Solaris i386 disklabel
    Unix文件系统-UFS and UFS2 (Sun/BSD/...)
    XFS, SGI's Journaled File System

下载：https://www.cgsecurity.org/wiki/TestDisk_Download
使用帮助手册：https://www.cgsecurity.org/wiki/TestDisk_CN
备注：帮助手册提供的编译源码的方式不管用，特别是在fedora环境，帮助手册提到的可用文件不存在。

操作: [参考阿里的文档](https://help.aliyun.com/document_detail/52046.html?spm=5176.229592.1220801.13.39fb3d92OyzrSh)
对于阿里的文档，有些疑惑，没看出来这个工具具体的价值，感觉就手工挂载一下分区就可以继续正常用了，特别是第一部分对于分区挂载丢失的处理。
第二部分有个恢复文件，只能将该分区现有的文件，copy到指定的目录，对于分区中已经被删除的文件，是无法恢复的，根本看不到被删除的文件的痕迹。
