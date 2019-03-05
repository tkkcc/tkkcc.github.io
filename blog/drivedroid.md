## drivedroid

android下的小工具,可以将手机上的镜像文件挂载成usb,做成启动盘.多个镜像文件不需要写引导,点点就能切换

很久以前我爸说看到有装机的人不用u盘而用手机,那时我查了试了下就一直用上了

后来有次搞机后就启动不来,写进微pe,重启电脑,启动选项里找不到;写进archiso,起到一半报io错

我想会不会是手机系统问题,怎么以前就可以呢,于是刷回系统,写进pe,还是找不到,写进arch,启动成功.看来有的系统做了限制,或者软件不兼容

既然arch能启,那微pe怎么就不行呢,试了很久,无意中挂载选择CD-ROM,成功.但我明明记得以前用usb也行的...

写archiso
```sh
sudo dd bs=4M if=/F:/ghost/archlinux-2019.01.01-x86_64.iso of=/dev/sda status=progress oflag=sync
```

写微pe,先把iso提取出来
```sh
sudo dd bs=4M if=/F:/ghost/pe.iso of=/dev/sda status=progress oflag=sync
```