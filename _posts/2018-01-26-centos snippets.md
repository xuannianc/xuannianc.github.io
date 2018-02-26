---
layout: post
title:  "centos snippets"
date:   2018-01-26
tag: centos
---

### 网络相关
* 查看网段可用 IP
    * `sudo nmap -v -sn -n 192.168.1.0/24 -oG - | awk '/Status: Down/{print $2}'`
    * 参考 [Nmap: find free IPs from the range](https://serverfault.com/questions/586714/nmap-find-free-ips-from-the-range)

### 进程、线程相关
* 查看僵尸进程
    * 使用 top 查看有多少个僵尸进程
        ![](/img/2018-01-26-centos snippets/Image1.png)
    * 使用 ps 查看哪些是僵尸进程 `ps -e -o stat,ppid,pid,cmd | grep -e '^[Zz]'`
* 查看线程个数
    * 查看当前系统允许的最大进程数，最大线程数，单独用户最大进程数
        ```
        cat /proc/sys/kernel/pid_max
        cat /proc/sys/kernel/thread_max
        ulimit -u
        ```
    * 查看当前运行了多少线程 `top -H`
    * 查看当前运行的所有线程 `ps -eLf`
    * 查看某个进程包含的线程个数 `pstree -p <pid> | wc -l`