>参考文章:https://linux.cn/article-10960-1.html

查看可以安装的安全更新：
```
# yum updateinfo list available
```

<br>查看已经安装的安全更新软件包清单：
```
# yum updateinfo list security installed

Last metadata expiration check: 0:05:21 ago on Sat 15 Jun 2019 07:42:17 PM PDT.
FEDORA-2019-7ec378191e Moderate/Sec. kernel-5.1.6-200.fc29.x86_64
FEDORA-2019-83858fc57b Moderate/Sec. kernel-5.1.8-200.fc29.x86_64
FEDORA-2019-7ec378191e Moderate/Sec. kernel-core-5.1.6-200.fc29.x86_64
FEDORA-2019-83858fc57b Moderate/Sec. kernel-core-5.1.8-200.fc29.x86_64
FEDORA-2019-7ec378191e Moderate/Sec. kernel-modules-5.1.6-200.fc29.x86_64
FEDORA-2019-83858fc57b Moderate/Sec. kernel-modules-5.1.8-200.fc29.x86_64

```

<br>查看可以更新的安全补丁：
```
# yum updateinfo list security
# yum --security check-update
# yum info-sec
# yum updateinfo summary
```

<br>统计安装过的安全加固包的数量；
```
#yum updateinfo list security all | grep -w "i" | wc -l
```

<br>查看某个安装包的漏洞更新记录：
```
# rpm -q --changelog openssh | grep -i CVE
```

<br>自动化统计脚本，执行起来巨慢：
```
# vi /opt/scripts/security-check.sh
#!/bin/bash
echo "+-------------------------+"
echo "|Security Advisories Count|"
echo "+-------------------------+"
for i in Important Moderate Low
do
    sec=$(yum updateinfo list security installed | grep $i | wc -l)
    echo "$i: $sec"
done | column -t
echo "+-------------------------+"
```

<br>查看更多更新想过的操作：
```
# yum update --help | grep "adv"
```