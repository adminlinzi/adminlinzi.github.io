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

``` 
# 阿里给出的安装方式
wget  http://zy-res.oss-cn-hangzhou.aliyuncs.com/server/extundelete-0.2.4.tar.bz2
yum -y install  bzip2  e2fsprogs-devel  e2fsprogs  gcc-c++  make    #安装相关依赖和库
tar -xvjf extundelete-0.2.4.tar.bz2
cd extundelete-0.2.4                                #进入程序目录
./configure                                         
make && make install

```

