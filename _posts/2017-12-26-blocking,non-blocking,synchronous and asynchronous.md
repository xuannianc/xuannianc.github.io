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
    * 就 io 方面，synchronous 和 non-blocking 是一样的
* asynchronous 
    * 当一个函数调用之后不需要等待函数返回可立即执行之后的代码。但是背后其实在做一些操作，这些操作完成之后会触发调用者的某些操作
    * asynchronous 的接口风格有下面几种：
        * 回调函数作为接口参数
        * 返回一个占位符 (Future,Promise,Deferred)
        * 通过 queue
        * posix signal
### blocking vs non-blocking
* blocking 的原因可以是
    * network io
    * disk io
    * mutexes
    * cpu intensive
* non-blocking 和 blocking 的区别类似于 synchronous 和 asynchronous 的区别
### asynchronous io 和 non-blocking io
* non-blocking io 指的是 io 操作的接口会立刻返回
    * 如果 io 操作本需要 blocking，那么接口会返回错误，而不会 blocking
        如当调用没有数据的 socket 的 recv 函数，本该 blocking.如果设置了 socket 为 non-blocking，那么会立刻返回 -1 并设置 errno 为 EAGAIN 或者 EWOULDBLOCK 错误码
        如当调用发送缓存已满的 socket 的 send 函数，本该 blocking.但如果设置了 socket 为 non-blocking，那么也会返回 -1 并设置 errno 为相应的错误码
    * select,poll,epoll 都是使用的 non-blocking 
    * libev 是对 select,poll 和 epoll 的封装
* asynchronous io
    * 和 non-blocking 相同之处就是调用后会立刻返回
    * 不同之处在于当 io 操作结束会触发调用者相应的操作（如回调函数）,而 non-blocking 不会，需要再次询问
    * libuv 实现了 asynchronous io
* non-blocking 和 asynchronous 适用于有非常多的不活跃的连接