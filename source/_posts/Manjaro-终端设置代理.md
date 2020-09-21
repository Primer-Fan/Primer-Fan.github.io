---
title: Manjaro 终端设置代理
date: 2020-09-09 20:48:35
tags:
  - Proxy
  - Manjaro
  - Linux
  - Terminal
  - Konsole
---

Manjaro里，konsole里的命令是默认不走代理的，当我们需要下载国外的aur包时，就会很难受，下面介绍如何给终端设置代理

<!-- more -->

如走socket5协议，比如使用ss或者ssr，在终端中直接执行(注意端口号根据自己的实际情况填)：

```shell
export http_proxy="socks5://127.0.0.1:1089"
export https_proxy="socks5://127.0.0.1:1089"
```

也可将上述命令写入bashrc达到永久设置的效果
