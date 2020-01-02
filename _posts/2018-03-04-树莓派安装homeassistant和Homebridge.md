layout:     post
title:      树莓派安装homeassistant和Homebridge
date:       2018-03-04 19:25:27
author:     HYJ
header-img: img/default.jpg
catalog: true
tags:
    - 树莓派

 <!-- more --> 


# 安装Python3并更换镜像源
1. `sudo apt-get install python3-pip python3-venv`
2. 编辑 `~/.pip/pip.conf` 文件，用以下内容取代：
   ```
   [global]
   index-url = https://mirrors.ustc.edu.cn/pypi/web/simple
   ```
> 注:应先创建`.pip`文件夹及`pip.conf`

# 安装Homeassistant
1. `cd /home/pi/Desktop && python3 -m venv homeassistant`
2. `cd homeassistant`
3. `source bin/activate`
4. `python3 -m pip install wheel`
5. `python3 -m pip install homeassistant==0.61.0`
6. `hass --open-ui`
> 若出现 `Error while setting up platform xiaomi_miio` 请执行 `pip3 install construct==2.8.21`
> 目前米家插件 `miio` 支持0.61.0和0.62.3这两个版本

# 设置homeassistant开机启动
1. 创建 `home-assistant@pi.service` ,并放入以下内容 
   `sudo vim /etc/systemd/system/home-assistant@pi.service`
   ```bash
   [Unit]
   Description=Home Assistant
   After=network.target

   [Service]
   Type=simple
   User=pi
   Environment=PATH="$VIRTUAL_ENV/bin:$PATH"
   ExecStart=/home/pi/Desktop/homeassistant/bin/hass -c "/home/pi/.homeassistant"

   [Install]
   WantedBy=multi-user.target
   ```
2. 更新系统设置 `sudo systemctl --system daemon-reload`
3. 设置HomeAssistant开机启动 `sudo systemctl enable home-assistant@pi.service`
4. 启动HomeAssistant `sudo systemctl start home-assistant@pi.service`

常用操作
```
#重新启动HomeAssistant
sudo systemctl restart home-assistant@pi.service
#查看HomeAssistant状态
sudo systemctl status home-assistant@pi.service
```

# 安装Homebridge
1. `sudo apt-get install gcc`
2. 安装nodejs 
   ```bash
   curl -sL https://deb.nodesource.com/setup_7.x | sudo -E bash - 
   sudo apt-get install -y nodejs
   ```
3. `sudo apt-get install libavahi-compat-libdnssd-dev`
4. `sudo npm install -g --unsafe-perm homebridge`
   ![](https://user-gold-cdn.xitu.io/2018/2/3/16159cbe79a71165?w=1030&h=876&f=png&s=183313)
5. `sudo npm install -g --unsafe-perm homebridge-homeassistant`
   ![](https://user-gold-cdn.xitu.io/2018/2/3/16159cc12e215b06?w=1033&h=876&f=png&s=177555)

# Homebridge配置
1. 在终端中输入
   ```bash
   mkdir ~/.homebridge
   cd ~/.homebridge
   vim config.json
   ```
2. 复制以下内容到`config.json`中
  ```
  {
  	"bridge": {
  		"name":"Homebridge",
  		"username":"B8:27:EB:AA:CE:43",
  		"port":51826,
  		"pin":"233-78-123"
  	},

  "platforms": [
  {
  	"platform": "HomeAssistant",
  	"name": "HomeAssistant",
  	"host": "http://127.0.0.1:8123",
  	"password": "HaHb7890",
  	"supported_types": ["light"]
  }]
  }
  ```
  其中:
* `bridge-name` : 你在 iPhone 的 HomeKit 上面看到桥接设备网关的名称
* `bridge-username` ： 树莓派 MAC 地址，可使用`ifconfig`查看
* `bridge-port` ： HomeBridge 使用的端口，默认就好
* `bridge-pin` ： 在 iPhone 上认证 HomeBridge 网关的密码，这个按照你自己喜好来输入吧
* `platforms-host` ： 你访问 HomeAssistant 的地址 + 端口
* `platforms-password` ： 你在 HomeAssistant 设置的登陆密码

3. 启动 `homebridge -D` ，用手机扫描出现的二维码完成配对

![](https://user-gold-cdn.xitu.io/2018/2/3/16159ed69446f903?w=1442&h=876&f=png&s=167151)

# 设置Homebridge开机启动
1. 创建 `homebridge@pi.service` ,并放入以下内容 
    `sudo vim /etc/systemd/system/homebridge@pi.service`
  ```
  [Unit]
  Description=Homebridge
  After=network.target
  Requires=home-assistant@pi.service

  [Service]
  Type=simple
  User=pi
  ExecStart=/usr/bin/homebridge

  [Install]
  WantedBy=multi-user.target
  ```

  ```
  #更新系统设置
  sudo systemctl --system daemon-reload

  #设置HomeAssistant开机启动
  sudo systemctl enable homebridge@pi.service

  常用操作
  sudo systemctl stop homebridge@pi.service
  sudo systemctl status homebridge@pi.service
  ```

## 如何更新Hhomeassistant
1. 切换至虚拟环境 `cd /home/pi/Desktop/homeassistant && source bin/activate`
2. 升级系统 `pip3 install --upgrade homeassistant`



# 参考资料

* 官方安装指南 [https://home-assistant.io/docs/installation/virtualenv/](https://home-assistant.io/docs/installation/virtualenv/)
* Github上Error while setting up platform xiaomi_miio的解决办法 [https://github.com/home-assistant/home-assistant/issues/11768](https://github.com/home-assistant/home-assistant/issues/11768)
* Installation in Python virtual environment [https://home-assistant.io/docs/installation/virtualenv/](https://home-assistant.io/docs/installation/virtualenv/)
* 官方开机启动脚本 [https://home-assistant.io/docs/autostart/systemd/](https://home-assistant.io/docs/autostart/systemd/)
* 配置systemctl开机启动 [https://bbs.hassbian.com/thread-27-1-1.html](https://bbs.hassbian.com/thread-27-1-1.html)
* Systemd 入门教程：命令篇
   [http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)
* Systemd 入门教程：实战篇
   [http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-part-two.html](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-part-two.html)
* 从米家到 HomeKit，你只需要一个树莓派
   [https://sspai.com/post/38358](https://sspai.com/post/38358)
* 入门智能家居，从 IFTTT 到 HomeKit 自动化（二） - 少数派 [https://sspai.com/post/39881](https://sspai.com/post/39881)
* 查看mac地址 [http://blog.itpub.net/291731/viewspace-694141](http://blog.itpub.net/291731/viewspace-694141)
* Systemd Service for homebridge [https://gist.github.com/johannrichard/0ad0de1feb6adb9eb61a](https://gist.github.com/johannrichard/0ad0de1feb6adb9eb61a)