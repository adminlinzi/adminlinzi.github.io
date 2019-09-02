>参考：http://manpages.ubuntu.com/manpages/disco/man8/debugfs.8.html
https://www.kernel.org/doc/Documentation/filesystems/debugfs.txt
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux_for_real_time/7/html/tuning_guide/debugfs
https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_MRG/2/html/Realtime_Tuning_Guide/Mount_debugfs.html


简介：
debugfs是ext2/ext3/ext4文件系统调试器，无需额外安装组件即可使用，Linux内核自带。可以对文件系统的状态进行调整，调整的对象包含了块设备（/dev/sda1之类的）或一个文件。
在使用过程中，需要指定路径，可以通过指定inode号或者绝对路径的方式，区别在于，一个是维护某个块存储的根目录，一个是维护指定的绝对路径。
只能root权限使用。
通常使用 ```# mount -t debugfs none /sys/kernel/debug ``` 加载debugfs工具。
在红帽官方的指导文档中，通常配合其他工具一起使用。

使用方法：
  debugfs [ -DVwcin ] [ -b blocksize ] [ -s superblock ] [ -f cmd_file ] [ -R request ] [ -d data_source_device ] [ -z undo_file ] [ device ]

操作案例：
```
我擦，有点复杂，以后再更新，没找到合适的参考案例。老外的教程讲的都是涉及内核开发的使用，文件恢复的案例没能看到，看来还是要翻man手册。
```
