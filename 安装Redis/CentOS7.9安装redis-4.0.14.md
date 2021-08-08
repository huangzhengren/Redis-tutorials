- [`CentOS7.9`安装`redis-4.0.14`](#centos79安装redis-4014)
  - [查看`CentOS`版本](#查看centos版本)
  - [在`/opt`下创建并进入`software`目录](#在opt下创建并进入software目录)
  - [下载`redis-4.0.14`](#下载redis-4014)
  - [解压`redis-4.0.14`到`/usr/local`下](#解压redis-4014到usrlocal下)
  - [创建符号链接](#创建符号链接)
  - [进入`/usr/loca/redis`目录并编译](#进入usrlocaredis目录并编译)
    - [报错`gcc: Command not found`](#报错gcc-command-not-found)
  - [通过`yum`仓库安装`gcc`，重新编译](#通过yum仓库安装gcc重新编译)
    - [报错`zmalloc.h:50:31: fatal error: jemalloc/jemalloc.h: No such file or directory`](#报错zmalloch5031-fatal-error-jemallocjemalloch-no-such-file-or-directory)
  - [`make`增加`MALLOC=libc`参数](#make增加malloclibc参数)
  - [执行`make test`命令](#执行make-test命令)
    - [报错`You need tcl 8.5 or newer in order to run the Redis test`](#报错you-need-tcl-85-or-newer-in-order-to-run-the-redis-test)
  - [进入`/opt/software`目录，下载`tcl8.6.11-src.tar.gz`](#进入optsoftware目录下载tcl8611-srctargz)
  - [解压`tcl8.6.11-src.tar.gz`到`/usr/local`下](#解压tcl8611-srctargz到usrlocal下)
  - [进入`/opt/software/tcl8.6.11/unix`目录，配置软件安装路径](#进入optsoftwaretcl8611unix目录配置软件安装路径)
  - [编译`tcl8.6.11`](#编译tcl8611)
  - [编译测试`tcl8.6.11`](#编译测试tcl8611)
  - [编译安装`tcl8.6.11`](#编译安装tcl8611)
  - [将编译好的二进制文件拷贝到`/usr/loca/bin`目录](#将编译好的二进制文件拷贝到usrlocabin目录)
  - [再次进入`/usr/loca/redis`目录，执行`make test`命令](#再次进入usrlocaredis目录执行make-test命令)
  - [编译安装`redis-4.0.14`](#编译安装redis-4014)
  - [在`/data/redis`目录下创建`conf`、`logs`、`pid`目录](#在dataredis目录下创建conflogspid目录)
  - [拷贝`/usr/local/redis/redis.conf`文件为`/data/redis/conf/redis_6380.conf`](#拷贝usrlocalredisredisconf文件为dataredisconfredis_6380conf)
  - [修改`redis_6380.conf`配置文件](#修改redis_6380conf配置文件)
  - [创建`redis`组，创建`redis`系统用户并加入到`redis`组](#创建redis组创建redis系统用户并加入到redis组)
  - [修改`/data/redis`目录属主和属组为`redis`](#修改dataredis目录属主和属组为redis)
  - [配置`systemd`管理`redis`](#配置systemd管理redis)
  - [重载配置，启动`redis`，查看`redis`状态、重启`redis`](#重载配置启动redis查看redis状态重启redis)
  - [停止`redis`,查看`redis`状态](#停止redis查看redis状态)
  - [确认`systemd`配置无误后，启动`redis`，进入日志所在目录查看日志](#确认systemd配置无误后启动redis进入日志所在目录查看日志)
    - [您请求的最大客户端数量为10000，至少需要10032个最大文件描述符。服务器无法将最大打开文件设置为10032，因为操作系统错误:操作不允许。当前最大打开文件是4096。`maxclients`已经减少到4064，以弥补低`ulimit`。如果需要更高的`maxclients`，请增加`ulimit -n`。](#您请求的最大客户端数量为10000至少需要10032个最大文件描述符服务器无法将最大打开文件设置为10032因为操作系统错误操作不允许当前最大打开文件是4096maxclients已经减少到4064以弥补低ulimit如果需要更高的maxclients请增加ulimit--n)
  - [调整系统中最大可同时存在的进程数和最大文件数](#调整系统中最大可同时存在的进程数和最大文件数)
  - [执行`source /etc/profile`，使配置生效](#执行source-etcprofile使配置生效)
  - [重启，依然报错，在`redis_6380.service`文件中加上`LimitNOFILE=65535`](#重启依然报错在redis_6380service文件中加上limitnofile65535)
  - [重载配置，重启`redis`查看日志,文件描述符的报错已解决](#重载配置重启redis查看日志文件描述符的报错已解决)
    - [解决警告`WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.`](#解决警告warning-the-tcp-backlog-setting-of-511-cannot-be-enforced-because-procsysnetcoresomaxconn-is-set-to-the-lower-value-of-128)
  - [重启`redis`](#重启redis)
    - [解决警告`WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.`](#解决警告warning-overcommit_memory-is-set-to-0-background-save-may-fail-under-low-memory-condition-to-fix-this-issue-add-vmovercommit_memory--1-to-etcsysctlconf-and-then-reboot-or-run-the-command-sysctl-vmovercommit_memory1-for-this-to-take-effect)
  - [重启redis](#重启redis-1)
    - [解决警告`WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.`](#解决警告warning-you-have-transparent-huge-pages-thp-support-enabled-in-your-kernel-this-will-create-latency-and-memory-usage-issues-with-redis-to-fix-this-issue-run-the-command-echo-never--syskernelmmtransparent_hugepageenabled-as-root-and-add-it-to-your-etcrclocal-in-order-to-retain-the-setting-after-a-reboot-redis-must-be-restarted-after-thp-is-disabled)
  - [重启`redis`](#重启redis-2)
  - [设置开机启动](#设置开机启动)

# `CentOS7.9`安装`redis-4.0.14`

## 查看`CentOS`版本

```shell
[root@node01 ~]# cat /etc/redhat-release
```

## 在`/opt`下创建并进入`software`目录

```shell
[root@node01 ~]# mkdir -p /opt/software
[root@node01 ~]# cd /opt/software
```

## 下载`redis-4.0.14`

```shell
[root@node01 software]# wget https://download.redis.io/releases/redis-4.0.14.tar.gz
```

## 解压`redis-4.0.14`到`/usr/local`下

```shell
[root@node01 software]# tar -zxvf redis-4.0.14.tar.gz -C /usr/local
```

## 创建符号链接

```shell
[root@node01 software]# ln -sv /usr/local/redis-4.0.14 /usr/local/redis
```

## 进入`/usr/loca/redis`目录并编译

```shell
[root@node01 software]# cd /usr/local/redis
[root@node01 redis]# make
```

### 报错`gcc: Command not found`

```
cd hiredis && make static
make[3]: Entering directory `/usr/local/redis-4.0.14/deps/hiredis'
gcc -std=c99 -pedantic -c -O3 -fPIC  -Wall -W -Wstrict-prototypes -Wwrite-strings -g -ggdb  net.c
make[3]: gcc: Command not found
make[3]: *** [net.o] Error 127
make[3]: Leaving directory `/usr/local/redis-4.0.14/deps/hiredis'
make[2]: *** [hiredis] Error 2
make[2]: Leaving directory `/usr/local/redis-4.0.14/deps'
make[1]: [persist-settings] Error 2 (ignored)
    CC adlist.o
/bin/sh: cc: command not found
make[1]: *** [adlist.o] Error 127
make[1]: Leaving directory `/usr/local/redis-4.0.14/src'
make: *** [all] Error 2
```

## 通过`yum`仓库安装`gcc`，重新编译

```shell
[root@node01 redis]# yum -y install gcc
[root@node01 redis]# make
```

### 报错`zmalloc.h:50:31: fatal error: jemalloc/jemalloc.h: No such file or directory`

```
cd src && make all
make[1]: Entering directory `/usr/local/redis-4.0.14/src'
    CC Makefile.dep
make[1]: Leaving directory `/usr/local/redis-4.0.14/src'
make[1]: Entering directory `/usr/local/redis-4.0.14/src'
    CC adlist.o
In file included from adlist.c:34:0:
zmalloc.h:50:31: fatal error: jemalloc/jemalloc.h: No such file or directory
 #include <jemalloc/jemalloc.h>
                               ^
compilation terminated.
make[1]: *** [adlist.o] Error 1
make[1]: Leaving directory `/usr/local/redis-4.0.14/src'
make: *** [all] Error 2
```

## `make`增加`MALLOC=libc`参数

```shell
[root@node01 redis]# make MALLOC=libc
```

## 执行`make test`命令

```shell
[root@node01 redis]# make test
```

### 报错`You need tcl 8.5 or newer in order to run the Redis test`

```
cd src && make test
make[1]: Entering directory `/usr/local/redis-4.0.14/src'
    CC Makefile.dep
make[1]: Leaving directory `/usr/local/redis-4.0.14/src'
make[1]: Entering directory `/usr/local/redis-4.0.14/src'
You need tcl 8.5 or newer in order to run the Redis test
make[1]: *** [test] Error 1
make[1]: Leaving directory `/usr/local/redis-4.0.14/src'
make: *** [test] Error 2
```

## 进入`/opt/software`目录，下载`tcl8.6.11-src.tar.gz`

```shell
[root@node01 redis]# cd /opt/software
[root@node01 software]# wget https://sourceforge.net/projects/tcl/files/Tcl/8.6.11/tcl8.6.11-src.tar.gz
```

## 解压`tcl8.6.11-src.tar.gz`到`/usr/local`下

```shell
[root@node01 software]# tar -zxvf tcl8.6.11-src.tar.gz
```

## 进入`/opt/software/tcl8.6.11/unix`目录，配置软件安装路径

```shell
[root@node01 software]# cd /opt/software/tcl8.6.11/unix
[root@node01 unix]# ./configure --prefix=/usr/local/tcl8.6.11
```

## 编译`tcl8.6.11`

```shell
[root@node01 unix]# make
```

## 编译测试`tcl8.6.11`

```shell
[root@node01 unix]# make test
```

## 编译安装`tcl8.6.11`

```shell
[root@node01 unix]# make install
```

## 将编译好的二进制文件拷贝到`/usr/loca/bin`目录

```shell
[root@node01 unix]# cp /usr/local/tcl8.6.11/bin/* /usr/local/bin
```

## 再次进入`/usr/loca/redis`目录，执行`make test`命令

```shell
[root@node01 unix]# cd /usr/local/redis
[root@node01 redis]# make test
```

## 编译安装`redis-4.0.14`

```shell
[root@node01 redis]# make PREFIX=/usr/local/redis install
```

## 在`/data/redis`目录下创建`conf`、`logs`、`pid`目录

```shell
[root@node01 redis]# mkdir -p /data/redis/{conf,logs,pid}
```

## 拷贝`/usr/local/redis/redis.conf`文件为`/data/redis/conf/redis_6380.conf`

```shell
[root@node01 redis]# cp /usr/local/redis/redis.conf /data/redis/conf/redis_6380.conf
```

## 修改`redis_6380.conf`配置文件

```shell
[root@node01 redis]# vi /data/redis/conf/redis_6380.conf
```

```
bind 0.0.0.0
protected-mode no
port 6380
daemonize yes
pidfile "/data/redis/pid/redis_6380.pid"
loglevel debug
logfile "/data/redis/logs/redis_6380.log"
dbfilename "redis_6380.rdb"
dir "/data/redis/logs/"
requirepass qiqi
```

## 创建`redis`组，创建`redis`系统用户并加入到`redis`组

```shell
[root@node01 redis]# groupadd redis
[root@node01 redis]# adduser -r -g redis -s /bin/false redis
```

## 修改`/data/redis`目录属主和属组为`redis`

```shell
[root@node01 redis]# chown -R redis:redis /data/redis
```

## 配置`systemd`管理`redis`

```shell
[root@node01 redis]# cat > /usr/lib/systemd/system/redis_6380.service << EOF
[Unit]
Description=Redis persistent key-value database
After=network.target
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/redis-server /data/redis/conf/redis_6380.conf --supervised systemd
ExecStop=/usr/local/bin/redis-cli -p 6380 -a qiqi SHUTDOWN
Type=notify
User=redis
Group=redis
RuntimeDirectory=redis
RuntimeDirectoryMode=0755

[Install]
WantedBy=multi-user.target
EOF
```

## 重载配置，启动`redis`，查看`redis`状态、重启`redis`

```shell
[root@node01 redis]# systemctl daemon-reload
[root@node01 redis]# systemctl start redis_6380.service
[root@node01 redis]# systemctl status redis_6380.service
[root@node01 redis]# systemctl restart redis_6380.service
```

## 停止`redis`,查看`redis`状态

```shell
[root@node01 redis]# systemctl stop redis_6380.service
[root@node01 redis]# systemctl status redis_6380.service
```

## 确认`systemd`配置无误后，启动`redis`，进入日志所在目录查看日志

```shell
[root@node01 redis]# systemctl start redis_6380.service
[root@node01 redis]# cd /data/redis/logs
[root@node01 logs]# tail -100f redis_6380.log
```

```
1560:C 08 Aug 15:32:34.519 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1560:C 08 Aug 15:32:34.519 # Redis version=4.0.14, bits=64, commit=00000000, modified=0, pid=1560, just started
1560:C 08 Aug 15:32:34.519 # Configuration loaded
1560:C 08 Aug 15:32:34.519 * supervised by systemd, will signal readiness
1560:M 08 Aug 15:32:34.532 # You requested maxclients of 10000 requiring at least 10032 max file descriptors.
1560:M 08 Aug 15:32:34.532 # Server can't set maximum open files to 10032 because of OS error: Operation not permitted.
1560:M 08 Aug 15:32:34.532 # Current maximum open files is 4096. maxclients has been reduced to 4064 to compensate for low ulimit. If you need higher maxclients increase 'ulimit -n'.
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 4.0.14 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6380
 |    `-._   `._    /     _.-'    |     PID: 1560
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

1560:M 08 Aug 15:32:34.535 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
1560:M 08 Aug 15:32:34.535 # Server initialized
1560:M 08 Aug 15:32:34.535 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
1560:M 08 Aug 15:32:34.536 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
1560:M 08 Aug 15:32:34.536 . Unrecognized RDB AUX field: 'redis-ver'
1560:M 08 Aug 15:32:34.536 . Unrecognized RDB AUX field: 'redis-bits'
1560:M 08 Aug 15:32:34.536 . Unrecognized RDB AUX field: 'ctime'
1560:M 08 Aug 15:32:34.536 . Unrecognized RDB AUX field: 'used-mem'
1560:M 08 Aug 15:32:34.536 . Unrecognized RDB AUX field: 'aof-preamble'
1560:M 08 Aug 15:32:34.536 * DB loaded from disk: 0.000 seconds
1560:M 08 Aug 15:32:34.536 * Ready to accept connections
1560:M 08 Aug 15:32:34.538 - DB 0: 3 keys (0 volatile) in 4 slots HT.
1560:M 08 Aug 15:32:34.538 - 0 clients connected (0 slaves), 544464 bytes in use
```

### 您请求的最大客户端数量为10000，至少需要10032个最大文件描述符。服务器无法将最大打开文件设置为10032，因为操作系统错误:操作不允许。当前最大打开文件是4096。`maxclients`已经减少到4064，以弥补低`ulimit`。如果需要更高的`maxclients`，请增加`ulimit -n`。

```
1560:M 08 Aug 15:32:34.532 # You requested maxclients of 10000 requiring at least 10032 max file descriptors.
1560:M 08 Aug 15:32:34.532 # Server can't set maximum open files to 10032 because of OS error: Operation not permitted.
1560:M 08 Aug 15:32:34.532 # Current maximum open files is 4096. maxclients has been reduced to 4064 to compensate for low ulimit. If you need higher maxclients increase 'ulimit -n'.
```

## 调整系统中最大可同时存在的进程数和最大文件数

```shell
[root@node01 logs]# cat >> /etc/security/limits.conf << EOF
* hard nproc 65535
* soft nproc 65535
* hard nofile 65535
* soft nofile 65535
EOF
```

## 执行`source /etc/profile`，使配置生效

```shell
[root@node01 logs]# source /etc/profile
```

## 重启，依然报错，在`redis_6380.service`文件中加上`LimitNOFILE=65535`

```shell
[root@node01 logs]# cat > /usr/lib/systemd/system/redis_6380.service << EOF
[Unit]
Description=Redis persistent key-value database
After=network.target
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/redis-server /data/redis/conf/redis_6380.conf --supervised systemd
ExecStop=/usr/local/bin/redis-cli -p 6380 -a qiqi SHUTDOWN
Type=notify
User=redis
Group=redis
RuntimeDirectory=redis
RuntimeDirectoryMode=0755
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

## 重载配置，重启`redis`查看日志,文件描述符的报错已解决

```shell
[root@node01 logs]# systemctl daemon-reload
[root@node01 logs]# systemctl restart redis_6380.service
```

```
1923:M 08 Aug 16:55:23.621 # User requested shutdown...
1923:M 08 Aug 16:55:23.621 * Saving the final RDB snapshot before exiting.
1923:M 08 Aug 16:55:23.622 * DB saved on disk
1923:M 08 Aug 16:55:23.622 * Removing the pid file.
1923:M 08 Aug 16:55:23.622 # Redis is now ready to exit, bye bye...
1941:C 08 Aug 16:55:23.634 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1941:C 08 Aug 16:55:23.635 # Redis version=4.0.14, bits=64, commit=00000000, modified=0, pid=1941, just started
1941:C 08 Aug 16:55:23.635 # Configuration loaded
1941:C 08 Aug 16:55:23.635 * supervised by systemd, will signal readiness
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 4.0.14 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6380
 |    `-._   `._    /     _.-'    |     PID: 1941
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

1941:M 08 Aug 16:55:23.637 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
1941:M 08 Aug 16:55:23.637 # Server initialized
1941:M 08 Aug 16:55:23.637 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
1941:M 08 Aug 16:55:23.637 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
1941:M 08 Aug 16:55:23.637 . Unrecognized RDB AUX field: 'redis-ver'
1941:M 08 Aug 16:55:23.637 . Unrecognized RDB AUX field: 'redis-bits'
1941:M 08 Aug 16:55:23.637 . Unrecognized RDB AUX field: 'ctime'
1941:M 08 Aug 16:55:23.637 . Unrecognized RDB AUX field: 'used-mem'
1941:M 08 Aug 16:55:23.637 . Unrecognized RDB AUX field: 'aof-preamble'
1941:M 08 Aug 16:55:23.637 * DB loaded from disk: 0.000 seconds
1941:M 08 Aug 16:55:23.637 * Ready to accept connections
1941:M 08 Aug 16:55:23.638 - DB 0: 3 keys (0 volatile) in 4 slots HT.
1941:M 08 Aug 16:55:23.638 - 0 clients connected (0 slaves), 853136 bytes in use
```

### 解决警告`WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.`

```shell
[root@node01 logs]# cat >> /etc/sysctl.conf << EOF
net.core.somaxconn=1024
EOF
[root@node01 logs]# sysctl -p
```

## 重启`redis`

```shell
[root@node01 logs]# systemctl restart redis_6380.service
```

```
1941:M 08 Aug 17:04:38.316 # User requested shutdown...
1941:M 08 Aug 17:04:38.316 * Saving the final RDB snapshot before exiting.
1941:M 08 Aug 17:04:38.317 * DB saved on disk
1941:M 08 Aug 17:04:38.317 * Removing the pid file.
1941:M 08 Aug 17:04:38.318 # Redis is now ready to exit, bye bye...
1976:C 08 Aug 17:04:38.337 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1976:C 08 Aug 17:04:38.337 # Redis version=4.0.14, bits=64, commit=00000000, modified=0, pid=1976, just started
1976:C 08 Aug 17:04:38.337 # Configuration loaded
1976:C 08 Aug 17:04:38.337 * supervised by systemd, will signal readiness
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 4.0.14 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6380
 |    `-._   `._    /     _.-'    |     PID: 1976
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

1976:M 08 Aug 17:04:38.339 # Server initialized
1976:M 08 Aug 17:04:38.340 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
1976:M 08 Aug 17:04:38.340 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
1976:M 08 Aug 17:04:38.340 . Unrecognized RDB AUX field: 'redis-ver'
1976:M 08 Aug 17:04:38.341 . Unrecognized RDB AUX field: 'redis-bits'
1976:M 08 Aug 17:04:38.341 . Unrecognized RDB AUX field: 'ctime'
1976:M 08 Aug 17:04:38.341 . Unrecognized RDB AUX field: 'used-mem'
1976:M 08 Aug 17:04:38.341 . Unrecognized RDB AUX field: 'aof-preamble'
1976:M 08 Aug 17:04:38.341 * DB loaded from disk: 0.000 seconds
1976:M 08 Aug 17:04:38.341 * Ready to accept connections
1976:M 08 Aug 17:04:38.341 - DB 0: 3 keys (0 volatile) in 4 slots HT.
1976:M 08 Aug 17:04:38.341 - 0 clients connected (0 slaves), 853136 bytes in use
```

### 解决警告`WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.`

```shell
[root@node01 logs]# cat >> /etc/sysctl.conf << EOF
vm.overcommit_memory=1
EOF
[root@node01 logs]# sysctl -p
```

## 重启redis

```shell
[root@node01 logs]# systemctl restart redis_6380.service
```

```
1976:M 08 Aug 17:11:23.006 # User requested shutdown...
1976:M 08 Aug 17:11:23.006 * Saving the final RDB snapshot before exiting.
1976:M 08 Aug 17:11:23.007 * DB saved on disk
1976:M 08 Aug 17:11:23.007 * Removing the pid file.
1976:M 08 Aug 17:11:23.007 # Redis is now ready to exit, bye bye...
1994:C 08 Aug 17:11:23.020 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1994:C 08 Aug 17:11:23.020 # Redis version=4.0.14, bits=64, commit=00000000, modified=0, pid=1994, just started
1994:C 08 Aug 17:11:23.020 # Configuration loaded
1994:C 08 Aug 17:11:23.020 * supervised by systemd, will signal readiness
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 4.0.14 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6380
 |    `-._   `._    /     _.-'    |     PID: 1994
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

1994:M 08 Aug 17:11:23.022 # Server initialized
1994:M 08 Aug 17:11:23.022 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
1994:M 08 Aug 17:11:23.022 . Unrecognized RDB AUX field: 'redis-ver'
1994:M 08 Aug 17:11:23.022 . Unrecognized RDB AUX field: 'redis-bits'
1994:M 08 Aug 17:11:23.022 . Unrecognized RDB AUX field: 'ctime'
1994:M 08 Aug 17:11:23.022 . Unrecognized RDB AUX field: 'used-mem'
1994:M 08 Aug 17:11:23.022 . Unrecognized RDB AUX field: 'aof-preamble'
1994:M 08 Aug 17:11:23.022 * DB loaded from disk: 0.000 seconds
1994:M 08 Aug 17:11:23.022 * Ready to accept connections
1994:M 08 Aug 17:11:23.022 - DB 0: 3 keys (0 volatile) in 4 slots HT.
1994:M 08 Aug 17:11:23.022 - 0 clients connected (0 slaves), 853136 bytes in use
```

### 解决警告`WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.`

```shell
[root@node01 logs]# cat /sys/kernel/mm/transparent_hugepage/enabled
[root@node01 logs]# echo never > /sys/kernel/mm/transparent_hugepage/enabled
[root@node01 logs]# cat /sys/kernel/mm/transparent_hugepage/enabled
[root@node01 logs]# echo 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' >> /etc/rc.local
[root@node01 logs]# chmod u+x /etc/rc.d/rc.local
```

## 重启`redis`

```shell
[root@node01 logs]# systemctl restart redis_6380.service
```

```
1159:M 08 Aug 17:30:32.644 # User requested shutdown...
1159:M 08 Aug 17:30:32.645 * Saving the final RDB snapshot before exiting.
1159:M 08 Aug 17:30:32.646 * DB saved on disk
1159:M 08 Aug 17:30:32.646 * Removing the pid file.
1159:M 08 Aug 17:30:32.647 # Redis is now ready to exit, bye bye...
1172:C 08 Aug 17:30:32.661 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1172:C 08 Aug 17:30:32.661 # Redis version=4.0.14, bits=64, commit=00000000, modified=0, pid=1172, just started
1172:C 08 Aug 17:30:32.661 # Configuration loaded
1172:C 08 Aug 17:30:32.661 * supervised by systemd, will signal readiness
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 4.0.14 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6380
 |    `-._   `._    /     _.-'    |     PID: 1172
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

1172:M 08 Aug 17:30:32.664 # Server initialized
1172:M 08 Aug 17:30:32.664 . Unrecognized RDB AUX field: 'redis-ver'
1172:M 08 Aug 17:30:32.664 . Unrecognized RDB AUX field: 'redis-bits'
1172:M 08 Aug 17:30:32.664 . Unrecognized RDB AUX field: 'ctime'
1172:M 08 Aug 17:30:32.664 . Unrecognized RDB AUX field: 'used-mem'
1172:M 08 Aug 17:30:32.664 . Unrecognized RDB AUX field: 'aof-preamble'
1172:M 08 Aug 17:30:32.664 * DB loaded from disk: 0.000 seconds
1172:M 08 Aug 17:30:32.664 * Ready to accept connections
1172:M 08 Aug 17:30:32.664 - DB 0: 3 keys (0 volatile) in 4 slots HT.
1172:M 08 Aug 17:30:32.664 - 0 clients connected (0 slaves), 853136 bytes in use
```

## 设置开机启动

```shell
[root@node01 logs]# systemctl list-unit-files redis_6380.service
[root@node01 logs]# systemctl enable redis_6380.service
[root@node01 logs]# systemctl list-unit-files redis_6380.service
```

