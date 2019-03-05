## manifold

妙算，大疆机载板子，就是NVIDIA Jetson TK1板子，系统ubuntu14.04 armhf

嵌入式系统，资源少，配置也不同

### Download

收录不多且是老版，只能去官网下

妙算用户手册与系统源码：https://www.dji.com/cn/manifold/info#downloads

M100助手：https://www.dji.com/cn/matrice100/info#downloads

### 论坛不让进！？

> Sorry, your User Group () This operation can not be performed

[修好了](https://forum.dji.com/thread-144896-1-1.html)，我怎么还不能进？还得开隐身模式？

### Recovery

系统自带nvidia显卡驱动，手贱包管理器安一个，再卸载，开机炸，缺模块

按用户手册操作，先下源码：https://www.dji.com/cn/manifold/info#downloads

#### backup

全盘备份

```sh
cd ~/Linux_for_Tegra/bootloader
sudo ./nvflash --read APP system.img --bl ardbeg/fastboot.bin --go
```

挂载查看

```sh
mkdir -p ~/tt 
sudo mount system.img ~/tt
sudo umount ~/tt
```

#### reset

恢复出厂设置，[各种问题，和楼上几位一起被坑](https://gist.github.com/jetsonhacks/2717a41f7e60a3405b34#gistcomment-2642957)

**警告，会把`bootloader/system.img`删了，务必备份**

这是通过打包`rootfs`成镜像再刷入，如果先backup再reset，backup的就没了

```sh
cd ~/Linux_for_Tegra
sudo ./flash.sh jetson-tk1 mmcblk0p1
```

#### restore

恢复，`-r`使用已有的`system.img`

**有问题，使用ubuntu14.04**

```sh
cd ~/Linux_for_Tegra
sudo ./flash.sh –r jetson-tk1 mmcblk0p1
```

#### do

1. 系统炸了备份也丢了怎么办，找个**同批**的，备份再恢复
2. 不同批的gpio接口（摄像头）有问题，跑demo失败，尝试未成功

### Config

[dotfile](https://github.com/tkkcc/dotfile/tree/manifold)，manifold分支，未测试，非一键

```sh
cd & git clone https://github.com/tkkcc/dotfile.git
cd dotfile
git checkout manifold
vim link
```

1. 系统更新后lightdm不读`~/.zlogin`,`~/.zprofile`,读`~/.profile`,且仅使用sh解析，注意语法
2. urxvt插件位置不同
3. 写c++，vscode能跑，但c++插件需要[mono](https://github.com/Microsoft/vscode-cpptools/issues/164#issuecomment-395440422)，debug慢，还是用codeblocks，clion未尝试
4. `sudo init 0`关机，`sudo init 6`重启，速度快点

### Tips

1. 重启无hdmi输出（有时按reset会出现），拔插hdmi即可，不必重启
2. [M100摄像头](https://github.com/dji-sdk/Manifold-Cam)