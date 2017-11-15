---
layout: post
title:  "Model Manager"
date:   2017-11-15
tag: django
---

# default manager
每一个 model 都至少有一个 manager,默认为 objects.可以直接通过 model 的类名获取，**但是不能通过 model 的实例获取**。

```
>>> blog=Blog.objects.get(name='Django models')
>>> Blog.objects
<django.db.models.manager.Manager object at 0x1089a5160>
>>> blog.objects
Traceback (most recent call last):
  File "<console>", line 1, in <module>
  File "/Users/znchen/python/amm/lib/python3.6/site-packages/django/db/models/manager.py", line 186, in __get__
    raise AttributeError("Manager isn't accessible via %s instances" % cls.__name__)
AttributeError: Manager isn't accessible via Blog instances
```

# related mamager