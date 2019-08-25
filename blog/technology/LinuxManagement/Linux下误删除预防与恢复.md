Linux下防止误删除的方案
快照

克隆


Linux下误删除的恢复方案<br>
应急：<br>
在数据被误删除后，第一时间要做的是卸载被删除数据所在的磁盘或磁盘分区，并且限制其只读不可写。
因为将文件删除后，仅仅是将文件的inode结点中的扇区指针清零，实际文件还存储在磁盘上，
如果磁盘以读写模式挂载，这些已删除的文件的数据块就可能被操作系统重新分配出去，在这些数据块被新的数据覆盖后，
这些数据就真的丢失了，恢复工具也回力无天。所以，以只读模式挂载磁盘可以尽量降低数据块中数据被覆盖的风险，以提高恢复数据成功的几率。

使用工具进行恢复（如果你丢失数据的磁盘是A，那你最好选择A以外的磁盘安装工具，避免被删除数据的位置被新数据覆盖。）：<br>
[TestDisk](https://github.com/adminlinzi/adminlinzi.github.io/blob/master/blog/technology/LinuxManagement/testdisk-恢复Linux下误删除数据.md) <br>
[extundelete](https://github.com/adminlinzi/adminlinzi.github.io/blob/master/blog/technology/LinuxManagement/extundelete-恢复Linux下误删除数据.md)<br>
[debugfs]()<br>
[R-Linux]()<br>
[ext3grep]()<br>


