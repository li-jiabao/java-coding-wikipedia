---
title: windows终端美化
author: lijiabao
date: 2021-03-17 21:00
description: 在这里主要介绍一下Windows下powershell终端的美化
categories:  Windows
tags: Windows
---

## powershell终端美化

### 安装oh-my-posh

1. `win+x`点击进入powershell的管理员模式，安装两个模块

```powershell
Install-Module posh-git -Scope CurrentUser
Install-Module oh-my-posh -Scope CurrentUser
# 安装过程中选择yes或者a都行
```

2. 导入模块

   ```powershell
   Import-Module posh-git
   Import-Module oh-my-posh
   # 如果报错，需要修改策略 执行这个命令：set-ExecutionPolicy RemoteSigned，再运行上面的命令
   ```

3. 设置主题：

   ```powershell
   # 使用下面的命令获取所有的主题名和示例
   Get-PoshThemes
   # 使用下面的命令设置主题
   Set-PoshPrompt -Theme theme-name  # theme-name就是上面命令展示出来的主题名
   # 编辑$PROFILE文件将上面的语句加入，就可以自动设置主题，然后使用命令：. $PROFILE 让配置生效 
   
   ## 还可以使用配置文件来执行更多配置的方法
   # 首先导出主题到配置文件中
   Export-PoshTheme -FilePath /target-directory/mytheme.omp.json
   # 然后设置主题
   Set-PoshPrompt -Theme /target-directory/mytheme.omp.json
   ```

4. 设置字体，需要下载[Meslo](https://github.com/ryanoasis/nerd-fonts/releases/download/v2.1.0/Meslo.zip)字体，然后在设置中设置好字体才可正常显示主题

