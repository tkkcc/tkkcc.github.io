## zerotier

一台主机只有内网ip,另一台不在该内网中的主机要想连接它就需要内网穿透技术.ngrok,frp等工具通过公网小鸡做中转,这很要求小鸡的地理位置.而zerotier则构建一个p2p网络,节点ab起初通过主节点连接,主节点计算出更短的a<->b路径发给ab,之后ab就用新路径连接,而这条路径由其他节点构建

### 实例

两台主机都无公网ip,且都有root权限

1. 各自安装zerotier-one,archlinux装`zerotier-one`,其他参考[官网](https://www.zerotier.com/download.shtml),安装后启动服务
  ```sh
  systemctl enable --now zerotier-one
  systemctl status --no-pager --full zerotier-one
  ```
  如果tun有问题,则需加载tun模块,并把`tun`写进`/etc/modules-load.d/0.conf`,使其启动时自动加载
  ```sh
  modprobe tun
  ```
  最后查看状态,ONLINE即OK
  ```sh
  zerotier-cli info
  ```
2. 在[官网](https://my.zerotier.com/login)创建用户,点[network](https://my.zerotier.com/network),创建一个网络.网络的概念是虚拟的,为的是方便管理.节点可以加入网络,网络管理员控制哪些用户能进入网络,网络中节点能互相通信.网络id假设为1c33c1ced0c05f4c

3. 将节点加入网络.在两台主机上运行
  ```sh
  zerotier-cli join 1c33c1ced0c05f4c
  ```
  回到网页中的网络管理界面,在`Members`栏目中能够看到加入的节点
  ![](/images/2019-03-04_19-07.png)
  将左边的Auth勾点上,绿色表示允许  
  界面上显示的内网ip即各自节点在该虚拟网络中的地址
  在一台主机中尝试ping另一台主机的ip
  ```
  ping 172.30.25.106
  ```
  各自能ping通就OK,之后可以开ssh服务器连接