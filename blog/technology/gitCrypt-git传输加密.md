>资源官网:https://www.agwa.name/projects/git-crypt
- 简介：git-crypt支持对git存储库中的文件进行透明的加密和解密。您选择保护的文件在提交时加密，签出时解密。git-crypt允许您自由地共享一个包含公共和私有内容混合的存储库。git-crypt会更好的控制权限范围，因此没有秘密密钥的开发人员仍然可以克隆并提交带有加密文件的存储库。这使您可以将秘密材料(例如密钥或密码)存储在与代码相同的存储库中，而无需锁定整个存储库。

- 环境：Fedora29 64bit ，mini安装，中文环境，本地已经创建了git repo。
- 安装：
```
# yum install git -y
# yum install gcc -y
# yum install gcc-c++ -y
# yum install openssl-devel -y
# yum install make -y  #这个命令非必须。
# cd /opt
# git clone https://www.agwa.name/git/git-crypt.git
# make
# make install

```
- 使用： <br>
```$ cd repo```<br>
```$ git-crypt init``` <br>
```.gitattributes``` 这个文件是用来定义具体加密的文件清单。 <br>
配合GPG使用，还没看懂。后续再补。 <br>
导出密钥文件，发给你的协作者。 <br>
```$ git-crypt export-key /path/to/keyfile``` <br>
协作者收到密钥文件以后，需要对```git clone```来的文件进行解密。 <br>
```$ git-crypt unlock /path/to/keyfile``` <br>
然后本地就可以正常看到解密以后的文件了。