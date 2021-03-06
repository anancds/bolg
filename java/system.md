---
title: 一些系统相关问题的定位
date: 2016-09-02 09:32:59
tags: System
---

# 系统相关

## I/O性能

* 查看I/O：

      iostat -xz 1

## 网络监测

* 查看网络sar(System Activity Reporter)

      sar –n DEV 1

如果“rxkB/s”和“txkB/s”两种相加超过100MB的话，说明网络已经接近饱和了。

## 内存相关

* 查看内存占用情况：

      free -g

## CPU

* 查看CPU负载：

      mpstat -P ALL 2

* 查看磁盘IO情况及CPU负载：

      vmstat 2

## 测试上下文切换次数

      vmstat 1

其中CS(Content Switch)表示上下文切换次数。如果是java程序，那么可以dump线程的一些信息来查看上下午切换。

      jstat 777 > /home/cds
      grep java.lang.Thread.State /home/cds | awk '{print $2$3$4$5}' | sort | uniq -c



## 进程

* 查看进程树：

      ps axf

* 查看进程详细信息：

      ps aux

* 查看进程：

      top

## 文件相关

* 查看系统允许打开的最大文件数：

      cat /proc/sys/fs/file-max

* 查看每个用户允许打开的最大文件数：ulimit -a，当然如果要修改这个值，需要修改/etc/security/limits.conf这个配置文件。

* 查出每个进程占用的文件数量：

      lsof -p 12 | wc -l

* 总共打开文件数：

      lsof -n | wc -l

* 显示使用fd为4的进程：

      lsof -d 4

* 显示开启文件abc.txt的进程：

      lsof abc.txt

* 显示使用22端口的进程：

      lsof -i:22

* 显示使用tcp协议的22端口的进程：

      lsof -i tcp:22

* 网络相关的文件数：

      lsof -n -i  | wc -l

## 网络连接相关

* 网络连接数：

      netstat -ant | wc -l

* 端口号是80的网络连接数：

      netstat -ant | grep ":80" | wc -l

* 查看端口号是2181的各种状态的连接数：

      netstat -ant | grep ":2181" | awk '{print $6}' | sort | uniq -c  | sort -nr

* 所有的网络连接数，按状态排序：

      netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print S[a],a}' | sort -nr

* 所有打开文件数，按进程PID排序：

      lsof -n | awk '{print $2}' | sort -n | uniq -c | sort -nr | more

## 查看系统负载

      uptime

这里系统的平均负载的定义是：* 在特定时间间隔内运行的平均进程数 *。如果一个进程满足以下条件则其就会位于运行队列中。

* 它没有在等待I/O操作结果。
* 它没有主动进入等待状态(也就是没有调用wait)。
* 没有被停止。

uptime描述的是系统最近1分钟、5分钟、15分钟的系统负载。

## 输出linux内核的环形缓存区信息(kernel ring buffer)

* 列出所有加载到内核中的驱动

      dmesg | more
      dmesg | less
      dmesg | tail

* 列出检测到的硬件，比如硬盘

      dmesg | grep sda

* 实时监控dmesg日志输出

      watch "dmesg | tail -20"

## 输出进程的CPU占有率

      pidstat 1
