---
title: 手动编译Nginx并配置Let's Encrypt
date: 2018-06-22 13:43:54
tags: Server
---

 <!-- more --> 

#### 前置准备

安装依赖

```bash
sudo apt-get install build-essential libtool
```

创建并进入临时目录

```bash
mkdir tmp && cd /tmp
```

下载并解压 [PCRE](https://ftp.pcre.org/pub/pcre/) 

```bash
wget https://ftp.pcre.org/pub/pcre/pcre-8.42.tar.gz && tar xzvf pcre-8.42.tar.gz
```

下载并解压 [zlib](http://www.zlib.net/)

```bash
wget http://www.zlib.net/zlib-1.2.11.tar.gz && tar xzvf zlib-1.2.11.tar.gz
```

下载并解压 [OpenSSL](https://www.openssl.org/source/) 

```bash
wget https://www.openssl.org/source/openssl-1.1.0h.tar.gz && tar xzvf openssl-1.1.0h.tar.gz
```



#### 安装 ngx_lua 依赖

下载并安装 [LuaJIT](http://luajit.org/download.html) 

```bash
wget http://luajit.org/download/LuaJIT-2.1.0-beta3.tar.gz
tar -xzvf LuaJIT-2.1.0-beta3.tar.gz
cd LuaJIT-2.1.0-beta3
sudo make && sudo make install
```

创建软链接

```bash
ln -sf luajit-2.1.0-beta3 /usr/local/bin/luajit
```

设置环境变量

```bash
export LUAJIT_LIB=/usr/local/lib
export LUAJIT_INC=/usr/local/include/luajit-2.1/
```
下载并解压 [ngx_devel_kit](https://github.com/simpl/ngx_devel_kit/releases) 

```bash
wget https://github.com/simplresty/ngx_devel_kit/archive/v0.3.1rc1.tar.gz && tar -xzvf v0.3.1rc1.tar.gz
```

下载并解压 [ngx_lua](https://github.com/openresty/lua-nginx-module/releases) 

```bash
wget https://github.com/openresty/lua-nginx-module/archive/v0.10.13.tar.gz && tar -xzvf v0.10.13.tar.gz
```

安装Lua

```bash
apt-get install lua5.1-0-dev
yum install lua-devel
```

删除所有的压缩文件 .tar.gz 

```bash
rm -rf *.tar.gz
```



#### 编译安装Nginx

下载nginx并进入Nginx目录

```bash
wget https://nginx.org/download/nginx-1.14.0.tar.gz && tar -xzvf nginx-1.14.0.tar.gz 
```


```bash
cd ~/nginx-1.14.0
```


```bash
./configure --prefix=/usr/share/nginx \
            --sbin-path=/usr/sbin/nginx \
            --pid-path=/run/nginx.pid \
            --error-log-path=/var/log/nginx/error.log \
            --http-log-path=/var/log/nginx/access.log \
            --with-ld-opt=-Wl,-rpath,/usr/local/lib/ \
            --with-http_ssl_module \
            --with-http_stub_status_module \
            --with-zlib=../zlib-1.2.11 \
            --with-pcre=../pcre-8.42 \
            --with-openssl=../openssl-1.1.0h \
            --add-module=../ngx_devel_kit-0.3.1rc1 \
            --add-module=../lua-nginx-module-0.10.13 
```

```bash
sudo make -j 2
sudo make install
```

#### 配置Nginx

检查Nginx版本和编译参数

```bash
sudo nginx -v && sudo nginx -V
```

检查语法和潜在的错误

```bash
sudo vim /etc/systemd/system/nginx.service
```

创建system unit文件

```bash
sudo vim /etc/systemd/system/nginx.service
```

```
[Unit]
Description=A high performance web server and a reverse proxy server
After=network.target

[Service]
Type=forking
PIDFile=/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t -q -g 'daemon on; master_process on;'
ExecStart=/usr/sbin/nginx -g 'daemon on; master_process on;'
ExecReload=/usr/sbin/nginx -g 'daemon on; master_process on;' -s reload
ExecStop=-/sbin/start-stop-daemon --quiet --stop --retry QUIT/5 --pidfile /run/nginx.pid
TimeoutStopSec=5
KillMode=mixed

[Install]
WantedBy=multi-user.target
```

启用并开启服务

```bash
sudo systemctl start nginx.service && sudo systemctl enable nginx.service
```

检查Nginx是否会开机启动

```bash
sudo systemctl is-enabled nginx.service
```
检查Nginx运行状态
```bash
sudo systemctl status nginx.service
ps aux | grep nginx
curl -I 127.0.0.1
```

创建UFW NGINX配置文件

```bash
sudo vim /etc/ufw/applications.d/nginx
```

```
[Nginx HTTP]
title=Web Server (Nginx, HTTP)
description=Small, but very powerful and efficient web server
ports=80/tcp

[Nginx HTTPS]
title=Web Server (Nginx, HTTPS)
description=Small, but very powerful and efficient web server
ports=443/tcp

[Nginx Full]
title=Web Server (Nginx, HTTP + HTTPS)
description=Small, but very powerful and efficient web server
ports=80,443/tcp
```

查看UFW配置

```bash
sudo ufw app list
```
#### 参考资料

* [How to Compile Nginx From Source on Ubuntu 16.04 - Vultr.com](https://www.vultr.com/docs/how-to-compile-nginx-from-source-on-ubuntu-16-04) 👍
* [NGINX Docs | Installing NGINX Open Source](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/#configure) 官方配置文档 👍
* [How To Install Nginx on Ubuntu 18.04 | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04)  包管理器安装Nginx，并配置UFW
* [手动编译 Nginx 并安装 VeryNginx | Viosey's Blog](https://blog.viosey.com/2017/10/12/compile-nginx-and-install-verynginx/) 编译安装Nginx和VeryNginx
* [Nginx模块Lua-Nginx-Module学习笔记（三）Nginx + Lua + Redis 已安装成功(非openresty 方式安装) - Tinywan - 博客园](https://www.cnblogs.com/tinywan/p/6534151.html)  列出了常见的安装问题



#### 安装 certbot

访问 [https://certbot.eff.org/](https://certbot.eff.org/) 获取不同系统的安装方式，以下为Nginx和Ubuntu16.04的安装方式

```bash
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python-certbot-nginx 
```

一键完成配置

```bash
sudo certbot --nginx
```

想要手动完成配置，请参考 [Let's Encrypt 给网站加 HTTPS 完全指南](https://ksmx.me/letsencrypt-ssl-https/)




