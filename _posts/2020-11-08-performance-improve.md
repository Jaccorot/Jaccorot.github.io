---
layout: post
title:  "图解性能优化"
date:   2020-11-08 16:00:00 +0800
categories: 性能 测试 调优 读书笔记 图解
tags: 性能测试
excerpt: 性能优化
---

* content
{:toc}
# os命令

## sar

* 概要信息

* 安装

```
yum install sysstat
```

* 使用

```
[jump@8-114-show ~]$ sar -f
Linux 3.10.0-957.el7.x86_64 (8-114-show) 	11/08/2020 	_x86_64_	(1 CPU)

06:40:01 PM     CPU     %user     %nice   %system   %iowait    %steal     %idle
06:50:02 PM     all      0.91      0.00      0.89      0.07      0.00     98.13
07:00:01 PM     all      0.79      0.00      0.89      0.06      0.00     98.27
07:10:01 PM     all      0.81      0.00      0.89      0.07      0.00     98.23
07:20:01 PM     all      0.83      0.00      0.89      0.07      0.00     98.21
07:30:01 PM     all      0.80      0.00      0.88      0.06      0.00     98.26
07:40:01 PM     all      0.80      0.00      0.90      0.07      0.00     98.23
07:50:01 PM     all      0.83      0.00      0.89      0.08      0.00     98.20
Average:        all      0.82      0.00      0.89      0.07      0.00     98.22


%user   用户空间的CPU使用

%nice   改变过优先级的进程的CPU使用率

%system   内核空间的CPU使用率

%iowait   CPU等待IO的百分比 

%steal   虚拟机的虚拟机CPU使用的CPU

%idle   空闲的CPU

在以上的显示当中，主要看%iowait和%idle，%iowait过高表示存在I/O瓶颈，即磁盘IO无法满足业务需求，如果%idle过低表示CPU使用率比较严重，需要结合内存使用等情况判断CPU是否瓶颈。  
```



## vmstat

* 概要信息
* 

```
[jump@8-114-show ~]$ vmstat 5 5
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 5  0      0 155600 152028 1309324    0    0     2    15    0    1  1  0 99  0  0
 0  0      0 155144 152032 1309324    0    0     0    22  298  579  1  2 97  0  0
 0  0      0 155096 152032 1309356    0    0     0    15  295  599  1  2 97  0  0
 0  0      0 155020 152036 1309360    0    0     0    56  239  458  1  1 99  0  0
 0  0      0 155004 152044 1309364    0    0     0    14  190  371  1  1 98  0  0
 
 
 字段说明：

Procs（进程）：

  r: 运行队列中进程数量

  b： 等待IO的进程数量

Memory（内存）：

  swpd: 使用虚拟内存大小

  free: 可用内存大小

  buff: 用作缓冲的内存大小

  cache: 用作缓存的内存大小

Swap：

  si: 每秒从交换区写到内存的大小

  so: 每秒写入交换区的内存大小

IO：（现在的Linux版本块的大小为1024bytes）

  bi: 每秒读取的块数

  bo: 每秒写入的块数

系统：

in: 每秒中断数，包括时钟中断。【interrupt】

cs: 每秒上下文切换数。        【count/second】

CPU（以百分比表示）：

  us: 用户进程执行时间(user time)

  sy: 系统进程执行时间(system time)

  id: 空闲时间(包括IO等待时间),中央处理器的空闲时间 。以百分比表示。

  wa: 等待IO时间

备注：

如果r经常大于4，id经常少于40，表示cpu的负荷很重。

如果bi，bo长期不等于0，表示内存不足。

如果disk经常不等于0，且在b中的队列大于3，表示io性能不好。
```

## ps

* 快照

* 通过OS的内核获取各个进程的信息

```
[jump@8-114-show ~]$ ps -axu
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1 125480  3544 ?        Ss    2019  58:17 /usr/lib/systemd/systemd --switched-root --system --deserialize 22

root      8388  0.0  0.0 116936   936 ?        S    20:13   0:00 sed -r s/.*offset(.*)sec.*/\1/
jump      8408  0.0  0.1 155360  1888 pts/0    R+   20:13   0:00 ps -axu
zabbix    8795  0.0  0.0  78768  1260 ?        S    Jul09   0:00 /usr/sbin/zabbix_agentd -c /etc/zabbix/zabbix_agentd.conf
zabbix    8796  0.1  0.0  78792  1548 ?        S    Jul09 284:56 /usr/sbin/zabbix_agentd: collector [idle 1 sec]
zabbix    8797  0.0  0.1 174204  3236 ?        S    Jul09  10:26 /usr/sbin/zabbix_agentd: listener #1 [waiting for connection]
zabbix    8798  0.0  0.1 174204  3248 ?        S    Jul09  10:24 /usr/sbin/zabbix_agentd: listener #2 [waiting for connection]
zabbix    8799  0.0  0.1 174200  3252 ?        S    Jul09  10:27 /usr/sbin/zabbix_agentd: listener #3 [processing request]
zabbix    8800  0.0  0.0  78768  1788 ?        S    Jul09   9:25 /usr/sbin/zabbix_agentd: active checks #1 [idle 1 sec]
```

## netstat

概要形式（性能统计信息） + 快照（路由信息）

* 确认通信量、套接字列表、路由信息
  * -a
    * 瞬间的套接字列表（快照）
  * -r
    * 瞬间的路由信息（快照）
  * -i
    * 各个接口统计信息（概要）

```
[jump@8-114-show ~]$ netstat -a
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:http            0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:https           0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:zabbix-agent    0.0.0.0:*               LISTEN
tcp        0      0 8-114-show:ssh          10.0.20.49:45346        ESTABLISHED
tcp        1      0 8-114-show:zabbix-agent 118.25.127.14:40636     CLOSE_WAIT
tcp        0      0 8-114-show:52986        169.254.0.4:http        TIME_WAIT
Active UNIX domain sockets (servers and established)
Proto RefCnt Flags       Type       State         I-Node   Path
unix  3      [ ]         STREAM     CONNECTED     364329287 /usr/local/yd.socket.client
unix  2      [ ACC ]     STREAM     LISTENING     19212    /var/run/acpid.socket
unix  2      [ ACC ]     STREAM     LISTENING     189896796 /run/docker/libnetwork/b3e693fb1d1347091a1693b6c727429541a43687be4add3354371dcbbbe54f39.sock
unix  2      [ ACC ]     STREAM     LISTENING     189896547 /var/run/docker.sock


[jump@8-114-show ~]$ netstat -r
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
default         gateway         0.0.0.0         UG        0 0          0 eth0
10.0.8.0        0.0.0.0         255.255.255.0   U         0 0          0 eth0
link-local      0.0.0.0         255.255.0.0     U         0 0          0 eth0
172.17.0.0      0.0.0.0         255.255.0.0     U         0 0          0 docker0

[jump@8-114-show ~]$ netstat -i
Kernel Interface table
Iface             MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
docker0          1500       10      0      0 0             8      0      0      0 BMU
eth0             1500 440713076      0      0 0      363582041      0      0      0 BMRU
lo              65536   355131      0      0 0        355131      0      0      0 LRU
```



## iostat

* 概要
* 磁盘使用率
  * -tx (t时间，x详细信息)
    * 响应时间和各种队列的长度

```
[jump@8-114-show ~]$ iostat
Linux 3.10.0-957.el7.x86_64 (8-114-show) 	11/08/2020 	_x86_64_	(1 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.50    0.00    0.48    0.09    0.00   98.93

Device:            tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn
vda               1.91         2.18        15.36   91403133  643388520
scd0              0.00         0.00         0.00      38946          0

cpu属性值说明：

%user：CPU处在用户模式下的时间百分比。

%nice：CPU处在带NICE值的用户模式下的时间百分比。

%system：CPU处在系统模式下的时间百分比。

%iowait：CPU等待输入输出完成时间的百分比。

%steal：管理程序维护另一个虚拟处理器时，虚拟CPU的无意识等待时间百分比。

%idle：CPU空闲时间百分比。

备注：

如果%iowait的值过高，表示硬盘存在I/O瓶颈

如果%idle值高，表示CPU较空闲

如果%idle值高但系统响应慢时，可能是CPU等待分配内存，应加大内存容量。

如果%idle值持续低于10，表明CPU处理能力相对较低，系统中最需要解决的资源是CPU。

cpu属性值说明:

tps：该设备每秒的传输次数

kB_read/s：每秒从设备（drive expressed）读取的数据量；

kB_wrtn/s：每秒向设备（drive expressed）写入的数据量；

kB_read：  读取的总数据量；

kB_wrtn：写入的总数量数据量；




[jump@8-114-show ~]$ iostat -tx 3 2
Linux 3.10.0-957.el7.x86_64 (8-114-show) 	11/08/2020 	_x86_64_	(1 CPU)

11/08/2020 08:31:45 PM
avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.50    0.00    0.48    0.09    0.00   98.93

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
vda               0.00     1.59    0.11    1.80     2.18    15.36    18.33     0.00    2.49    4.32    2.38   0.48   0.09
scd0              0.00     0.00    0.00    0.00     0.00     0.00    64.69     0.00    0.39    0.39    0.00   0.29   0.00

11/08/2020 08:31:48 PM
avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.67    0.00    1.01    0.34    0.00   97.99

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
vda               0.00     2.35    0.00    3.02     0.00    25.50    16.89     0.00    0.89    0.00    0.89   0.44   0.13
scd0              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00

rrqm/s：  每秒进行 merge 的读操作数目.即 delta(rmerge)/s

wrqm/s： 每秒进行 merge 的写操作数目.即 delta(wmerge)/s

%util： 一秒中有百分之多少的时间用于 I/O，磁盘使用率

如果%util接近100%，说明产生的I/O请求太多，I/O系统已经满负荷

   idle小于70% IO压力就较大了，一般读取速度有较多的wait。
```

## top

* 快照

```
[jump@8-114-show ~]$ top
top - 20:37:52 up 486 days,  3:49,  1 user,  load average: 0.00, 0.01, 0.05
Tasks:  83 total,   1 running,  82 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1882220 total,   147296 free,   261576 used,  1473348 buff/cache
KiB Swap:        0 total,        0 free,        0 used.  1414716 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
    1 root      20   0  125480   3544   2168 S  0.0  0.2  58:17.84 systemd
    2 root      20   0       0      0      0 S  0.0  0.0   0:42.97 kthreadd
    3 root      20   0       0      0      0 S  0.0  0.0  17:32.92 ksoftirqd/0
    5 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kworker/0:0H
    7 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 migration/0
    8 root      20   0       0      0      0 S  0.0  0.0   0:00.00 rcu_bh
    9 root      20   0       0      0      0 S  0.0  0.0  57:43.80 rcu_sched
```

## 数据包转储（wireshaark,tcpdump)

* 事件记录

## pstack

* 快照

## strack

* 事件记录

## profiler

* 概要

