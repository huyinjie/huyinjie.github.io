---
title: 使用Clang编译C++
date: 2017/12/20 17:20:00 +0800
updated: 2017/12/20 17:20:00
tags: Linux
---
 <!-- more --> 

#### 前置准备（更加推荐使用aptitude安装）

```bash
sudo apt-get install clang
sudo apt-get install libstdc++
sudo apt-get install libc++-dev
```

#### 常用编译选项

1.编译C语言时，使用如下指令

   ```bash
   clang/gcc -Wall -g xxx.c -o xxx
   ```

2.编译C++语言时，使用如下指令

   ```bash
   clang++ -Wall -g -std=c++11 -stdlib=libc++ crypt1.cpp -o crypt1
   ```



#### ZSH快速编译指令

在`.zshrc`中加入以下Alias

```bash
alias clangc++="clang++ -Wall -g -std=c++11 -stdlib=libc++"
```



#### 参考资料

* http://www.cnblogs.com/Lelpuchcr/archive/2013/06/08/3127659.html
* http://hczhcz.github.io/2014/03/27/oh-my-zsh.html
* https://stackoverflow.com/questions/26333823/clang-doesnt-see-basic-headers
* http://blog.csdn.net/yili_xie/article/details/4803314