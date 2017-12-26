---
layout: post
title:  "blocking,non-blocking,synchronous and asynchronous"
date:   2017-12-26
tag: 概念
---
# blocking,non-blocking,synchronous and asynchronous
### asynchronous vs synchronous
* synchronous
    * 当一个函数调用的调用者不能继续执行直到函数返回或者抛出异常，这样的函数调用就是同步的。
    * 同步函数调用可以通过 blocking 来实现，也可以是一个 CPU 密集型的操作
* asynchronous 
    * 当一个函数调用之后不需要等待函数返回可立即执行之后的代码，当被调用的函数执行完成之后主程序会收到信号（可以通过回调函数实现）
### blocking vs non-blocking
* blocking 和 non-blocking 的区别类似于 synchronous 和 asynchronous 的区别
### asynchronous 和 non-blocking
* non-blocking io 指的是 io 操作的接口会立刻返回
    * 如果 io 操作本需要 block，那么接口会返回错误
        如当调用没有数据的 socket 的 recv 函数，本该 blocking.如果设置了 socket 为 non-blocking，那么会立刻返回 EAGAIN or EWOULDBLOCK 错误码
        如当调用发送缓存已满的 socket 的 send 函数，也会立刻返回相应的错误码
* asynchronous 
    * 和 non-blocking 相同之处就是调用后会立刻返回
    * 不同之处在调用时注册回调函数，当 io 操作结束会调用该回调函数（回调函数只是异步的一种实现方式）