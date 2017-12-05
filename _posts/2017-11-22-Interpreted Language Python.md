---
layout: post
title:  "Interpreted Language Python"
date:   2017-11-22
tag: python 基础
---

# 解释性语言
### 解释性语言和编译性语言的区别
* 解释性语言没有编译过程(如 R 语言)或者编译成 bytecode 再由 vm 来解释(如 python)
* 编译性语言的编译过程直接编译成机器能运行的指令而非虚拟机
* 解释性语言通常是动态类型的
	* 编译器做的工作很少，解释器缺少类型的信息所有运行起来会比较慢，能做的优化也相对较少
* 编译性语言通常是静态类型的

### python 的动态语言特性
* 定义一个 modulus 函数来求模，定义完就会编译成 code object

```
In [177]: def modulus(a, b):
     ...:     return a % b
     ...:

In [178]: dis(modulus.__code__)
  2           0 LOAD_FAST                0 (a)
              2 LOAD_FAST                1 (b)
              4 BINARY_MODULO
              6 RETURN_VALUE

In [179]: modulus(10,3)
Out[179]: 1
```
* 也可以传递字符串，但是在编译时并不知道参数的类型，bytecode 并没有改变，所以 BINARY_MODULE 必须能处理字符串

```
In [180]: modulus('hello %s','world')
Out[180]: 'hello world'
```
* 其他类型实现了__mod__方法，可以在该方法里做任何事。这些类型的对象都可以传递给 modulus 作为参数

```
In [181]: class Superise:
     ...:     def __init__(self,x):
     ...:         self.x = x
     ...:     def __mod__(self,other):
     ...:         return self.x + other.x
     ...:

In [182]: a = Superise(10)

In [183]: b = Superise(3)

In [184]: modulus(a,b)
Out[184]: 13
```
* 
* 