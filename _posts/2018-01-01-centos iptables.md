---
layout: post
title:  "iptables"
date:   2018-01-01
tag: linux 系统管理
---
### Tables
* iptables 默认有三个 tables，通过 -t 指定，默认显示 filter 表
    * filter 表用于包的过滤
    ![](/img/2018-01-01-iptables/Image1.jpg)
    * nat 表用于地址的转换
    ![](/img/2018-01-01-iptables/Image2.jpg)
    * mangle 表用于对包进行特殊处理

### Chains
* filter 表默认有三个 Chain，三者是独立的
    * INPUT 处理目的地址是本机的数据包
    * OUTPUT 处理源地址是本机的数据包
    * FORWARD 处理目的地址和源地址都不是本机的数据包

### Policy
* 每一个 Chain 都有一个 Policy。默认值是 ACCEPT，表示不满足 Chain 里面所有 rules 的包都允许通过
![](/img/2018-01-01-iptables/Image3.jpg)
* 比较安全的做法是设置 Policy 为 DROP，表示不满足 Chain 里面所有 rules 的包都丢弃
```
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT DROP
```

### Rule
* 删除
    * 删除一条记录：指定 Chain 和 Number
        * Number 通过 `iptables -L --line-numbers` 获得
        * iptables -D \<Chain> \<Number>

        ```
        [root@internal ~]# iptables -L --line-numbers
        Chain INPUT (policy ACCEPT)
        num  target     prot opt source               destination         
        1    ACCEPT     all  --  anywhere             anywhere             state RELATED,ESTABLISHED
        2    ACCEPT     icmp --  anywhere             anywhere            
        3    ACCEPT     all  --  anywhere             anywhere            
        4    ACCEPT     tcp  --  anyw
        
        here             anywhere             state NEW tcp dpt:ssh
        5    REJECT     all  --  anywhere             anywhere             reject-with icmp-host-prohibited

        Chain FORWARD (policy ACCEPT)
        num  target     prot opt source               destination         
        1    REJECT     all  --  anywhere             anywhere             reject-with icmp-host-prohibited

        Chain OUTPUT (policy ACCEPT)
        num  target     prot opt source               destination         
        [root@internal ~]# iptables -D INPUT 5
        [root@internal ~]# iptables -L --line-numbers
        Chain INPUT (policy ACCEPT)
        num  target     prot opt source               destination         
        1    ACCEPT     all  --  anywhere             anywhere             state RELATED,ESTABLISHED
        2    ACCEPT     icmp --  anywhere             anywhere            
        3    ACCEPT     all  --  anywhere             anywhere            
        4    ACCEPT     tcp  --  anywhere             anywhere             state NEW tcp dpt:ssh

        Chain FORWARD (policy ACCEPT)
        num  target     prot opt source               destination         
        1    REJECT     all  --  anywhere             anywhere             reject-with icmp-host-prohibited

        Chain OUTPUT (policy ACCEPT)
        num  target     prot opt source               destination         
        ```
    * 删除一条记录：根据 Rule 详细说明删除(-S 用来获取 rules 的详细说明，-A 改成 -D 进行删除)
    ```
    [root@internal ~]# iptables -S INPUT
    -P INPUT ACCEPT
    -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
    -A INPUT -p icmp -j ACCEPT
    -A INPUT -i lo -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
    -A INPUT -i ens33 -p tcp -m tcp --dport 80 -m state --state NEW -j ACCEPT
    [root@internal ~]# iptables -D INPUT -i ens33 -p tcp -m tcp --dport 80 -m state --state NEW -j ACCEPT
    ```
    * 删除整张 table 的所有 rules
    ```
    iptables -F
    iptables -t nat -F
    iptables -t mangle -F
    ```
    * 删除整张 table 的所有的自定义 chain
    ```
    iptables -X
    iptables -t nat -X
    iptables -t mangle -X
    ```
    
### 添加
* Append(-A)
    * 示例一：reject INPUT 接收的所有数据包
    ![](/img/2018-01-01-iptables/Image4.png)
    * 示例二：drop 源地址为某个范围的数据包
    ```
    iptables -A INPUT -m iprange --src-range 192.168.1.100-192.168.1.200 -j DROP
    ```
* Insert(-A)
    * 示例一：插入允许 SSH 连接请求和数据传输的数据包到 INPUT 和 OUTPUT 的第一行
    ![](/img/2018-01-01-iptables/Image5.png)

### 查看
* 以详细信息列出 rules
    * 列出所有 Chain 的 rules `iptables -S`
    * 列出指定 Chain 的 rules `iptables -S <chain name>`
* 以 table 为单元列出 rules
    * 列出 filter 表的所有 Chain 的 rules `iptables -L`
    * 列出指定 table 的 指定 Chain 的 rules `iptables -t <table name> -L <chain name>`
    * 列出 rule 处理的包的个数和大小 `iptables -vL`
        * 所有 rules 清零 `iptables -Z`
        * 某个 chain 的 rules 清零 `iptables -Z INPUT`
        * 某个 chain 的第几条 rule 清零 `iptables -Z INPUT <rule number>`
    * 以数字显示源地址、目标地址和端口等 `iptables -nL`

### 保存和修复
* 任意平台
```
iptables-save > /root/my.active.firewall.rules
iptables-restore < /root/my.active.firewall.rules
```
* centos/redhat 
    * `service iptables save` 规则会保存到 /etc/sysconfig/iptables
    * `service iptables restart` 会加载 /etc/sysconfig/iptables 的规则   

### 参考
* [Linux: 25 Iptables Netfilter Firewall Examples For New SysAdmins](https://www.cyberciti.biz/tips/linux-iptables-examples.html)