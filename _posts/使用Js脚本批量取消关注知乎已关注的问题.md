---
title: 使用Js脚本批量取消关注知乎已关注的问题
date: 2018-06-09 16:04:26
tags: Script
---

曾经在知乎上每看到一个问题都要点一下“关注问题”，久而久之关注的问题越来越多，每天都有很多通知发来，本文介绍了使用一段简单的Js脚本来批量取消关注。
 <!-- more --> 

#### 打开浏览器控制台
* 首先，进入知乎主页，点击“我的邀请”
  ![Snipaste_2018-06-09_16-08-05](https://huyinjiexyz-1251543717.file.myqcloud.com/source/_posts/%E4%BD%BF%E7%94%A8Js%E8%84%9A%E6%9C%AC%E6%89%B9%E9%87%8F%E5%8F%96%E6%B6%88%E5%85%B3%E6%B3%A8%E7%9F%A5%E4%B9%8E%E5%B7%B2%E5%85%B3%E6%B3%A8%E7%9A%84%E9%97%AE%E9%A2%98/Snipaste_2018-06-09_16-08-05.png)

* 点击右侧“邀请我回答的问题”
  ![Snipaste_2018-06-09_16-12-37](https://huyinjiexyz-1251543717.file.myqcloud.com/source/_posts/%E4%BD%BF%E7%94%A8Js%E8%84%9A%E6%9C%AC%E6%89%B9%E9%87%8F%E5%8F%96%E6%B6%88%E5%85%B3%E6%B3%A8%E7%9F%A5%E4%B9%8E%E5%B7%B2%E5%85%B3%E6%B3%A8%E7%9A%84%E9%97%AE%E9%A2%98/Snipaste_2018-06-09_16-12-37.png)

* 按下 F12 打开浏览器控制台


&nbsp;&nbsp;

#### 运行Js脚本

将以下脚本复制进控制台，回车运行

```javascript
setInterval(function(){
  unSubscribeButton = document.getElementsByClassName("follow-link zg-unfollow meta-item");
  for(i=0;i<unSubscribeButton.length;i++){
    console.log(unSubscribeButton[i]);
    unSubscribeButton[i].click();
    }
  window.scrollBy(0,500);
  },
100);
```

&nbsp;&nbsp;

##### 参考资料
* [How to Scroll a Page With JavaScript](https://www.mediacollege.com/internet/javascript/page/scroll.html)
* [JS脚本实现浏览器自动点击（阿里员工秒杀月饼） - CSDN博客](https://blog.csdn.net/Ani521smile/article/details/52575063)