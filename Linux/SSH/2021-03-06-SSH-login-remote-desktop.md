
## 远程登陆桌面环境

### xserver权限

`man Xorg.wrap`查看详细可配置项

首先需要设置xorg-server的访问权限，需要在配置文件中`/etc/X11/Xwrapper.config`加入下面的内容：
```
allowed_users=anybody  # 可选的设置项有：rootonly/console/anybody
needs_root_rights=yes
```
主要是用来设置指定那些用户有从wrapper访问xserver的权限，默认是console

如果你不关注安全性只关注功能性，可以按上述的操作来做

==但是上述的操作，任何人都可以使用root权限来访问开启Xserver，Xserver有硬件的访问特权以及许多的文件访问权限，这样可能会产生一个安全漏洞，可能会有人借此攻击服务器。==

### SSH隧道转发达成访问远程图形界面

这种方式可以实现通过在没有图形界面的服务器上安装需要图形界面安装的软件的时候，利用有桌面图形化界面的机器远程访问到该服务器，然后将其转发到当前有图形化界面的机器。

#### 配置文件修改

想要在本地打开远程服务器上的应用，光有上面的xserver权限还不够，还需要可以获取图形化展示，这需要在ssh客户端和服务器端都进行相应的配置文件修改才行。

**服务器端配置文件修改**
找到`/etc/ssh/sshd_config`配置文件中的X11Forwarding，取消注释并设置为yes，然后重启sshd服务：
```
sudo systemctl restart sshd.service
```

**客户端配置文件修改**
找到`/etc.ssh/ssh_config`找到HOSTS配置项下面的ForwardAgent，ForwardX11,ForwardX11Trusted这三项配置，设置为yes并且取消注释。

上述的两项配置完成之后，还需要下载`xorg-xhost`软件包，在客户端配置允许远程x界面连接过来：
```
xhost + remote-host
```

#### 连接ssh图形用户界面

```
ssh -X username@remote-host
```
可能还需要将远程X的显示界面定位到本地显示器：
```
export DISPLAY=localhost:10.0
```

这样配置完成之后，如果你有桌面环境，这个时候就可以在你登陆的命令行界面输入你想要打开的应用进行操作了，然后他就会在相应的界面打开应用。
如果你在visual console环境登陆的话，是不可以打开应用的。会报错can‘t open display

#### 常用的远程图形用户界面

图形文件管理器：`gnome-disk-image-mounter`



## 参考的博客文章内容

背景

大多数时候我们不希望在服务器上安装图形界面，但有时候有些程序需要图形界面，比如安装Oracle的时候。此时，可以配置让Linux使用远程的X Server进行图形界面显示。

首先要明确的是Linux X Window System的基本原理，X是一个开放的协议规范，当前版本为11，俗称X11。X Window System由客户端和服务端组成，服务端X Server负责图形显示，而客户端库X Client根据系统设置的DISPLAY环境变量，将图形显示请求发送给相应的X Server。

因此，我们只需要在远端开启一个X Server，并在目标机器上相应的设置DISPLAY变量，即可完成图形的远程显示。

“真理体验”版

环境：远程无图形机器A（IP 192.168.9.135，OS CentOS 6.2），本地有图形机器B（IP 192.168.1.135，OS CentOS 6.2），子网192.168.0.0

X Server是Gnome等桌面环境的基础，一个桌面环境通常包含了XDM（X Display Manager，通常的图形化用户登录界面就属于XDM）、窗口管理器（X Server显示的图形是没有“窗口”边框的，通过替换窗口管理器可以实现不同的视觉效果，比如实现3D效果的Compiz）等组件。

进行图形显示并不需要桌面环境，只要有X Server即可。

现在要在B机器上开启一个X Server，然后配置A机器的DISPLAY环境变量指向B上面的X Server，在A上启动一个图形程序，图形应该在B上面进行显示。

Linux提供了一个startx脚本来启动X Server，startx脚本通过调用xinit来完成此工作。xinit完成两个工作，首先在后台启动一个X Server，然后根据配置启动一系列客户端程序连接到X Server，这些客户端程序只有最后一个可以并且必须在前台运行，当这个前台的客户端程序退出时X Server将被关闭。

B机器上有桌面环境，查看进程可以看到如下进程在运行：

/usr/bin/Xorg :0 -nr -verbose -audit 4 -auth /var/run/gdm/auth-for-gdm-Ikd3i7/database -nolisten tcp vt1

这表示在display 0上运行着一个X Server，这里的X Server是Xorg。出于安全考虑，这个X Server不监听TCP连接（-nolisten tcp），所以无法通过网络连接上这个X Server。X Server可以通过TCP和域套接字进行连接，后面讲述DISPLAY变量时会详细描述。

我们可以通过startx或直接使用xinit来手动启动一个X Server，startx的选项与xinit相同，选项直接传递给xinit。xinit选项分为两个部分，以符号 “--”为界，前面是客户端选项，后面是X Server选项。

startx [ [ client ] options ... ] [ -- [ server ] [ display ] options ... ]

要注意的是“client”（即要运行的客户端程序）必须以绝对路径的形式出现。如果不指定“client”，startx或xinit会根据用户和系统全局的配置文件启动一个客户端程序（一般xinit默认启动xterm程序）。

现在我们启动一个X Server：

xinit /usr/bin/xterm -- :1 &

这个命令可以在桌面环境下的终端里面运行，也可以在字符终端下运行。机器B上的桌面环境显示在终端Ctrl-Alt-F1上，F2-F6都是字符终端，F7-F12留给图形终端。在B机器的F2字符终端上执行以上命令，将在F7终端出现xterm。

接下来要配置A机器的DISPLAY变量，以便将图形显示到B机器上。以另一终端登录A机器，设置DISPLAY变量：

export DISPLAY=192.168.1.135:1.0

DISPLAY变量的格式为[Address]:{NumA}.{NumB}。其中Address为X Server地址，如果Address为空，则通过域套接字连接到本地的X Server。NumA为display number，这与传递给xinit的display选项对应，这个display number为X Server的监听端口号送去6000。因此，实际上此时B机器上的X Server在监听6001端口，可以使用netstat命令验证。NumB为screen number（可能是多显示器的情况下指定显示在哪个显示器，未验证），通常情况下都为0.

现在关闭B机器的防火墙（或者打开相应端口），以便A机器的X客户端程序可以连接上B机器上的X Server。然后在A机器上刚才设置DISPLAY变量的那个会话中，启动一个带有图形界面的程序，图形将会显示在B机器的F7终端上。

如果提示无法连接到DISPLAY指定的X Server，可能是由于X Server打开了访问控制。在A机器上已设置DISPLAY变量的会话中使用xhost命令查看授权信息：

xhost

如果显示无法打开display，则可以确定是因为X Server开启了访问控制。

在B机器F7终端由xinit打开的xterm中使用xhost授权A机器访问：

xhost + 192.168.9.135

然后在B机器F7终端xterm上使用xhost命令查看ACL，可以看到192.168.9.135已获得授权。此时，在A机器已设置DISPLAY的会话中运行xhost，同样可以看到ACL，再运行图形程序，图形应显示在B机器F7终端上。

SSH隧道转发版

SSH提供了X11转发的功能，可以使用SSH简单地实现上一节描述的功能。

首先确认A机器上的SSH Server打开的X11转发功能。检查SSH Server配置文件/etc/ssh/sshd_config，确认有如下配置：

X11Forwarding yes

然后在从B机器上SSH到A机器：

ssh -X 192.168.9.135

-X选项打开SSH的X11转发功能。

在此会话中查看A机器上的DISPLAY变量，应与下面类似：

localhost:10.0

在此会话中查看A机器上的TCP监听端口，应有6010端口。

在此会话中启动A机器上的图形程序，图形应显示在B机器上。

实际上，SSH在A机器上打开了一个监听端口6010，并且在登录会话开始时为会话设置了DISPLAY变量为localhost:10.0。随后此会话中的图形程序运行时，X11 client库会将X请求发送到SSH监听6010端口中，然后A机器上的SSH将X请求转发到B机器，B机器的SSH客户端收到X请求后交给B机器上的X Server显示。

SSH隧道转发Windows版

原理与上一节类似，X请求也是通过SSH进行转发。要在Windows上显示Linux的图形界面，必须并运行一个X Server，这里选用Xming。

安装并运行Xming以后，使用ssh客户端连接上机器A，这里选用SecureCRT作为ssh客户端（使用putty同样可以）。

打开SecureCRT的会话选项作如下设置：

Linux在远程X Server上显示图形界面

勾选这个选项的作用实际上与上一节中的ssh命令的-X选项相同。

设置好会话选项以后，如果当前会话已经登录机器A，注销再重新登录。

登录到机器A，查看DISPLAY变量：

Linux在远程X Server上显示图形界面

启动一个图形程序，界面将在Windows上显示：

Linux在远程X Server上显示图形界面

小结

通过上述的几个小实验，应该对X11的基本原理有了比较清晰的理解，以后遇到某些软件必须使用图形界面的时候，可以在Windows上使用Xming来进行远程图形显示，不必为此在服务器上安装臃肿的图形环境了。
