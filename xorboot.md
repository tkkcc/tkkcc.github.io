## xorboot

UEFI引导,需要启动这些
1. vhd中的win10
2. archlinux
3. 微pe镜像
4. archlinuxiso

虽然[archwiki](https://wiki.archlinux.org/index.php/Arch_boot_process#Boot_loader)给了很多方案,但没有一种能通吃以上所有.
很长一段时间我都用`grub/grubx64.efi`引导archlinuxiso镜像,用`microsoft/bootx64.efi`引导vhd与pe镜像,两个引导器就要两个配置文件,在efi文件夹下要分门别类三个文件夹
```sh
~ ls /esp/efi
arch/  grub/  microsoft/
```

xorboot作为全能引导器能够通吃上面4种需求
```sh
~ ls /esp/efi
arch/  xorboot/
```

### 配置

关于win,pe,vhd的引导点点就行,需要配的是linux与linuxiso

> `/dev/nvme0n1p1`是ext4文件系统,是archlinux根目录  
> `/dev/nvme0n1p3`是ntfs文件系统,是archlinux镜像所在分区


#### archlinux
efibootmgr里怎么写,grub里怎么写,这里也怎么写
```
系统类型: linux内核
文件名: /efi/arch/vmlinuz-linux
命令行: root=/dev/nvme0n1p1 quiet
initrd: /efi/arch/initramfs-linux.img
```

#### archlinuxiso

<!-- xorboot 引导 archlinuxiso 镜像 -->
在老版(2017-12-27)中只能用`linux内核`方式来引导镜像,方法与引导本地archlinux类似.为了拿到`vmlinuz`和`initramfs.img`需要先解压镜像到一个目录如`/archlinux`,再按如下参数配
```
系统类型: linux内核
文件名: /archlinux/ARCH/BOOT/X86_64/VMLINUZ
命令行: img_dev=/dev/nvme0n1p3 img_loop=/archlinux.iso archisobasedir=arch archisolabel=ARCH
initrd: /archlinux/ARCH/BOOT/X86_64/ARCHISO.IMG
```

在[新版](http://wuyou.net/forum.php?mod=viewthread&tid=157812&extra=&page=387)中,Pauly已修复,正常用`linuxiso`方式,不需要先解压,其中最关键的是kernel最后的.号
```
系统类型: linux iso
文件名: /ghost/archlinux-2019.01.01-x86_64.iso
kernel: /arch/boot/x86_64/vmlinuz.
命令行: img_dev=/dev/nvme0n1p3 img_loop=${isofile} archisobasedir=arch archisolabel=ARCH
initrd: /arch/boot/x86_64/archiso.img
```

### UEFI启动项

两个文件夹对应两个UEFI启动项,还需要添加到UEFI,在win下用Pauly的`bootice`或`xorboot`,在linux下用`efibootmgr`

```sh
sudo efibootmgr -d /dev/nvme0n1 -p 4 -L a -l /efi/arch/vmlinuz-linux -u "root=/dev/nvme0n1p1 rw initrd=/efi/arch/initramfs-linux.img" -c
sudo efibootmgr -d /dev/nvme0n1 -p 4 -L x -l /efi/xorboot/bootx64.efi -c
```
要重启进xorboot,可以用下面`reboot_to_win`,省的开机按热键选择,有的主板还关不掉蜂鸣声
```sh
#!/bin/sh
WIN_NUM=$(sudo efibootmgr|sed -n "s/.*\([0-9]\{4\}\).*x.*/\1/pI"|head -1)
sudo efibootmgr -n "$WIN_NUM"
reboot
```


