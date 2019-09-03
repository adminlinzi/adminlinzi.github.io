>参考：https://manpages.debian.org/wheezy/ext3grep/ext3grep.8

ext3grep是一个简单的程序，用于恢复EXT3文件系统上的文件。
关键点：单独的分区，单独的挂载点，存在丢失的文件。
个人感受，和主流的几款恢复工具有着类似的地方，都是需要单独的对整个分区进行扫描操作，而且是对块设备进行操作。核心点就是对某个分区进行扫描做文件恢复。

操作主流程：

下载和安装：
需要根据自己系统的发行版，在网上直接搜资源包，centos和Ubuntu，Debian都有各自的部署方式，Debian系列最省事，centos和fedora需要单独下载包。
centos安装这个软件的时候，可能会需要```# yum install libstdc++.so.6 -y ```

使用：
一个独立的ext3分区A，一个单独的挂载点B，一个用来测试数据恢复的文件C。
当前A是挂载到B的，并且在B存在一个C。删除C，模拟出现文件丢失。
卸载挂载点B，扫描分区A：``` $ ext3grep --dump-name /dev/A ``` ，可以看到所有你删除过的文件以及它的绝对路径。
如果删除的文件较少且需要恢复全部：``` $ ext3grep --restore-all /dev/A ```
如果扫描到大量删除的文件，但是你只想恢复一部分，需要使用``` $ ext3grep --restore-file 带有绝对路径的待恢复的文件的名字 /dev/A  ```
如果需要恢复指定时间段的文件：``` $ ext3grep --restore-all --after `date -d 'Jan 1 2019 9:00am' '+%s'` --before `date -d 'Jan 5 2019 00:00am' '+%s'` /dev/A ```


恢复完成以后，会自动将一些文件默认恢复到指定的目录 RESTORED_FILES ,这目录默认在当前用户家目录下面：
```
$ cd RESTORED_FILES
$ ls
```

更详细操作指导: ``` $ man ext3grep ```