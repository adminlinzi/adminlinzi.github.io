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
重新make && make install 即可。<br>

``` 
# 阿里给出的安装方式
wget  http://zy-res.oss-cn-hangzhou.aliyuncs.com/server/extundelete-0.2.4.tar.bz2
yum -y install  bzip2  e2fsprogs-devel  e2fsprogs  gcc-c++  make    #安装相关依赖和库
tar -xvjf extundelete-0.2.4.tar.bz2
cd extundelete-0.2.4                                #进入程序目录
./configure                                         
make && make install

```

使用：
模拟练习，现在有俩磁盘，一个是系统盘，一个是数据盘，挂载到了/test目录，数据盘里面有个文件test.txt被误删除了。需要恢复。
配置模拟的环境,磁盘初始化配置这里不做记录，按照[阿里云的文档](https://help.aliyun.com/document_detail/25426.html?spm=a2c4g.11186623.2.16.af491e49zJHQT0#concept-jl1-qzd-wdb)参考即可：
``` 
mkdir /test                               #新建zhuyun目录
mount /dev/sdb1 /test                     #将磁盘挂载到zhuyun目录下
echo test > test.txt                      #写入测试文件
md5sum test.txt                           #查看记录md5值  
rm -rf test.txt
cd ~
fuser -k /test                            #结束使用某分区的进程树（确认没有资源占用的话，可以跳过此步）
umount /dev/sdb1                          #卸载磁盘，防止可以被恢复的数据被覆盖了。
extundelete --inode 2 /dev/sdb1           #为查找某i节点中的内容，使用2则说明为整个分区搜索，如果需要进入目录搜索，只须要指定目录I节点即可。这是可以看到删除的文件名和inode
/usr/local/bin/extundelete  --restore-inode 12  /dev/sdb1    #恢复删除的文件

```
备注：测试显示是失败的，找不到可以恢复的文件。