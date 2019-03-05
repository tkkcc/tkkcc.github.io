## arch

### i3

高效，高可定制的平铺式窗口管理器

> dmenu，rofi，ulauncher以及其他electron系快速启动软件都没有altrun顺手，i3弥补了这一遗憾

1. 通过mode特性可实现win下altrun功能。和win下一样用grave键（esc下面的键）触发模式，接下来所有键都可触发命令再回默认模式

   ```
   set $rua_mode rua
   mode "$rua_mode" {
           bindsym 1 $e $web, mode "default"
           bindsym space $e $term, mode "default"
   		bindsym 2 $e $term, mode "default"
   		bindsym 3 $e $fm, mode "default"
   		bindsym 4 $e $term -e ranger, mode "default"
   		bindsym v $e code, mode "default"
   		bindsym i $e intellij-idea-ultimate-edition, mode "default"
   		bindsym p $e pycharm, mode "default"
   		bindsym l $e eclipse, mode "default"
   		bindsym g $e gpick, mode "default"
   		bindsym c $e clion, mode "default"
   		bindsym n $e netease-cloud-music, mode "default"
   		bindsym b $e sudo baobab, mode "default"
   		bindsym x $e xfce4-terminal, mode "default"
   		bindsym h $e xfce4-terminal -e 'ssh do1', mode "default"
   		bindsym 9 $e wps, mode "default"
   		bindsym o $e virtualbox, mode "default"
   		bindsym t $e typora, mode "default"

           bindsym f fullscreen, mode "default"

           bindsym s layout stacking, mode "default"
           bindsym w layout tabbed, mode "default"
           bindsym e layout toggle split, mode "default"

   		bindsym q kill, mode "default"
   		bindsym a floating toggle, mode "default"
   		
           bindsym F1 mode $wifi_mode
           bindsym F9 $e i3exit logout, mode "default"
           bindsym F12 $e i3exit suspend, mode "default"
   #bindsym h $e i3exit hibernate, mode "default"
           bindsym F10 $e i3exit reboot, mode "default"
           bindsym F11 $e i3exit shutdown, mode "default"

   		bindsym grave $e clion, mode "default"
   		bindsym Return mode "default"
   		bindsym Escape mode "default"
   		bindsym tab mode "default"
   }
   bindsym grave mode "$rua_mode"
   ```

2. rua下用f1切到二级启动（rua1），分配1234切换wifi，电源模式

   ```
   set $wifi_mode rua1
   mode "$wifi_mode" {
           bindsym 1 $e netctl-auto switch-to pcdn, mode "default"
           bindsym 2 $e netctl-auto switch-to hit, mode "default"
           bindsym 3 $e sudo tlp ac, mode "default"
           bindsym 4 $e sudo tlp bat, mode "default"
           bindsym grave  mode "default"
           bindsym Return mode "default"
           bindsym Escape mode "default"
           bindsym Tab mode "default"
   }
   ```

3. win+f1234调节音量亮度。如果用fn键加热键来调节，手腕需要抬高而且不看键盘按不准，改后方便许多，与ahk统一

   ```
   bindsym $mod+F3 $e mybright 
   bindsym $mod+F4 $e mybright -d 
   bindsym $mod+F1 $e "pactl set-sink-mute 0 false ; pactl set-sink-volume 0 +2%"
   bindsym $mod+F2 $e "pactl set-sink-mute 0 false ; pactl set-sink-volume 0 -2%"
   bindsym $mod+Escape $e pactl set-sink-mute 0 toggle
   ```

4. bar使用隐藏模式，不设键触发，按grave键就能看到时间电量，工作区提醒时也会显示

   ```
   bar {
       mode hide
       hidden_state hide
       modifier off
       strip_workspace_numbers yes
   	tray_output none
   	...
   }
   ```

5. 截屏我选deepin，还用老方法

   ```
   bindsym --release Print $e "deepin-screenshot -n && xclip -selection clipboard -t image/png -i $(\ls $D/*.png -t | head -n1)" 
   ```

6. 无边框是最爽的

   ```
   new_window none
   new_float none
   ```

### terminal emulators

> **urxvt**超快超流畅

配置文件`~/.Xresources`，改完还得重载，所以写个别名

```
alias vxr='v ~/.Xresources && xrdb -merge ~/.Xresources'
```

我的配置，仿paper-vim配色，选择即复制，ctrl+shift+v粘贴

```
URxvt*skipBuiltinGlyphs: true
URxvt*scrollTtyOutput: false
URxvt*scrollWithBuffer: true
URxvt*scrollTtyKeypress: true

URxvt.secondaryScreen: 1
URxvt.secondaryScroll: 0
URxvt.secondaryWheel: 1

URxvt.allow_bold: false
URxvt.font: xft:YaHei Consolas Hybrid:size=16
URxvt.url-launcher: /usr/bin/xdg-open
URxvt.matcher.button: 1
URxvt.scrollBar: false

URxvt.perl-ext-common: default,clipboard
URxvt.clipboard.autocopy: true
URxvt.keysym.C-C: perl:clipboard:copy
URxvt.keysym.C-V: perl:clipboard:paste

urxvt*foreground: #444444
urxvt*background: #EDEDEC
URxvt*highlightColor:                 #0087AF
URxvt*highlightTextColor:             #EEEEEE
URxvt*cursorColor:                    #444444
URxvt*iso14755: False

!! black dark/light
*color0: #073642
*color8: #002b36

!! red dark/light
*color1: #dc322f
*color9: #cb4b16

!! green dark/light
*color2: #859900
*color10: #586e75

!! yellow dark/light
*color3: #b58900
*color11: #657b83

!! blue dark/light
*color4: #268bd2
*color12: #839496

!! magenta dark/light
*color5: #d33682
*color13: #6c71c4

!! cyan dark/light
*color6: #2aa198
*color14: #93a1a1

!! white dark/light
*color7: #eee8d5
*color15: #fdf6e3
```

> 和urxvt比，alacritty，kitty，xfce4-terminal，termite都太卡了，前两个有gpu加速比后面两好点

### chrome 

**ublock**禁字体少一遍渲染，本地添加加web字体如fontawesome

**surfingkeys**真正的全键盘操作，关闭左右侧/全部tab,easymotion,快速开关,自定义快捷键，直写js，左侧按键布局设计，单手实现大部分功能

我的配置：朴素的百度谷歌切换，Ctrl-j/k上下，aa/as/zz/zx快速关闭左右侧单/多标签，增大jk滚动距离

```
mapkey('ss', 'switcher', function() {
    const word = document.querySelector('input:not([type=hidden])').value;
    if (window.location.href.indexOf('baidu') != -1) 
        window.location.href = `https://www.google.com/search?&q=${word}`
     else
        window.location.href = `https://www.baidu.com/s?wd=${word}`
});
unmap('sw');
addSearchAliasX('o', 'stackoverflow', 'http://stackoverflow.com/search?q=');
addSearchAliasX('a', 'aur', 'https://aur.archlinux.org/packages/?O=0&K=');
addSearchAliasX('w', 'archwiki', 'https://wiki.archlinux.org/index.php?title=Special%3ASearch&go=Go&search=');
addSearchAliasX('r', 'r18', 'http://www.r18.com/common/search/searchword=');
addSearchAliasX('n', 'bgm', 'http://bgm.tv/subject_search/');
addSearchAliasX('m', 'moegirl', 'https://zh.moegirl.org/zh-hans/');
addSearchAliasX('j', 'javlibrary', 'http://www.javlibrary.com/cn/vl_searchbyid.php?keyword=');
addSearchAliasX('y', 'youtube', 'https://www.youtube.com/results?page={startPage?}&utm_source=opensearch&search_query=');
addSearchAliasX('t', 'btdb', 'https://btdb.to/q/');
addSearchAliasX('i', 'bilibili', 'http://www.bilibili.tv/search?keyword=');
addSearchAliasX('d', 'douban', 'https://www.douban.com/search?q=');
addSearchAliasX('z', 'zhihu', 'https://www.zhihu.com/search?type=content&q=');
cmap('<Ctrl-j>', '<Tab>');
cmap('<Ctrl-k>', '<Shift-Tab>');
map('gxz','gx0');
map('gxx','gxt');
map('gxc','gxT');
map('gxv','gx$');
map('zz','gx0');
map('aa','gxt');
map('as','gxT');
map('zx','gx$');
settings.scrollStepSize = 150;
mapkey('gl', '#12Open Chrome flag', function() {
    tabOpenLink("chrome://flags/");
});
```

### font

用极限社区找的宋体等宽的浪漫雅园做主字体，win下挺好，arch在字体大小小于17时会缩成一团，换为普通的浪漫雅园解决，monosapce还是用[yahei consolas hybrid](https://github.com/yakumioto/YaHei-Consolas-Hybrid-1.12)

### file manager

thunar启动够快，自定义快捷键，自定义打开方式，配置文件`~/.config/Thunar/accels.scm`

ranger全快捷键，我的配置：回收站，删除，unar解压，默认应用

```
############rc.conf###############
map ua shell unar %s
map <DELETE> shell -s trash-put %s
map <S-DELETE> shell -s rm -rf %s
map D shell -s trash-put %s
map R shell -s sudo rm -rf %s
map <F1> copy
map <F2> cut
map <F3> paste
map <F4> edit
map <F5> console touch%space
map <F6> console mkdir%space
map <F7> shell -s trash-put %s
map <F8> shell -s rm -rf %s
map e get_cumulative_size
map ,m shell -s trash-put %s
map ,j shell code .
map ,k bulkrename
map <C-f> fzf_select
###########rifile.conf###############
ext 1                         = man "$1"
ext s[wmf]c, has zsnes, X     = zsnes "$1"
ext s[wmf]c, has snes9x-gtk,X = snes9x-gtk "$1"
ext nes, has fceux, X         = fceux "$1"
ext exe                       = wine "$1"
name ^[mM]akefile$            = make
mime ^audio|ogg$, terminal, has mpv      = mpv -- "$@"
mime ^video,       has mpv,      X, flag f = mpv -- "$@"
mime ^video,       has mpv,      X, flag f = mpv --fs -- "$@"
ext pdf, has foxitreader,     X, flag f = foxitreader "$@"
ext pdf, has google-chrome-stable,  X, flag f = google-chrome-stable "$@"
ext docx?|dotx, has wps,     X,  flag f = wps "$@"
ext pptx?, has wpp,     X,  flag f = wpp "$@"
ext xlsx?|csv, has et,      X, flag f= et "$@"
mime ^image, has feh,       X, flag f = feh  -- "$@"
ext rar|tar|tgz|zip|7z|gz flag t = unar "$@" 
label editor = $EDITOR -- "$@"
flag t = $EDITOR -- "$@"
ext py  = python -- "$1"
ext py  = python2 -- "$1"
ext pl  = perl -- "$1"
ext rb  = ruby -- "$1"
ext js  = node -- "$1"
ext sh  = sh -- "$1"
ext php = php -- "$1"
mime application/x-executable = "$1"
```

### text editor

vim：multi cusors，expand region，quick comment

以上功能用gedit，mousepad，leafpad不能实现，vscode，idea启动又太慢，vim最佳

### virtual system

qq是刚需，而wine实在不稳，果然还得上虚拟机，**但**，如果已有一个原生启动的win，难道还要再建一个虚拟机系统？或者，有时需要win下高性能运行，必须原生启动，是否还要再装一个win系统？

[**只维护一份系统，原生虚拟两种启动**](/2018/01/14/partition/)

1. 虚拟机启动磁盘上的win，通过virtualbox或vmware建立磁盘vmdk映射[参考](https://superuser.com/questions/325974/how-to-create-vmdk-file-in-virtualbox-for-adding-usb-as-a-new-os)，然而测试发现linux与win同硬盘会有写入问题，虚拟机下已写入但切到linux下或原生启动win则看不到，并且再原生启动win会出磁盘检测
2. 原生启动虚拟机系统，利用vhd(x)来实现：建立vhd硬盘，在vhd里装系统，在虚拟机中安装虚拟机驱动。在虚拟机下通过共享文件夹方式加入磁盘，缺点是磁盘文件系统是vboxfs，非原文件系统，测试附加ntfs下的centbrowser不能正常运行，其他软件没遇到问题，文件删除有时提示找不能，实际已删除，刷新即可
3. 这两种方案我都有尝试，由于是单硬盘，还是用第二种，很稳定，在virtualbox中的win启动速度很快，打ever17也没问题

### 翻墙

ss/ssr-libev有acl功能，相比pac更方便添加黑白名单，且能作用于全局（ss-redir），ss-libev维护中

一个维护中的[acl](https://github.com/ACL4SSR/ACL4SSR)，我使用[白名单，无去广告](https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/nobanAD.acl)

快速修改acl

```
alias vac='v $C/ssr/nobanAD.acl;pkill ss-local;ss-local -c $HOME/t.json --acl $C/ssr/nobanAD.acl'
alias vaca='v $C/ssr/nobanAD.acl;pkill ss-local;ss-local -c $HOME/t.json&'
```

ssr-python无acl功能，不再维护

chrome无需扩展自带pac功能

```
google-chrome-stable --proxy-pac-url=file://$HOME/w1081
google-chrome-stable --proxy-pac-url=https://pac.txthinking.com/white/SOCKS5%20127.0.0.1:1081;%20SOCKS%20127.0.0.1:1081
```

一个不怎么更新的[pac](https://github.com/txthinking/pac)

[v2ray](/2017/09/27/v2ray/)有时断流，线程多会卡，自带分流效果极差，速度区别不大，备用

### feh

pacman安装的feh不带配置，所以先从[github](https://github.com/derf/feh/tree/master/examples)上下，放到`.config/feh`

关于feh的自定义action，man中写的不全，其实action可以写在`theme`文件里，`keys`文件里可为action再绑其他快捷键

我的配置：按d放入回收站，按D删除，按u移动到自定义文件夹

```
##############button文件##############
# Switch menu and zoom keys, useful for two-button touchpads
menu 2
zoom 3

##############keys文件##############
delete D C-d C-Delete
action_2 d Delete
toggle_filenames f
action_1 u
menu_parent h Left
menu_child  l Right
menu_down   j Down
menu_up     k Up
menu_select space Return
# Same for image navigation ...
next_img j Right space
prev_img k Left BackSpace
# and image movement
scroll_up    J C-Up
scroll_down  K C-Down
scroll_left  H C-Left
scroll_right L C-Right
zoom_fit s
save_image
save_filelist

##############themes文件##############
feh -ZF --action1 "mv %F /F:/2016/pic/t" --action2 "trash-put %F"
```

### imagemagick

缩小图片体积

```
convert -resize 50% a.jpg a.jpg
```

### mpv

跨平台高效播放器，支持全键盘操作，配置文件`.config/mpv/`

我的配置：左右键跳5s，滚轮修改声音，默认全屏

```
###########input.conf############
WHEEL_UP      add volume 1
WHEEL_DOWN    add volume -1
RIGHT seek  5
LEFT  seek -5
###########mpv.conf#############
fs=yes
```

### extra

1. markdown typora，IDE vscode clion pycharm

4. 开了imwheel而wps不能用`ctrl+滚轮`缩放，在`.imwheelrc`中加入

   ```
   "^wps$"
       Control_L, Up,   Control_L|Button4
       Control_L, Down, Control_L|Button5
   ```

### 后记

~~一个月前~~我试图从xfce4直切到sway，当然也伴随着从x到wayland，结果xfce和sway都炸了，大部分软件都打不开，之后重装了一次，纯wayland，尝试sway，way-cooler，使用后感觉基础功能不完善（如thunar窗口间拖拽），最主要是chrome，firefox都启不出，但vscode却没问题

没办法还回到x，尝试i3，没装n卡驱动，按wiki做了点集显优化，速度比第一次快了太多

---

### 20171102前

### 截屏到剪贴板

用xfce4-screenshooter或scrot或deepin-screenshot截稍大点的图到剪贴板再快速粘贴时会有bug。粘到wps，卡几秒，图还是没进去；粘到googledocs，过几秒图才进去。

解决方法:**先保存到桌面再用xclip转一下**

将以下命令赋给截屏键，其中$D为桌面路径，在deepin截图时选择保存到桌面

```sh
bash -c 'deepin-screenshot -n && xclip -selection clipboard -t image/png -i $(\ls $D/*.png -t | head -n1)'
```

[1]: https://wiki.archlinux.org/index.php/Bumblebee_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)
[2]: https://wiki.archlinux.org/index.php/Downgrading_packages_(简体中文)


### ck内核+nvidia闭源驱动 [参考](https://wiki.archlinux.org/index.php/repo-ck)

内核有很多：官核，lts，zen，ck... 安装ck可以aur调参编译，但要很长时间，所以我用预编译的。不装n卡驱动可能开机有问题，我很长时间没装也没问题，但升级内核后就卡开机，且换核解决不了。

1. 按照[repo-cl](http://repo-ck.com/)添加pacman.conf，加sign，更新pacman数据库

2. 为了安装对cpu优化过的版本，先看下cpu架构，运行

   ```sh
   gcc -c -Q -march=native --help=target | grep march
   ```

3. 我是`haswell`，所以

   ![DeepinScreenshot_select-area_20171019225203](/images/DeepinScreenshot_select-area_20171019225203.png)

   我需要独显，且为940m，不需要visualbox，所以选择2 6

4. 更新grub

   ```sh
   sudo grub-mkconfig -o /boot/grub/grub.cfg 
   ```

   查看grub.cfg，可能需要手动把ck从submenu拉到主menu来

   ![DeepinScreenshot_select-area_20171019225713](/images/DeepinScreenshot_select-area_20171019225713.png)

5. 安装[Bumblebee][1] 

6. 如果__没装Bumblebee，开机可能黑屏__，通过各种办法再进系统/终端安装即可，当然要按照[wiki][1]操作

7. 如果__安装了nvidia-settings并运行了，可能卡开机__，因为它生成了xorg.conf，`ctrl+alt+f2` 再开一个终端把`/etc/X11/xorg.conf`mv掉就能瞬间进系统

8. 可以用[acpi_call](https://wiki.archlinux.org/index.php/Hybrid_graphics#Fully_Power_Down_Discrete_GPU)把nvidia关了


### 滚挂解决方案

升级内核后卡开机

1. 试试按`ctrl+alt+f2`开另一终端，`ctrl+alt+f1`切回第一个。

2. 不行就进iso，arch-chroot进本地系统

3. 尝试[回滚内核][2]

4. 回滚内核只是图一时之快，不能解决长久问题，通过`dmesg` `journalctl`来看启动问题

5. 遇到过**nouveau报错**，换各种内核还是__随机性卡开机，再次重启或多次重启后正常进系统__，最后**安装独显驱动**解决

### 翻墙方案

工具：(ssr-python+cow)+v2ray+SwitchyOmega

1. 之前用__ProxySwitchySharp__有bug，大幅减速，换__SwitchyOmega__解决

2. ssr的python-client直接从github上clone，备份随处可见。python客户端只提供socks代理，一些应用不支持，可用工具转http代理

3. 环境变量设置**全代**（telegram yaourt npm）

   ```shell
   assignProxy(){
      PROXY_ENV=("http_proxy" "ftp_proxy" "https_proxy" "all_proxy" "HTTP_PROXY" "HTTPS_PROXY" "FTP_PROXY" "ALL_PROXY")
      for envar in $PROXY_ENV
      do
        export $envar=$1
      done
      for envar in ("no_proxy" "NO_PROXY")
      do
         export $envar=$2
      done
   }
   clrProxy(){
     assignProxy "" # This is what 'unset' does.
   }

   setProxy(){
   #   user=YourUserName
   #read -p "Password: " -s pass &&  echo -e " " 
   #   proxy_value="http://$user:$pass@ProxyServerAddress:Port"
      proxy_value=$1
      no_proxy_value="localhost,127.0.0.1,LocalAddress,LocalDomain.com"
      assignProxy $proxy_value $no_proxy_value
    }
   setProxy "http://127.0.0.1:7774" 
   #7774为cow的入口，出口为ssr
   ```

   加入开机脚本（如使用zsh，可将以上代码加入～/.zlogin）

   cow的分流不好用，不用直接全局

4. cow配置在~/.cow/do_ssr_all.json

   ```sh
   listen = http://127.0.0.1:7774
   alwaysProxy = true 
   proxy = socks5://127.0.0.1:1081
   ```

5. v2ray配置在~/.v2ray/do.json，参考 [v2ray客户端配置](http://bilabila.xyz/2017/09/27/v2ray/)

6. ssr配置在~/.ssr/do.json

7. 将cow、ssr、v2ray加入开机启动

   ```shell
   cow -rc=$HOME/.cow/do_ssr_all&
   #cow -rc=$HOME/.cow/ssr&
   #cow -rc=$HOME/.cow/no&
   v2ray -config=$HOME/.v2ray/do.json&
   #v2ray -config=$HOME/.v2ray/cac.json&
   sudo python /home/kkcckc/bin/shadowsocksr/shadowsocks/local.py -c /home/kkcckc/.ssr/do.json -d start;
   ```

8. chrome扩展设置

   rule:https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt

   ![](/images/DeepinScreenshot_select-area_20171012184916.png)

9. npm设置

   ```sh
   npm config set https-proxy http://127.0.0.1:7774
   ```


### 系统优化


2. vim不能复制给系统剪贴板，装gvim

3. 风扇声音大，机械不停转，使用[TLP](https://wiki.archlinux.org/index.php/TLP)，~~风扇从此不再转~~。调参走的是Android调核的老路，省电流畅不能兼得，TLP配不好会加长开关机时间

4. chrome、foxitReader滚动幅度小，使用[imwheel](https://wiki.archlinux.org/index.php/Imwheel)，我的配置如下

   ```shell
   {23:33}~ ➭ cat .imwheelrc 
   "^google-chrome$"
       None, Up, Button4, 4
       None, Down, Button5, 4
       Shift_L,   Up,   Shift_L|Button4, 4
       Shift_L,   Down, Shift_L|Button5, 4
       Control_L, Up,   Control_L|Button4
       Control_L, Down, Control_L|Button5
   "^Telegram$"
       None, Up, Button4, 4
       None, Down, Button5, 4
       Shift_L,   Up,   Shift_L|Button4, 4
       Shift_L,   Down, Shift_L|Button5, 4
       Control_L, Up,   Control_L|Button4
       Control_L, Down, Control_L|Button5
   "^FoxitReader$"
       None, Up, Button4, 4
       None, Down, Button5, 4
       Shift_L,   Up,   Shift_L|Button4, 4
       Shift_L,   Down, Shift_L|Button5, 4
       Control_L, Up,   Control_L|Button4
       Control_L, Down, Control_L|Button5
   ```

5. 感觉chrome滚动卡，相比centbrowser卡爆了？尤其是youtube、stackoverflow？试试关__CrxMouse__的**Enable Scrolling**，当然还有**TLP**

6. win分区挂载，在根目录下建几个文件夹/C: /F: /G: /H:，将win下磁盘名挂到相应目录下，好处是固定路径，可以添加到文件管理器收藏夹

   ![DeepinScreenshot_select-area_20170912235426](/images/DeepinScreenshot_select-area_20170912235426.png)

   另一好处是chrome下本地url可以跨平台。下图一个本地html路径在win和linux指向同一文件，都能正确加载网页

   ![DeepinScreenshot_select-area_20170912235648](/images/DeepinScreenshot_select-area_20170912235648.png)

   开机自动挂载，写入fstab也可

   ```sh
   sudo mount -o uid=1000 /dev/sdb1 /C:
   sudo mount -o uid=1000 /dev/sdb3 /F:
   sudo mount -o uid=1000 /dev/sda1 /G:
   ```

7. **ntfs分区**只读不可写 **read only system**，重新挂载也没用，极有可能是**win没有正常关机**，重启进win正常关机即可。

8. win linux同桌面，win下我的桌面为f:\desktop，linux下把桌面删了新建软连接即可

   ```shell
    rm -Rf Desktop && ln /F:/Desktop Desktop -sf
   ```

   桌面上没有程序图标，只有文件和文件夹，程序都用各自的快速启动工具打开（altrun rua）

9. 桌面文件夹位置重启不保存？试试下面的命令

   ```shell
   chattr -i ~/.config/xfce4/desktop/icons*
   ```

10. 磁盘莫名奇妙满了？看看是不是session目录的问题，可用以下命令禁用session保存功能

  ```shell
  rm ~/.cache/sessions/* -rf
  chmod 000 ~/.cache/sessions
  ```

11. 键盘不能调节亮度？试试xbacklight命令，有用的话手动加入快捷键即可

    ![DeepinScreenshot_select-area_20170913001154](/images/DeepinScreenshot_select-area_20170913001154.png)

    没用的话试试向/sys/class/backlight/intel_backlight/brightness写入数值

12. 清理点空间

   ```sh
   #缓存
   yaourt -Scc
   #"无用"包
   yaourt -Rns $(yaourt -Qtdq)
   ```

   盘设小了？先用各种办法得到一块相邻的空闲空间，再用gparted扩盘，扩盘无损的

   安装`baobab`查看磁盘占用，

13. __pavucontrol__导致插拔耳机不会自动切换音量，win下安装声卡驱动同样问题

14. xfce自带截图太慢？试试Deepin Screenshot


### 开发

1. netbeans的中scenebuilder路径不能配置，使用终端打开scenebuilder即可

   ```sh
   gluon-scenebuilder ~/NetBeansProjects/app0/src/Lab1Gui/Gui.fxml
   ```

   不加文件名的话scenebuilder窗口**不能全屏**

2. eclipse出现**Could not find or load main class**，极有可能是使用了__ntfs__的工作目录，试试在用户目录下建eclipse-workspace，切换到该目录即可

   ```sh
   /home/kkcckc/eclipse-workspace/
   ```

   如果你一激动把工作目录清了，eclipse会因为.metadata的丢失不能正常启动，卸载重装都没用，试试在卸载时加n参数不保存配置再重装

   ```sh
   yaourt -Rn eclipse-java
   yaourt -S eclipse-java
   ```

3. scenebuilder和javafx报warning

   ```sh
   (java:7605): Gtk-WARNING **: Unable to locate theme engine in module_path: "murrine"
   ```

   安装gtk-engine-murrine即可

   ```sh
   yaourt -S gtk-engine-murrine
   ```

4. 打开ntfs上文件(夹)vscode，atom，eclipse都有bug。

    而netbeans，clion，idea，pycharm支持

5. 写c/c++用clion，其他：netbeans（补全不够），idea（c插件早就不更），vscode（提示和调试都不成熟），codeblocks（一直炸）


### 还是好用

![DeepinScreenshot_select-area_20170913002237](/images/DeepinScreenshot_select-area_20170913002237.png)




