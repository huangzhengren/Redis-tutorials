- [`CentOS7.9`下`redis-4.0.14`搭建主从复制](#centos79下redis-4014搭建主从复制)
  - [基础信息](#基础信息)
  - [`node01`配置](#node01配置)
  - [`node02`配置](#node02配置)
  - [`node03`配置](#node03配置)
  - [启动三台服务器的`redis`](#启动三台服务器的redis)
  - [查看`node01`日志](#查看node01日志)
  - [查看`node02`日志](#查看node02日志)
  - [查看`node03`日志](#查看node03日志)
  - [在`node01`执行`INFO Replication`查看复制信息](#在node01执行info-replication查看复制信息)
  - [在`node02`执行`INFO Replication`查看复制信息](#在node02执行info-replication查看复制信息)
  - [在`node03`执行`INFO Replication`查看复制信息](#在node03执行info-replication查看复制信息)
  - [在`node01`上执行`SET name qiqi`，在`node02`和`node03`上执行`GET name`，确认从节点复制成功](#在node01上执行set-name-qiqi在node02和node03上执行get-name确认从节点复制成功)

# `CentOS7.9`下`redis-4.0.14`搭建主从复制

## 基础信息

```
机器名  节点    IP             端口
node01 master 192.168.86.101 6380
node02 slave  192.168.86.102 6380
node03 slave  192.168.86.103 6380
```

## `node01`配置

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
masterauth qiqi
```

## `node02`配置

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
masterauth qiqi
slaveof 192.168.86.101 6380
```

## `node03`配置

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
masterauth qiqi
slaveof 192.168.86.101 6380
```

## 启动三台服务器的`redis`

```shell
[root@node01 conf]# systemctl start redis_6380.service
[root@node02 conf]# systemctl start redis_6380.service
[root@node03 conf]# systemctl start redis_6380.service
```

## 查看`node01`日志

```
1396:C 09 Aug 10:03:49.004 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1396:C 09 Aug 10:03:49.004 # Redis version=4.0.14, bits=64, commit=00000000, modified=0, pid=1396, just started
1396:C 09 Aug 10:03:49.004 # Configuration loaded
1396:C 09 Aug 10:03:49.004 * supervised by systemd, will signal readiness
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 4.0.14 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6380
 |    `-._   `._    /     _.-'    |     PID: 1396
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

1396:M 09 Aug 10:03:49.019 # Server initialized
1396:M 09 Aug 10:03:49.020 * Ready to accept connections
1396:M 09 Aug 10:03:49.020 - 0 clients connected (0 slaves), 852847 bytes in use
1396:M 09 Aug 10:03:52.491 - Accepted 192.168.86.102:44974
1396:M 09 Aug 10:03:52.505 * Slave 192.168.86.102:6380 asks for synchronization
1396:M 09 Aug 10:03:52.506 * Full resync requested by slave 192.168.86.102:6380
1396:M 09 Aug 10:03:52.506 * Starting BGSAVE for SYNC with target: disk
1396:M 09 Aug 10:03:52.506 * Background saving started by pid 1400
1400:C 09 Aug 10:03:52.508 * DB saved on disk
1400:C 09 Aug 10:03:52.508 * RDB: 0 MB of memory used by copy-on-write
1396:M 09 Aug 10:03:52.555 * Background saving terminated with success
1396:M 09 Aug 10:03:52.556 * Synchronization with slave 192.168.86.102:6380 succeeded
1396:M 09 Aug 10:03:54.068 - 0 clients connected (1 slaves), 1918797 bytes in use
1396:M 09 Aug 10:03:56.857 - Accepted 192.168.86.103:39375
1396:M 09 Aug 10:03:56.973 * Slave 192.168.86.103:6380 asks for synchronization
1396:M 09 Aug 10:03:56.973 * Full resync requested by slave 192.168.86.103:6380
1396:M 09 Aug 10:03:56.974 * Starting BGSAVE for SYNC with target: disk
1396:M 09 Aug 10:03:56.974 * Background saving started by pid 1401
1401:C 09 Aug 10:03:56.979 * DB saved on disk
1401:C 09 Aug 10:03:56.979 * RDB: 0 MB of memory used by copy-on-write
1396:M 09 Aug 10:03:56.998 * Background saving terminated with success
1396:M 09 Aug 10:03:56.998 * Synchronization with slave 192.168.86.103:6380 succeeded
1396:M 09 Aug 10:03:59.120 - 0 clients connected (2 slaves), 1936163 bytes in use
1396:M 09 Aug 10:04:04.217 - 0 clients connected (2 slaves), 1935131 bytes in use
```

## 查看`node02`日志

```
1406:C 09 Aug 10:03:53.232 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1406:C 09 Aug 10:03:53.232 # Redis version=4.0.14, bits=64, commit=00000000, modified=0, pid=1406, just started
1406:C 09 Aug 10:03:53.232 # Configuration loaded
1406:C 09 Aug 10:03:53.232 * supervised by systemd, will signal readiness
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 4.0.14 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6380
 |    `-._   `._    /     _.-'    |     PID: 1406
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

1406:S 09 Aug 10:03:53.234 # Server initialized
1406:S 09 Aug 10:03:53.234 * Ready to accept connections
1406:S 09 Aug 10:03:53.234 - 0 clients connected (0 slaves), 852871 bytes in use
1406:S 09 Aug 10:03:53.234 * Connecting to MASTER 192.168.86.101:6380
1406:S 09 Aug 10:03:53.234 * MASTER <-> SLAVE sync started
1406:S 09 Aug 10:03:53.244 * Non blocking connect for SYNC fired the event.
1406:S 09 Aug 10:03:53.247 * Master replied to PING, replication can continue...
1406:S 09 Aug 10:03:53.249 * Partial resynchronization not possible (no cached master)
1406:S 09 Aug 10:03:53.251 * Full resync from master: 34e1ef603c82a04ca9f3214f3517157871b2ca86:0
1406:S 09 Aug 10:03:53.299 * MASTER <-> SLAVE sync: receiving 176 bytes from master
1406:S 09 Aug 10:03:53.300 * MASTER <-> SLAVE sync: Flushing old data
1406:S 09 Aug 10:03:53.300 * MASTER <-> SLAVE sync: Loading DB in memory
1406:S 09 Aug 10:03:53.300 . Unrecognized RDB AUX field: 'redis-ver'
1406:S 09 Aug 10:03:53.300 . Unrecognized RDB AUX field: 'redis-bits'
1406:S 09 Aug 10:03:53.300 . Unrecognized RDB AUX field: 'ctime'
1406:S 09 Aug 10:03:53.300 . Unrecognized RDB AUX field: 'used-mem'
1406:S 09 Aug 10:03:53.300 . Unrecognized RDB AUX field: 'aof-preamble'
1406:S 09 Aug 10:03:53.300 * MASTER <-> SLAVE sync: Finished with success
1406:S 09 Aug 10:03:58.286 - 1 clients connected (0 slaves), 1918759 bytes in use
1406:S 09 Aug 10:04:03.354 - 1 clients connected (0 slaves), 1918801 bytes in use
```

## 查看`node03`日志

```
1299:C 09 Aug 10:03:57.466 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1299:C 09 Aug 10:03:57.466 # Redis version=4.0.14, bits=64, commit=00000000, modified=0, pid=1299, just started
1299:C 09 Aug 10:03:57.466 # Configuration loaded
1299:C 09 Aug 10:03:57.466 * supervised by systemd, will signal readiness
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 4.0.14 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6380
 |    `-._   `._    /     _.-'    |     PID: 1299
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

1299:S 09 Aug 10:03:57.471 # Server initialized
1299:S 09 Aug 10:03:57.492 * Ready to accept connections
1299:S 09 Aug 10:03:57.493 - 0 clients connected (0 slaves), 852871 bytes in use
1299:S 09 Aug 10:03:57.494 * Connecting to MASTER 192.168.86.101:6380
1299:S 09 Aug 10:03:57.495 * MASTER <-> SLAVE sync started
1299:S 09 Aug 10:03:57.544 * Non blocking connect for SYNC fired the event.
1299:S 09 Aug 10:03:57.654 * Master replied to PING, replication can continue...
1299:S 09 Aug 10:03:57.657 * Partial resynchronization not possible (no cached master)
1299:S 09 Aug 10:03:57.661 * Full resync from master: 34e1ef603c82a04ca9f3214f3517157871b2ca86:0
1299:S 09 Aug 10:03:57.685 * MASTER <-> SLAVE sync: receiving 176 bytes from master
1299:S 09 Aug 10:03:57.685 * MASTER <-> SLAVE sync: Flushing old data
1299:S 09 Aug 10:03:57.685 * MASTER <-> SLAVE sync: Loading DB in memory
1299:S 09 Aug 10:03:57.685 . Unrecognized RDB AUX field: 'redis-ver'
1299:S 09 Aug 10:03:57.685 . Unrecognized RDB AUX field: 'redis-bits'
1299:S 09 Aug 10:03:57.686 . Unrecognized RDB AUX field: 'ctime'
1299:S 09 Aug 10:03:57.686 . Unrecognized RDB AUX field: 'used-mem'
1299:S 09 Aug 10:03:57.686 . Unrecognized RDB AUX field: 'aof-preamble'
1299:S 09 Aug 10:03:57.686 * MASTER <-> SLAVE sync: Finished with success
1299:S 09 Aug 10:04:03.365 - 1 clients connected (0 slaves), 1918801 bytes in use
1299:S 09 Aug 10:04:08.566 - 1 clients connected (0 slaves), 1918801 bytes in use
1299:S 09 Aug 10:04:13.922 - 1 clients connected (0 slaves), 1918801 bytes in use
1299:S 09 Aug 10:04:18.968 - 1 clients connected (0 slaves), 1918801 bytes in use
```

## 在`node01`执行`INFO Replication`查看复制信息

```shell
[root@node01 conf]# redis-cli -p 6380
127.0.0.1:6380> AUTH qiqi
OK
127.0.0.1:6380> INFO Replication
# Replication
role:master
connected_slaves:2
slave0:ip=192.168.86.102,port=6380,state=online,offset=714,lag=0
slave1:ip=192.168.86.103,port=6380,state=online,offset=700,lag=1
master_replid:34e1ef603c82a04ca9f3214f3517157871b2ca86
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:714
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:714
```

## 在`node02`执行`INFO Replication`查看复制信息

```shell
[root@node02 conf]# redis-cli -p 6380
127.0.0.1:6380> AUTH qiqi
OK
127.0.0.1:6380> INFO Replication
# Replication
role:slave
master_host:192.168.86.101
master_port:6380
master_link_status:up
master_last_io_seconds_ago:1
master_sync_in_progress:0
slave_repl_offset:868
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:34e1ef603c82a04ca9f3214f3517157871b2ca86
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:868
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:868
```

## 在`node03`执行`INFO Replication`查看复制信息

```shell
[root@node03 conf]# redis-cli -p 6380
127.0.0.1:6380> AUTH qiqi
OK
127.0.0.1:6380> INFO Replication
# Replication
role:slave
master_host:192.168.86.101
master_port:6380
master_link_status:up
master_last_io_seconds_ago:9
master_sync_in_progress:0
slave_repl_offset:952
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:34e1ef603c82a04ca9f3214f3517157871b2ca86
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:952
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:952
```

## 在`node01`上执行`SET name qiqi`，在`node02`和`node03`上执行`GET name`，确认从节点复制成功

```shell
[root@node01 conf]# redis-cli -p 6380
127.0.0.1:6380> AUTH qiqi
OK
127.0.0.1:6380> SET name qiqi
OK
```

```shell
[root@node02 conf]# redis-cli -p 6380
127.0.0.1:6380> AUTH qiqi
OK
127.0.0.1:6380> GET name
"qiqi"
```

```shell
[root@node03 conf]# redis-cli -p 6380
127.0.0.1:6380> AUTH qiqi
OK
127.0.0.1:6380> GET name
"qiqi"
```







