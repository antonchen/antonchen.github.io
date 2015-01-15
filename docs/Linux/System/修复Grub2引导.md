# 修复Grub2引导

使用安装盘进入Live CD,打开终端，切换到root用户
```bash
sudo su -
```
查看分区状态，根据分区大小来确认Linux分区
```bash
fdisk -l
```
挂载分区到当前Live CD
```bash
mount /dev/sdb2 /mnt #根分区
mount /dev/sdb1 /mnt/boot #如果没有单独boot分区此步骤忽略
```
安装Grub2到MBR
```bash
grub-install --root-directory=/mnt/boot /dev/sda
```
重启修复成功
```bash
reboot
```