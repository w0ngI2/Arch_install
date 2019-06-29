# arch_install
ArchLinux script Installer (安装脚本)

![Image text](https://raw.githubusercontent.com/BaSierL/arch_install/master/screenshot1.png)


# Auangz安装Arch Linux + Desktop 攻略

### 验证启动模式
```
[auroot@Archlinux ~]# ls /sys/firmware/efi/efivars
```

### 检查网络
```
[auroot@Arch ~]# ip link     #查看网卡设备
[auroot@Arch ~]# ip link set [网卡] up   #开启网卡设备
[auroot@Arch ~]# systemctl start dhcpcd      #开启DHCP服务
[auroot@Arch ~]# wifi-menu    #连接wifi
```

### 配置 Mirrort
```
[auroot@Arch ~]# vim /etc/pacman.d/mirrort
## China      # 中科大
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
## China      # 网易云
Server = https://mirrors.163.com/archlinux/$repo/os/$arch
## China      # 清华
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
```

### 磁盘分区

**分区命令**
```
[auroot@Arch ~]# cfdisk /dev/sd[a-z][0-9] 
```
**格式化命令**
```
[auroot@Arch ~]# mkfs.vfat  /dev/sd[a-z][0-9]    # efi/esp  fat32  
[auroot@Arch ~]# mkfs.ext4 /dev/sd[a-z][0-9]     # ext4 
[auroot@Arch ~]# mkfs.ext3 /dev/sd[a-z][0-9]     # ext3
[auroot@Arch ~]# mkswap /dev/sd[a-z][0-9]        # swap
  
```

### 挂载分区
```
[auroot@Arch ~]# swapon /dev/sd[a-z][0-9]         # 挂着swap
[auroot@Arch ~]# swapoff /dev/sd[a-z][0-9]        # 卸载swap  
[auroot@Arch ~]# mount /dev/sd[a-z][0-9] /mnt     # 挂着根目录
[auroot@Arch ~]# mkdir -p /mnt/boot/EFI           # 创建efi引导目录
[auroot@Arch ~]# mount /dev/sda1 /mnt/boot/EFI    # 挂着efi分区
```

### 安装系统
```
[auroot@Arch ~]# pacstrap -i /mnt base base-devel
```
```
[auroot@Arch ~]# genfstab -U /mnt >> /mnt/etc/fstab     # 创建fstab分区表，记得检查
[auroot@Arch ~]# arch-chroot /mnt /bin/bash             # chroot 进入创建好的系统
```

### 配置系统
**设置时区**
```
[auroot@Arch ~]# ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime     # 上海

[auroot@Arch ~]# hwclock --systohc       #运行 hwclock 以生成 /etc/adjtime
```

**本地化**
```
[auroot@Arch ~]# vim /etc/locale.gen    # 把以下复制到这个文件
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8  

[auroot@Arch ~]# locale-gen             # 生成 locale
```

**系统语言**
```
[auroot@Arch ~]# echo "LANG=en_US.UTF-8" > /etc/locale.conf       # 英文
[auroot@Arch ~]# echo "LANG=zh_CN.UTF-8" > /etc/locale.conf       # 中文
```

**主机名**
```
[auroot@Arch ~]# echo "Archlinux" > /etc/hostname
```
**配置GRUB**

执行passwd 给root设置一个密码。
安装grub工具，到这一步，一定要看清楚。
```
[auroot@Arch ~]# pacman -S vim grub efibootmgr
# 最后
[auroot@Arch ~]# grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=Archlinux
[auroot@Arch ~]# grub-mkconfig -o /boot/grub/grub.cfg
```

### 弄完重启
```
[auroot@Arch ~]# exit
[auroot@Arch ~]# swapoff /dev/sd[a-z][0-9]      #卸载swap
[auroot@Arch ~]# umount -R /mnt && reboot now   #卸载 根分区、efi分区
```
![Image text](https://github.com/BaSierL/arch_install/blob/master/README.md#%E6%A3%80%E6%9F%A5%E7%BD%91%E7%BB%9C)
