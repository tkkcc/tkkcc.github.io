## iodine

dns隧道过认证

### 域名设置

![1](/images/DeepinScreenshot_select-area_20171225200908.png)

### 服务端

环境：centos7.4

```
#安装
yum install iodine
#运行 可以先开个screen: screen -S iodine
iodined -fP 0 10.0.0.1 a.bilabila.tk -c
```

f：前台 P：密码（0） 10.0.0.1：客户端ip c:禁用ip检测

运行结果：

```
Opened dns0
Setting IP of dns0 to 10.0.0.1
Setting MTU of dns0 to 1130
Opened IPv4 UDP socket
Listening to dns for domain a.bilabila.tk
```

### 客户端

环境：archlinux

```
#安装
pacman -S iodine
#运行
sudo iodine -f 159.89.128.65 a.bilabila.tk -P 0 
```

f：前台 P：密码（与服务端一样）

运行结果：

```
Opened dns0
Opened IPv4 UDP socket
Sending DNS queries for a.bilabila.tk to 159.89.128.65
Autodetecting DNS query type (use -T to override).
Using DNS type NULL queries
Version ok, both using protocol v 0x00000502. You are user #0
Setting IP of dns0 to 10.0.0.2
Setting MTU of dns0 to 1130
Server tunnel IP is 10.0.0.1
Testing raw UDP data to the server (skip with -r)
Server is at 159.89.128.65, trying raw login: OK
Sending raw traffic directly to 159.89.128.65
Connection setup complete, transmitting data.
```

测试：

```
ping 10.0.0.1
```

### 正片

如果你一路做下来就成功了，那你太6。我在其中遇到不少问题

1. 服务端命令是**iodined**，客户端是 **iodine**，两个不同，命令打错耗费了不少时间，直到见到[同胞](https://superuser.com/questions/1222536/dns-tunneling-iodine-issue)

   服务端如果看到如下提示，那很可能就是打错命令

   ```
   Autodetecting DNS query type (use -T to override).....................
   iodine: No suitable DNS query type found. Are you connected to a network?
   iodine: If you expect very long roundtrip delays, use -T explicitly.
   iodine: (Also, connecting to an "ancient" version of iodined won't work.)
   ```

2. iodine连通，即显示

   ```
   Connection setup complete, transmitting data.
   ```

   **这时就开始连续ping会导致永远不通（ping会卡住）**，原因未知，之后iodine闲置60s自动退出

   **实测先等30s再开始ping或者发其他请求没有该问题**

3. ping通后使用ss/ssr/ssh/v2ray/cow建立代理，如已有ssr，直接把客户端配置中server改为10.0.0.1即可。程序要上网都得通过代理

4. 60s时间不发包，iodine就自动断开，即使加了`-I`参数依然没用，所以挂个ping，每隔30s发个包，类似锐捷的心跳包

5. 综上，完整的客户端命令即为开iodine，等30s后开始每隔30s发包，加个别名方便启动。

   ```
   alias iod='sudo iodine -f 159.89.128.65 a.bilabila.tk -P 0 &;sleep 30;ping 10.0.0.1 -i 30'
   ```

6. 实测不提速也不降速，和正常验证后的全局代理一样。



