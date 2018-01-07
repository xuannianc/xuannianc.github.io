---
layout: post
title:  "vyos Firewall"
date:   2017-12-31
tag: vyos
---

# Firewall
### 概念
* Filter
    * in 过滤所有进入到该接口将被转发的数据包，类似于 iptables 的 FORWARD 的一部分
    * out 过滤所有从该接口发出的数据包(这部分类似于 iptables 的 OUTPUT)，不管是自己发送的还是转发的(这部分类似于 iptables FORWARD 的一部分)
    * local 过滤所有目标地址为本接口的数据包，类似于 iptables 的 INPUT
* Group (分组)
    * ip address group
    * port group
    ```
    set firewall group port-group PORT-TCP-SERVER1 port 80
    set firewall group port-group PORT-TCP-SERVER1 port 443
    set firewall group port-group PORT-TCP-SERVER1 port 5000-5010
    ```
    * network group
    ```
    set firewall group network-group NET-INSIDE network 192.168.0.0/24
    set firewall group network-group NET-INSIDE network 192.168.1.0/24
    ```
* Rule-Sets (规则集合)
    * 创建 rule-set
    ```
    set firewall name INSIDE-OUT default-action drop
    set firewall name INSIDE-OUT rule 1010 action accept
    set firewall name INSIDE-OUT rule 1010 state established enable
    set firewall name INSIDE-OUT rule 1010 state related enable
    set firewall name INSIDE-OUT rule 1020 action drop
    set firewall name INSIDE-OUT rule 1020 state invalid enable
    ```
    * 把 rule-set 应用到某个 interface
    ```
    set interfaces ethernet eth1 firewall out name INSIDE-OUT
    ```
* Zone-based firewall
    * 示例一
        * 网络拓扑
        [Zone-based firewall](http://www.brocade.com/content/html/en/vrouter5600/42r1/vrouter-42r1-firewall/GUID-6AA5000B-0ED2-431C-B305-EFD6EFD925AE.html)
        * 配置命令
        [Filtering traffic between the transit zones](http://www.brocade.com/content/html/en/vrouter5600/35r6/vrouter-35r6-firewall/GUID-1804184C-0047-4940-B2A7-B2B1676EB115.html)
### 参考
* [Configuring an interface-based firewall on the Vyatta network appliance](https://support.rackspace.com/how-to/configuring-interface-based-firewall-on-the-vyatta-network-appliance/)