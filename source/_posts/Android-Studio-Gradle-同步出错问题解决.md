---
title: Android Studio Gradle 同步出错问题解决
date: 2020-09-04 19:31:51
tags: Android Studio
---

# 问题描述
  在我们第一次用Android Studio建立项目时，会发现有个叫做Gradle的东西下载进度贼慢，然后下载半天后还会出错，这个问题发生的原因是Gradle下载地址在国外，而且Android Studio默认不走代理，导致下载失败

<!-- more -->

# 解决方案
  打开项目所在文件夹，打开gradle/wrapper/gradle-wrapper.properties，比如我的这个文件打开是这样的：
```
#Fri Sep 04 14:05:47 CST 2020
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-6.1.1-all.zip
```
  我们去[https\://services.gradle.org/distributions/gradle-6.1.1-all.zip](https://services.gradle.org/distributions/gradle-6.1.1-all.zip) 这个网站手动下载这个压缩包，然后放到/home/fanjiaming/.gradle/wrapper/dists/gradle-6.1.1-all/cfmwm155h49vnt3hynmlrsdst/目录下，注意最后一层目录大家应该不一样，gradle-6.1.1-all.zip.lck与gradle-6.1.1-all.zip.part文件不要动（第一次尝试自动下载时就会生成上方的目录和这两个文件），回到Android Studio，关闭当前项目然后再打开，就可以自动配置完成了
  
  在linux上以上方法可以解决问题，但是在win10下却还是因为网络问题报错Received close_notify during handshake，这里我们换一种更通用的解决方案，使用代理，打开Android Studio的Setting，设置Proxy，设置为manually（手动配置），填入回路地址127.0.0.1，然后填入自己的代理端口，然后就可以在Android Studio内愉快的科学上网了