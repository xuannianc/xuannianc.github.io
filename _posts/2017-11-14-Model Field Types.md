---
layout: post
title:  "Model Field Types"
date:   2017-11-14
tag: django
---

##1.  CharField  
### 对应于 mysql 中的 varchar,必须指定 max_length 属性
##2.  AutoField
### 自动生成 id 字段作为 PRIMARY_KEY
* 如果用户创建的 model 所有的 Field 都没有指定 `primary_key=True`,那么 django 会自动创建`id = models.AutoField(primary_key=True)`  
* 如果用户创建了 id 字段,那么必须指定该字段 `primary_key=True`.否则会报下面的错：  
**(models.E004) 'id' can only be used as a field name if the field also sets 'primary_key=True'**.

### 对应于 mysql 中的 int,值的范围为 [-2^31,2^31)
如果想要更大范围的值可以考虑使用 **BigAutoField**  ,**BigAutoField** 和 **AutoField** 的区别就是范围不同，**BigAutoField** 的范围为 [-2^63,2^63)  
3. 
