# v2ray

v2ray是很强大的代理工具，这里只介绍如何用它来翻墙

v2ray的linux版和ssr-python一样既可做服务端，又可做客户端。

### 服务端

**环境：centos7.3**

1. 使用官方提供的安装命令

   ```sh
   bash <(curl -L -s https://install.direct/go.sh)
   ```
   有时会失败，可以这样

   ```sh
   mkdir -p /etc/v2ray
   wget https://install.direct/go.sh
   source go.sh
   ```

2. 写入配置文件

   ```sh
   cat >/etc/v2ray/config.json<<EOF
   {
     "log": {
       "access": "/var/log/v2ray/access.log",
       "error": "/var/log/v2ray/error.log",
       "loglevel": "info"
     },
     "inbound": {
       "port": 8086, 		//开放8086端口 防火墙要开
       "listen": null,
       "protocol": "vmess",
       "settings": {
         "auth": null,
         "udp": true,
         "ip": null,
         "clients": [
           {
             "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",//uuid自己生成
             "alterId": 100,
             "security": "aes-128-gcm" 
           }
         ]
       },
       "streamSettings": {
         "security": "",
         "wsSettings": null,
         "network": "tcp", 			//tcp伪装
         "kcpSettings": null,
         "tcpSettings": {
           "header": {
             "request": {
               "path": [
                 "/"
               ],
               "version": "1.1",
               "method": "GET",
               "headers": {
                 "Host": "bing.com",
                 "Connection": [
                   "keep-alive"
                 ],
                 "Accept-Encoding": [
                   "gzip, deflate"
                 ],
                 "Pragma": "no-cache",
                 "User-Agent": [
                   "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.75 Safari/537.36",
                   "Mozilla/5.0 (iPhone; CPU iPhone OS 10_0_2 like Mac OS X) AppleWebKit/601.1 (KHTML, like Gecko) CriOS/53.0.2785.109 Mobile/14A456 Safari/601.1.46"
                 ]
               }
             },
             "type": "http",
             "response": {
               "status": "200",
               "headers": {
                 "Transfer-Encoding": [
                   "chunked"
                 ],
                 "Connection": [
                   "keep-alive"
                 ],
                 "Content-Type": [
                   "application/octet-stream",
                   "video/mpeg"
                 ],
                 "Pragma": "no-cache"
               },
               "reason": "OK",
               "version": "1.1"
             }
           },
           "connectionReuse": true
         }
       }
     },
     "outbound": {
       "tag": null,
       "protocol": "freedom",
       "settings": null,
       "streamSettings": null,
       "mux": null
     },
     "inboundDetour": null,
     "outboundDetour": [
       {
         "protocol": "blackhole",
         "settings": null,
         "tag": "blocked"
       }
     ],
     "dns": null,
     "routing": {
       "strategy": "rules",
       "settings": {
         "domainStrategy": null,
         "rules": [
           {
             "type": "field",
             "port": null,
             "outboundTag": "blocked", //禁止访问服务器ip
             "ip": [
               "0.0.0.0/8",
               "10.0.0.0/8",
               "100.64.0.0/10",
               "127.0.0.0/8",
               "169.254.0.0/16",
               "172.16.0.0/12",
               "192.0.0.0/24",
               "192.0.2.0/24",
               "192.168.0.0/16",
               "198.18.0.0/15",
               "198.51.100.0/24",
               "203.0.113.0/24",
               "::1/128",
               "fc00::/7",
               "fe80::/10"
             ],
             "domain": null
           }
         ]
       }
     }
   }
   EOF
   systemctl restart v2ray
   systemctl status v2ray
   ```

   注：uuid找网站生成即可，服务端客户端需对应

   看看有没有报错，在`running`即可


### 客户端

**环境： archlinux**

1. aur安装便于更新

   ```sh
   yaourt -S v2ray
   ```

2. 写入配置

   ```sh
   sudo chmod a+w /etc/v2ray/config.json
   cat > /etc/v2ray/config.json <<EOF
   {
       "log": {
           "access": "",
           "error": "",
           "loglevel": "info"
       },
       "inbound": {
           "port": 1083,         //本地端口
           "listen": "0.0.0.0",
           "protocol": "http", //改socks5更优，但http对系统全局代理兼容更好
           "settings": {
               "auth": "noauth",
               "udp": true,
               "ip": "127.0.0.1",
               "clients": null
           },
           "streamSettings": null
       },
       "outbound": {
           "tag": "agentout",
           "protocol": "vmess",
           "settings": {
               "vnext": [
                   {
                       "address": "64.0.0.000", //服务器ip
                       "port": 8086,
                       "users": [
                           {
                               "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", //与服务求统一
                               "alterId": 100,
                               "security": "aes-128-gcm"
                           }
                       ]
                   }
               ]
           },
           "streamSettings": {
               "security": "", 
               "wsSettings": null, 
               "network": "tcp", 
               "kcpSettings": null, 
               "tcpSettings": {
                "header": {
                 "request": {
                  "path": [
                   "/"
                  ], 
                  "version": "1.1", 
                  "method": "GET", 
                  "headers": {
                   "Host": "bing.com",
                   "Connection": [
                    "keep-alive"
                   ], 
                   "Accept-Encoding": [
                    "gzip, deflate"
                   ], 
                   "Pragma": "no-cache", 
                   "User-Agent": [
                    "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.75 Safari/537.36", 
                    "Mozilla/5.0 (iPhone; CPU iPhone OS 10_0_2 like Mac OS X) AppleWebKit/601.1 (KHTML, like Gecko) CriOS/53.0.2785.109 Mobile/14A456 Safari/601.1.46"
                   ]
                  }
                 }, 
                 "type": "http",   
                 "response": {
                  "status": "200", 
                  "headers": {
                   "Transfer-Encoding": [
                    "chunked"
                   ], 
                   "Connection": [
                    "keep-alive"
                   ], 
                   "Content-Type": [
                    "application/octet-stream", 
                    "video/mpeg"
                   ], 
                   "Pragma": "no-cache"
                  }, 
                  "reason": "OK", 
                  "version": "1.1"
                 }
                }, 
                "connectionReuse": true
               }
              },
           "mux": {
               "enabled": true
           }
       },
       "inboundDetour": null,
       "outboundDetour": [
           {
               "protocol": "freedom",
               "settings": {
                   "response": null
               },
               "tag": "direct"
           },
           {
               "protocol": "blackhole",
               "settings": {
                   "response": {
                       "type": "http"
                   }
               },
               "tag": "blockout"
           }
       ],
       "dns": {
           "servers": [
               "localhost"
           ]
       },
       "routing": {
           "strategy": "rules",
           "settings": {
               "domainStrategy": "AsIs", //不做dns解析 反：IPIfNonMatch
               "rules": [
                   {
                       "type": "field",
                       "port": null,
                       "outboundTag": "direct",
                       "ip": [
                           "0.0.0.0/8",
                           "10.0.0.0/8",
                           "100.64.0.0/10",
                           "127.0.0.0/8",
                           "169.254.0.0/16",
                           "172.16.0.0/12",
                           "192.0.0.0/24",
                           "192.0.2.0/24",
                           "192.168.0.0/16",
                           "198.18.0.0/15",
                           "198.51.100.0/24",
                           "203.0.113.0/24",
                           "::1/128",
                           "fc00::/7",
                           "fe80::/10"
                       ],
                       "domain": null,
                       "outboundTag": "direct"
           		},
           		{
         			   "type": "chinasites", //不用chinaip
          			   "outboundTag": "direct"
                	}
               ]
           }
       }
   }
   EOF
   sudo systemctl restart v2ray
   systemctl status v2ray
   ```


   有时可以完胜ssr

3. 建议不用v2ray来选择，还是全代

   ```sh
   {
       "log": {
           "access": "",
           "error": "",
           "loglevel": "none"
       },
       "inbound": {
           "port": 1083,
           "listen": "0.0.0.0",
           "protocol": "http",
           "settings": {
               "auth": "noauth",
               "udp": true,
               "ip": "127.0.0.1"
           }
       },
       "outbound": {
           "tag": "agentout",
           "protocol": "vmess",
           "settings": {
               "vnext": [
                   {
                       "address": "www.baidu.com",
                       "port": 8086,
                       "users": [
                           {
                               "id": "××××××××-××××-××××-8888-e8c71f4e9422",
                               "alterId": 100,
                               "security": "aes-128-gcm"
                           }
                       ]
                   }
               ]
           },
           "streamSettings": {
               "security": "", 
               "network": "tcp", 
               "tcpSettings": {
                "header": {
                 "request": {
                  "path": [
                   "/"
                  ], 
                  "version": "1.1", 
                  "method": "GET", 
                  "headers": {
                   "Host": "bing.com",
                   "Connection": [
                    "keep-alive"
                   ], 
                   "Accept-Encoding": [
                    "gzip, deflate"
                   ], 
                   "Pragma": "no-cache", 
                   "User-Agent": [
                    "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.75 Safari/537.36", 
                    "Mozilla/5.0 (iPhone; CPU iPhone OS 10_0_2 like Mac OS X) AppleWebKit/601.1 (KHTML, like Gecko) CriOS/53.0.2785.109 Mobile/14A456 Safari/601.1.46"
                   ]
                  }
                 }, 
                 "type": "http", 
                 "response": {
                  "status": "200", 
                  "headers": {
                   "Transfer-Encoding": [
                    "chunked"
                   ], 
                   "Connection": [
                    "keep-alive"
                   ], 
                   "Content-Type": [
                    "application/octet-stream", 
                    "video/mpeg"
                   ], 
                   "Pragma": "no-cache"
                  }, 
                  "reason": "OK", 
                  "version": "1.1"
                 }
                }, 
                "connectionReuse": true
               }
              },
           "mux": {
               "enabled": true
           }
       }
   }
   ```

### 参考

1. [v2ray](https://github.com/v2ray/v2ray-core)
2. [v2ray.fun](https://github.com/FunctionClub/v2ray.fun)
