# Supervisor

[TOC]

## <u>文档标识</u>

| 文档名称 | Supervisor |
| -------- | ---------- |
| 版本号   | <V1.0.0>   |

## <u>文档修订历史</u>

| 版本   | 日期       | 描述   | 文档所有者 |
| ------ | ---------- | ------ | ---------- |
| V1.0.0 | 2022.11.29 | create | 杨丝雨     |
|        |            |        |            |
|        |            |        |            |

## <u>相关文档参考</u>

[Supervisor官网]: http://supervisord.org/
[Supervisor官方文档]: http://supervisord.org/introduction.html
[Supervisor中文文档]: https://www.rddoc.com/doc/Supervisor/3.3.1/zh/



## [进程管理工具(Supervisor) ](http://supervisord.org/introduction.html)简介

[Supervisor](http://supervisord.org/introduction.html#overview)是用Python开发的一个client/server服务，是Linux/Unix系统下的一个进程管理工具，不支持Windows系统。它可以很方便的监听、启动、停止、重启一个或多个进程。用Supervisor管理的进程，当一个进程意外被杀死，supervisort监听到进程死后，会自动将它重新拉起，很方便的做到进程自动恢复的功能，不再需要自己写shell脚本来控制。

## **什么是守护进程**

　　在linux或者unix操作系统中，守护进程（Daemon）是一种运行在后台的特殊进程，它独立于控制终端并且周期性的执行某种任务或等待处理某些发生的事件。由于在linux中，每个系统与用户进行交流的界面称为终端，每一个从此终端开始运行的进程都会依附于这个终端，这个终端被称为这些进程的控制终端，当控制终端被关闭的时候，相应的进程都会自动关闭。但是守护进程却能突破这种限制，它脱离于终端并且在后台运行，并且它脱离终端的目的是为了避免进程在运行的过程中的信息在任何终端中显示并且进程也不会被任何终端所产生的终端信息所打断。它从被执行的时候开始运转，直到整个系统关闭才退出。

使用 Supervisor 来做这个事情。原因有两点：

-  1、它是微软官方文档推荐的，降低学习成本。
-  2、它并不一定是最好的，但一定是文档最全的。

## **Supervisor 特征**

- 简单
  - Supervisor通过简单的INI样式（可以**修改为.conf后缀**）配置文件进行配置，该文件易于学习。它提供了许多每个进程选项，使您的生活更轻松，如重新启动失败的进程和自动日志轮换。
- 集中
  - 主管为您提供一个**启动，停止和监控流程**的位置。流程可以单独控制，也可以成组控制。您可以将Supervisor配置为提供本地或远程命令行和Web界面。
- 高效
  - 主管**通过fork / exec启动其子进程，子进程不进行守护。当进程终止时，操作系统会立即向Supervisor发出信号**，这与某些依赖麻烦的PID文件和定期轮询重新启动失败进程的解决方案不同。
- 扩展
  - Supervisor有一个**简单的事件通知协议**，用任何语言编写的程序都可以用它来监视它，以及一个用于控制的XML-RPC接口。它还使用可由Python开发人员利用的扩展点构建。
- 兼容
  - 除了Windows之外，Supervisor几乎可以处理所有事情。它**在Linux，Mac OS X，Solaris和FreeBSD上经过测试和支持。它完全用Python编写，因此安装不需要C编译器**。
- 久经考验
  - 虽然Supervisor今天非常活跃，但它并不是新软件。主管已存在多年，已在许多服务器上使用。



## **各个平台安装Supervisor**

### 在 linux 中使用以下命令进行安装

- centos

```shell
yum install supervisor -y
```

-  ubuntu

```shell
sudo apt-get install supervisor -y
```

-  python

```shell
pip install supervosor easy_install supervisor
```

### 在 masOS 中直接使用brew工具进行安装即可

```shell
brew install supervisor
```



## **Supervisor 配置**

linux 安装完后会有一个主配置文件/etc/supervisord.conf，和一个/etc/supervisord.d 自配置文件目录

```shell
ls /etc/supervisord.*
/etc/supervisord.conf
/etc/supervisord.d:
```

修改主配置文件，设置自配置文件生效的后缀

```shell
vim /etc/supervisord.conf   在最后一行
[include]
files = supervisord.d/*.conf
```



## 配置文件参数说明

```shell
#;号为注释内容

[unix_http_server]
file=/tmp/supervisor.sock   #UNIX socket 文件，supervisorctl 会使用
;chmod=0700                 #socket文件的mode，默认是0700
;chown=nobody:nogroup       #socket文件的owner，格式：uid:gid

;[inet_http_server]         #HTTP服务器，提供web管理界面
;port=127.0.0.1:9001        #Web管理后台运行的IP和端口，如果开放到公网，需要注意安全性
;username=user              #登录管理后台的用户名
;password=123               #登录管理后台的密码

[supervisord]
logfile=/tmp/supervisord.log #日志文件，默认是 $CWD/supervisord.log
logfile_maxbytes=50MB        #日志文件大小，超出会rotate，默认 50MB，如果设成0，表示不限制大小
logfile_backups=10           #日志文件保留备份数量默认10，设为0表示不备份
loglevel=info                #日志级别，默认info，其它: debug,warn,trace
pidfile=/tmp/supervisord.pid #pid 文件
nodaemon=false               #是否在前台启动，默认是false，即以 daemon 的方式启动
minfds=1024                  #可以打开的文件描述符的最小值，默认 1024
minprocs=200                 #可以打开的进程数的最小值，默认 200

[supervisorctl]
serverurl=unix:///tmp/supervisor.sock #通过UNIX socket连接supervisord，路径与unix_http_server部分的file一致
;serverurl=http://127.0.0.1:9001 # 通过HTTP的方式连接supervisord

#[program:xx]是被管理的进程配置参数，xx是进程的名称
[program:xx]
command=/opt/apache-tomcat-8.0.35/bin/catalina.sh run  # 程序启动命令
autostart=true       # 在supervisord启动的时候也自动启动
startsecs=10         # 启动10秒后没有异常退出，就表示进程正常启动了，默认为1秒
autorestart=true     # 程序退出后自动重启,可选值：[unexpected,true,false]，默认为unexpected，表示进程意外杀死后才重启
startretries=3       # 启动失败自动重试次数，默认是3
user=tomcat          # 用哪个用户启动进程，默认是root
priority=999         # 进程启动优先级，默认999，值小的优先启动
redirect_stderr=true # 把stderr重定向到stdout，默认false
stdout_logfile_maxbytes=20MB  # stdout 日志文件大小，默认50MB
stdout_logfile_backups = 20    # stdout 日志文件备份数，默认是10

stdout_logfile=/opt/apache-tomcat-8.0.35/logs/catalina.out  #stdout 日志文件，需要注意当指定目录不存在时无法正常启动，所以需要手动创建目录（supervisord 会自动创建日志文件）
stopasgroup=false     #默认为false,进程被杀死时，是否向这个进程组发送stop信号，包括子进程
killasgroup=false     #默认为false，向进程组发送kill信号，包括子进程

#包含其它配置文件,类似于NGINX的conf.d一样的意思
[include]
files = /etc/supervisor/config.d/*.ini    #可以指定一个或多个以.ini结束的配置文件
```



## 示例Nginx守护进程配置

### 配置文件

```shell
# vim /etc/supervisord.conf
; Sample supervisor config file.

[unix_http_server]
file=/var/run/supervisor/supervisor.sock   ; (the path to the socket file)


[inet_http_server]         ; inet (TCP) server disabled by default
port=192.168.10.168:9001        ; (ip_address:port specifier, *:port for all iface)
username=admin              ; (default is no username (open server))
password=admin               ; (default is no password (open server))

[supervisord]
logfile=/var/log/supervisor/supervisord.log  ; (main log file;default $CWD/supervisord.log)
logfile_maxbytes=50MB       ; (max main logfile bytes b4 rotation;default 50MB)
logfile_backups=10          ; (num of main logfile rotation backups;default 10)
loglevel=info               ; (log level;default info; others: debug,warn,trace)
pidfile=/var/run/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
nodaemon=false              ; (start in foreground if true;default false)
minfds=1024                 ; (min. avail startup file descriptors;default 1024)
minprocs=200                ; (min. avail process descriptors;default 200)

[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

[supervisorctl]
serverurl=unix:///var/run/supervisor/supervisor.sock ; use a unix:// URL  for a unix socket


[include]
files = supervisord.d/*.conf
```



```shell
# vim /etc/supervisord.d/project.conf
[program:nginx]
command=/etc/nginx/sbin/nginx -c /etc/nginx/conf/nginx.conf			# 运行程序的命令
stderr_logfile=/etc/nginx/logs/error.log 	# 错误日志文件
stdout_logfile=/etc/nginx/logs/access.log # 输出日志文件
autostart=true			# 是否自动启动
autorestart=true 		# 程序意外退出是否自动重启
startsecs=0 	# 自动重启间隔
priority=1
stopasgroup=true
killasgroup=true
```

### **启动 Supervisor 服务**

开启服务，并设为开机自启

```shell
systemctl start supervisord.service && systemctl enable supervisord.service
```

查询服务状态

```shell
systemctl status supervisord.service
```



## supervisor命令的交互使用

```shell
# supervisor启动成功后。输入supervisorctl进入交互页面，如果加了被管理的进程就可以看到，想停止nginx,可以使用stop help查看帮助，stop nginx.....
supervisorctl status
supervisorctl stop nginx
supervisorctl start nginx
supervisorctl restart nginx
supervisorctl reread
supervisorctl update
```



## Supervisor Web 界面

需要开启`supervisord.conf`注释掉的这4行：

```
[inet_http_server]         ; inet (TCP) server disabled by default
port=192.168.10.168:9001        ; (ip_address:port specifier, *:port for all iface)
username=admin              ; (default is no username (open server))
password=admin               ; (default is no password (open server))
```

> 访问：http://ip:9001



## <u>附录：supervisord 管理</u>

Supervisord 安装完成后有两个可用的命令行 supervisord 和 supervisorctl，命令使用解释如下：

- supervisord，初始启动 Supervisord，启动、管理配置中设置的进程。
- supervisorctl stop programxxx，停止某一个进程(programxxx)，programxxx 为 [program:beepkg] 里配置的值，这个示例就是 beepkg。
- supervisorctl start programxxx，启动某个进程
- supervisorctl restart programxxx，重启某个进程
- supervisorctl stop groupworker: ，重启所有属于名为 groupworker 这个分组的进程(start,restart 同理)
- supervisorctl stop all，停止全部进程，注：start、restart、stop 都不会载入最新的配置文件。
- supervisorctl reload，载入最新的配置文件，停止原有进程并按新的配置启动、管理所有进程。
- supervisorctl update，根据最新的配置文件，启动新配置或有改动的进程，配置没有改动的进程不会受影响而重启。