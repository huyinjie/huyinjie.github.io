---
layout:     post
title:      在Powershell中使用智能补全
subtitle:   Using Powershell with Intellisense
date:       2018-11-18 13:49:46 +0800
author:     HYJ
header-img: img/default.jpg
catalog: true
tags: [Windows, Powershell]
---

 <!-- more --> 


### Npm Intellisense
参考资料

> 1\. [tab completion with powershell · Today I Learned (secretGeek)](https://til.secretgeek.net/npm/tab_completion_with_powershell.html)  
> 2\. [create profile · Today I Learned (secretGeek)](https://til.secretgeek.net/powershell/create_profile.html)

需要使用到以下两个Powershell插件

* [TabExpansionPlusPlus](https://github.com/lzybkr/TabExpansionPlusPlus)  
* [NPMTabCompletion](https://www.powershellgallery.com/packages/NPMTabCompletion/1.1)

```powershell
Find-Module TabExpansionPlusPlus -repository PsGallery | Install-Module -Scope AllUsers
Find-Module NPMTabCompletion -repository PsGallery | Install-Module -Scope AllUsers
```

创建一个 `profile` 文件，其默认位置 `C:\Users\$username\Documents\WindowsPowerShell` 中

```powershell
New-Item -path $profile -type file -force
```

打开 `Microsoft.PowerShell_profile.ps1` 文件，加入以下内容

```powershell
Import-Module TabExpansionPlusPlus
Import-Module NPMTabCompletion    
```
以后在任意目录内打开Powershell使用npm时都能带有自动补全
