# ext4+ntfs+efi三分区方案

vhd原生虚拟双启

![](/images/DeepinScreenshot_select-area_20180114083306.png)<!--more-->

### 前言

> linux下需要win虚拟机跑tim，黑魂需要原生启动win

基于这两种需求，传统方案是linux&win双系统，linux下再开个小虚拟机

目前的[精简xp](http://ak202020.blog.163.com/blog/#m=0&t=1&c=fks_084075087094081074084094082095086085086064083080087069082)装完虚拟机驱动占1.5G，开3G的虚拟硬盘够用了

ms官网的win7vdi扩容硬盘，占8G以上

> 一份软件不想装两份，两边都能跑

两个系统都使用绿色/便携软件

> 一个系统设置的修改不做两次 DRY

到这步我就没办法了，只能用一个win

**只维护一份系统，原生虚拟两种启动**，按win在实盘上或在虚拟硬盘上有两种方案

1. win在实盘上，需要虚拟机启动实盘上的win。通过virtualbox或vmware建立磁盘vmdk映射[参考](https://superuser.com/questions/325974/how-to-create-vmdk-file-in-virtualbox-for-adding-usb-as-a-new-os)，测试发现有写入问题，虚拟机下已写入但切到linux下或原生启动win则看不到，并且再原生启动win会出磁盘检测。猜测是linux与win同硬盘导致，做了些权限上的尝试没成功。对于多固态，这种方法可能不错
2. win在虚拟硬盘上，需要原生启动虚拟硬盘上的系统，利用vhd(x)来实现。建立vhd硬盘，在vhd里装系统，物理硬盘添加vhd引导

**基于第二种方案，一个vhd原生虚拟双启**

> vhd放哪

放ntfs上没问题，放ext4上可以吗？无忧的[讨论](http://bbs.wuyou.net/forum.php?mod=viewthread&tid=385239)

如果vhd放linux分区，就可以实现单分区，很理想，很美好，但我没尝试

> linux需要多分区吗

对于多固态多分区可以提速；对于单固态，把home分区提出来可以在不同发行版间切换，多个linux使用一个home分区，维护一份用户配置

> win软件放物理磁盘中还是vhd中

都可以，对于多个物理/虚拟硬盘win，软件肯定只用一份，而win可以挂载vhd，linux下虚拟机也可以挂载vhd和物理分区，所以软件放哪都可以共享

> 文件系统

win可以NTFS，FAT，ReFS

linux很多，看过评测后还用ext4

**目前方案：40G的ext4，80G的ntfs，一个fat(esp)，win软件和vhd在ntfs中**

![](/images/DeepinScreenshot_select-area_20180114111140.png)

### 操作

1. 建立vhd

   - win/pe用bootice

     ![](/images/v0.jpg)

   - linux下用virtualbox/vmware

     ![](/images/DeepinScreenshot_select-area_20180114114916.png)

2. vhd内系统安装与引导

   - win/pe下挂载vhd，使用磁盘精灵建分区表分区，使用ghost/winNTSetup/虚拟光驱等安装，系统镜像在ntfs上可以访问到

     ![](/images/DeepinScreenshot_select-area_20180114120150.png)

   - linux下使用虚拟机软件

     用virtualbox挂载pe和vhd，启动pe把系统镜像装入vhd

     ![](/images/DeepinScreenshot_select-area_20180114115602.png)

     > 系统镜像如何访问

     方法1：事先用UltraISO把系统镜像注入pe镜像，制作一个新pe

     ![](/images/DeepinScreenshot_select-area_20180114120849.png)

     打开wepe.iso镜像，将系统镜像0705.gho拖入，另存为iso，再用virtualbox引导即可访问到该系统镜像

     方法2：vmdk硬盘映射

     建立包含系统镜像分区的硬盘映射，使用如下命令或用vmware图形化操作

     ```
     VBoxManage internalcommands createrawvmdk -filename /path/to/file.vmdk -rawdisk /dev/sda
     ```

     虚拟机将vmdk挂载即可访问到该系统镜像

     ![](/images/DeepinScreenshot_select-area_20180114192344.png)

     > 利用共享文件夹

     共享文件夹需要虚拟机系统安装虚拟机驱动，如果pe/ramos强到可以安装驱动或许可行，但至少微pe1.2安装会卡住

3. 虚拟机启动vhd

   建立10G固定vhd再拆分一个，ntfs磁盘用共享文件夹挂载为F盘，软件/桌面/文档都在F盘中

   ![](/images/DeepinScreenshot_select-area_20180114193508.png)

   分配两个核(i74710MQ)+2G内存，流畅度足够

   挂载的F盘文件系统为vboxfs，其上的centbrowser不能正常运行，其他没问题

   注意到这里右上角有个白色方块，参考[后记](#后记)中的i3技巧

4. 实盘启动vhd

   ![](/images/v1.png)

   F盘显示为网络位置，实则为ntfs物理分区，不影响使用

### 后记

> 物理硬盘win访问linux分区

用磁盘精灵只读访问

> linux下虚拟机win访问linux分区

用虚拟机共享文件夹读写访问

用vmdk硬盘映射读写访问（同硬盘写无效）

>虚拟机没声音

没加入audio组

> linux下ntfs磁盘如何作共享文件夹

先挂载，再设置虚拟机共享文件夹为该挂载点

> i3技巧

虚拟机开始启动会开一个新窗，这时就可以关闭左侧的`oracle vm virtualbox manager`窗口，打开help中about选项，默认是浮动状态，将其拖至右上角，只留一个白色小角。**利用i3默认的鼠标移动切换焦点，当鼠标移至右上角时，焦点就到了about窗口，这就回到i3控制**，可以切其他工作区

> win镜像

[悠远](http://llssnn.blog.163.com/)的win8，不装独显驱动占用不到5G，从17年5月用到现在没遇到不能跑的程序和游戏