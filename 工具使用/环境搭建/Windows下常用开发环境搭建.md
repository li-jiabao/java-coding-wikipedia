# Windows环境搭建

## 1 wsl安装配置

详细的安装步骤：

### 步骤1：启动适用与Linux的Windows子系统

首先需要启动适用于Linux的Windows子系统的可选功能，然后才可以安装Linux分发版，有两种方法开启：

1. 使用powershell命令行开启

   ```powershell
   dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
   ```

2. 找到设置启动或关闭Windows功能，进入，找到Linux的Windows子系统选项勾选

完成操作后重启系统

### 步骤2：检查运行WSL 2的要求

若要更新到 WSL 2，需要运行 Windows 10。

- 对于 x64 系统：**版本 1903** 或更高版本，采用 **内部版本 18362** 或更高版本。
- 对于 ARM64 系统：**版本 2004** 或更高版本，采用 **内部版本 19041** 或更高版本。
- 低于 18362 的版本不支持 WSL 2。 需要先更新系统

若要检查 Windows 版本及内部版本号，win+ R，键入“winver”（或者在 Windows 命令提示符下输入 `ver` 命令）

### 步骤 3 - 启用虚拟机功能

安装 WSL 2 之前，必须启用“虚拟机平台”可选功能。 计算机需要[虚拟化功能](https://docs.microsoft.com/zh-cn/windows/wsl/troubleshooting#error-0x80370102-the-virtual-machine-could-not-be-started-because-a-required-feature-is-not-installed)才能使用此功能。以管理员身份打开 PowerShell 并运行下面命令后重启：

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

### 步骤 4 - 下载 Linux 内核更新包

1. 下载最新包：
   - [适用于 x64 计算机的 WSL2 Linux 内核更新包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)。如果使用的是 ARM64 计算机，请下载 [ARM64 包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_arm64.msi)。 如果不确定自己计算机的类型，请打开命令提示符或 PowerShell，并输入：`systeminfo`查看
2. 运行上一步中下载的更新包。 （双击以运行 - 系统将提示你提供提升的权限，选择“是”以批准此安装。）

### 步骤 5 - 将 WSL 2 设置为默认版本

打开 PowerShell，然后在安装新的 Linux 发行版时运行以下命令，将 WSL 2 设置为默认版本：

```powershell
wsl --set-default-version 2
```

### 步骤 6 - 安装所选的 Linux 分发

1. 打开Microsoft Store，并选择你偏好的 Linux 分发版。
2. 选择好之后选择获取即可
3. 安装好之后会跳出创建用户和密码的窗口，设置好即可

除了上面的方法，还可以选择手动下载并安装[手动下载Linux分发版Linux子系统](https://docs.microsoft.com/zh-cn/windows/wsl/install-manual)。

安装好可以输入wsl直接进入wsl的Linux系统环境

### wsl Linux配置

对于pppoe联网的电脑，wsl并不能和Windows宿主机共享网络，因此需要进行一定的设置：

1. 在网络适配器设置中，将宽带连接设置共享，共享设备里面选择wsl的vethernet（wsl的虚拟网络连接）

2. 设置好之后需要设置wsl的ip地址和路由地址

3. 设置ip地址：`sudo ip addr add 192.168.137.10/24 dev eth0`，一般设置为局域网下的ip地址

4. 设置默认路由：`sudo ip route delete default`然后：`sudo ip route add default via 192.168.137.1`

5. 设置完之后域名解析可能有错，因此需要修改DNS设置，首先需要增加`/etc/wsl.conf`配置文件

   ```ini
   # 设置wsl防止每次重启wsl后，/etc/resolv.conf都被修改回原来的
   [network]
   generateResolvConf = false;
   ```

6. 设置了上面的配置文件，就可以配置DNS，首先删除resolv.conf文件：`sudo rm -rv /etc/resolv.conf`，然后新建这个文件，往里面写入：

   ```ini
   # 加入常用的一些DNS服务器IP地址即可
   nameserver 8.8.8.8
   ```

设置好了网络便可以开始正常使用Linux了，可以更改镜像源安装你自己常用的需要的软件包即可。

## 2 Windows terminal安装配置

安装可以直接在微软商店下载，也可以去GitHub搜索并下载安装即可

### 配置

#### 安装oh-my-posh

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

#### 配置文件

常用的terminal配置可以查看微软官方的指导即可。

一般在配置文件setting.json的default项目下增加一些常见的比如字体大小主题等基础设置

## 3 nodejs安装配置

官网下载压缩包，解压到你需要的路径下，一般放在D盘，解压之后需要配置环境变量以及全部包的安装路径以及缓存等内容。

### 全局包路径和cache

在安装目录下面添加两个文件夹：`node_global`和`node_cache`然后在nodejs配置命令进行配置

```bash
npm config set prefix "D:\Coder\nodejs\node_global"  # 路径选择安装路径下面的即可
npm config set cache "D:\Coder\nodejs\node_cache"  # 缓存路径
```

### 环境变量设置

进入高级系统设置的环境变量设置：

在系统变量里面添加：`NODE_PATH`：内容填入`D:\Coder\nodejs\node_global\node_modules`

在环境变量PATH里面添加：`D:\Coder\nodejs\`和`D:\Coder\nodejs\node_global`

设置好之后便可以在命令行使用`npm install 包名 -g `-g指定全局安装

## 4 JDK安装配置

Oracle官网下载压缩包，解压

### 环境变量配置

系统变量添加：`JAVA_HOME`，内容填写安装路径：`D:\Coder\JDK\jdk11`

添加：`CLASSPATH`，内容填写：`.;%JAVA_HOME%\lib;%JAVA_HOME%\lib\tools.jar`

PATH里面添加：`%JAVA_HOME%\bin`

此时已经可以使用命令行：`java --version`和`javac -version`查看是否配置好

## 5 Tomcat配置

官网下载压缩包解压缩到想要的路径下面

### 环境变量配置

添加系统变量：`CATALINA_HOME`，路径填写安装路径：`D:\Coder\tomcat`

系统变量PATH里面添加：`%CATALINA_HOME%\lib`、`%CATALINA_HOME%\bin`、`%CATALINA_HOME%\lib\servlet.jar`

配置好了之后便可以在命令行下`startup.bat`启动tomcat了

## 6 Maven 配置

下载解压放到指定目录

### 环境变量配置

添加系统变量：`M2_HOME`，路径指定安装路径：`D:\Coder\Maven`

PATH：`%M2_HOME%\bin`

测试：`mvn -version`查看是否配置成功

### 配置文件修改

#### 本地仓库修改

```xml
  <!-- localRepository
   | The path to the local repository maven will use to store artifacts.
   |
   | Default: ${user.home}/.m2/repository
  <localRepository>/path/to/local/repo</localRepository>
  -->
  <!-- 这个地方的路径改成你的本地仓库所在路径即可 -->
	<localRepository>D:\tools\repository</localRepository>
```

#### 修改maven的JDK默认版本

在profiles标签下添加一个profile标签，修改maven默认的JDK版本。

```xml
<profile> 
    <id>JDK-1.8</id>       
    <activation>       
        <activeByDefault>true</activeByDefault>       
        <jdk>1.8</jdk>       
    </activation>       
    <properties>       
        <maven.compiler.source>1.8</maven.compiler.source>       
        <maven.compiler.target>1.8</maven.compiler.target>       
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>       
    </properties>       
</profile>
```

### 添加国内镜像源

添加<mirrors>标签下<mirror>，添加国内镜像源，这样下载jar包速度很快。默认的中央仓库有时候甚至连接不通。一般使用阿里云镜像库即可。这里我就都加上了，Maven会默认从这几个开始下载，没有的话就会去中央仓库了。
```xml
<!-- 阿里云仓库 -->
<mirror>
    <id>alimaven</id>
    <mirrorOf>central</mirrorOf>
    <name>aliyun maven</name>
    <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
</mirror>

<!-- 中央仓库1 -->
<mirror>
    <id>repo1</id>
    <mirrorOf>central</mirrorOf>
    <name>Human Readable Name for this Mirror.</name>
    <url>http://repo1.maven.org/maven2/</url>
</mirror>

<!-- 中央仓库2 -->
<mirror>
    <id>repo2</id>
    <mirrorOf>central</mirrorOf>
    <name>Human Readable Name for this Mirror.</name>
    <url>http://repo2.maven.org/maven2/</url>
</mirror>
```

## 7 常用软件安装

### typora

这个软件主要是markdown的书写记录，非常好用方便，还可以使用这个软件结合Picgo图床工具，方便的将你的图片转为网址，便可以永久访问图片，不同担心图片是否存在。

### Picgo

搜索官网下载即可，下载之后需要配置你自己的图床，常用的由github图床和smms图床，还可以安装gitee插件支持gitee图床，相比github图床，网速更好一些

gitee插件安装需要安装nodejs环境，如上配置安装环境即可

### IDEA

这是Java的必备的开发软件包，专业的Java，去官网下载安装即可，也支持压缩包安装（推荐），方便，随时可以压缩打包到其他电脑上面，相当方便。

### eval reset插件安装

在plugins界面设置repository：https://plugins.zhile.io，也可以离线下载安装：https://plugins.zhile.io/files/ide-eval-resetter-2.1.6.zip

### SublimeText

简洁的编辑器，很好用推荐

官网下载，也有压缩包版本，推荐使用压缩包，方便，可以任意电脑使用

### noteoad++

windows下有些文件需要管理员权限才能修改保存，这个软件可以直接获取权限进行修改，比较方便，而且软件简洁小巧

### Edge

很多数据已经在edge浏览器上，直接登陆账号便可以同步，很方便，chrome也有这个功能，但是因为长期使用，很多记录在edge上面 