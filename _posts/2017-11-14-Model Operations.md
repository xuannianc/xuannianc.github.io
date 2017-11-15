---
layout: post
title:  "Model Operations"
date:   2017-11-14
tag: django
---

```
from django.db import models

# Create your models here.


class Blog(models.Model):
    name = models.CharField(max_length=100)
    tagline = models.TextField()

    def __str__(self):  # __unicode__ on Python 2
        return self.name


class Author(models.Model):
    name = models.CharField(max_length=200)
    email = models.EmailField()

    def __str__(self):  # __unicode__ on Python 2
        return self.name


class Entry(models.Model):
    blog = models.ForeignKey(Blog)
    headline = models.CharField(max_length=255)
    body_text = models.TextField()
    pub_date = models.DateField()
    mod_date = models.DateField()
    authors = models.ManyToManyField(Author)
    n_comments = models.IntegerField()
    n_pingbacks = models.IntegerField()
    rating = models.IntegerField()

    def __str__(self):  # __unicode__ on Python 2
        return self.headline

```
# 1.  创建对象
### 方法一：使用 Model 的 save() 方法
* 创建单个独立的对象  Blog

```python
blog = Blog(name='django models', tagline='things about models such as field types,field options...')
blog.save()
```
* 创建多(Entry)对一(Blog)关系的对象，被关联的对象必须已经保存到数据库,否则会报错

```python
blog = Blog(name='django models', tagline='things about models such as field types,field options...')
entry = Entry(blog=blog,
              headline='model field types',
              body_text='sfjennvdjjlwefeiosnfzvjlf')
entry.save()
```
***ValueError: save() prohibited to prevent data loss due to unsaved related object 'blog'.***  
blog 应该先保存再创建 entry,如下：

```poython
blog = Blog(name='django models', tagline='things about models such as field types,field options...')
blog.save() # 先 save 
entry = Entry(blog=blog,
              headline='model field types',
              body_text='sfjennvdjjlwefeiosnfzvjlf')
entry.save()
```
* 创建多(Entry)对多(Author)关系的对象时，关联双方的对象都必须先保存到数据库再建立关联关系

```python
blog = Blog(name='django models', tagline='things about models such as field types,field options...')
blog.save()
author1 = Author(name='znchen', email="znchen@gmail.com")
author2 = Author(name='xnchen', email="xnchen@gmail.com")
author1.save()
author2.save()
entry = Entry(blog=blog,
              headline='model field types',
              body_text='sfjennvdjjlwefeiosnfzvjlf',
              authors=[author1,author2]) # 直接指定
entry.save()
```
***ValueError: "<Entry: model field types>" needs to have a value for field "entry" before this many-to-many relationship can be used.***  
应先创建 entry,创建成功之后再建立关联关系  

```python
blog = Blog.objects.get(name='django models')
author1 = Author.objects.get(name='znchen')
author2 = Author.objects.get(name='xnchen')
entry = Entry(blog=blog,
              headline='model field types',
              body_text='sfjennvdjjlwefeiosnfzvjlf')
entry.save() # 先 save 再建立关联关系
entry.authors.add(author1) # 建立关联关系
entry.authors.add(author2)
entry.save()
```
### 方法二：使用 Manager 的 create() 方法

* 创建单个独立的对象  Blog

```python
blog = Blog.objects.create(name='django models',
                           tagline='things about models such as field types,field options...')
print(blog.name)
```
* 创建多(Entry)对一(Blog)关系的对象，被关联的对象必须已经保存到数据库,否则会报错

```python
blog = Blog(name='django models',
                           tagline='things about models such as field types,field options...')
entry = Entry.objects.create(blog=blog,
                             headline='model field types',
                             body_text='sfjennvdjjlwefeiosnfzvjlf')
print(entry.headline)
```
***ValueError: save() prohibited to prevent data loss due to unsaved related object 'blog'.***   
应该先把 blog 保存到数据库

```python
blog = Blog.objects.create(name='django models',
                           tagline='things about models such as field types,field options...')
entry = Entry.objects.create(blog=blog,
                             headline='model field types',
                             body_text='sfjennvdjjlwefeiosnfzvjlf')
print(entry.headline)
```
* 创建多(Entry)对多(Author)关系的对象时，关联双方的对象都必须先保存到数据库再建立关联关系  

```python
blog = Blog.objects.get(name='django models')
author2 = Author.objects.get(name='xnchen')
author1 = Author.objects.get(name='znchen')
entry = Entry.objects.create(blog=blog,
                             headline='model field types',
                             body_text='sfjennvdjjlwefeiosnfzvjlf',
                             authors=[author1, author2])
print(entry.headline)
```
应先创建 entry,创建成功之后再建立关联关系  

```python
blog = Blog.objects.get(name='django models')
author2 = Author.objects.get(name='xnchen')
author1 = Author.objects.get(name='znchen')
entry = Entry.objects.create(blog=blog,
                             headline='model field types',
                             body_text='sfjennvdjjlwefeiosnfzvjlf')
entry.authors.add(author1)
entry.authors.add(author2)
entry.save()
print(entry.headline)
```
# 2.  删除对象
### delete() 方法
* 调用 QuerySet 的 delete() 方法，***不会间接调用 model 对象的 delete() 方法***
	* 返回值是一个 tuple,第一个元素是一共删除的对象的个数，第二个元素是一个 dict,统计分别删除了各个类型的多少对象

* 调用 model 对象的 delete() 方法

	```
	>>> entry=Entry.objects.get(pk=4)
	>>> entry
	<Entry: model field types>
	>>> entry.delete()
	(2, {'experiment.Entry_authors': 1, 'experiment.Entry': 1})
	>>> entry=Entry.objects.get(pk=4)
	Traceback (most recent call last):
	  File "<console>", line 1, in <module>
	  File "/Users/znchen/python/amm/lib/python3.6/site-packages/django/db/models/manager.py", line 85, in manager_method
	    return getattr(self.get_queryset(), name)(*args, **kwargs)
	  File "/Users/znchen/python/amm/lib/python3.6/site-packages/django/db/models/query.py", line 385, in get
	    self.model._meta.object_name
	experiment.models.DoesNotExist: Entry matching query does not exist.
	```
* QuerySet 的 delete() 方法是唯一没有暴露在 Manager 的方法，防止误删除

	```
	>>> Entry.objects.delete()
	Traceback (most recent call last):
	  File "<console>", line 1, in <module>
	AttributeError: 'Manager' object has no attribute 'delete'
	```
	
### 关联对象的删除
* ForeignKey 字段的 on_delete 参数可以控制删除策略
	* 默认值为 CASCADE,删除 Blog 对象，会同时删除关联的 Entry 对象，同时也会删除 Entry_Author 中关联的条目   

	```
	>>> Blog.objects.all().delete()
	(4, {'experiment.Entry_authors': 2, 'experiment.Entry': 1, 'experiment.Blog': 1})
	```
	* on_delete 其他值可以参见 [ForeignKey](https://docs.djangoproject.com/en/1.10/ref/models/fields/#arguments)
* 多对多的删除，删除 Entry 或者 Author 会删除第三张关联关系表的条目
* OneToOne 字段也有 on_delete 参数

# 3.  更新对象
### 更新某个对象的字段
* 更新普通字段

```python
blog = Blog.objects.get(name='django models')
blog.name = 'Django models'
blog.save()
```
* 更新 foreign key 字段

```python
blog = Blog.objects.create(name='django forms',
                           tagline='things about form fields such as field types,field options...')
blog.save()
entry = Entry.objects.get(headline='model field types')
entry.blog = blog
entry.save()
```

* 更新 manytomany 字段

```python
author1 = Author.objects.get(name='znchen')
entry = Entry.objects.get(headline='model field types')
entry.authors.remove(author1)
entry.save()
```
### 调用 QuerySet 的 update() 同时更新多个对象
* update() 返回被更新的对象数

```
>>> entry=Entry.objects.get(pk=9)
>>> entry.n_pingbacks
10
>>> entry=Entry.objects.get(pk=8)
>>> entry.n_pingbacks
0
>>> Entry.objects.filter(pk__in=[8,9]).update(n_pingbacks=5)
2
>>> entry=Entry.objects.get(pk=8)
>>> entry.n_pingbacks
5
```
* QuerySet 的更新不会更新 auto\_now 属性的字段，也不会间接调用 model 的 save() 方法，因此不会触发 pre\_save 和 post\_save 等 signals.

```
>>> entry=Entry.objects.get(pk=5)
>>> entry.mod_date
datetime.date(2017, 11, 16)
>>> Entry.objects.filter(pk__in=[5]).update(body_text='dfjsafnvjdidjfei')
1
>>> entry=Entry.objects.get(pk=5)
>>> entry.mod_date
datetime.date(2017, 11, 16)
```
* update 语句等式右侧可以使用 F() 对象，常用来进行字段的自增，但是这里的 F() 不能引用关联对象的属性

```
>>> entry=Entry.objects.get(pk=5)
>>> entry.n_pingbacks
10
>>> from django.db.models import F
>>> Entry.objects.filter(pk__in=[5]).update(n_pingbacks=F('n_pingbacks')+1)
1
>>> entry=Entry.objects.get(pk=5)
>>> entry.n_pingbacks
11
>>> Entry.objects.filter(pk__in=[5]).update(body_text=F('blog__name'))
Traceback (most recent call last):
  File "<console>", line 1, in <module>
  File "/Users/znchen/python/amm/lib/python3.6/site-packages/django/db/models/query.py", line 637, in update
    rows = query.get_compiler(self.db).execute_sql(CURSOR)
  File "/Users/znchen/python/amm/lib/python3.6/site-packages/django/db/models/sql/compiler.py", line 1148, in execute_sql
    cursor = super(SQLUpdateCompiler, self).execute_sql(result_type)
  File "/Users/znchen/python/amm/lib/python3.6/site-packages/django/db/models/sql/compiler.py", line 824, in execute_sql
    sql, params = self.as_sql()
  File "/Users/znchen/python/amm/lib/python3.6/site-packages/django/db/models/sql/compiler.py", line 1096, in as_sql
    val = val.resolve_expression(self.query, allow_joins=False, for_save=True)
  File "/Users/znchen/python/amm/lib/python3.6/site-packages/django/db/models/expressions.py", line 463, in resolve_expression
    return query.resolve_ref(self.name, allow_joins, reuse, summarize)
  File "/Users/znchen/python/amm/lib/python3.6/site-packages/django/db/models/sql/query.py", line 1448, in resolve_ref
    raise FieldError("Joined field references are not permitted in this query")
django.core.exceptions.FieldError: Joined field references are not permitted in this query
```	
# 4.  比较对象  
使用 == 比较，底层比较的 primary key.如果 primary key 相同,那么两个对象相同。

```
>>> entry1=Entry.objects.get(Q(headline__startswith='form'),body_text__contains='feios')
>>> entry1.id
5
>>> entry2=Entry.objects.get(Q(headline__startswith='form'),Q(body_text__contains='feios'))
>>> entry2.id
5
>>> entry1==entry2
True
```
# 5.  拷贝对象
* 单个对象的拷贝（如 Blog)

	```
	>>> blog=Blog.objects.get(pk=7)
	>>> blog.pk=None
	>>> blog.save() # 创建之后的 Blog 对象的 id 为 8
	>>> blog=Blog.objects.get(pk=8)
	>>> blog
	<Blog: django forms>
	>>> blog=Blog.objects.get(pk=7)
	>>> blog
	<Blog: django forms>
	```
* 包含 ForeignKey 字段的对象的拷贝（如 Entry,新的 Entry 也会和原来的 Entry 关联相同的 Blog)

	```
	>>> entry=Entry.objects.get(pk=6)
	>>> entry.blog_id
	7
	>>> entry.pk=None
	>>> entry.save()
	>>> new_entry=Entry.objects.order_by('-pk')[0]
	>>> new_entry.id
	8
	>>> new_entry.blog_id
	7
	```

* 包含 ManyToMany 字段的对象的拷贝（如 Entry,新的 Entry 不会和原来的 Entry 关联相同的 Author，需要重新建立关联关系）

	```
	>>> entry=Entry.objects.get(pk=5)
	>>> entry.authors.all()
	<QuerySet [<Author: znchen>]>
	>>> entry.pk=None
	>>> entry.save()
	>>> new_entry=Entry.objects.order_by("-pk")[0]
	>>> new_entry.pk
	9
	>>> new_entry.authors.all()
	<QuerySet []>
	```
	
# 6.  获取关联对象
### 多对一 ForeignKey(如 Entry->Blog)
* 通过 Entry 对象获取 Blog 对象，第一次获取会去查询数据库。而 select_related() QuerySet 在访问关联对象之前就已经查询数据库

```
>>> e = Entry.objects.get(id=2)
>>> print(e.blog)  # Hits the database to retrieve the <Blog: django forms>
>>> print(e.blog)  # Doesn't hit the database; uses <Blog: django forms>
>>> e = Entry.objects.select_related().get(id=2)
>>> print(e.blog)  # Doesn't hit the database; uses <Blog: django forms>
>>> print(e.blog)  # Doesn't hit the database; uses <Blog: django forms>
```
* 通过 Blog 对象获取 Entry 对象(通过 entry_set 这个 Manager 获取，Manager 的名字可以通过 ForeignKey 的 related_name 参数设置）

```
>>> blog=Blog.objects.get(pk=7)
>>> blog.entry_set.all()
<QuerySet [<Entry: form field types>, <Entry: form field options>, <Entry: form field options>, <Entry: form field types>]>
>>> blog.entry_set.count()
4
```

### 多对多 ManyToMany(如 Entry<>Author)
