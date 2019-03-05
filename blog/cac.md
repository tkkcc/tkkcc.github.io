
## cac
vps快速配置

传家宝炸机是分分钟的事，新机快速配置是必要的
测试cac(centos7)、do/vultr(centos7.4)，vpsao(debian8)

1. 配置秘钥，禁用密码，关防火墙，安装必要软件

   ```sh
   mkdir .ssh -p
   cat > .ssh/authorized_keys<<EOF
   ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC370VrMpOfOcqkhw02o4du8fN0Xs4Vhz4GNGjsKAO6WA5zg5yi/0jyaauNEEoDSU92tkWRxJjNo1z72Qs04/ATDBApyxCQeLFQRdzHZFR9RtoEbEBJZkfHUucSUqWLbTnaxGPfAOj5MNiDhC/NDbAXb6mMKVlKLnFYar1hzBE9xnyJObsOuKSR/9MyHnMXlIIwbVCDiFET711Bam6MemODgFWp8yYv1ItH2Ek8IzE7a4D7LIfwiyyV2rDZezqZ3ba5S9fdQZh7+9G3iXBptq5/pXn7/VsvLoNF4c9rU7yF3/He+QwU4Dpm2/BxKBwR0Tst8lIW5MX2JS2qi7InGXiZ kkcckc@tcy
   EOF
   chmod 700 .ssh
   chmod 600 .ssh/authorized_keys
   sed -i 's/^PasswordAuthentication yes/#&/' /etc/ssh/sshd_config
   sed -i 's/^PubkeyAuthentication no/#&/' /etc/ssh/sshd_config
   echo 'PasswordAuthentication no' >> /etc/ssh/sshd_config
   echo 'PubkeyAuthentication yes' >> /etc/ssh/sshd_config
   systemctl restart sshd
   systemctl stop iptables
   systemctl disable iptables
   systemctl stop firewalld
   systemctl disable firewalld
   echo>/var/log/secure

   yum install lrzsz zsh wget vim unzip httpd wget -y
   chsh -s /bin/zsh root
   ```

   秘钥当然是自己生成的公钥（win下xshell linux下ssh-keygen），将公钥写入服务器

2. vim zshrc配置

   ```sh
   rm ~/.vimrc ~/.zshrc -f
   cat> /etc/vimrc<<EOF
   set nocompatible
   set backspace=indent,eol,start  "Allow backspace in insert mode
   set showmode                    "Show current mode down the bottom
   set autoread                    "Reload files changed outside vim
   " This makes vim act like all other editors, buffers can
   " exist in the background without being in a window.
   " http://items.sjbach.com/319/configuring-vim-right
   set hidden
   set scrolloff=8         "Start scrolling when we're 8 lines away from margins
   set sidescrolloff=15
   set sidescroll=1
   set nowrap       "Don't wrap lines
   set linebreak    "Wrap lines at convenient points
   set incsearch       " Find the next match as we type the search

   nmap <C-s> :update<CR>
   vmap <C-s> <C-c>:update<CR>
   imap <C-s> <C-o>:update<CR>inoremap <c-z> <c-o>:u<CR>
   set ttyfast
   inoremap <c-r> <c-o><c-r>>
   imap <c-z> <c-o>:u<CR>
   set wrapscan
   set showcmd
   set wildmenu
   set hlsearch
   set ttymouse=xterm2
   set clipboard=unnamedplus,unnamed,autoselect,exclude:cons\|linux
   set number
   syntax enable
   set autoindent
   set smartindent
   set shiftwidth=4
   set cursorline
   "set showmatch
   "let python_highlight_all = 1
   set fileencodings=utf-8,gb2312
   "set clipboard=unnamedplus
   set tabstop=4
   "set cindent
   set nobackup
   set nowb
   set noswapfile
   " Using vim-impaired, enable moving lines
   " - Bubble single lines
   nmap <C-Up> [e
   nmap <C-Down> ]e
   " - Bubble multiple lines
   vmap <C-Up> [egv
   vmap <C-Down> ]egv

   " Indent/unindent visual mode selection
   vmap <tab> >gv
   vmap <S-tab> <gv

   " Easy navigation between windows
   map <C-h> <C-w>h
   map <C-j> <C-w>j
   map <C-k> <C-w>k
   map <C-l> <C-w>l
   " Jump to the last position when reopening a file
   au BufReadPost * if line("'\"") > 1 && line("'\"") <= line("$") | exe "normal! g'\"" | endif
   " Enhance % for matching if/end/tags in some language (html, jsp...)
   EOF

   cat> /etc/zshrc<<EOF
   alias ng='service nginx reload'
   alias nc='vi /usr/local/nginx/conf/nginx.conf'
   alias phpini='vi /usr/local/php/etc/php.ini'
   alias pc='vi /usr/local/php/etc/php-fpm.conf'
   alias pg='service php-fpm restart'

   alias sq='systemctl start'
   alias pc='ps aux | grep cow'
   alias sw='systemctl status'
   alias se='systemctl stop'
   alias sr='systemctl restart'
   alias ff='find . -type f -iname'
   alias fd='find . -type d -iname'

   alias du='du -h'
   alias df='df -h'
   alias f='find -iname'
   alias v='vim'

   alias ls='ls                                  -v --classify --group-directories-first --color=auto'
   alias  l='ls -l              --human-readable -v --classify --group-directories-first --color=auto'
   alias ll='ls -l              --human-readable -v --classify --group-directories-first --color=auto'
   alias la='ls -l --almost-all --human-readable -v --classify --group-directories-first --color=auto'

   alias  grep='grep --color=auto'
   alias egrep='grep --color=auto'
   alias zgrep='grep --color=auto'

   alias cp='nocorrect cp -iv' # -i to prompt for every file
   alias mv='nocorrect mv -iv'
   alias rm='nocorrect rm -Iv' # -I to prompt when more than 3 files
   alias rmdir='rmdir -v'
   alias chmod='chmod -v'
   alias chown='chown -v'

   alias -g G='| grep -in'
   alias -g T='| tail'
   alias -g L='| less'

   alias cd..='cd ..'
   alias '..'='cd ..'
   alias -g ...='../..'
   alias -g ....='../../..'
   alias -g .....='../../../..'
   alias -g ......='../../../../..'
   alias -g .......='../../../../../..'

   alias   g='git'
   alias  gs='git status'
   alias  gl='git log'
   alias  gp='git push'
   alias gaa='git add -A'
   alias gch='git checkout'
   alias gcm='git commit -m'
   alias gbr='git branch'
   alias glg='git log --graph --pretty=oneline --abbrev-commit'

   alias -s gz='tar -xzvf'
   alias -s tgz='tar -xzvf'
   alias -s zip='unzip'
   alias -s bz2='tar -xjvf'
   alias -s php=v
   alias -s py=v
   alias -s rb=v
   alias -s html=v
   alias -s md=typora
   alias -s zshrc=v
   alias -s json=v
   alias -s zsh=v
   alias -s conf=v
   alias -s xml=v
   EOF
   ```

3. ssr安装

   ```sh
   wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh && chmod +x shadowsocks-all.sh && ./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
   ```

4. ssr配置

   ```sh
   ln -s /etc/init.d/shadowsocks-r /usr/bin/ssr
   cat>/etc/shadowsocks-r/config.json<<EOF
   {
     "server":"0.0.0.0",
     "server_ipv6": "[::]",
     "local_address":"127.0.0.1",
     "local_port":1080,
     "port_password":{
         "8081":"star733",
         "8083":{"protocol":"origin", "obfs":"plain", "password":"star733"},
         "443":{"protocol":"origin", "obfs":"plain", "password":"star733"},
         "8110":{"protocol":"origin", "obfs":"plain", "password":"star733"},
         "8100":{"method":"none","protocol":"auth_chain_a", "obfs":"tls1.2_ticket_auth", "password":"star733"},
         "8101":{"method":"none","protocol":"auth_chain_a", "obfs":"plain", "password":"star733"},
         "8102":{"method":"none","protocol":"auth_chain_b", "obfs":"plain", "password":"star733"},
         "8103":{"method":"none","protocol":"auth_chain_c", "obfs":"plain", "password":"star733"},
         "8104":{"method":"none","protocol":"auth_chain_d", "obfs":"plain", "password":"star733"}
     },
     "timeout":300,
     "method": "rc4-md5",
     "protocol": "auth_sha1_v4",
     "protocol_param": "",
     "obfs": "http_simple",
     "obfs_param": "",
     "redirect": "",
     "dns_ipv6": false,
     "fast_open": false,
     "workers": 1
   }
   EOF
   ssr restart

   ```

5. ss-libev配置

   ```
   ln -s /etc/init.d/shadowsocks-libev /usr/bin/ss
   cat>/etc/shadowsocks-libev/config.json<<EOF
   {
       "server":"0.0.0.0",
       "server_port":443,
       "local_address":"127.0.0.1",
       "local_port":1080,
       "password":"star733",
       "timeout":300,
       "method":"aes-256-cfb",
       "fast_open":true,
       "no_delay":true
   }
   EOF
   ssr restart
   ssr status
   ```

6. bbr

   ```sh
   wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh
   chmod +x bbr.sh
   ./bbr.sh
   ```

7. bbr(openvz)

   ```
   # debian nanqinlang魔改 vpsao5刀年付测试成功
   wget https://raw.githubusercontent.com/nanqinlang-tcp/tcp_nanqinlang/master/LKL/bash/tcp_nanqinlang-lkl-debian-1.1.1.sh
   bash tcp_nanqinlang-lkl-debian-1.1.1.sh
   # centos nanqinlang魔改 测试失败
   wget https://raw.githubusercontent.com/nanqinlang-tcp/tcp_nanqinlang/master/LKL/bash/tcp_nanqinlang-lkl-centos-1.1.1.sh
   bash tcp_nanqinlang-lkl-centos-1.1.1.sh
   # 91yun 测试成功 但效果不如魔改
   wget --no-check-certificate https://github.com/91yun/uml/raw/master/lkl/install.sh && bash install.sh
   ```

8. aria2安装

   老版不支持token，也就不支持aria2ng，以下为1.31版源码编译

   ```sh
   # install
   yum install bzip2 -y
   yum install gcc* -y
   cd /tmp
   wget https://github.com/aria2/aria2/releases/download/release-1.31.0/aria2-1.31.0.tar.gz
   tar zxf aria2-1.31.0.tar.gz
   cd aria2-1.31.0
   ./configure
   make&&make install
   ```

   编译时间很长，不如直接把我之前编译好的拿来用(推荐)

   ```sh
   rm /usr/local/bin/aria2c -Rf
   wget https://raw.githubusercontent.com/tkkcc/vps/master/aria2c -O /usr/local/bin/aria2c
   chmod a+x /usr/local/bin/aria2c
   ```

7. aria2配置

   ```sh
   mkdir /etc/aria2/
   echo > /etc/aria2/aria2.session
   #chsh -s /bin/bash www
   echo "/usr/local/bin/aria2c --conf=/etc/aria2/aria2.conf -D" >> /etc/rc.local
   chmod +x /etc/rc.d/rc.local
   cat>/etc/aria2/aria2.conf<<EOF
   dir=/downloads
   #disk-cache=32M
   file-allocation=none
   continue=true
   max-concurrent-downloads=10
   max-connection-per-server=16
   min-split-size=10M
   split=25
   #max-overall-download-limit=0
   #max-download-limit=0
   #max-overall-upload-limit=0
   #max-upload-limit=0
   disable-ipv6=false
   input-file=/etc/aria2/aria2.session
   save-session=/etc/aria2/aria2.session
   save-session-interval=60
   enable-rpc=true
   rpc-allow-origin-all=true
   rpc-listen-all=true
   #event-poll=select
   rpc-listen-port=40053
   rpc-secret=bilabila
   #rpc-user=bilibili
   #rpc-passwd=bilabila
   #follow-torrent=true
   listen-port=51413
   bt-max-peers=55
   enable-dht=true
   enable-dht6=true
   #dht-listen-port=6881-6999
   bt-enable-lpd=true
   enable-peer-exchange=true
   #bt-request-peer-speed-limit=50K
   peer-id-prefix=-TR2770-
   user-agent=Transmission/2.77
   seed-ratio=0
   #force-save=false
   #bt-hash-check-seed=true
   bt-seed-unverified=true
   bt-save-metadata=true
   EOF
   ```

8. lnmp安装

   ```sh
   cd
   wget -c http://soft.vpser.net/lnmp/lnmp1.4.tar.gz && tar zxf lnmp1.4.tar.gz && cd lnmp1.4 && ./install.sh lnmp
   ```

9. web

   ```sh
   mkdir -p /www /downloads 
   chown www:www /www -R
   chown www:www /downloads -R

   #ariang
   cd
   rm aria2* -Rf
   wget https://github.com/mayswind/AriaNg/releases/download/0.2.0/aria-ng-0.2.0.zip
   unzip aria-ng-0.2.0.zip -d /www/aria2ng

   #htpasswd
   htpasswd -bc /www/.1 0 bilabila

   #kodexplorer
   cd 
   wget http://static.kodcloud.com/update/download/kodexplorer4.24.zip --no-check-certificate
   unzip kodexplorer4.24.zip -d /www/kod
   chmod -Rf 777 /www/kod/
   ```

10. nginx配置

    ```sh
    cat>/usr/local/nginx/conf/nginx.conf<<EOF
    user  www www;
    worker_processes  auto;
    events {
        worker_connections  2000;
        accept_mutex on;
        multi_accept on;
        use epoll;
    }
    http {
        include       mime.types;
        default_type  application/octet-stream;
        server_names_hash_bucket_size 128;
        client_header_buffer_size 32k;
        large_client_header_buffers 4 32k;
        client_max_body_size 10G;
        client_body_temp_path /www/nginx_tmp;
        sendfile   on;
        tcp_nopush on;
        keepalive_timeout 60;
        tcp_nodelay on;
        fastcgi_connect_timeout 300;
        fastcgi_send_timeout 300;
        fastcgi_read_timeout 300;
        fastcgi_buffer_size 64k;
        fastcgi_buffers 4 64k;
        fastcgi_busy_buffers_size 128k;
        fastcgi_temp_file_write_size 256k;
        gzip on;
        gzip_min_length  1k;
        gzip_buffers     4 16k;  
        gzip_http_version 1.1;
        gzip_comp_level 2;
        gzip_types     text/plain application/javascript application/x-javascript text/javascript text/css application/xml application/xml+rss;
        gzip_vary on;
        gzip_proxied   expired no-cache no-store private auth;
        gzip_disable   "MSIE [1-6]\.";
        server_tokens off;
        access_log off;
        index index.html index.php;
        server {
            root  404;
            auth_basic "hahca";
            auth_basic_user_file /www/.1;
        }
        #server {
        #    server_name  ~^files?\.;
        #    root  /downloads;
        #    index index.php;
        #    include enable-php.conf;
        #    location ~ /\. {deny all;}
        #    #auth_basic "haha";
        #    #auth_basic_user_file /www/.1;
        #}
        server {
            server_name  ~^vfm2?\.;
            root  /www/vfm2/vfm;
            index index.php;
            include enable-php.conf;
            location ~ /\. {deny all;}
            #auth_basic "haha";
            #auth_basic_user_file /www/.1;
        }
        server {
            server_name  ~^kod?\.;
            root  /www/kod;
            index index.php;
            include enable-php.conf;
            location ~ /\. {deny all;}
            #auth_basic "haha";
            #auth_basic_user_file /www/.1;
        }
        server {
            server_name ~^aria2?\.;
            root   /www/aria2ng;
            location ~ /\.ht {deny  all;}
        }
        server {
            server_name ~^lab2?\.;
            location / {
              proxy_pass http://localhost:8080;
            }
        }
        
        server {
            server_name ~^downloads?\.;
            root /downloads;
            autoindex on;
            autoindex_exact_size on;
            autoindex_localtime on;
            #deny all;
            auth_basic "qunidaye";
            auth_basic_user_file /www/.1;
        }
    }
    EOF
    service nginx reload
    ```

11. v2ray安装

    ```bash
    #bash <(curl -L -s https://install.direct/go.sh)
    mkdir -p /etc/v2ray
    wget https://install.direct/go.sh
    source go.sh
    ```

14. v2ray配置（tcp）

    ```sh
    cat >/etc/v2ray/config.json<<EOF
    {
      "log": {
        "access": "/var/log/v2ray/access.log",
        "error": "/var/log/v2ray/error.log",
        "loglevel": "info"
      },
      "inbound": {
        "port": 8086,
        "listen": null,
        "protocol": "vmess",
        "settings": {
          "auth": null,
          "udp": true,
          "ip": null,
          "clients": [
            {
              "id": "05d30690-07a4-0b6b-931d-08c71f4e9422",
              "alterId": 100,
              "security": "aes-128-gcm"
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
              "outboundTag": "blocked",
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
    ```

15. h5ai

    ```
    cd /downlaod
    wget https://release.larsjung.de/h5ai/h5ai-0.29.0.zip
    unzip h5ai-0.29.0.zip
    chown www:www _h5ai
    # vim /usr/local/php/etc/php.ini 修改scandir、exec、passthru
    service php-fpm restart
    service nginx reload
    ```

16. mongodb

    ```
    cat >/etc/yum.repos.d/mongodb-org-3.4.repo <<EOF
    [mongodb-org-3.4]
    name=MongoDB Repository
    baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
    gpgcheck=1
    enabled=1
    gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
    EOF
    sudo yum install mongodb-org
    sudo systemctl start mongod
    sudo systemctl enable mongod
    ```

    ​


