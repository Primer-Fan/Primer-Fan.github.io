---
title: Manjaro KDE + Win10 双系统安装与基本配置
date: 2020-08-24 18:03:14
tags: 
  - Linux
  - Manjaro
  - 双系统
---

看了不少Manjaro的安利，决定把Ubuntu换成Manjaro。（Ps：Pacman确实比apt好用太多了）

{% asset_img manjaro.png xiaoguotu %}

<!-- more -->

# 删除Ubuntu18.04（同时适用于Manjaro）
1. 在Windows磁盘管理下，删除Ubuntu的所有分区
2. 删除Ubuntu引导文件
不知道自己Ubuntu引导文件在哪的可以用easyUEFI查看一下
  1. 挂载EFI分区
    用管理员身份打开命令提示符输入以下命令并回车
    ```
    diskpart
    list disk
    select disk 0 //选择FEI引导分区所在的磁盘
    list partition
    select partition 1 //选择EFI引导分区, 类型为系统的分区
    assign letter=p //为所选分区分配盘符
    ```
  2. 删除EFI分区
    下载安装Total Commander, 选中p盘->以管理员身份->EFI->ubuntu, 右键选中这个ubuntu文件夹, 按shift+delete删除
    回到挂在EFI分区时进入的命令提示符, 在diskpart界面移除p盘
    ```
    remove letter=p
    ```

# 安装Manjaro KDE双系统
1. 制作启动盘
  - 从官网下载Manjaro KDE系统镜像 [传送门](https://manjaro.org/download/) 。由于众所周知的原因，官网下载比较慢，可以使用Manjarocn的地址[传送门](https://manjaro.org.cn/category/download-manjaro)，建议选择Minimal
  - 使用Rufus工具制作启动盘，记得选上gpt+uefi，最新版Rufus可能没法选择，可以使用旧版Rufus，然后点击制作后最好选择DD模式写入
2. 为Manjaro空出分区
  - 右键开始图标->磁盘管理
  - 选择一个磁盘右键压缩卷，建议腾出足够的空间以供Linux的学习使用
3. 关闭Win10的快速启动
  右键电源图标->电源选项->选择电源按钮的功能->更改当前不可用设置->取消勾选启用快速启动
4. BIOS设置
  根据自己电脑型号自行搜索按键进入BIOS，联想是F2，Secure Boot设为Disabled，UEFI选项设为UEFI或Enabled
5. 安装Manjaro
  1. 关机插入U盘，开机按快捷键进入BIOS，设置启动优先级，将USB放在第一位，保存退出
  2. 进入Manjaro，双击install Manjaro，选择语言键盘时区等，选择覆盖我们之前腾出的分区，有需求可以手动配置分区，顺着提示安装就好
  3. 注意断开网络连接，因为Manjaro安装好后如果有网络会尝试连接默认的国外软件源，就会出现卡在93%、94%的样子，断掉连接进入系统后设置国内源再更新就好

# Manjaro系统设置

- 选择国内镜像：
```
sudo pacman-mirrors -i -c China -m rank
```
- 添加archlinuxcn源
```
sudo nano /etc/pacman.conf
# 在最后添加下面的代码，不同的镜像可以去镜像官网查看添加方式，这里添加中科大的源
[archlinuxcn]
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
```
- 更新源与系统
```
sudo pacman -Syyu
```
- 安装archlinuxcn-keyring
```
sudo pacman -S archlinuxcn-keyring
```
- 使用rtc时间（解决双系统时间不同步问题）
```
timedatectl set-local-rtc true
```
- 安装vim
```
sudo pacman -S vim
```
- 安装输入法
```
sudo pacman -S fcitx-im
sudo pacman -S fcitx-configtool
sudo pacman -S fcitx-sunpinyin # 自己选择，我比较倾向于sunpinyin，也可以选择谷歌或者搜狗

# 配置，编辑~/.xprofile 添加
export GTK_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"

# 重启打开fcitx-configtool设置一下就可以用了
```
- 安装AUR管理工具
```
sudo pacman -S yay
```
- 安装常用软件
```
sudo pacman -S google-chrome netease-cloud-music
```


# 美化

首先因为未知原因，KDE桌面可能会出现安装应用后搜不到图标，只能通过终端命令运行的bug，解决方案：
右键任务栏开始图标->编辑应用程序->KDE菜单编辑器菜单栏->编辑/恢复到系统菜单（KDE版本不同可能略有差别，但是总体流程差不多）

- dock
  1. 右键自带任务栏，edit panel->more options->remove
  2. 命令行安装latte-dock，zoom设为0即可消除抖动，其他的根据自己的喜好配置即可
- 顶栏
  1. 添加顶栏，右键桌面，add panel->application menu bar
  2. 下载小部件，右键桌面，add widgets->get new widgets->download new plasma widgets，搜索下载application title，better inline clock，mcos bs better inline bettery，launchpad plasma
  3. 右键顶栏，add widgets，然后根据自己的审美和喜好修改顶栏就ok了，我的个人顺序是左侧applicaiton title，global menu，右侧从右到左simple menu，cos bs better inline bettery，better inline clock，System Tray，System load viewer，最后在中间添加一个Spacer来填充空白区域（在add widgets按钮旁边）launchpad plasma拖动到latte-dock
- 主题
System settings里面可以方便的下载与配置主题
- global theme：breath2
- Plasma style：WhiteSur-dark
- application style
  - application style：breeze
  - window decorations：artim-dark
- colors：WhiteSur
- icons：mkos-big-sur
- cursors：mcmojave cursors
- workspace vehavior:
  - desktop effects: 勾选magic lamp
  - screen edges 与 touch screen 全部选择 no action
- window management -> window behavior -> advanced -> window placement 设置为 centered