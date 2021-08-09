- [搭建Redis-Sentinel哨兵监控](#搭建redis-sentinel哨兵监控)
  - [基础环境](#基础环境)
  - [`node01`、`node02`、`node03`关闭防火墙，禁止开机启动](#node01node02node03关闭防火墙禁止开机启动)
  - [在`node01`、`node02`、`node03`的`/data/redis`目录下创建`conf`、`logs`、`pid`目录](#在node01node02node03的dataredis目录下创建conflogspid目录)
  - [在`node01`、`node02`、`node03`拷贝redis.conf和sentinel.conf配置文件](#在node01node02node03拷贝redisconf和sentinelconf配置文件)
  - [在`node01`、`node02`、`node03`修改属主和属组为`redis`](#在node01node02node03修改属主和属组为redis)
  - [`node01`节点`data/redis/conf/redis_6380.conf`配置](#node01节点dataredisconfredis_6380conf配置)
  - [`node02`节点`data/redis/conf/redis_6380.conf`配置](#node02节点dataredisconfredis_6380conf配置)
  - [`node03`节点`data/redis/conf/redis_6380.conf`配置](#node03节点dataredisconfredis_6380conf配置)
  - [在`node01`配置`/usr/lib/systemd/system/redis_6380.service`](#在node01配置usrlibsystemdsystemredis_6380service)
  - [在`node02`配置`/usr/lib/systemd/system/redis_6380.service`](#在node02配置usrlibsystemdsystemredis_6380service)
  - [在`node03`配置`/usr/lib/systemd/system/redis_6380.service`](#在node03配置usrlibsystemdsystemredis_6380service)
  - [`node01`配置`data/redis/conf/sentinel_16380.conf`](#node01配置dataredisconfsentinel_16380conf)
  - [`node02`配置`data/redis/conf/sentinel_16380.conf`](#node02配置dataredisconfsentinel_16380conf)
  - [`node03`配置`data/redis/conf/sentinel_16380.conf`](#node03配置dataredisconfsentinel_16380conf)
  - [在`node01`配置`/usr/lib/systemd/system/sentinel_16380.service`](#在node01配置usrlibsystemdsystemsentinel_16380service)
  - [在`node02`配置`/usr/lib/systemd/system/sentinel_16380.service`](#在node02配置usrlibsystemdsystemsentinel_16380service)
  - [在`node03`配置`/usr/lib/systemd/system/sentinel_16380.service`](#在node03配置usrlibsystemdsystemsentinel_16380service)
  - [`node01`、`node02`、`node03`重载配置](#node01node02node03重载配置)
  - [启动三台服务器的`redis`](#启动三台服务器的redis)
  - [启动三台服务器的redis-sentinel](#启动三台服务器的redis-sentinel)

# 搭建Redis-Sentinel哨兵监控

## 基础环境

```shell
机器名  节点    IP             端口
node01 master 192.168.86.101 6380
node02 slave  192.168.86.102 6380
node03 slave  192.168.86.103 6380
```

```
redis编译安装完毕，创建了redis用户
```

## `node01`、`node02`、`node03`关闭防火墙，禁止开机启动

```shell
[root@node01 redis]# systemctl stop firewalld
[root@node01 redis]# systemctl disable firewalld
```

```shell
[root@node02 redis]# systemctl stop firewalld
[root@node02 redis]# systemctl disable firewalld
```

```shell
[root@node02 redis]# systemctl stop firewalld
[root@node02 redis]# systemctl disable firewald
```

## 在`node01`、`node02`、`node03`的`/data/redis`目录下创建`conf`、`logs`、`pid`目录

```shell
[root@node01 redis]# mkdir -p /data/redis/{conf,logs,pid}
```

```shell
[root@node02 redis]# mkdir -p /data/redis/{conf,logs,pid}
```

```shell
[root@node03 redis]# mkdir -p /data/redis/{conf,logs,pid}
```

## 在`node01`、`node02`、`node03`拷贝redis.conf和sentinel.conf配置文件

```shell
[root@node01 redis]# cp /usr/local/redis/redis.conf /data/redis/conf/redis_6380.conf
[root@node01 redis]# cp /usr/local/redis/sentinel.conf /data/redis/conf/sentinel_16380.conf
```

```shell
[root@node02 redis]# cp /usr/local/redis/redis.conf /data/redis/conf/redis_6380.conf
[root@node02 redis]# cp /usr/local/redis/sentinel.conf /data/redis/conf/sentinel_16380.conf
```

```shell
[root@node03 redis]# cp /usr/local/redis/redis.conf /data/redis/conf/redis_6380.conf
[root@node03 redis]# cp /usr/local/redis/sentinel.conf /data/redis/conf/sentinel_16380.conf
```

## 在`node01`、`node02`、`node03`修改属主和属组为`redis`

```shell
[root@node01 redis]# chown -R redis:redis /data/redis
```

```shell
[root@node02 redis]# chown -R redis:redis /data/redis
```

```shell
[root@node01 redis]# chown -R redis:redis /data/redis
```

## `node01`节点`data/redis/conf/redis_6380.conf`配置

```
bind 0.0.0.0
protected-mode no
port 6380
daemonize yes
pidfile "/data/redis/pid/redis_6380.pid"
loglevel debug
logfile "/data/redis/logs/redis_6380.log"
dbfilename "redis_6380.rdb"
dir "/data/redis/logs"
requirepass qiqi
masterauth qiqi
```

## `node02`节点`data/redis/conf/redis_6380.conf`配置

```
bind 0.0.0.0
protected-mode no
port 6380
daemonize yes
pidfile "/data/redis/pid/redis_6380.pid"
loglevel debug
logfile "/data/redis/logs/redis_6380.log"
dbfilename "redis_6380.rdb"
dir "/data/redis/logs"
requirepass qiqi
masterauth qiqi
slaveof 192.168.86.101 6380
```

## `node03`节点`data/redis/conf/redis_6380.conf`配置

```
bind 0.0.0.0
protected-mode no
port 6380
daemonize yes
pidfile "/data/redis/pid/redis_6380.pid"
loglevel debug
logfile "/data/redis/logs/redis_6380.log"
dbfilename "redis_6380.rdb"
dir "/data/redis/logs"
requirepass qiqi
masterauth qiqi
slaveof 192.168.86.101 6380
```

## 在`node01`配置`/usr/lib/systemd/system/redis_6380.service`

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
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target

EOF
```

## 在`node02`配置`/usr/lib/systemd/system/redis_6380.service`

```shell
[root@node02 redis]# cat > /usr/lib/systemd/system/redis_6380.service << EOF 
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

## 在`node03`配置`/usr/lib/systemd/system/redis_6380.service`

```shell
[root@node03 redis]# cat > /usr/lib/systemd/system/redis_6380.service << EOF 
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

## `node01`配置`data/redis/conf/sentinel_16380.conf`

```
bind 0.0.0.0
port 16380
dir "/data/redis/logs"
logfile "sentinel_16380.log"
sentinel deny-scripts-reconfig yes
sentinel monitor qiqi-redis 192.168.86.101 6380 2
sentinel down-after-milliseconds qiqi-redis 50000
sentinel parallel-syncs qiqi-redis 1
sentinel failover-timeout qiqi-redis 120000
sentinel auth-pass qiqi-redis qiqi
```

## `node02`配置`data/redis/conf/sentinel_16380.conf`

```
bind 0.0.0.0
port 16380
dir "/data/redis/logs"
logfile "sentinel_16380.log"
sentinel deny-scripts-reconfig yes
sentinel monitor qiqi-redis 192.168.86.101 6380 2
sentinel down-after-milliseconds qiqi-redis 50000
sentinel parallel-syncs qiqi-redis 1
sentinel failover-timeout qiqi-redis 120000
sentinel auth-pass qiqi-redis qiqi
```

## `node03`配置`data/redis/conf/sentinel_16380.conf`

```
bind 0.0.0.0
port 16380
dir "/data/redis/logs"
logfile "sentinel_16380.log"
sentinel deny-scripts-reconfig yes
sentinel monitor qiqi-redis 192.168.86.101 6380 2
sentinel down-after-milliseconds qiqi-redis 50000
sentinel parallel-syncs qiqi-redis 1
sentinel failover-timeout qiqi-redis 120000
sentinel auth-pass qiqi-redis qiqi
```

## 在`node01`配置`/usr/lib/systemd/system/sentinel_16380.service`

```shell
[root@node01 redis]# cat > /usr/lib/systemd/system/sentinel_16380.service << EOF
Description=Redis Sentinel
After=network.target
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/redis-sentinel /data/redis/conf/sentinel_16380.conf --supervised systemd
ExecStop=/usr/local/bin/redis-cli -p 16380 -a qiqi SHUTDOWN
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

## 在`node02`配置`/usr/lib/systemd/system/sentinel_16380.service`

```shell
[root@node02 redis]# cat > /usr/lib/systemd/system/sentinel_16380.service << EOF
Description=Redis Sentinel
After=network.target
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/redis-sentinel /data/redis/conf/sentinel_16380.conf --supervised systemd
ExecStop=/usr/local/bin/redis-cli -p 16380 -a qiqi SHUTDOWN
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

## 在`node03`配置`/usr/lib/systemd/system/sentinel_16380.service`

```shell
[root@node03 redis]# cat > /usr/lib/systemd/system/sentinel_16380.service << EOF
Description=Redis Sentinel
After=network.target
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/redis-sentinel /data/redis/conf/sentinel_16380.conf --supervised systemd
ExecStop=/usr/local/bin/redis-cli -p 16380 -a qiqi SHUTDOWN
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

## `node01`、`node02`、`node03`重载配置

```shell
[root@node01 redis]# systemctl daemon-reload
```

```shell
[root@node02 redis]# systemctl daemon-reload
```

```shell
[root@node03 redis]# systemctl daemon-reload
```

## 启动三台服务器的`redis`

```shell
[root@node01 redis]# systemctl start redis_6380.service
```

```shell
[root@node02 redis]# systemctl start redis_6380.service
```

```shell
[root@node03 redis]# systemctl start redis_6380.service
```

## 启动三台服务器的redis-sentinel

```shell
[root@node01 redis]# systemctl start sentinel_16380.service
```

```shell
[root@node02 redis]# systemctl start sentinel_16380.service
```

```shell
[root@node03 redis]# systemctl start sentinel_16380.service
```

