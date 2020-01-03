---
layout:     post
title:      使用wsl-terminal美化WSL
date:       2018-02-17 20:00:00 +0800
author:     HYJ
header-img: img/default.jpg
catalog: true
tags: [WSL, Linux]
---


本文基于Windows10 1709更新后在Windows Store安装的Bash
 <!-- more --> 

# 下载安装`wsl-terminal`
`wsl-terminal`是一个[Github](https://github.com/goreliu/wsl-terminal)上的一个基于`mintty`和`wslbridge`的WSL模拟器

1. [下载](https://github.com/goreliu/wsl-terminal/releases) wsl-terminal
2. 解压后运行目录下的`open-wsl.exe` 
3. 运行`tools`目录下的`1-add-open-wsl-terminal-here-menu.js` ，注意当前该脚本需要有完全控制System的权限

# 安装Zsh和oh-my-zsh

1. 安装Zsh

   ```bash
   sudo apt-get install zsh
   sudo apt-get install git-core
   ```

2. 安装oh-my-zsh

   ```bash
   sudo wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh
   ```

3. 将其配置为默认Shell

   ```bash
   chsh -s /bin/zsh
   zsh
   ```

   用`vim`打开`.bashrc` , 在文件开头输入以下内容

   ```C
   # Launch Zsh
   if [ -t 1 ]; then
   exec zsh
   fi
   ```

   

# 修改颜色及主题

1. 用`vim`打开`.zshrc`  , 将主题修改为`agnoster ` ，重启终端

2. 下载 [DejaVu Sans Mono for Powerline](https://github.com/powerline/fonts/blob/master/DejaVuSansMono/DejaVu%20Sans%20Mono%20for%20Powerline.ttf) 并安装以修复相关字体缺失问题

3. 在`wsl-terminal` ->`选项` ->`字体` 中修改体字为`DejaVu Sans Mono for Powerline` ，注意`wsl-terminal`所在目录的权限问题

4. 在`wsl-terminal` ->`选项` ->`外观` ->`主题` 中选择自己喜欢的主题

5. 下载 [dircolors-solarized](https://github.com/seebi/dircolors-solarized) ，在`.zshrc` 中加入`eval dircolors ~/Git/dircolors-solarized/dircolors.256dark` 以激活。

6. 用`vim` 打开 `dircolors.256dark` ，将`EXEC 00;38;5;64` 替换为 `EXEC 00;38;5;244`







## 参考资料

* https://medium.com/@Andreas_cmj/how-to-setup-a-nice-looking-terminal-with-wsl-in-windows-10-creators-update-2b468ed7c326