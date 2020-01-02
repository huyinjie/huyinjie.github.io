---
title: WSL18.04 Setup I
date: 2018-07-21 16:08:31
tags:

---
 <!-- more --> 

目前Windows Store中已经有多个Linux发行版： [Ubuntu](https://www.microsoft.com/store/p/ubuntu/9nblggh4msv6), [OpenSUSE](https://www.microsoft.com/store/apps/9njvjts82tjx), [SLES](https://www.microsoft.com/store/apps/9p32mwbh6cns), [Kali Linux](https://www.microsoft.com/store/apps/9PKR34TNCV07)  , [Debian GNU/Linux](https://www.microsoft.com/store/apps/9MSVKQC78PK6), WLinux。本文使用Ubuntu18.04。

<!-- TOC -->

- [安装Ubuntu 18.04 WSL](#安装ubuntu-1804-wsl)
- [将 Bash Console Here 添加到右键菜单](#将-bash-console-here-添加到右键菜单)
- [常用操作](#常用操作)
    - [常用操作](#常用操作-1)
    - [安装zsh和oh-my-zsh](#安装zsh和oh-my-zsh)
- [其他](#其他)

<!-- /TOC -->

> 已知WSL18.04在首次apt update时有 *Error: insufficient privileges to access the ebtables rulesets. 但似乎这个报错可以被忽略，该问题已经被解决(2018.9.23)，见 [Errors in Ubuntu 18.04 on Windows 10 - Microsoft Community](https://answers.microsoft.com/en-us/windows/forum/windows_10-windows_install/errors-in-ubuntu-1804-on-windows-10/fe349f3d-3d58-4d90-9f8f-c14d7c12af8b) , 这应该是一个Ubuntu的 [bug](https://bugs.launchpad.net/ubuntu/+source/ebtables/+bug/1774120)

### 安装Ubuntu 18.04 WSL
官方安装教程 [Windows Subsystem for Linux Installation Guide for Windows 10](https://docs.microsoft.com/en-us/windows/wsl/install-win10)

首先启用 “Windows Subsystem for Linux” 选项，在管理员模式下启动 Powershell 并运行以下指令

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

重启计算机后，打开 Microsoft Store，下载 WSL

![Snipaste_2018-07-21_16-09-14](https://huyinjiexyz-1251543717.cos.ap-shanghai.myqcloud.com/source/_posts/WSL18-04-Setup/Snipaste_2018-07-21_16-09-14.png)


### 将 Bash Console Here 添加到右键菜单

如果你的系统是 Insider 17666 (1809 later) 或更新版本，则WSL已经在右键菜单；若不是，则需要手动添加。

1\. 将以下内容复制进 Powershell ，回车运行  
该代码将会在“目录空白处”和“文件夹图标”上右键时出现"Bash Console Here"

```powershell
$null = 0..1 | % {
  $key = New-Item -Force $('HKCU:\Software\Classes\Directory{0}\shell\BashConsoleHere' -f ('', '\Background')[$_])
  $key | New-ItemProperty -Name '(Default)' -Value 'Bash Console Here' -Type String
  $key | New-ItemProperty -Name 'Icon' -Value '%LOCALAPPDATA%\lxss\bash.ico' -Type ExpandString
  $key = New-Item -Force "$($key.PsPath)\command"
  $key | New-ItemProperty -Name '(Default)' -Value "cmd /c cd `"$(('%1', '%V')[$_])`" && bash.exe" -Type String
}
```

⚠若只想在目录空白处右键时出现 "Bash Console Here", 将以下内容保存为一个 `.reg` 文件，双击运行

```powershell
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\SOFTWARE\Classes\Directory\Background\shell\BashConsoleHere]
@="Bash Console Here"
"Icon"=hex(2):25,00,4c,00,4f,00,43,00,41,00,4c,00,41,00,50,00,50,00,44,00,41,\
  00,54,00,41,00,25,00,5c,00,6c,00,78,00,73,00,73,00,5c,00,62,00,61,00,73,00,\
  68,00,2e,00,69,00,63,00,6f,00,00,00

[HKEY_CURRENT_USER\SOFTWARE\Classes\Directory\Background\shell\BashConsoleHere\command]
@="cmd /c cd \"%V\" && bash.exe"
```

2\. 最后，将 [bash.ico](https://www.dropbox.com/s/9rfkst6gxd8bw1u/bash.ico?dl=0) 放入对应位置 `%LOCALAPPDATA%\lxss\bash.ico` 

> 参考:  
>["Bash Here" Context Menu in Windows Explorer · Issue #603 · Microsoft/WSL](https://github.com/Microsoft/WSL/issues/603#issuecomment-395523617)


> 其他参考资料  
> 1\. [Windows Explorer doesn't show correctly the custom icon - Super User](https://superuser.com/questions/690249/windows-explorer-doesnt-show-correctly-the-custom-icon)  
> 2\. [registry - Set icon for custom right-click context menu item for all desktop shortcuts (windows explorer) - Stack Overflow](https://stackoverflow.com/questions/3681032/set-icon-for-custom-right-click-context-menu-item-for-all-desktop-shortcuts-win)  
> 3\. [How to launch Bash shell from right-click context menu on Windows 10](https://www.windowscentral.com/how-launch-bash-shell-right-click-context-menu-windows-10)

### 常用操作

#### 常用操作

| 目的     | 命令                   |
|--------|----------------------|
| 更新软件源  | sudo apt-get update  |
| 升级所有软件 | sudo apt-get upgrade |


#### 安装zsh和oh-my-zsh
```bash
sudo apt-get install zsh git-core
sudo wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh
chsh -s `which zsh` #或 chsh -s /usr/bin/zsh
```

将以下内容添加进 `.bashrc`
```
if [ -t 1 ]; then
exec zsh
fi
```
重新打开WSL使更改生效

> 参考： [Use Zsh in WSL on Windows 10 – Vinh Pham – Medium](https://medium.com/@uniqvinh/use-zsh-in-wsl-on-windows-10-5d439a749c4c)


### 其他
若想配合VcXsrv使用GUI, 可以参考这篇知乎专栏: [在Win10中使用带有Unity桌面的Ubuntu](https://zhuanlan.zhihu.com/p/37643715?utm_source=ZHShareTargetIDMore&utm_medium=social&utm_oi=27258127384576)

使用Mintty访问WSL [https://github.com/mintty/wsltty](https://github.com/mintty/wsltty)

Github上的 [kpocza/LoWe](https://github.com/kpocza/LoWe) 为WSL提供了音频和视频播放支持，其他关于WSL的信息/工具/发行版可参考 [sirredbeard/Awesome-WSL](https://github.com/sirredbeard/Awesome-WSL)