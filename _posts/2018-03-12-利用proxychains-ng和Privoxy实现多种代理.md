---
layout:     post
title:      利用Proxychains-ng和Privoxy实现多种代理
date:       2018-03-12 20:11:18 +0800
author:     HYJ
header-img: img/default.jpg
catalog: true
tags: [Proxy]
---

<!-- TOC -->
# 目录

- [临时使用代理](#临时使用代理)
- [安装Shadowsocks](#安装shadowsocks)
    - [安装Shadowsocks-qt5](#安装shadowsocks-qt5)
    - [安装shadowsocks Python](#安装shadowsocks-python)
    - [安装Shadowsocks-libev](#安装shadowsocks-libev)
- [使用proxychains-ng](#使用proxychains-ng)
    - [安装Proxychains-ng](#安装proxychains-ng)
    - [配置Proxychains-ng](#配置proxychains-ng)
    - [Proxychains-ng使用方法](#proxychains-ng使用方法)
- [安装并配置Privoxy](#安装并配置privoxy)
    - [配置浏览器](#配置浏览器)
- [浏览器实现PAC代理](#浏览器实现pac代理)
- [参考资料](#参考资料)

<!-- /TOC -->

# 临时使用代理
这种方法只对当前终端有效，如果是http代理，则输入：

```
export http_proxy=http://proxyAddress:port
```
或
```
export ALL_PROXY=socks5://127.0.0.1:1080
```

更多（临时）方法推荐访问 [让终端走代理的几种方法](https://blog.fazero.me/2015/09/15/%E8%AE%A9%E7%BB%88%E7%AB%AF%E8%B5%B0%E4%BB%A3%E7%90%86%E7%9A%84%E5%87%A0%E7%A7%8D%E6%96%B9%E6%B3%95/) 



# 安装Shadowsocks

## 安装Shadowsocks-qt5

Ubuntu/Debian

在 [Releases · shadowsocks/shadowsocks-qt5](https://github.com/shadowsocks/shadowsocks-qt5/releases) 下载最新版本，双击运行。

Archlinux

```bash
sudo pacman -S 	shadowsocks-qt5
```



## 安装shadowsocks Python

```bash
sudo apt-get install libsodium-dev
sudo apt-get install python-setuptools
sudo easy_install pip
pip install https://github.com/shadowsocks/shadowsocks/archive/master.zip -U
```

`shadowsocks Python` 使用 `ssserver` 启动服务器端，使用 `sslocal ` 启动客户端

运行方法

```bash
sslocal -s 服务器地址 -p 服务器端口 -l 本地端端口 -k 密码 -m 加密方法
```

指定配置文件的位置

```bash
sslocal -c /etc/shadowsocks/config.json
```

配合nohup和&可以使之后台运行，关闭终端也不影响

```bash
nohup sslocal -s 服务器地址 -p 服务器端口 -l 本地端端口 -k 密码 -m 加密方法 &
```



## 安装Shadowsocks-libev

参考官方教程 [shadowsocks/shadowsocks-libev: libev port of shadowsocks](https://github.com/shadowsocks/shadowsocks-libev#build-deb-package-from-source) 

先安装编译依赖

```bash
## Debian / Ubuntu
sudo apt-get install --no-install-recommends gettext build-essential autoconf libtool libpcre3-dev asciidoc xmlto libev-dev libc-ares-dev automake libmbedtls-dev libsodium-dev
## CentOS / Fedora / RHEL
sudo yum install gettext gcc autoconf libtool automake make asciidoc xmlto c-ares-devel libev-devel
## Arch
sudo pacman -S gettext gcc autoconf libtool automake make asciidoc xmlto c-ares libev
```

安装 `Libsodium`

```bash
export LIBSODIUM_VER=1.0.13
wget https://download.libsodium.org/libsodium/releases/libsodium-$LIBSODIUM_VER.tar.gz
tar xvf libsodium-$LIBSODIUM_VER.tar.gz
pushd libsodium-$LIBSODIUM_VER
./configure --prefix=/usr && make
sudo make install
popd
sudo ldconfig
```

安装 `MbedTLS`

```bash
export MBEDTLS_VER=2.6.0
wget https://tls.mbed.org/download/mbedtls-$MBEDTLS_VER-gpl.tgz
tar xvf mbedtls-$MBEDTLS_VER-gpl.tgz
pushd mbedtls-$MBEDTLS_VER
make SHARED=1 CFLAGS=-fPIC
sudo make DESTDIR=/usr install
popd
sudo ldconfig
```

编译 `shadowsocks-libev` 的deb安装包

```bash
cd /usr/local/src
rm *shadowsocks-libev*.{deb,changes}
git clone https://github.com/shadowsocks/shadowsocks-libev.git --recursive
cd shadowsocks-libev
./autogen.sh
dpkg-buildpackage -b -us -uc -i -d
cd ..
dpkg -i *shadowsocks-libev*.deb
```

注意每一步有没有报错，假如出现`shadowsocks-libev depends on apg | pwgen; however:  Package apg is not installed. Package pwgen is not installed. `  使用当前包管理器安装缺少的依赖即可 `sudo apt-get install  apg  pwgen`

`shadowsocks-libev` 使用 `ss-server` 启动服务器端，使用 `ss-local ` 启动客户端



# 使用proxychains-ng

[Proxychains ng](https://github.com/rofl0r/proxychains-ng) 是新一代的proxychains（原项目已不再维护），这是Github上对其的介绍：一个预加载器，它将调用与动态链接程序中的套接字挂钩，并通过一个或多个socks/http代理重定向它。

## 安装Proxychains-ng 
Archlinux下直接安装即可
```bash
sudo pacman -S proxychains-ng
```

截至目前(Ubuntu Bionic)，Ubuntu的包管理器仍未收录 proxychains-ng，所以需要手动编译安装。

```bash
git clone https://github.com/rofl0r/proxychains-ng.git
cd proxychains-ng
./configure --prefix=/usr --sysconfdir=/etc
make
sudo make install
sudo make install-config
```

安装完成后可删除目录  `cd .. && rm -rf proxychains-ng` 



## 配置Proxychains-ng

```bash
sudo vim /etc/proxychains.conf
```

将其中的 `socks4     127.0.0.1 9050` 修改成自己的socks地址。


## Proxychains-ng使用方法

在需要代理的命令前加上 proxychains4

```bash
proxychains4 command
```

`ZSH`下可使用如下alias： `alias pc="proxychains4"` ，这样每次只需要输入`pc` ,若要使用`sudo`命令 ，请在`sudo ` 后输入`proxychains4`


# 安装并配置Privoxy
Archlinux
```bash
sudo pacman -S privoxy
```

Ubuntu
```bash
sudo apt-get install privoxy
```

编辑 `/etc/privoxy/config` , 添加socks5转发，并监听本地8010端口

```json
forward-socks5 / 127.0.0.1:1080 .
listen-address  127.0.0.1:8010
```

重启服务使配置生效
```bash
sudo systemctl restart privoxy.service
```

设置开机启动
```bash
sudo systemctl enable privoxy.service
```

## 配置浏览器

Privoxy只能代理**HTTP**和HTTPS流量，各浏览器代理方法详见 [Starting Privoxy](http://www.privoxy.org/user-manual/startup.html) 

> 有关Privoxy的PAC用法请参考 [ss-local + privoxy 代理](https://www.zfl9.com/ss-local.html)  
> 这里提供了有关Privoxy的更详细教程 [Privoxy 教程](https://blog.zfanw.com/privoxy-tutorial/#%E5%90%AF%E5%8A%A8-privoxy)



# 浏览器实现PAC代理

安装 [SwitchyOmega](https://github.com/FelisCatus/SwitchyOmega) ，Firefox（官方强烈建议使用Nightly版本）在[这里](https://addons.mozilla.org/en-US/firefox/addon/switchyomega/)安装该插件

具体PAC配置方式可以参考 [ubuntu使用shadowsocks](https://www.sundabao.com/ubuntu%E4%BD%BF%E7%94%A8shadowsocks/)


# 参考资料
* [Setup and test proxychains-ng on Ubuntu 14.04](https://gist.github.com/marcinwol/b8e502eede230cc33c43)
* [利用proxychains在终端使用socks5代理 | fazero](https://blog.fazero.me/2015/08/31/%E5%88%A9%E7%94%A8proxychains%E5%9C%A8%E7%BB%88%E7%AB%AF%E4%BD%BF%E7%94%A8socks5%E4%BB%A3%E7%90%86/)
* [Ubuntu 16.04编译shadowsocks-libev - 予而不语](https://heiybb.com/ubuntu-ss.hf)
* [Shadowsocks (简体中文) - ArchWiki](https://wiki.archlinux.org/index.php/Shadowsocks_(简体中文))