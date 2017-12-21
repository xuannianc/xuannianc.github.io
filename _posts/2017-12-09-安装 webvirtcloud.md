---
layout: post
title:  "安装 webvirtcloud"
date:   2017-12-08
tag: kvm
---

# webvirtcloud
#### vmware 创建虚拟机设置其 CPU 支持虚拟

![](/img/2017-12-09-安装 webvirtcloud/Image2.jpg)

#### 安装软件包
* 安装 nginx,supervisor,libxml2,libvirt
    ```
    yum install gcc glibc epel-release nginx supervisor libvirt-devel libxml2-devel python-devel openssl-devel mysql-devel -y
    ```
* 创建虚拟 python 环境，克隆 git 仓库
```
pip install virtualenv
virtualenv kvmmgr_venv
cd kvmmgr_venv
git clone git@github.com:VanWade/webvirtcloud.git
```
* 安装 requirements.txt
    ```
    cd webvirtcloud/conf
    pip install -r requirements.txt
    Django==1.8.11
    libvirt-python==3.10.0
    libxml2-python==2.9.1
    MySQL-python==1.2.5
    numpy==1.13.3
    uWSGI==2.0.15
    websockify==0.8.0
    ```

#### 配置
* 配置 uwsgi  
  修改 kvmmgr_venv/webvirtcloud/conf/uwsgi/kvmmgr\_uwsgi.ini
* 配置 supervisor  
    ```
    cp kvmmgr_venv/webvirtcloud/conf/supervisor/kvmmgr_supervisor.ini /etc/supervisord.d/
    ```
* 配置 nginx  
    ```
    cp kvmmgr_venv/webvirtcloud/conf/nginx/kvmmgr_nginx.conf /etc/nginx/conf.d/
    ```

#### 启动

* 关闭防火墙
```
setenforce 0
systemctl stop firewalld
```
* 启动 supervisord
```
systemctl start supervisord
supervisorctl status 查看是否启动成功
```
![](/img/2017-12-09-安装 webvirtcloud/Image3.jpg)
* 启动 nginx
```
systemctl start nginx
```