---
layout: post
title:  "Code object"
date:   2017-11-22
tag: python 高级
---

# Code object
### 介绍
* python 代码的执行分为四个阶段：分词(lexing)、解析(parsing)、编译(compiling)、解释(interpreting)
	* 分词就是把代码分成 token
	* 解析就是生成抽象语法树(AST)表示 token 之间的关系
	* 编译就是根据抽象语法树生成 code object
	* 解释就是执行这些 code object,准确的说是执行 code object 的 bytecode
* python3.6 可以通过对象的\_\_code__属性获取其 code object

```
In [164]: def test(x):
     ...:     a = 3
     ...:     return a + x
     ...:

In [165]: test.__code__
Out[165]: <code object test at 0x103156ed0, file "<ipython-input-164-7d286276c909>", line 1>
```
### code object 的属性
* co_varnames 变量的名字

```
In [168]: test.__code__.co_varnames
Out[168]: ('x', 'a')
```
* co_consts 常量的值

```
In [167]: test.__code__.co_consts
Out[167]: (None, 3)
```
* co_code 字节码

```
In [169]: test.__code__.co_code
Out[169]: b'd\x01}\x01|\x01|\x00\x17\x00S\x00'
```
### dis.dis 可以用来反编译 code object
```
In [173]: dis(test.__code__)
  2           0 LOAD_CONST               1 (3)
              2 STORE_FAST               1 (a)

  3           4 LOAD_FAST                1 (a)
              6 LOAD_FAST                0 (x)
              8 BINARY_ADD
             10 RETURN_VALUE
```
* 第一列表示源代码的行数
* 第二列表示字节的 offset
* 第三列表示字节代表的指令，[opcodepy](https://github.com/python/cpython/blob/master/Lib/opcode.py) 包含所有指令对应的字节
* 第四列表示参数在 co_consts 或者 co_varnames 的下标
* 第五列表示参数