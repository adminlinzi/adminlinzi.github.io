Linux下防止误删除的方案



Linux下误删除的恢复方案

**TestDisk**
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

**extundelete**
资源:http://extundelete.sourceforge.net
编译的时候报错
```
insertionops.cc: In function ‘std::ostream& operator<<(std::ostream&, const ext2_inode&)’:
insertionops.cc:36:36: error: ‘const struct ext2_inode’ has no member named ‘i_dir_acl’; did you mean ‘i_file_acl’?
   os << "Directory ACL: " << inode.i_dir_acl << std::endl;
                                    ^~~~~~~~~
                                    i_file_acl
make[2]: *** [Makefile:437: extundelete-insertionops.o] Error 1
make[1]: *** [Makefile:268: all-recursive] Error 1
make: *** [Makefile:208: all] Error 2

```
编辑文件：extundelete-0.2.4/src/insertionops.cc
i_dir_acl 改为i_file_acl
重新make && make install 即可。
