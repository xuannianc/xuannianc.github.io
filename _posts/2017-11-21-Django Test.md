---
layout: post
title:  "Testing in Django"
date:   2017-11-21
tag: django
---
# Writing tests
### tests 写在哪里？
* django-admin startapp 会在每一个 app 目录下创建一个 tests.py
* django-admin test 会查找当前目录及子目录下以 test 开头的文件中所有 unittest.TestCase 的子类
* 如果有非常多的 TestCase,写在一个 tests.py 中显得非常臃肿。可以创建一个 tests 包，在下面创建 test\_models.py,test\_views.py 和 test\_forms.py 等文件

### tests 怎么写？
* 通常我们为每一个 Model 或 View 写一个类继承自 django.test.TestCase,django.test.TestCase 继承自 unittest.TestCase.

# Running tests
### python manage.py test
* `python manage.py test am_master` 运行 am_master 目录及子目录下匹配 test*.py 的文件中的所有 unittest.TestCase 子类下的所有 test 开头的方法
* `python manage.py test am_master.tests` 运行 am_master 目录下 tests.py 模块的所有 unittest.TestCase 子类下的所有 test 开头的方法
* `python manage.py test am_master.tests.TestViewsCase` 运行 am\_master 目录下的 tests 模块的 TestViewsCase 类下的所有 test 开头的方法
* `python manage.py test am_master.tests.TestViewsCase.test_install_addons` 运行 am\_master 目录下的 tests 模块的 TestViewsCase 类下的 test\_install\_addons 方法
* 可以通过 -p(--pattern) 指定文件名匹配规则，默认为 test\*.py.如：
  `python manage.py test --pattern="tests_*.py"`
* 终止 test
	* Ctrl+C 会等执行完正在运行的 test 方法再退出。test runner 再退出的过程中会报告有关已经运行了的 test 的结果并且删除 test databases.
	* 再次 Ctrl+C,test runner 会立刻退出，不对已经运行的 test 进行汇报也不会删除 test databases.  

### test databases
* test runnner 会在运行 test 方法之前创建 test databases,为 settings.py 中定义的 DATABASES 的中每一个 database 创建一个对应的副本.
	* 副本的默认的名称规则是 test_NAME,NAME 是 settings.py 中的 DATABASES 中 database 的 NAME.
	* 副本的名称可以在 DATABASES.xxx.TEST.NAME 指定，具体参见 [ TEST Dict ](https://docs.djangoproject.com/en/1.10/ref/settings/#test)
	* 副本是由 DATABASES.xxx.USER 来创建的
* 当所有 test 方法运行结束之后默认会删除这些 test databases,但可以在运行时指定 --keepdb 来保留这些 test databases,***运行时的数据不会保留***

### test order
*	默认 TestCase 是按照如下顺序被执行的，可以在运行时指定 --reverse 来按照相反顺序运行
	1.  django.test.TestCase 的子类
	2.  django-based 的一些类，如 SimpleTestCase
	3.  unittest.TestCase 的子类

```
class TestOrderCase2(unittest.TestCase):
    def test_order(self):
        print('------unittest.TestCase')
        self.assertEquals(1, 1)


class TestOrderCase1(SimpleTestCase):
    def test_order(self):
        print('------django.test.SimpleTestCase')
        self.assertEquals(1, 1)


class TestOrderCase(TestCase):
    def test_order(self):
        print('------django.test.TestCase')
        self.assertEquals(1, 1)

$ python manage.py test experiment
Creating test database for alias 'default'...
------django.test.TestCase
.------django.test.SimpleTestCase
.------unittest.TestCase
.
----------------------------------------------------------------------
Ran 3 tests in 0.008s

OK
Destroying test database for alias 'default'...

```
### 并行运行
* 如果 tests 是相互独立的，可以使用 test --parallel 并行执行

# Testing tools
### the test client
* test client 的作用
	* 发送 GET 和 POST 请求到某个 URL 然后检查 response,如 header,content,status code
* test client 和 Selenium 的 区别
	* test client 
* 创建 django.test.Client(enforce\_csrf\_checks=False,\*\*defaults) 
	*  enforce\_csrf\_checks=True 用来测试 CSRF 保护的 view
	*  **defaults 可以用来指定一些默认的 Header,如`c = Client(HTTP_USER_AGENT='Mozilla/5.0')`
* request
	* get(path, data=None, follow=False, secure=False, **extra)
	
		*  **extra 用来指定 Header,优先级比 Client 中设置的高，参数名参照 [CGI标准](http://www.w3.org/CGI/)
		*  follow=True 会跟随重定向发送请求，response 的 redirect_chain 属性可以获取 redirect 的信息
		
		```
		>>> response = c.get('/redirect_me/', follow=True)
		>>> response.redirect_chain
		[('http://testserver/next/', 302), ('http://testserver/final/', 302)]
		```
		*  secure=True 发送 HTTPS 请求
		
		```
		>>> c = Client()
		>>> c.get('/customers/details/', {'name': 'fred', 'age': 7})
		>>> c.get('/customers/details/?name=fred&age=7')
	
		相当于发送请求 /customers/details/?name=fred&age=7
		```
	* post(path, data=None, content_type=MULTIPART\_CONTENT, follow=False, secure=False, **extra)
		* content_type 默认是 multipart/form-data
		* 传递 select 使用的数据`{'choices': ('a', 'b', 'd')}`
		* 传递文件
		
		```
		>>> c = Client()
		>>> with open('wishlist.doc') as fp:
		...     c.post('/customers/wishes/', {'name': 'fred', 'attachment': fp})
		```
		* post 的 path 如果后面跟参数可以通过` request.GET `获取
		
* reponse (和 HttpResponse 相比多了一些数据用来验证)
	* client 发送请求的 client
	* content 响应的 body,类型是 bytestring
	* json() 如果响应的 content-type 是 application/json，可以用该方法获取响应的内容
	* status_code 响应码  