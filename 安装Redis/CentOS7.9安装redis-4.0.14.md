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

