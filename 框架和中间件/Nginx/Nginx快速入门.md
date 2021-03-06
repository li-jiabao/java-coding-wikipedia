
# Nginx快速入门

Nginx是一个高性能的HTTP和反向代理服务，由俄罗斯的伊戈尔开发的，最早的公开版本是在2004年10月4日。

Nginx是一个很强大的高性能Web和反向代理服务，有很多优越的特性：在连接高并发的情况下，nginx是一个apache不错的替代品。

**Nginx的功能：**
- [[#反向代理]]
- [[#负载均衡]]
- [[#动静分离]]


## 安装

### Windows

windows下的安装比较简单，之间在官网上下载对应window操作的系统的压缩包，然后将他直接解压，找到解压之后的文件，有个`nginx.exe`的可执行文件，直接运行即可，建议在cmd中运行该可执行文件。

### Linux

Linux包管理器安装：
`sudo pacman -S nginx`archlinux
`sudo yum install nginx`centos
`sudo apt-get nginx`ubuntu

**源码编译安装：**
需要`gcc openssl zlib pcre`环境
在官网上下载不是windows压缩包的gz压缩文件
解压并进入到nginx文件夹：
执行：`./configure --prefix=install-directory`进行配置。其中install-directory是你想要安装到的文件目录
make：`make`
make install：`make install`
然后就可以直接进入到安装文件的sbin目录，`./nginx`运行
想要全局使用nginx运行，需要将sbin添加到环境变量中：`export PATH=$PATH:安装目录/sbin`

## Nginx操作

`nginx -s signal`

其中的signal有：
- stop快速关闭
- quit优雅的逐步关闭
- reload重载配置文件
- reopen重启log文件

启动：进入到安装文件sbin目录`./nginx`，有环境变量可以直接：`nginx`
停止：`./nginx -s stop`或者`nginx -s stop`
重载配置文件：`./nginx -s reload`
查看nginx进程信息：`ps -ax | grep nginx`

## 正向代理和反向代理

### 正向代理

正向代理就是一个位于客户端和原始服务器之间的服务器。为了从原始服务器获取内容，客户端向代理发送一个请求并指定目标原始服务器，然后代理再向原始服务器转交请求并将获得的内容到客户端。类似正向代理的有**VPN**

### 反向代理

反向代理方式是指以代理服务器来接受internet上的连接请求，然会将请求转发给内部网络上的服务器，并将服务器上的结果返回给internet上请求的客户端，此时的代理服务器就是一个反向代理服务器。

反向代理一般用在当你的网络请求过多时，一台服务器并不能满足高并发的请求，这时候就需要有多台服务器来缓解。

### 两者之间的区别

**位置不同：**
正向代理架设在客户端和目标主机之间
反向代理架设在服务器端

**代理对象不同：**
正向代理，代理的是客户端，服务端不知道实际发起请求的客户端
反向代理，代理服务器，客户端不知道实际提供服务的服务端

## 负载均衡

负载均衡是为了避免单独的一个服务器压力过大，将来自用户的请求转发给不同的服务器。也就是根据实际服务器的性能和容量，来配置不同的权重或者使用轮询来保证负载均衡。
简单负载均衡配置例子：
```
upstream dynamic_server {
	server server-host1:port;
	server server-host2:port;
	server server-host3:port;
	# 可以通过添加权重改变处理连接的频率，weight越大处理连接越多，默认为1
	server server-host4:port weight=4;
}

# 设置好了之后，还需要在http模块的location模块中指定访问反向代理的客户端到服务器
location / {
	index index.jsp index.html
	proxy_pass http://dynamic_server;
}
```


## 动静分离

在项目开发过程中，有些请求是需要后台来处理的，有些不需要经过后台处理的（css,js,html, jpg等文件），不需要处理的文件称为静态文件。
让动态网站里面的动态网页根据一定的规则把不变的资源和变动的资源区分开来，这样我们在后面可以将静态资源做一些特别的处理来提高访问速度，比如：将网站或者项目的一些静态资源做缓存操作从而提高访问速度。

**动静分离的目前的实现方法：**
- 把静态文件独立成单独的域名，放在独立的服务器上面（主流推崇的方法）
- 动态和静态文件混合放在一起发布，通过nginx分开，通过location指定不同的后缀名实现不同的请求转发

**简单的nginx实现：**
```
worker_process 1;

events {
	work_connection  1024;
}

http {
	server {
		listen  10000;  # 监听的端口号
		server_name  localhost;   # 域名
	}
	
	# 绑定到根目录下的服务器地址
	location / {
		proxy_pass  http://localhost:8888;
		proxy_set_header X-Read-IP $remote_addr;
	}
	
	# 拦截静态资源
	location ~ .*\.(html|htm|jpg|jpeg|bmp|png|ico|js|css)$ {
		root /static_directory;  # 指定你的静态资源所在的路径位置即可
	}
}
```


## Nginx配置

配置文件中几个模块的解释和示例：
```
# user nobody;   # 主要的上下文中的一个配置项

events {
	# 处理连接的配置文件
}

http {
	# 特定的对应http和所有的虚拟服务器的配置
	
	server {
		# http的虚拟服务器1的配置
		 
		upstream upstream-name {
			# 配置需要反向代理的服务器列表
		}
		
		location /one {
			# 处理/one开始uri的配置
		}
		location {
			# 处理/two开始的uri的配置
		}
	}
	
	server {
		# 配置http虚拟服务器2
	}
}

stream {
	# TCP/UDP的配置，类似http，只不过是针对TCP/UDP的
	server {
		# TCP/UDP虚拟服务器配置
	}
}

mail {
	# 邮件服务的配置
	server {
		# 类似http
	}
}

# 对于不同的context也就是http、TCP/UDP和mail，server可以配置的内容还是有些差距的
# 对于http的server控制特定域名和ip的资源请求处理，不同的location定义了如何处理特定的URI
# 对于mail和TCP/UDP，server是用来处理到达特定的TCP端口或者UNIX
socket的traffic
```

配置文件：`conf`文件夹下面，基础配置在`conf/nginx.conf`里面进行配置

负载均衡和反向代理的配置，需要首先在http{}里面加上upstream配置，然后再location中设置`proxy_pass http://upstreamname;`示例：
```config
http {
	upstream upstream-name {
		# 下面是设置反向代理代理的服务器ip和端口，并通过权重来实现负载均衡
		server server-host1:port weight=3;
		server server-host2:port weight=1;
		server server-host3:port weight=2;
	}
	
	# 配置https服务
	server {
		listen 443;
		server_name domain-name; # domain-name是你自己申请了ssl证书的域名
		ssl on;
		# ssl证书pem和key路径
		ssl_certificate  /pem-directory;  # pem证书文件路径
		ssl_certificate_key  /key-directory; # key证书文件路径
		location / {
			proxy_pass http://ip:port;  # 代理服务器ip和端口或者反向代理的服务器列表名，如上的upstrea-name
		}
	}
	
	server {
	listen   80;
	servername domain-name;  # domain-name是自己的域名
	rewrite ^(.*)$ https://$host$1 permanent; # 将请求转化成https
	location / {
		# 设置反向代理指向http://localhost:80的根目录
		proxy_pass http://upstream-name/;
	}
}
}
```

修改了配置文件之后，如果nginx服务是开启的，你需要重载配置文件：
`nginx -s reload`
