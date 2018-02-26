---
layout: post
title:  "django errors"
date:   2018-01-26
tag: django
---

### settings 相关
* 设置 LANGUAGE_CODE = 'zh_CN' 会报错
    * site-packages/django/conf/locale 下有目录 zn_Hans
    * 但是 LANGUAGE_CODE 却要设置为 zn-Hans
    ![](/img/2018-01-31-django errors/Image1.png)

### urls 相关
    * django 1.10 开始不再支持用字符串形式写 view
        [view-must-be-a-callable](https://stackoverflow.com/questions/38744285/django-urls-error-view-must-be-a-callable-or-a-list-tuple-in-the-case-of-includ/38744286)