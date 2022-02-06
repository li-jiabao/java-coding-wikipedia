---
title: Archlinux显示器配置
author: lijiabao
date: 2021-03-19 12:00
categories: Markdown
tags: Markdown
---
介绍archlinux下怎么配置双显示器的问题

## 显示器配置



### 查看显示器

首先，在你连接好显示器之后，你需要首先查看一下自己的显示器对应的情况和名字，使用下面的命令：
```shell
xrandr -q
```

![查看显示器情况](https://cdn.jsdelivr.net/gh/li-jiabao/NoteImg@main/img/%E6%9F%A5%E7%9C%8B%E6%98%BE%E7%A4%BA%E5%99%A8%E6%83%85%E5%86%B5.png)

得到上面的结果之后，你就可以知道两个显示器的名字分别是eDP1和DP1，显示disconnected说明没有连接

### 显示器的配置

这里主要有两种配置方案，一种命令行配置，另外一种是修改X11配置文件来配置

#### 命令行配置

使用下面的命令来配置：

```shell
xrandr --output eDP1 --mode 1920×1080 --auto --output DP1 --mode 1024×768 --auto --rightof eDP1
# 上面命令解释，参数--output是指屏幕显示输出到哪个屏幕
# --auto是自动配置分辨率，而eDP1和DP1是显示屏对应的名字，上一步命令查看的内容
# --rightof是指就将当前屏幕放在哪个屏幕的右边
# --mode 后面设置屏幕分辨率
# 这步命令就是将我的eDP1屏幕放在左边，DP1放在eDP1的右边
```

#### 配置文件修改

首先，你需要在管理员权限下去修改X11配置文件`/etc/X11/xorg.conf`

```yaml
# 找到如下部分，Monitor就是指你的显示器
Section "Monitor"
	Identifier   "eDP1" # 显示器名字，xrandr -q查看到的显示器名字
	VendorName   "Monitor Vendor"
	ModelName    "Monitor Model"
    Option       "Primary" "true" # 这里将eDP1设置为主显示器
    Option       "PreferredMode" "1920×1080"
EndSection

Section "Monitor"
	Identifier   "DP1"  # 这里写的是显示器的名字
	VendorName   "Monitor Vendor"
	ModelName    "Monitor Model"
    Option       "RightOf" "eDP1" # 将DP1设置在eDP1的右边
    Option       "PreferredMode" "1024×768"
EndSection
```

如果需要对显示器进行更细致的配置，在Option配置项填写你自己需要的配置就可了，待配置完毕保存推出，然后重启X11服务即可