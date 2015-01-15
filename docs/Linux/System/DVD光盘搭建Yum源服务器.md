# DVD光盘搭建Yum源服务器


因带宽占用和速度方面考虑在内网使用DVD光盘搭建一台Yum源服务器。

OS: CentOS 64位 6.5

准备所有文件和目录
```bash
mkdir -p /data/www/mirrors
mount /dev/cdrom /mnt #DVD1
cp -r /mnt/Packages /data/www/mirrors/
cp /mnt/RPM-GPG-KEY-CentOS-6 /data/www/mirrors/
umount /mnt/ #DVD1
mount /dev/cdrom /mnt #DVD2
cp /mnt/Packages/* /data/www/mirrors/Packages/
umount /mnt/ #DVD2
```

安装createrepo 并生成库
```bash
yum install -y createrepo
mount /dev/cdrom /mnt/ #DVD1
createrepo -g /mnt/repodata/*-c6-x86_64-comps.xml -o /data/www/mirrors/Packages/ /data/www/mirrors/Packages/
ls /data/www/mirrors/Packages/repodata/|wc -l
#检查 结果为9
```
注意：`*-c6-x86_64-comps.xml`是类似`b4e0b9342ef85d3059ff095fa7f140f654c2cb492837de689a58c581207d9632-c6-x86_64-comps.xml`的文件。

 搭建文件传输服务，这里我安装RPM的Apache
```bash
yum install -y httpd
sed -i s"#Options FollowSymLinks#Options Indexes FollowSymLinks#"g /etc/httpd/conf/httpd.conf
rm -f /etc/httpd/conf.d/*

cat >/etc/httpd/conf.d/yum.conf<<EOF 
<VirtualHost *:80>
DocumentRoot "/data/www/"
</VirtualHost>
EOF
```

编写Yum配置文件

```bash
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.local.bak
cat >/etc/yum.repos.d/CentOS-Local.repo<<EOF 
[Local]
name=CentOS-$releasever - Local
baseurl=http://10.0.0.5/mirrors/Packages/
gpgcheck=1
gpgkey=http://10.0.0.5/mirrors/RPM-GPG-KEY-CentOS-6
EOF
cp /etc/yum.repos.d/CentOS-Local.repo /data/www/mirrors/
```

至此Yum服务器配置完成，客户机只需要执行以下操作即可使用。
```bash
wget -P /etc/yum.repos.d/ "http://10.0.0.5/mirrors/CentOS-Local.repo"
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.local.bak
yum clean all
yum makecache
```