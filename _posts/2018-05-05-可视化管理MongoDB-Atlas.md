---
layout:     post
title:      可视化管理 MongoDB Atlas
date:       2018-05-05 16:19:17
author:     HYJ
header-img: img/tag-bg-o.jpg
catalog: true
tags:
    - MongoDB
---

# 连接到MongoDB Compass
1. 进入MongoDB Atlas控制台，点击 connect
  ![1-1](https://huyinjiexyz-1251543717.cos.ap-shanghai.myqcloud.com/source/_posts/可视化管理MongoDB-Atlas/1-1.png)

2. 将当前ip添加到白名单，然后点击Connect with mongoDB Compass
  ![1-2](https://huyinjiexyz-1251543717.cos.ap-shanghai.myqcloud.com/source/_posts/可视化管理MongoDB-Atlas/1-2.png)

3. 选择当前的MongoDB Compass版本，复制URL
  ![1-3](https://huyinjiexyz-1251543717.cos.ap-shanghai.myqcloud.com/source/_posts/可视化管理MongoDB-Atlas/1-3.png)

4. MongoDB Compass会自动检测剪贴板URL，补全Password，点击connect完成连接
  ![1-4](https://huyinjiexyz-1251543717.cos.ap-shanghai.myqcloud.com/source/_posts/可视化管理MongoDB-Atlas/1-4.png)



# 连接到Robo 3T
在2016年8月后Robomongo添加了对SSL和MongoDB Atlas的连接支持，（官方连接教程见 [Robomongo RC10 brings support to SSL](http://blog.robomongo.org/robomongo-rc10/)），在2017年7月后Robomongo改名为Robo 3T ([Robomongo is now Robo 3T, with MongoDB 3.4 support](http://blog.robomongo.org/robomongo-is-robo-3t/))


1. 打开Robo 3T，点击Manage Connections --> Create
  ![2-1](https://huyinjiexyz-1251543717.cos.ap-shanghai.myqcloud.com/source/_posts/可视化管理MongoDB-Atlas/2-1.png)

2. 进入MongoDB Atlas控制台，将IP白名单设置为**ALLOW ACCESS FROM ANYWHERE** ；点击当前实例名，复制集群中PRIMARY的Address，将其复制进Robo 3T
  ![2-2-1](https://huyinjiexyz-1251543717.cos.ap-shanghai.myqcloud.com/source/_posts/可视化管理MongoDB-Atlas/2-2-1.png)
  ![2-2-2](https://huyinjiexyz-1251543717.cos.ap-shanghai.myqcloud.com/source/_posts/可视化管理MongoDB-Atlas/2-2-2.png)
  ![2-2-3](https://huyinjiexyz-1251543717.cos.ap-shanghai.myqcloud.com/source/_posts/可视化管理MongoDB-Atlas/2-2-3.png)

3. 点击 `Authentication` 标签, 将 `Database` 设置为 ` admin` 并填入 `User Name`  和  `Password` , `Auth Mechanism` 设置为 `SCRAM-SHA-1` 。
  ![2-3](https://huyinjiexyz-1251543717.cos.ap-shanghai.myqcloud.com/source/_posts/可视化管理MongoDB-Atlas/2-3.png)

4.  将 `Authentication Method` 设置为 `Select self-signed Certificate` 。
  ![2-4](https://huyinjiexyz-1251543717.cos.ap-shanghai.myqcloud.com/source/_posts/可视化管理MongoDB-Atlas/2-4.png)


# 参考资料

* [MongoDB Compass 图形界面管理工具初探 - MongoDB中文社区](http://www.mongoing.com/blog/post/getting-started-with-mongodb-compass)
* [Connect to MongoDB Atlas Using Robo 3T (Robomongo) — Datduh!](https://www.datduh.com/blog/2017/7/26/how-to-connect-to-mongodb-atlas-using-robo-3t-robomongo)
* [robo3t - How can I connect to mongodb atlas using robomongo? - Stack Overflow](https://stackoverflow.com/questions/43694799/how-can-i-connect-to-mongodb-atlas-using-robomongo?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa)
* [[1.0 RC1] Can't connect to replica set to MongoDB Atlas Cluster 3.4 · Issue #1299 · Studio3T/robomongo](https://github.com/Studio3T/robomongo/issues/1299)