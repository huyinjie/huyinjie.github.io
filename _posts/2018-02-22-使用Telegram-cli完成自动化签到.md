---
layout:     post
title:      使用Telegram-cli完成自动化签到
date:       2018-02-22 20:00:00 +0800
author:     HYJ
header-img: img/default.jpg
catalog: true
tags:
    - Linux
---


如今越来越多的网站使用Telegram Bot签到，[telegram-cli](https://github.com/vysheng/tg) 是一个Telegram的[非官方客户端](https://telegram.org/apps)，可以实现在终端中访问Telegram，本文使用telegram-cli+crontab完成了自动签到
 <!-- more --> 
# 在 CentOS 下安装 telegram-cli

快速编译安装

```bash
cd ~/
git clone --recursive https://github.com/vysheng/tg.git && cd tg
yum install jansson-devel lua-devel readline-devel libconfig-devel libevent-devel
./configure
make
```
将编译生成的`bin/telegram-cli`和`tg-server.pub`移动到同一目录下，这里创建一个`mytg`目录
> 不必在 https://my.telegram.org/apps 上获取publickey，除非此项目自带的key已经过时
```bash
mkdir mytg
cp bin/telegram-cli mytg/
cp tg-server.pub mytg/
cd mytg
```
在`mytg`中创建`telegram.config`配置文件，并放入以下内容
```bash
vim ~/tg/mytg/telegram.config
```

```bash
default_profile = "mytg";

mytg = {
    config_directory = "~/tg/mytg/";
};
```
运行`telegram-cli`
```bash
./telegram-cli -k tg-server.pub -c telegram.config
```
&nbsp;&nbsp;
这里使用一个脚本文件来快速发送消息，新建`~/tg/mytg/telegram_standalone.sh`

```bash
vim ~/tg/mytg/telegram_standalone.sh
```

```bash
#!/bin/bash

MAIN_DIRECTORY="/root/tg/mytg/"
USER=$1
SUBJECT=$2
TEXT=$3

cd $MAIN_DIRECTORY
if [[ $? -ne 0 ]]; then
        echo "Error to enter in the main directory"
        exit 1
fi

./telegram-cli -k tg-server.pub -c telegram.config -WR -e "msg $USER $SUBJECT" || exit 1

exit 0
```

修改脚本权限

```bash
chmod +x ~/tg/mytg/telegram_standalone.sh
```



# 修改系统时区

如果使用国外VPS执行crontab定期任务，务必修改时区并**重启crontab服务**

修改为北京时区

```bash
ntpdate -u cn.pool.ntp.org
timedatectl set-timezone Asia/Shanghai
```

重启crontab服务

```bash
service crond restart
```



# 新建crontab任务

将vim设为默认文本编辑器

```bash
export VISUAL=vim
```

或
```bash
export EDITOR=vim
```

新建crontab任务，运行

```bash
crontab -e
```

将打开vim，进入Insert编辑模式，放入以下内容

```
* 10 * * * /root/tg/mytg/telegram_standalone.sh username 签到
```

其中，`username` 可修改为Telegram中任意Bot的id，`签到` 为向该Bot发送的签到指令，可任意修改，以上脚本每天上午10点会自动执行。

运行`crontab -l` 可查看目前创建的crontab任务。

假如你的账号添加了太多Telegram群组，telegram-cli可能无法接收/发送所有消息，可以使用`dialog_list`来加载会话列表或使用`-w`参数启动telegram-cli，使程序启动后会话列表先加载再发出信息。

> 关于telegram-cli更多的启动参数详见 https://github.com/vysheng/tg/wiki/Telegram-CLI-Arguments



## 参考资料

* https://github.com/vysheng/tg/issues/1325
* https://github.com/vysheng/tg/issues/290
* https://github.com/vysheng/tg/issues/1350
* http://blog.csdn.net/brad_chen/article/details/50318297
* 参考安装脚本 
    * https://gist.github.com/gnumoksha/a95f237d82733ce1f748
    * https://github.com/vysheng/tg/issues/1026
    * https://www.91yun.co/archives/5691
* 修改时区问题 https://menyifan.com/2016/11/08/crontab_timezone/
    * https://webcache.googleusercontent.com/search?q=cache:e9NEi3g1wQAJ:https://www.phpbulo.com/archives/171.html+&cd=3&hl=zh-CN&ct=clnk&gl=us
    * https://my.oschina.net/doraemon/blog/12303
* [Linux Tools Quick Tutorial](http://linuxtools-rst.readthedocs.io/zh_CN/latest/index.html) 上的 [crontab 教程](http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html#crontab)
* 将vim设为默认文本编辑器 https://superuser.com/questions/281617/change-default-text-editor-for-crontab-to-vim