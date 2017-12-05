---
layout: post
title:  "Service Account"
date:   2017-11-15
tag: kubernetes 基础
---

# Service Account

### 介绍
* ServiceAccount 用于对 apiserver 访问权限的控制
* kubectl 默认以 admin 用户来访问 apiserver
* pod 里面的进程访问 apiserver 默认以 default 用户 
![](/img/2017-12-01-Service Account/Image1.jpg)
* 每一个 namespace 都有一个名为 "default " 的 Service Account
![](/img/2017-12-01-Service Account/Image2.jpg)
### 创建 Service Account
![](/img/2017-12-01-Service Account/Image3.jpg)
### automountServiceAccountToken
* Service Account 默认会自动关联一个 token
* 可以设置 sa.automountServiceAccountToken=false 让 Service Account 不自动关联 token
* 也可以设置 pod.spec.automountServiceAccountToken=false 让 pod 不自动关联 token
![](/img/2017-12-01-Service Account/Image4.jpg)
### 手动创建 token 并关联到 Service Account
![](/img/2017-12-01-Service Account/Image5.jpg)