# 虚拟环境

## 安装虚拟环境
```shell
pip install virtualenv
or
pip3 install virtualenv
```
## 创建虚拟机环境
```shell
virtualenv 虚拟环境名称
```
## 进入虚拟环境
```shell
- win平台： 执行activate，
- *nix平台：source /path/to/virtualenv/activate
- 退出：deactivate
```
## 创建虚拟环境指定python解释器
```
- win平台： virtualenv -p \C\python.exe 虚拟环境名称
- *nix平台：virtualenv -p /usr/bin/python 虚拟环境名称
```
## virtualenvwrapper

virtualenvwrapper 这个软件包可以统一管理虚拟环境，不用单独进入到某个目录下面执行命令，如果直接使用此软件包可以不用安装virtualenv

1. 安装虚拟环境
    + win平台：pip install virtualenvwrapper-win
    + *nix平台：pip install virtualenvwrapper
2. 创建虚拟环境
```shell
   mkvirtualenv 虚拟环境名称
```
3. 查看当前有多个虚拟环境
```shell
   lsvirtualenv
```
4. 切换到某个虚拟环境
```shell
   workon 虚拟环境名称
```
5. 退出虚拟环境
```shell
   deactivate
```
6. 删除虚拟环境
```shell
   rmvirtualenv 虚拟环境
```
7. 指定python版本
```shell
   mkvirtualenv --python=/usr/local/python/bin/python 虚拟环境名称
```
8. 指定创建虚拟环境生成目录
   - *nix平台：安装完成后，在~/.bashrc写入以下内容，便于管理所有创建的虚拟环境。
        ```shell 
		export WORKON_HOME=~/Envs
		source /usr/local/bin/virtualenvwrapper.sh　
        ```
   - win平台：我的电脑->属性->高级系统设置->环境变量->系统变量中添加参数WORKON_HOME,将这个参数的值设置为需要的路径
9. 离线安装依赖包
```shell
把依赖包都下到packages文件夹里
pip download -d packages/ -r requirements.txt
pip install --no-index --find-links=packages/ -r requirements.txt
```
        
# Django

## Django 基础

### 安装Django软件包
```python
pip install django
OR
pip install Django==2.0.2安装指定版本
```

### 创建django项目
```python
django-admin startproject projectName
```

### 创建app
```python
django-admin startapp appName 
OR 
python manage.py startapp appName
```

### 创建数据库表或者修改数据库表字段
1. 创建修改的文件
```python
python manage.py makemigrations
```
2. 将生产的py文件应用到数据库
```python
python manage.py migrate
``` 

### 启动运行
```python
python manage.py runserver 
or 指定端口
python manage.py runserver 8080
or 指定IP端口
python manage.py runserver 172.0.0.1:8080
注意：
在指定ip的情况下需要在settings.py的
ALLOWED_HOSTS中配置允许访问的IP地址：ALLOWED_HOSTS = ['127.0.0.1']
```

### 清空数据库
```python
python manage.py flush
```

### 创建超级管理员
```python
python manage.py createsuperuer
修改密码
python manage.py changepassword username
```

### 导入导出数据

1. 导出app的数据
```shell
python manage.py dumpdata appname > appname.json
```
2. 导入app的数据
```shell
python manage.py loaddata appname.json
```

### 运行终端环境
```shell
python manage.py shell
```
### 数据库命令
```shell
python manage.py dbshell
```

## Django项目

### 项目结构介绍
   * manage.py: 和项目交互基本上都用这个文件，python mange.py help 查看帮忙文档
   * settings.py: 项目的设置项,所有配置相关
   * urls.py: 使用URL配置路由
   * wsgi.py: 项目与WSGI协议兼任的web服务器入口
### project 和 app的关系
   * app 是django 项目的组成部分，一个app代表项目的一个模块，所有URL请求的响应都是由app来处理。
   * Django 项目由多个app组成，一个app可以被应用到其他项目，django也可以拥有不同的app  

## URL分发器

### 视图函数

1. 视图一般都在app的views.py中，并且视图的第一个参数永远都是request(一个HtppRequest)对象，这个对象存储了请求过来的所有信息，包括携带的参数以及一些头部信息。
    ```python
    def index(request):
        return  HttpResponse("index")
        
    注意：
      视图函数的返回值必须是'django.http.response.HttpRespnseBase'的子类的对象
    ```
 
### URL映射

1. 为什么需要在urls.py中寻找映射关系？ 
 因为在settings.py中指定URL映射指定的位置```ROOT_URLCONF = 'untitled1.urls'```

2. 在urls.py中所有映射都应该在`urlpatterns`变量中

    ```python
    urlpatterns = [
    path('admin/', admin.site.urls),
    ]
    ```

3. 所有映射都应该使用`path`或者`re_path`函数
	
### URL中添加参数

1. 访问方式：/book/detail/1/

    ```
   def book(request):
		return  HttpResponse("图书首页")

   def bookDetail(request,bookId):
	    text = '您获取的图书ID {id}'
	    return HttpResponse(text.format(id=bookId))

   urlpatterns = [
	    path('book/',views.book),
	    path('book/detail/<bookId>/',views.bookDetail),
	]
   注意：
     此种方式必须保证视图函数中的参数和url中的参数名保持一致，url可以传递多个参数
    ```

2. 访问方式：/book/add/?a=4&b=5 

    ```python
	def add(request):
	    a = request.GET.get['a']
	    b = request.GET.get['b']
	    c = int(a)+int(b)
	    return HttpResponse(str(c))

	urlpatterns = [
    	path('book/add/',views.add),
	]
   注意： 
     此种查询字符串方法在url中不需要单独匹配查询字符串部分，因为查询字符串方法使用的是'GET'请求，所以只需要在视图函数中使用request.GET.get('参数名')的方式获取。
     获取的数据是以字典类型存储，所有操作方式和字典一样。
    ```

### path函数

* path函数的定义为：path(route,view,name=None,kwargs=None)
* route参数：url的匹配规则。这个参数中可以指定url中需要传递的参数，传递参数通过<>尖括号来行指定，并且在传递参数的时候，可以指定这个参数的数据类型，必须int类型（<int:id>）在以后匹配的时候只会匹配到id为int类型的url,并且在视图函数中获取这个参数的值后，就已经转换成一个int类型
 
    - str:  非空的字符串类型，默认转换器，但是不能包裹斜杠`/`
    - int： 匹配任意的零或者正数的整形
    - slug: 由英文中的横杠(-)，或者下划线_连接英文字符或者数字而成的字符串
    - uuid: 匹配uuid字符串
    - path: 匹配非空的英文字符串，可以包括斜杠

    ```python
    path('book/detail/<int:bookId>/',views.bookDetail)
    from django.urls import converters
    注意：通过此导入包的方式查看转换器的实现方法
    ```

### url模块化(分层)

1. project中的urls.py 

    针对每个app定制化相关的url
    在projectName项目中的urls.py中指定每个app中的urls.py(需要单独创建)
    实现所有以`book/`开始的url都指定到book app中的urls.py中

    ```python
    from django.urls import path,include	
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('book/',include('book.urls'))
    ]
    ```

2. app中的urls.py
    
    ```python
    from django.urls import path
    from book import views

    urlpatterns = [
        path('',views.book),
        path('detail/<int:bookId>/',views.bookDetail),
        path('add/',views.add),
    ]
    注意：完整的`url`会根据project中的`urls.py`和 app中的`urls.py`进行拼接
    ```

### 通过path中的name参数实现对url的命名

1. python 反向解析URL
    ```python
    from django.shortcuts import reverse,redirect
    def index(request):
        # 通过reverse 反向解析的地址是：/login/
        # 如果在APP中配置的有命名空间，reverse('appName:login') 的方式指定跳转的页面地址
        return redirect(reverse('login'))  
        
    def login(reqeust):
        return HttpResponse("登陆页面")

    urlpatterns = [
        path('index/',views.index,name='index'),
        path('login/',views.login,name='login'),
    ]
    注意：
        redirect实现跳转功能，reverse实现url反转功能对path的name反转成相对应的url地址
    因为url是经常变化的，如果在代码中写死，需要经常修改代码，可以通过给url命名的方式，使用url命名进行反转，减少修改代码的次数。
    ```
2. HTML 反向解析URL
    ```html
    <body>
    <ul class="nav">
        <li><a href="/">首页</a></li>
        <li><a href="{% url 'appname:book' %}">读书</a></li> #指定命名空间
        <li><a href="{% url 'movie' %}">电影</a></li>
        <li><a href="{% url 'city' %}">同城</a></li>
        <li><a href="{% url 'book_detail' book_id='1' category='1' %}"> 最火的一篇文章</a></li> #传递参数
        <li><a href="{% url  'login'  %}?next=/">登录</a></li> #传递参数
    </ul>
    </body>
    ```

### 应用命名空间
 
1. 如果有多个app存在的情况下，会出现url以及url命名重复的情况，
可以通过app命名空间的方式解决在app中的urls.py中通过app_name的方式指定命名空间

    ```python
    from django.urls import path
    from book import views
    #应用命名空间
    app_name = 'book'

    urlpatterns = [
        path('',views.book),
        path('detail/<int:bookId>/',views.bookDetail),
        path('add/',views.add),
        path('index/',views.index,name='index'),
        path('login/',views.login,name='login'),
    ]
    ```


2. 指定命名空间以后在进行url反转的时候需要特别指定app的命名空间

    通过`应用命名空间:url命名`的方式访问
    ```python
    from django.shortcuts import reverse,redirect
    def index(request):
        return redirect(reverse('book:login'))

    def login(reqeust):
        return HttpResponse("登陆页面")
    ```


### 实例命名空间

* 一个app应用可以创建多个实例，使用多个url映射同一个app,所以在使用反转的时候
如果只使用应用命名空间，会发生混乱的情况，可以通过使用实例命名空间解决。
在project中urls.py中指定实例命名空间

    ```python
    from django.contrib import admin
    from django.urls import path,include
    from book import views
    from django.urls import converters

    urlpatterns = [
        path('admin/', admin.site.urls),
        path('book1/',include('book.urls',namespace='book1'))
        path('book2/',include('book.urls',namespace='book2'))
    ]

    from django.shortcuts import reverse,redirect
    def index(request):
        currentNameSpace = request.resolver_match.namespace
        print(currentNameSpace)
        return redirect(reverse('%s:login' % currentNameSpace))

    def login(reqeust):
        return HttpResponse("登陆页面")
    通过request.resolver_match.namespace 方法获取当前实例的命名空间
    ```

### re_path函数

* 通过正则表达式实现一些复杂的需求

    ```python
    from django.urls import re_path
    urlpatterns = [
        re_path(r"^list/(?P<year>\d{4})/$",views.article),
    ]

    def article(reqeust,year):
        return HttpResponse("文章首页%s"%year)
    注意：
        1. 写正则表达式的时候推荐使用原生字符串，以`r`开头的字符串
        2. 在正则表达式中定义变量，需要使用圆括号括起来，这个参数如果有名字，
           需要使用`?P<参数的名字>`然后在后面添加正则表达式的规则。
           在视图函数中可以通过此参数的名字调用
        3. 除非特别要求，如果能用`path`实现的尽量不要使用`re_path`
    ```

### reverse函数

1. 通过反转方法传递关键字参数

    ```python
    from django.urls import path,re_path
    from book import views

    #应用命名空间
    app_name = 'book'

    urlpatterns = [
        path('index/',views.index,name='index'),
        path('detail/<id>/',views.detail,name='detail'),
    ]

    from django.shortcuts import reverse,redirect

    def index(request):
        return redirect(reverse('book:detail',kwargs={"id":1}))

    def detail(request,id):
        #/book/detail/1/
        return HttpResponse("详细信息%s" % id)
    ```

2. 通过反转参数传递查询字符串参数

    ```python
    def login(reqeust):
        return HttpResponse("登陆页面")

    def index(request):
        #/login/?next=/
        return redirect(reverse('book:login')+"?next=/")

    ```

### url映射时指定默认参数

* 默认参数

    ```python
    urlpatterns = [
        path('books/',views.bookList,name='books'),
        path('books/<int:page>/',views.bookList,name='books')
    ]
    book_list = ['三国演义','水浒传','西游记','红楼梦']

    * 通过page=0指定默认参数

    def bookList(request,page=0):
        return HttpResponse(book_list[page])
    ```
当访问books/的时候，因为没有传递page参数，所有会匹配到第一个url,因为指定的有page=0为默认参数，所以直接返回第一个值
如果访问books/1/的时候，因为从传递参数的时候包含了page，因此会匹配到第二个url，然后把传递进来的参数传递个bookList函数中的page参数



### 自定义path转换器

* 需求根据以下url的形式定义转换器

    ```python
    1. /book/list/python/
    2. /book/list/python+django/
    3. /book/list/python+django+flash/

    from django.urls import converters,register_converter
    #自定义转换器
    class CategoryConverter(object):
        regex = r'\w+|(\w+\+\w+)+'
        #传递到视图函数之前对数据进行自定义操作
        def to_python(self,value):
            result = value.split('+')
            return result
        #返回数据之前对数据进行自定义操作
        def to_url(self,value):
            if isinstance(value,list):
                result = "+".join(value)
                return result
            else:
                raise RuntimeError("转换url的时候，分类参数必须为列表")
    #通过此方法把自定的转换器注册到django系统里面
    register_converter(CategoryConverter,'cate')

    urlpatterns = [
        path('list/<cate:categories>/',views.articleList,name='list')
    ]

    def articleList(request,categories):
        print(reverse('book:list',kwargs={"categories":categories}))
        return HttpResponse("文章列表：%s"% categories)
    为了方便以后维护建议在app下面创建converters.py文件

    class CategoryConverter(object):
        regex = r'\w+|(\w+\+\w+)+'
        #传递到视图函数之前对数据进行自定义操作
        def to_python(self,value):
            result = value.split('+')
            return result
        #返回数据之前对数据进行自定义操作
        def to_url(self,value):
            if isinstance(value,list):
                result = "+".join(value)
                return result
            else:
                raise RuntimeError("转换url的时候，分类参数必须为列表")
    #通过此方法把自定的转换器注册到django系统里面
    register_converter(CategoryConverter,'cate')

    默认添加converters.py文件以后，是不会被执行的，通过在app在__init__.py文件中导入这个文件，实现自动加载自定义的转换器
    from .  import  converters
    ```

* 自定义url转换器按照以下步骤实现
    1. 定义一个类，直接继承ojbect就可以
    2. 在类中定义个regex的属性，用来保存url转换的正则表达式
    3. 实现to_python(self,value)方法，主要是将url中的值进行转换传递给视图函数
    4. 实现to_url(self,value)方法，主要是将传进来的参数转换后拼接成正确的url
    5. 定义好的转换器通过register_converter(CategoryConverter,'自定义名字')
       注册到django服务中

## 模板

### 渲染模板的方法

```python
1. from django.template.loader import render_to_string      
    def index(request):
        return HttpResponse(render_to_string('index.html'))

2. from django.shortcuts import render
    def index(request):
        return render(request,'index.html')
注意：第二种比较常用
```

### 模板查找模板路径

在项目的settings.py文件中，有个TEMPLATES配置，这个配置包含了模板引擎的配置，查找路径的配置，模板上下文等的配置。
1. DIRS: 这个列表中存放所有模板的路径，在视图函数中使用render或者render_to_string渲染模板的时候，会在这个列表的路径中查找模板。
2. APP_DIRS:模板为True,这个配置为True后，会在INSTALLED_APPS安装了的app下的templates文件中查找模板。
3. 查找顺序：比如代码render('index.html')，会先在DIRS这个列表中依次查找路径下有没有模板，如果有，就返回，如果DIRS列表中所有的路径都没有，那么会检查当前这个视图所处的app是否已经安装，如果已经安装，先在当前这个app的templates文件夹中查找模板，如果没有找到，会在其他已经安装了的app中的templates文件夹中查找。如果都没有找到会返回返回一个TemplateDoesNotExist报错

    ```python
    TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')]
        ,
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
    ]
    ```

### 模板变量

通过使用context参数传递字典的方式，把视图函数中的变量传到模板中,在html模板中使用{{}}的方式调用,模板中的变量名就是context字典中的key。
通过context字典的值可以传入 类对象，列表，字典，元组，字符串，整形。在html模板中通过{{对象.属性名}}
例如：列表name.0, 字典name.key 模板中可以使用以上类型中的一些属性例如字典的keys和items,values等方法

#### 字符串

```python
def index(request):
    aa = '这是测试模板变量的名字'
    return render(request,'front.html',context={"name":aa})
html模板：{{ name}}
```

#### 字典
```python
def index(request):
    name = {'username':'张三'}
    return render(request,'front.html',context={"name":name})
html 模板:{{ name.username }} {{ name.keys }} {{ name.items }}
```
#### 列表
```python
def index(request):
    name = ['张三','李斯']
    return render(request,'front.html',context={"name":name})
html 模板：{{ name.0 }} {{ name }}
```
#### 模板标签

查看模板标签内部方法：from django.template import defaulttags
#### for 标签
```html
遍历列表
{% for i in TutorialList %}
	{{ i }}
{% endfor %}
遍历字典
{% for key, value in info_dict.items %}
    {{ key }}: {{ value }}
{% endfor %}
使用逗号(,)连接元素
{% for item in List %}
    {{ item }}, 
{% endfor %}
用变量 forloop.last 这个变量，如果是最后一项其为真，否则为假
{% for item in List %}
    {{ item }}{% if not forloop.last %},{% endif %} 
{% endfor %}
当列表中可能为空值时用 for  empty
{% for athlete in athlete_list %}
    <li>{{ athlete.name }}</li>
{% empty %}
    <li>抱歉，列表为空</li>
{% endfor %}
反向遍历列表
{% fir person in person reversed %}
	{{ person }}
{% ebdfor %}	

变量	描述
forloop.counter	索引从 1 开始算
forloop.counter0	索引从 0 开始算
forloop.revcounter	索引从最大长度到 1
forloop.revcounter0	索引从最大长度到 0
forloop.first	当遍历的元素为第一项时为真
forloop.last	当遍历的元素为最后一项时为真
forloop.parentloop	用在嵌套的 for 循环中，获取上一层 for 循环的 forloop
```
#### as 标签 
```html
语句将内容取别名（相当于定义一个变量）
{% url 'some-url-name' arg arg2 as the_url %}
```

#### if 标签
```html
 ==, !=, >=, <=, >, < 这些比较都可以在模板中使用
and, or, not, in, not in 也可以在模板中使用
{% if var >= 90 %}
	成绩优秀
{% elif var >= 80 %}
	成绩良好
{% elif var >= 70 %}
	成绩一般
{% elif var >= 60 %}
	需要努力
{% else %}
	不及格
{% endif %}

{% if num <= 100 and num >= 0 %}
	num在0到100之间
{% else %}
	数值不在范围之内！
{% endif %}

{% if 'aaa' in List %}
	在列表中
{% endif %}
```
#### with 标签
```html
在模板中定义变量,在with语句中定义的变量只能在 {%with%}{%endwith%}中使用，不能在这个标签外使用,
在定义变量的时候，不能在等号，左右两边留有空格
{%  with lisi=persons.1 %}
	{{ lisi }}
{% endwith %}
{% with persons.1 as lisi %}
	{{ lisi }}
{% endwith %}
```
#### url标签
```html
book 是django定义的app_name ，list是定义的url name,和django中的reverse作用类似
{% url 'book:list' %}
使用url反转的时候传递参数
位置参数
<a href="{% url 'bool:list' 1 %}">测试</a>
关键字参数
<a href="{% url 'bool:list' book_id=1 %}">测试</a>
如果多个参数需要通过空格进行隔离
<a href="{% url 'book:list' book_id=1 page=2 %}">测试</a>
查询字符串参数
<a href="{% url 'book:list' %}?page=1">测试</a>
```
#### spaceless 标签
```html
移除html标签中的空白字符，包括空格，tab键，换行符等
spaceless 只会移除html标签之间的空白字符，不会移除标签和文本直接的空白字符
{% spaceless %}
	<p>
		<a href="foo/">foo</a>
	<p>
{% endspaceless %}
```
#### autoescape 标签
```html
	开启和关闭这个标签内元素的自动转义功能。自动转义可以将一些特殊的字符，转义成html语法能识别的字符,off关闭模板的自动转义显示原因字符串的作用，默认是开启自动转义功能，如果不确定的情况下最好使用自动转义功能，可以减少XSS漏洞攻击的可能，XSS攻击通常指的是通过利用网页开发时留下的漏洞，通过巧妙的方法注入恶意指令代码到网页，使用户加载并执行攻击者恶意制造的网页程序。这些恶意网页程序通常是JavaScript，但实际上也可以包括Java、 VBScript、ActiveX、 Flash 或者甚至是普通的HTML。攻击成功后，攻击者可能得到包括但不限于更高的权限（如执行一些操作）、私密网页内容、会话和cookie等各种内容
    {% autoescape off %}
        <a href="http://www.baidu.com">百度</a>
    {% endautoescape %}

    {% autoescape on %}
        <a href="http://www.baidu.com">百度</a>
    {% endautoescape %}
```
#### verbatim 标签
```html
默认在模板中会解析一些特殊字符。比如{% %} 以及 {{}}等，如果某个代码段中不想使用DTL的解析引擎，可以把代码放在verbvatim标签中。
{% verbatim %}
    {% if aaa %}
{% endverbatim %}
```
### 模板过滤器

导入此模块可以查django提供的过滤器: from django.template import defaultfilters
在模板中有时候需要对一些数据进行处理以后才能使用。一般在python中通过函数时间，在DTL模板中通过过滤器实现，过滤器使用 | 来使用。

#### add 
将传进来的参数添加到原来的值上，这个过滤器会尝试将值和参数转换成整形相加，如果转换失败会将值和参数进行拼接，如果是字符串，那么拼接成字符串，如果是列表，拼接成列表
{{ value|add"2" }}
add过滤器实现的代码
```python
def add(value,arg):
    try:
        return int(value)+int(arg)
    except (ValueError,TypeError):
        try:
            return value+arg
        except Exception:
            return ''
```

#### cut
移除值中所有指定的字符串类似python中的replace(args,"")
{{ value|cut"args" }}
cut过滤器实现的代码
```python
def cut(value,arg):
    safe = isinstance(value,SafeData)
    value = value.replcase(arg,'')
    if safe and arg != ";":
        return mark_safe(value)
    return value
```
#### safe 过滤器
关闭自动转义和 {% autoescape off %}功能一样
{{ value|safe }}
#### 形式
小写：``` {{ name | lower }}```

#### 串联
先转义文本到HTML，再转换每行到 <p> 标签
```html
{{ my_text|escape|linebreaks }
```

#### 过滤器的参数

- 显示前30个字：{{ bio | truncatewords:"30" }}
- 过滤器列表：{{ 123|add:"5" }} 给value加上一个数值
- {{ "AB'CD"|addslashes }}： 单引号加上转义号，一般用于输出到javascript中
- {{ "abcd"|capfirst }}： 第一个字母大写
- {{ "abcd"|center:"50" }}： 输出指定长度的字符串，并把值对中
- {{ "123spam456spam789"|cut:"spam" }}： 查找删除指定字符串
- {{ value|date:"Y/m/d" }}： 格式化日期
- {{ value|default:"(N/A)" }}： 值不存在，使用指定值
- {{ value|default_if_none:"(N/A)" }}： 值是None，使用指定值
- {{ 列表变量|dictsort:"数字" }} ：排序从小到大
- {{ 列表变量|dictsortreversed:"数字" }} ：排序从大到小
- {% if 92|divisibleby:"2" %}： 判断是否整除指定数字
- {{ string|escape }}： 转换为html实体
- {{ 21984124|filesizeformat }}：               以1024为基数，计算最大值，保留1位小数，增加可读性
- {{ list|first }} ：返回列表第一个元素
- {{ "ik23hr&jqwh"|fix_ampersands }}： &转为&
- {{ 13.414121241|floatformat }} ：保留1位小数，可为负数，几种形式
- {{ 13.414121241|floatformat:"2" }}： 保留2位小数
- {{ 23456 |get_digit:"1" }} ：从个位数开始截取指定位置的1个数字
- {{ list|join:", " }} ：用指定分隔符连接列表
- {{ list|length }} ：返回列表个数
- {% if 列表|length_is:"3" %} ：列表个数是否指定数值
- {{ "ABCD"|linebreaks }}： 用新行用<p> 、 <br /> 标记包裹
- {{ "ABCD"|linebreaksbr }}： 用新行用<br /> 标记包裹
- {{ 变量|linenumbers }}： 为变量中每一行加上行号
- {{ "abcd"|ljust:"50" }}： 把字符串在指定宽度中对左，其它用空格填充
- {{ "ABCD"|lower }}： 小写
- {% for i in "1abc1"|make_list %}ABCDE,{% endfor %}： 把字符串或数字的字符个数作为一个列表
- {{ "abcdefghijklmnopqrstuvwxyz"|phone2numeric }}： 把字符转为可以对应的数字？？
- {{ 列表或数字|pluralize }}： 单词的复数形式，如列表字符串个数大于1，返回s，否则返回空串
- {{ 列表或数字|pluralize:"es" }}： 指定es
- {{ 列表或数字|pluralize:"y,ies" }}： 指定ies替换为y
- {{ object|pprint }}： 显示一个对象的值
- {{ 列表|random }}： 返回列表的随机一项
- {{ string|removetags:"br p div" }}： 删除字符串中指定html标记
- {{ string|rjust:"50" }}： 把字符串在指定宽度中对右，其它用空格填充
- {{ 列表|slice:":2" }}： 切片
- {{ string|slugify }}： 字符串中留下减号和下划线，其它符号删除，空格用减号替换
- {{ 3|stringformat:"02i" }}： 字符串格式，使用Python的字符串格式语法
- {{ "E<A>A</A>B<C>C</C>D"|striptags }}： 剥去[X]HTML语法标记
- {{ 时间变量|time:"P" }}： 日期的时间部分格式
- {{ datetime|timesince }}： 给定日期到现在过去了多少时间
- {{ datetime|timesince:"other_datetime" }}： 两日期间过去了多少时间
- {{ datetime|timeuntil }}： 给定日期到现在过去了多少时间，与上面的区别在于2日期的前后位置。
- {{ datetime|timeuntil:"other_datetime" }}： 两日期间过去了多少时间
- {{ "abdsadf"|title }}： 首字母大写
- {{ "A B C D E F"|truncatewords:"3" }}： 截取指定个数的单词
- {{ "<a>1<a>1<a>1</a></a></a>22<a>1</a>"|truncatewords_html:"2" }}： 截取指定个数的html标记，并补完整
- <ul>{{ list|unordered_list }}</ul>： 多重嵌套列表展现为html的无序列表
- {{ string|upper }} ：全部大写
- <a href="{{ link|urlencode }}">linkage</a>： url编码
- {{ string|urlize }}： 将URLs由纯文本变为可点击的链接。（没有实验成功）
- {{ string|urlizetrunc:"30" }}： 同上，多个截取字符数。（同样没有实验成功）
- {{ "B C D E F"|wordcount }}： 单词数
- {{ "a b c d e f g h i j k"|wordwrap:"5" }}： 每指定数量的字符就插入回车符
- {{ boolean|yesno:"Yes,No,Perhaps" }}： 对三种值的返回字符串，对应是 非空,空,None
- {{ value|stringtags }}删除字符串中所有html标签
- {{ value|truncatechar:5 }}如果字符串长度超过了过滤器长度，就会进行切割，并且会拼接三个点作为省略号，三个点占用三个字符长度
- {{ value|truncatechar_html:5 }}类似truncatechar，但是不会切割html标签

#### 自定义过滤器
暂时没有做记录

#### include 标签

有时候一些代码是在许多模版中都用到的。如果我们每次都重复的去拷贝代码那肯定不符合项目的规范。一般我们可以把这些重复性的代码抽取出来，就类似于Python中的函数一样，以后想要使用这些代码的时候，就通过include包含进来。这个标签就是include。示例代码如下：
```html
# header.html
<p>我是header</p>

# footer.html
<p>我是footer</p>

# main.html
{% include 'header.html' %}
<p>我是main内容</p>
{% include 'footer.html' %}
include标签寻找路径的方式。也是跟render渲染模板的函数是一样的。

默认include标签包含模版，会自动的使用主模版中的上下文，也即可以自动的使用主模版中的变量。如果想传入一些其他的参数，那么可以使用with语句。示例代码如下：

# header.html
<p>用户名：{{ username }}</p>

# main.html
{% include "header.html" with username='huangyong' %}
```

#### 模板继承

在前端页面开发中。有些代码是需要重复使用的。这种情况可以使用include标签来实现。也可以使用另外一个比较强大的方式来实现，那就是模版继承。模版继承类似于Python中的类，在父类中可以先定义好一些变量和方法，然后在子类中实现。模版继承也可以在父模版中先定义好一些子模版需要用到的代码，然后子模版直接继承就可以了。并且因为子模版肯定有自己的不同代码，因此可以在父模版中定义一个block接口，然后子模版再去实现。以下是父模版的代码：
```html
{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
    <link rel="stylesheet" href="{% static 'style.css' %}" />
    <title>{% block title %}我的站点{% endblock %}</title>
</head>

<body>
    <div id="sidebar">
        {% block sidebar %}
        <ul>
            <li><a href="/">首页</a></li>
            <li><a href="/blog/">博客</a></li>
        </ul>
        {% endblock %}
    </div>
    <div id="content">
        {% block content %}{% endblock %}
    </div>
</body>
</html>
```

这个模版，我们取名叫做base.html，定义好一个简单的html骨架，然后定义好两个block接口，让子模版来根据具体需求来实现。子模板然后通过extends标签来实现，示例代码如下：
```html
{% extends "base.html" %}

{% block title %}博客列表{% endblock %}

{% block content %}
    {% for entry in blog_entries %}
        <h2>{{ entry.title }}</h2>
        <p>{{ entry.body }}</p>
    {% endfor %}
{% endblock %}
需要注意的是：extends标签必须放在模版的第一行。
子模板中的代码必须放在block中，否则将不会被渲染。
如果在某个block中需要使用父模版的内容，那么可以使用{{block.super}}来继承。比如上例，{%block title%}，如果想要使用父模版的title，那么可以在子模版的title block中使用{{ block.super }}来实现。

在定义block的时候，除了在block开始的地方定义这个block的名字，还可以在block结束的时候定义名字。比如{% block title %}{% endblock title %}。这在大型模版中显得尤其有用，能让你快速的看到block包含在哪里。
```

#### 加载静态文件

在一个网页中，不仅仅只有一个html骨架，还需要css样式文件，js执行文件以及一些图片等。因此在DTL中加载静态文件是一个必须要解决的问题。在DTL中，使用static标签来加载静态文件。要使用static标签，首先需要```{% load static %}```。加载静态文件的步骤如下：

1. 首先确保```django.contrib.staticfiles```已经添加到```settings.INSTALLED_APPS```中。

2. 确保在settings.py中设置了STATIC_URL

    ```STATIC_URL = /static/```
    这个static可以修改，修改以后在代码中其他地方调用的时候，就需要指定修改以后的名字，但是不建议修改此名字在浏览器中请求静态文件的url：```127.0.0.1/static/xxx.jpng```

3. 配置APP的static目录

    在已经安装了的app下创建一个文件夹叫做static，然后再在这个static文件夹下创建一个当前app的名字的文件夹，再把静态文件放到这个文件夹下。例如你的app叫做book，有一个静态文件叫做ynsy.jpg，那么路径为book/static/book/ynsy.jpg。（为什么在app下创建一个static文件夹，还需要在这个static下创建一个同app名字的文件夹呢？原因是如果直接把静态文件放在static文件夹下，那么在模版加载静态文件的时候就是使用ynsy.jpg，如果在多个app之间有同名的静态文件，这时候可能就会产生混淆。而在static文件夹下加了一个同名app文件夹，在模版中加载的时候就是使用app/ynsy.jpg，这样就可以避免产生混淆。）

4. 如果有一些静态文件是不和任何app挂钩的。那么可以在settings.py中添加STATICFILES_DIRS，以后DTL就会在这个列表的路径中查找静态文件。比如可以设置为:

    ```python
     STATICFILES_DIRS = [
        os.path.join(BASE_DIR,"static")
     ]
    ```

5. 在模版中使用load标签加载static标签。比如要加载在项目的static文件夹下的style.css的文件。那么示例代码如下：

    ```html	
     {% load static %}
     <link rel="stylesheet" href="{% static 'style.css' %}">
    ```

6. 如果不想每次在模版中加载静态文件都使用load加载static标签，那么可以在settings.py中的TEMPLATES/OPTIONS添加'builtins'：['django.templatetags.static']，这样以后在模版中就可以直接使用static标签，而不用手动的load了。

    ```python
    TEMPLATES = [
        {
            'BACKEND': 'django.template.backends.django.DjangoTemplates',
            'DIRS': [os.path.join(BASE_DIR, 'templates')]
            ,
            'APP_DIRS': True,
            'OPTIONS': {
                'context_processors': [
                    'django.template.context_processors.debug',
                    'django.template.context_processors.request',
                    'django.contrib.auth.context_processors.auth',
                    'django.contrib.messages.context_processors.messages',
                ],
                'builtins': ['django.templatetags.static'],
            },
        },
        ]
    ```

7. 如果没有在settings.INSTALLED_APPS中添加django.contrib.staticfiles。那么我们就需要手动的将请求静态文件的url与静态文件的路径进行映射了。示例代码如下：

    ```python
    from django.conf import settings
    from django.conf.urls.static import static
    urlpatterns = [
    #其他的url映射
    ] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
    ```

#### 上传文件
    Django media文件夹详解
    Django中media文件夹是我们文件(比如头像、文件、视频等)数据十分重要的存放处，这里以用户头像的上传以及media文件的访问为例为大家详细讲解下media的相关实例。
    在Django项目的settings.py文件中，我们可以配置两个关于media的参数：MEDIA_ROOT与MEDIA_URL，下面我们就文件的上传与访问一一对这两个参数进行讲解：

1. 首先我们在自己的Django项目中创建一个media文件夹

2. 在settings.py中设置MEDIA_ROOT：
    ```
    # 配置上传文件路径
    MEDIA_URL = '/media/'
    MEDIA_ROOT = os.path.join(BASE_DIR,'media')
    ```

3. 在urls.py urlpatterns中设置media路由的相关配置：
    ```
    from django.urls import path,re_path
    from django.views.static import serve
    from cnblog import settings
    ##media配置——配合settings中的MEDIA_ROOT的配置，就可以在浏览器的地址栏访问media文件夹及里面的文件了
    re_path(r'media/(?P<path>.*)$',serve,{'document_root':settings.MEDIA_ROOT}),
    ```

4. 视图配置
    ```
    def upload_file(request):
        #获取文件信息
        file = request.FILES.get('file')
        #获取文件名
        name = file.name
        #配置上传文件路径
        with open(os.path.join(settings.MEDIA_ROOT,name),'wb') as fp:
            for chunk in file.chunks():
                #保存文件
                fp.write(chunk)
        #获取上传文件完成url路径
        url = request.build_absolute_uri(settings.MEDIA_URL + name)
        context = {
            'url': url
        }
        #返回url 完成路径
        return render(request,'index.html',context=context)
    ```

## 数据库

### 常用软件

我们使用Django来操作MySQL，实际上底层还是通过Python来操作的。因此我们想要用Django来操作MySQL，首先还是需要安装一个驱动程序。在Python3中，驱动程序有多种选择。比如有pymysql以及mysqlclient等。这里我们就使用mysqlclient来操作。mysqlclient安装非常简单。只需要通过pip install mysqlclient即可安装。

常见MySQL驱动介绍：

* MySQL-python：也就是MySQLdb。是对C语言操作MySQL数据库的一个简单封装。遵循了Python DB API v2。但是只支持Python2，目前还不支持Python3。
* mysqlclient：是MySQL-python的另外一个分支。支持Python3并且修复了一些bug。
* pymysql：纯Python实现的一个驱动。因为是纯Python编写的，因此执行效率不如MySQL-python。并且也因为是纯Python编写的，因此可以和Python代码无缝衔接。

* MySQL Connector/Python：MySQL官方推出的使用纯Python连接MySQL的驱动。因为是纯Python开发的。效率不高。

### 数据库配置
```python
DATABASES = {
'default': {
    # 数据库引擎（是mysql还是oracle等）
    'ENGINE': 'django.db.backends.mysql',
    # 数据库的名字
    'NAME': 'dfz',
    # 连接mysql数据库的用户名
    'USER': 'root',
    # 连接mysql数据库的密码
    'PASSWORD': 'root',
    # mysql数据库的主机地址
    'HOST': '127.0.0.1',
    # mysql数据库的端口号
    'PORT': '3306',
}
}
```
### 原生sql

在Django中操作数据库有两种方式。第一种方式就是使用原生sql语句操作，第二种就是使用ORM模型来操作。
在Django中使用原生sql语句操作其实就是使用python db api的接口来操作。如果你的mysql驱动使用的是pymysql，那么你就是使用pymysql来操作的，只不过Django将数据库连接的这一部分封装好了，我们只要在settings.py中配置好了数据库连接信息后直接使用Django封装好的接口就可以操作了。示例代码如下：

```python
# 使用django封装好的connection对象，会自动读取settings.py中数据库的配置信息
from django.db import connection

# 获取游标对象
cursor = connection.cursor()
# 拿到游标对象后执行sql语句
cursor.execute("select * from book")
# 获取一条数据
row = cursor.fetchone()
# 返回多条数据
rows = cursor.fetchmany()
# 获取所有的数据
rows = cursor.fetchall()

# 遍历查询到的数据
for row in rows:
    print(row)
以上的execute以及fetchall方法都是Python DB API规范中定义好的。任何使用Python来操作MySQL的驱动程序都应该遵循这个规范。所以不管是使用pymysql或者是mysqlclient或者

Python DB API下规范下cursor对象常用接口：

description：如果cursor执行了查询的sql代码。那么读取cursor.description属性的时候，将返回一个列表，这个列表中装的是元组，元组中装的分别是(name,type_code,display_size,internal_size,precision,scale,null_ok)，其中name代表的是查找出来的数据的字段名称，其他参数暂时用处不大。

rowcount：代表的是在执行了sql语句后受影响的行数。

close：关闭游标。关闭游标以后就再也不能使用了，否则会抛出异常。

execute(sql[,parameters])：执行某个sql语句。如果在执行sql语句的时候还需要传递参数，那么可以传给parameters参数。示例代码如下：

 cursor.execute("select * from article where id=%s",(1,))
fetchone：在执行了查询操作以后，获取第一条数据。

fetchmany(size)：在执行查询操作以后，获取多条数据。具体是多少条要看传的size参数。如果不传size参数，那么默认是获取第一条数据。

fetchall：获取所有满足sql语句的数据
```

### 创建ORM模型

ORM模型一般都是放在app的models.py文件中。每个app都可以拥有自己的模型。并且如果这个模型想要映射到数据库中，那么这个app必须要放在```settings.py```的```INSTALLED_APP```中进行安装。以下是写一个简单的书籍ORM模型。示例代码如下：

```python
from django.db import models
class Book(models.Model):
    name = models.CharField(max_length=20,null=False)
    author = models.CharField(max_length=20,null=False)
    pub_time = models.DateTimeField(default=datetime.now)
    price = models.FloatField(default=0)
```

以上便定义了一个模型。这个模型继承自```django.db.models.Model```，如果这个模型想要映射到数据库中，就必须继承自这个类。这个模型以后映射到数据库中，表名是模型名称的小写形式，为book。在这个表中，有四个字段，一个为name，这个字段是保存的是书的名称，是varchar类型，最长不能超过20个字符，并且不能为空。第二个字段是作者名字类型，同样也是varchar类型，长度不能超过20个。第三个是出版时间，数据类型是datetime类型，默认是保存这本书籍的时间。第五个是这本书的价格，是浮点类型。
还有一个字段我们没有写，就是主键id，在django中，如果一个模型没有定义主键，那么将会自动生成一个自动增长的int类型的主键，并且这个主键的名字就叫做id。
        
映射模型到数据库中：
将ORM模型映射到数据库中，总结起来就是以下几步：

在settings.py中，配置好DATABASES，做好数据库相关的配置。
在app中的models.py中定义好模型，这个模型必须继承自```django.db.models```。
将这个app添加到settings.py的INSTALLED_APP中。
在命令行终端，进入到项目所在的路径，然后执行命令```python manage.py makemigrations```来生成迁移脚本文件。
同样在命令行中，执行命令```python manage.py migrate```来将迁移脚本文件映射到数据库中。

1. 根据主键（primary key）查询
再通过主键字段进行查询的时候可以通过使用该关键字pk，可以不用管主键字段的名称是什么

    ```python
    def bookList(request):
        book = models.Book.objects.get(pk=1)
        print(book)
        return HttpResponse("查询图书 %s " % book)
    ```
2. 根据其他条件
通过filter过滤条件的方式查询，返回的是一个QuerySet对象，通过使用first()指定返回首条数据对象

    ```python
    def bookList(request):
        #book = models.Book.objects.filter(name='西游记')
        book = models.Book.objects.filter(name='西游记').first()
        print(book)
        return HttpResponse("查询图书 %s " % book)
    ```
3. 添加数据

    ```python
    def bookCreate(request):
        book = models.Book(name='西游记',author='吴承恩',price=100)
        book.save()
        return HttpResponse("添加成功")
    ```
4. 删除数据

    ```python
    def bookCreate(request):
        book = models.Book.objects.get(pk=1)
        book.delete()
        return HttpResponse("删除成功")
    ```
5. 修改数据

    ```python
    def bookChange(request):
        book = models.Book.objects.get(pk=1)
        book.price = 200
        book.save()
        return HttpResponse("修改成功") 
   ```

### 模型常用属性

常用字段：
在Django中，定义了一些Field来与数据库表中的字段类型来进行映射。以下将介绍那些常用的字段类型。

* AutoField：
映射到数据库中是int类型，可以有自动增长的特性。一般不需要使用这个类型，如果不指定主键，那么模型会自动的生成一个叫做id的自动增长的主键。如果你想指定一个其他名字的并且具有自动增长的主键，使用AutoField也是可以的。

* BigAutoField：
64位的整形，类似于AutoField，只不过是产生的数据的范围是从1-9223372036854775807。

* BooleanField：
在模型层面接收的是True/False。在数据库层面是tinyint类型。如果没有指定默认值，默认值是None。如果在没有指定null=True的情况下，是不允许值为None，所以可以可以使用NullBooleanField 字段

* CharField：
在数据库层面是varchar类型。在Python层面就是普通的字符串。这个类型在使用的时候必须要指定最大的长度，也即必须要传递max_length这个关键字参数进去。

* DateField：
日期类型。在Python中是datetime.date类型，可以记录年月日。在映射到数据库中也是date类型。使用这个Field可以传递以下几个参数：

    * auto_now：在每次这个数据保存的时候，都使用当前的时间。比如作为一个记录修改日期的字段，可以将这个属性设置为True。

    * auto_now_add：在每次数据第一次被添加进去的时候，都使用当前的时间。比如作为一个记录第一次入库的字段，可以将这个属性设置为True。

    * DateTimeField：
    日期时间类型，类似于DateField。不仅仅可以存储日期，还可以存储时间。映射到数据库中是datetime类型。这个Field也可以使用auto_now和auto_now_add两个属性。

    * TimeField：
    时间类型。在数据库中是time类型。在Python中是datetime.time类型。
    注意：
        在用时间字段的时候   ，需要需要注意navie时间和aware时间的区别，
        * navie时间：不知道自己的时间表示的是哪个时区的。
        * aware时间：知道自己的时间表示的是哪个时区的。
        * pytz库：
        专门用来处理时区的库。这个库会经常更新一些时区的数据，不需要我们担心。并且这个库在安装Django的时候会默认的安装。如果没有安装，那么可以通过pip install pytz的方式进行安装。
        
        * astimezone方法：
        将一个时区的时间转换为另外一个时区的时间。这个方法只能被aware类型的时间调用。不能被navie类型的时间调用。
        示例代码如下：
        ```pyton
        import pytz
        from datetime import datetime
        now = datetime.now() # 这是一个navie类型的时间
        utc_timezone = pytz.timezone("UTC") # 定义UTC的时区对象
        utc_now = now.astimezone(utc_timezone) # 将当前的时间转换为UTC时区的时间
        >> ValueError: astimezone() cannot be applied to a naive datetime # 会抛出一个异常，原因就是因为navie类型的时间不能调用astimezone方法
        
        now = now.replace(tzinfo=pytz.timezone('Asia/Shanghai'))
        utc_now = now.astimezone(utc_timezone)
        # 这时候就可以正确的转换。
        replace方法：
        可以将一个时间的某些属性进行更改。
        
        django.utils.timezone.now方法：
        会根据settings.py中是否设置了USE_TZ=True获取当前的时间。如果设置了，那么就获取一个aware类型的UTC时间。如果没有设置，那么就会获取一个navie类型的时间。
        
        django.utils.timezone.localtime方法：
        会根据setting.py中的TIME_ZONE来将一个aware类型的时间转换为TIME_ZONE指定时区的时间。
        
        navie和aware介绍以及在django中的用法：
        https://docs.djangoproject.com/en/2.0/topics/i18n/timezones/
        ```

* EmailField：
类似于CharField。在数据库底层也是一个varchar类型。最大长度是254个字符。

* FileField：
用来存储文件的。这个请参考后面的文件上传章节部分。

* ImageField：
用来存储图片文件的。这个请参考后面的图片上传章节部分。

* FloatField：
浮点类型。映射到数据库中是float类型。

* IntegerField：
整形。值的区间是-2147483648——2147483647。

* BigIntegerField：
大整形。值的区间是-9223372036854775808——9223372036854775807。

* PositiveIntegerField：
正整形。值的区间是0——2147483647。

* SmallIntegerField：
小整形。值的区间是-32768——32767。

* PositiveSmallIntegerField：
正小整形。值的区间是0——32767。

* TextField：
大量的文本类型。映射到数据库中是longtext类型。

* UUIDField：
只能存储uuid格式的字符串。uuid是一个32位的全球唯一的字符串，一般用来作为主键。

* URLField：
类似于CharField，只不过只能用来存储url格式的字符串。并且默认的max_length是200。

* Field的常用参数：

* null：
如果设置为True，Django将会在映射表的时候指定是否为空。默认是为False。在使用字符串相关的Field（CharField/TextField）的时候，官方推荐尽量不要使用这个参数，也就是保持默认值False。因为Django在处理字符串相关的Field的时候，即使这个Field的null=False，如果你没有给这个Field传递任何值，那么Django也会使用一个空的字符串""来作为默认值存储进去。因此如果再使用null=True，Django会产生两种空值的情形（NULL或者空字符串）。如果想要在表单验证的时候允许这个字符串为空，那么建议使用blank=True。如果你的Field是BooleanField，那么对应的可空的字段则为NullBooleanField。

* blank：
标识这个字段在表单验证的时候是否可以为空。默认是False。
这个和null是有区别的，null是一个纯数据库级别的。而blank是表单验证级别的。

* db_column：
这个字段在数据库中的名字。如果没有设置这个参数，那么将会使用模型中属性的名字。

* default：
默认值。可以为一个值，或者是一个函数，但是不支持lambda表达式。并且不支持列表/字典/集合等可变的数据结构。

* primary_key：
是否为主键。默认是False。

* unique：
在表中这个字段的值是否唯一。一般是设置手机号码/邮箱等。

* choices：
选择字段

    ```python
    JOBS_TYPE=(
    (0,_("Batch Cmd")),
    (1,_("Batch Scripts")),
    (2,_("File Distribution")),
    (3,_("Batch Task")),
    (4,_("Crontab Task")),
    )
    type = models.CharField(choices=JOBS_TYPE, blank=True, null=True, verbose_name=_("Jobs Type"))
    ```

更多Field参数请参考官方文档：https://docs.djangoproject.com/zh-hans/2.0/ref/models/fields/

* 模型中Meta配置

    对于一些模型级别的配置。我们可以在模型中定义一个类，叫做Meta。然后在这个类中添加一些类属性来控制模型的作用。比如我们想要在数据库映射的时候使用自己指定的表名，而不是使用模型的名称。那么我们可以在Meta类中添加一个db_table的属性。示例代码如下：
    ```python
    class Book(models.Model):
        name = models.CharField(max_length=20,null=False)
        desc = models.CharField(max_length=100,name='description',db_column="description1")

    class Meta:
        db_table = 'book_model'

    ```

    以下将对Meta类中的一些常用配置进行解释。

    - ordering：
    设置在提取数据的排序方式。后面章节会讲到如何查找数据。比如我想在查找数据的时候根据添加的时间排序，那么示例代码如下：
    ```python
    class Book(models.Model):
    name = models.CharField(max_length=20,null=False)
    desc = models.CharField(max_length=100,name='description',db_column="description1")
    pub_date = models.DateTimeField(auto_now_add=True)

    class Meta:
        db_table = 'book_model'
        ordering = ['pub_date']

    https://docs.djangoproject.com/en/2.0/ref/models/meta/
    ```
    -  verbose_name
    对象的人类可读名称，单数形式和复数形式：
    ```python
    verbose_name = "图书"
    verbose_name_plural = "图书"
    ```

    - index
    指定索引
    ```python
    indexes = [
            models.Index(fields=['name']),
        ]
    ```
    
    - db_tablespace
    有些数据库有数据库表空间，比如Oracle。你可以通过db_tablespace来指定这个模型对应的数据库表放在哪个数据库表空间。

    - get_latest_by
    由于Django的管理方法中有个lastest()方法，就是得到最近一行记录。如果你的数据模型中有 DateField 或 DateTimeField 类型的字段，你可以通过这个选项来指定lastest()是按照哪个字段进行选取的。
    一个 DateField 或 DateTimeField 字段的名字. 若提供该选项, 该模块将拥有一个 get_latest() 函数以得到 "最新的" 对象(依据那个字段):
    ```
    get_latest_by = "order_date"
    ```
    - db_table
    db_table是用于指定自定义数据库表名的。Django有一套默认的按照一定规则生成数据模型对应的
    数据库表名，若不提供该参数, Django 会使用 app_label + '_' + module_name 作为表的名字.
    如果你想使用自定义的表名，就通过这个属性指定，比如：
    ```
    table_name='my_owner_table'  
    ```

    - permissions
    permissions主要是为了在Django Admin管理模块下使用的，如果你设置了这个属性可以让指定的方法权限描述更清晰可读。
    要创建一个对象所需要的额外的权限. 如果一个对象有 admin 设置, 则每个对象的添加,删除和改变权限会人(依据该选项)自动创建.下面这个例子指定了一个附加权限: 
    ```
    can_deliver_pizzas:
    permissions = (("can_deliver_pizzas", "Can deliver pizzas"),)
    ```
    这是一个2-元素 tuple 的tuple或列表, 其中两2-元素 tuple 的格式为:(permission_code, human_readable_permission_name).

    - unique_together
    unique_together这个选项用于：当你需要通过两个字段保持唯一性时使用。这会在 Django admin 层和数据库层同时做出限制(也就是相关的 UNIQUE 语句会被包括在 CREATE TABLE 语句中)。比如：一个Person的FirstName和LastName两者的组合必须是唯一的，那么需要这样设置：
    ```
    unique_together = (("first_name", "last_name"),)
    ```

* 常用函数
    
    ```python
    class BlogUser(AbstractUser):
        nickname = models.CharField('昵称', max_length=100, blank=True)
        created_time = models.DateTimeField('创建时间', default=now)
        last_mod_time = models.DateTimeField('修改时间', default=now)
        source = models.CharField("创建来源", max_length=100, blank=True)

        def get_absolute_url(self):
            # 模板可以直接通过{{ BlogUser.get_absolute_url }} 的方式获取url地址
            return reverse(
                'blog:author_detail', kwargs={
                    'author_name': self.username})

        def get_full_url(self):
            # 返回url的完整地址
            site = get_current_site().domain
            url = "https://{site}{path}".format(site=site,
                                                path=self.get_absolute_url())
            return url

        def __str__(self):
        # 指定调用Model以后返回的数据信息
        return self.name

        def get_full_name(self):
            # 返回first_name plus the last_name, with a space in between.
            return self.name

        def get_short_name(self):
            # 返回first_name
            return self.name

        class Meta:
            ordering = ['-id']
            verbose_name = "用户"
            verbose_name_plural = verbose_name
            get_latest_by = 'id'

    ```


### 外键和表关系

1. 外键

    在MySQL中，表有两种引擎，一种是InnoDB，另外一种是myisam。如果使用的是InnoDB引擎，是支持外键约束的。外键的存在使得ORM框架在处理表关系的时候异常的强大。因此这里我们首先来介绍下外键在Django中的使用。

    类定义为class ForeignKey(to,on_delete,**options)。第一个参数是引用的是哪个模型，第二个参数是在使用外键引用的模型数据被删除了，这个字段该如何处理，比如有CASCADE、SET_NULL等。这里以一个实际案例来说明。比如有一个User和一个Article两个模型。一个User可以发表多篇文章，一个Article只能有一个Author，并且通过外键进行引用。那么相关的示例代码如下：

    ```python
    class User(models.Model):
        username = models.CharField(max_length=20)
        password = models.CharField(max_length=100)

    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        author = models.ForeignKey("User",on_delete=models.CASCADE)
    ```
	以上使用ForeignKey来定义模型之间的关系。即在article的实例中可以通过author属性来操作对应的User模型。这样使用起来非常的方便。示例代码如下：
    ```python
    article = Article(title='abc',content='123')
    author.save()
    author = User(username='张三',password='111111')
    article.author = author
    article.save()
    
    # 修改article.author上的值
    article.author.username = '李四'
    article.save()
    ```

	为什么使用了ForeignKey后，就能通过author访问到对应的user对象呢。因此在底层，Django为Article表添加了一个属性名_id的字段（比如author的字段名称是author_id），这个字段是一个外键，记录着对应的作者的主键。以后通过article.author访问的时候，实际上是先通过author_id找到对应的数据，然后再提取User表中的这条数据，形成一个模型。
		
	如果想要引用另外一个app的模型，那么应该在传递to参数的时候，使用app.model_name进行指定。以上例为例，如果User和Article不是在同一个app中，那么在引用的时候的示例代码如下：
    ```python
    #User模型在user这个app中
    class User(models.Model):
        username = models.CharField(max_length=20)
        password = models.CharField(max_length=100)
    
    #Article模型在article这个app中
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
    
    author = models.ForeignKey("user.User",on_delete=models.CASCADE)
    ```

	如果模型的外键引用的是本身自己这个模型，那么to参数可以为'self'，或者是这个模型的名字。在论坛开发中，一般评论都可以进行二级评论，即可以针对另外一个评论进行评论，那么在定义模型的时候就需要使用外键来引用自身。示例代码如下：
	```python
    class Comment(models.Model):
        content = models.TextField()
        origin_comment = models.ForeignKey('self',on_delete=models.CASCADE,null=True)
        #或者
        #origin_comment = models.ForeignKey('Comment',on_delete=models.CASCADE,null=True)
    ```
2. 外键删除操作

	如果一个模型使用了外键。那么在对方那个模型被删掉后，该进行什么样的操作。可以通过on_delete来指定。可以指定的类型如下：
		
	* CASCADE：级联操作。如果外键对应的那条数据被删除了，那么这条数据也会被删除。
	* PROTECT：受保护。即只要这条数据引用了外键的那条数据，那么就不能删除外键的那条数据。
	* SET_NULL：设置为空。如果外键的那条数据被删除了，那么在本条数据上就将这个字段设置为空。如果设置这个选项，前提是要指定这个字段可以为空。
	* SET_DEFAULT：设置默认值。如果外键的那条数据被删除了，那么本条数据上就将这个字段设置为默认值。如果设置这个选项，前提是要指定这个字段一个默认值。
	* SET()：如果外键的那条数据被删除了。那么将会获取SET函数中的值来作为这个外键的值。SET函数可以接收一个可以调用的对象（比如函数或者方法），如果是可以调用的对象，那么会将这个对象调用后的结果作为值返回回去。
	* DO_NOTHING：不采取任何行为。一切全看数据库级别的约束。
	* 以上这些选项只是Django级别的，数据级别依旧是RESTRICT！
		

###	表关系
    
表之间的关系都是通过外键来进行关联的。而表之间的关系，无非就是三种关系：一对一、一对多（多对一）、多对多等。以下将讨论一下三种关系的应用场景及其实现方式。
		
1. 一对多

	应用场景：比如文章和作者之间的关系。一个文章只能由一个作者编写，但是一个作者可以写多篇文章。文章和作者之间的关系就是典型的多对一的关系。
	实现方式：一对多或者多对一，都是通过ForeignKey来实现的。还是以文章和作者的案例进行讲解。

    ```python
    class User(models.Model):
        username = models.CharField(max_length=20)
        password = models.CharField(max_length=100)
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        author = models.ForeignKey("User",on_delete=models.CASCADE)

    那么以后在给Article对象指定author，就可以使用以下代码来完成：
        
    article = Article(title='abc',content='123')
    author = User(username='',password='111111')
    # 要先保存到数据库中
    author.save()
    article.author = author
    article.save()
    并且以后如果想要获取某个用户下所有的文章，可以通过article_set来实现。示例代码如：

    user = User.objects.first()
    # 获取第一个用户写的所有文章
    articles = user.article_set.all()
    for article in articles:
        print(article)
    ```
   

2. 一对一
         
    应用场景：比如一个用户表和一个用户信息表。在实际网站中，可能需要保存用户的许多信息，但是有些信息是不经常用的。如果把所有信息都存放到一张表中可能会影响查询效率，因此可以把用户的一些不常用的信息存放到另外一张表中我们叫做UserExtension。但是用户表User和用户信息表UserExtension就是典型的一对一了。
            
    实现方式：Django为一对一提供了一个专门的Field叫做OneToOneField来实现一对一操作。示例代码如下：
    ```pyhton
     class User(models.Model):
         username = models.CharField(max_length=20)
         password = models.CharField(max_length=100)
    
     class UserExtension(models.Model):  
         birthday = models.DateTimeField(null=True)  
         school = models.CharField(blank=True,max_length=50)  
         user = models.OneToOneField("User", on_delete=models.CASCADE)

    在UserExtension模型上增加了一个一对一的关系映射。其实底层是在UserExtension这个表上增加了一个user_id，来和user表进行关联，并且这个外键数据在表中必须是唯一的，来保证一对一。
    ```
		
3. 多对多
         
    应用场景：比如文章和标签的关系。一篇文章可以有多个标签，一个标签可以被多个文章所引用。因此标签和文章的关系是典型的多对多的关系。
            
    实现方式：Django为这种多对多的实现提供了专门的Field。叫做ManyToManyField。还是拿文章和标签为例进行讲解。示例代码如下：

    ```python
     class Article(models.Model):
         title = models.CharField(max_length=100)
         content = models.TextField()
         tags = models.ManyToManyField("Tag",related_name="articles")
    
     class Tag(models.Model):
         name = models.CharField(max_length=50)
    在数据库层面，实际上Django是为这种多对多的关系建立了一个中间表。这个中间表分别定义了两个外键，引用到article和tag两张表的主键。
    
    related_name和related_query_name：
    related_name：
    还是以User和Article为例来进行说明。如果一个article想要访问对应的作者，那么可以通过author来进行访问。但是如果有一个user对象，想要通过这个user对象获取所有的文章，该如何做呢？这时候可以通过user.article_set来访问，这个名字的规律是模型名字小写_set。示例代码如下：
    
    user = User.objects.get(name='张三')
    user.article_set.all()
    如果不想使用模型名字小写_set的方式，想要使用其他的名字，那么可以在定义模型的时候指定related_name。示例代码如下：
    
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        # 传递related_name参数，以后在方向引用的时候使用articles进行访问
        author = models.ForeignKey("User",on_delete=models.SET_NULL,null=True,related_name='articles')
    以后在方向引用的时候。使用articles可以访问到这个作者的文章模型。示例代码如下：
    
    user = User.objects.get(name='张三')
    user.articles.all()
    如果不想使用反向引用，那么可以指定related_name='+'。示例代码如下：
    
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        # 传递related_name参数，以后在方向引用的时候使用articles进行访问
        author = models.ForeignKey("User",on_delete=models.SET_NULL,null=True,related_name='+')
    以后将不能通过user.article_set来访问文章模型了。
    
    related_query_name：
    在查找数据的时候，可以使用filter进行过滤。使用filter过滤的时候，不仅仅可以指定本模型上的某个属性要满足什么条件，还可以指定相关联的模型满足什么属性。比如现在想要获取写过标题为abc的所有用户，那么可以这样写：
    
    users = User.objects.filter(article__title='abc')
    如果你设置了related_name为articles，因为反转的过滤器的名字将使用related_name的名字，那么上例代码将改成如下：
    
    users = User.objects.filter(articles__title='abc')
    可以通过related_query_name将查询的反转名字修改成其他的名字。比如article。示例代码如下：
    
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        # 传递related_name参数，以后在方向引用的时候使用articles进行访问
        author = models.ForeignKey("User",on_delete=models.SET_NULL,null=True,related_name='articles',related_query_name='article')
    那么在做反向过滤查找的时候就可以使用以下代码：
    
    users = User.objects.filter(article__title='abc')
    ```

### ORM查询条件

#### exact 

```python
使用精确 = 进行查找，如果提供的是一个None，那么在sql层面就是被接受为NULL
aritcle = Article.objects.filter(id__exact=1)
print(aritcle.query) #查看执行的sql语句
在使用fileter的时候，可以通过query查看执行的sql，是因为filter返回的是一个QuerySet对象。
SELECT "article"."id", "article"."title", "article"."content" FROM "article" WHERE "article"."id" = 1;
如果使用的是get获取的数据，不能使用query查看执行sql,因为get返回的是条件满足的orm模型而不是QuerySet对象
article = Article.objects.get(id__exact=1)
article = Article.objects.get(id__exact=None)

aritcle = Article.objects.filter(title__exact='hello world')

注意：
    在linux下面
    当排序规则是utf8_general_ci的时候大小写不敏感，
    当排序规则是utf8_bin的时候大小写敏感
    在win下面
    不管排序规则是什么大小写都不敏感
```

#### iexact

```python 
使用like 进行查找
aritcle = Article.objects.filter(title__iexact='hello world')
print(aritcle.query)
SELECT "article"."id", "article"."title", "article"."content" FROM "article" WHERE "article"."title" LIKE hello world;

注意: exact 和 iexact 的区别就是  like 和 = 的区别，但是iexact 查询的时候不会带%value%做模糊匹配
```

#### contains

```python
大小写敏感
aritcle = Article.objects.filter(title__contains='hello')
print(aritcle.query)
SELECT "article"."id", "article"."title", "article"."content" FROM "article" WHERE "article"."title" LIKE BINARY %hello%
``` 

#### icontains

```python
大小写不敏感
aritcle = Article.objects.filter(title__contains='hello')
print(aritcle.query)
SELECT "article"."id", "article"."title", "article"."content" FROM "article" WHERE "article"."title" LIKE %hello%

注意: contains 和 icontains 区分大小写( LIKE BINAR 和  LIKE )，在翻译成sql的时候使用like 并且会带%value%做模糊查询
```

#### in

```python 
提取那些给定的field的值是否在给定的容器中，容器可以为list,tuple或者任何一个可迭代的对象，包括QuerySet
 aritcle = Article.objects.filter(id__in=[1,2,3])
 print(aritcle.query)
SELECT "article"."id", "article"."title", "article"."content" FROM "article" WHERE "article"."id" IN (1, 2, 3)

定义模型
class Category(models.Model):
    name = models.CharField(max_length=100)

class Meta:
    db_table = 'category'

class Article(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    category = models.ForeignKey('Category',on_delete=models.CASCADE,null=True)

class Meta:
    db_table = 'article'        

#查找id为 1,2,3的文章分类
 categories = Category.objects.filter(article__id__in=[1,2,3])
 print(categories.query)
 #SQL
 SELECT "category"."id", "category"."name" FROM "category" INNER JOIN "article" ON ("category"."id" = "article"."category_id") WHERE "article"."id" IN (1, 2, 3)

#查找标题中含有hello的分类
aritcles = Article.objects.filter(title__contains='hello')
categories = Category.objects.filter(article__in=aritcles)
print(aritcles.query)
print(categories.query)
#SQL
SELECT "article"."id", "article"."title", "article"."content", "article"."category_id" FROM "article" WHERE "article"."title" LIKE %hello% 
SELECT "category"."id", "category"."name" FROM "category" INNER JOIN "article" ON ("category"."id" = "article"."category_id") WHERE "article"."id" IN (SELECT U0."id" FROM "article" U0 WHERE U0."title" LIKE %hello% )

```

#### gt lt gte lte

* gt: 某个field的值大于给定的值

    ```python
    articles = Article.objects.filter(id__gt=2)
    print(articles.query)
    #SQL
    SELECT "article"."id", "article"."title", "article"."content", "article"."category_id" FROM "article" WHERE "article"."id" > 2
    ```
* gte: 大于等于
    ```python
    articles = Article.objects.filter(id__gte=2)
    print(articles.query)
    #SQL
    SELECT "article"."id", "article"."title", "article"."content", "article"."category_id" FROM "article" WHERE "article"."id" >= 2
    ```
* lt: 某个field的值小于给定的值
    ```pytohn
    articles = Article.objects.filter(id__lt=2)
    print(articles.query)
    #SQL
    SELECT "article"."id", "article"."title", "article"."content", "article"."category_id" FROM "article" WHERE "article"."id" < 2
    ```
* lte: 小于等于
    ```python
    articles = Article.objects.filter(id__lte=2)
    print(articles.query)
    #SQL
    SELECT "article"."id", "article"."title", "article"."content", "article"."category_id" FROM "article" WHERE "article"."id" <= 2
    ```

#### startswith

```python 
判断某个字段的值是否是以某个值开始的，大小写敏感
articles = Article.objects.filter(title__startswith='hello')
print(articles.query)
#SQL
SELECT "article"."id", "article"."title", "article"."content", "article"."category_id" FROM "article" WHERE "article"."title" LIKE BINARY hello%
```

#### istartswith

    和startswith功能一样，大小写不敏感
#### endsswith

```python 
articles = Article.objects.filter(title__endswith='world')
print(articles.query)
#SQL
SELECT "article"."id", "article"."title", "article"."content", "article"."category_id" FROM "article" WHERE "article"."title" LIKE %world
```

#### iendsswith:

    和endswith功能一样，大小写不敏感
#### range

```python
判断某个field的值是否在给定的区间中。
from django.utils.timezone import make_aware
from datetime import datetime
start_date = make_aware(datetime(year=2019,month=1,day=1))
end_date = make_aware(datetime(year=2019,month=10,day=11))
articles = Article.objects.filter(create_time__range=(start_date,end_date))
print(articles.query)
#SQL
SELECT "article"."id", "article"."title", "article"."content", "article"."create_time", "article"."category_id" FROM "article" WHERE "article"."create_time" BETWEEN 2019-01-01 00:00:00 AND 2019-10-11 00:00:00
需要注意的是，以上提取数据，不会包含最后一个值。也就是不会包含2019/10/11的文章。
而且另外一个重点，因为我们在settings.py中指定了USE_TZ=True，并且设置了TIME_ZONE='Asia/Shanghai'，因此我们在提取数据的时候要使用django.utils.timezone.make_aware先将datetime.datetime从navie时间转换为aware时间。make_aware会将指定的时间转换为TIME_ZONE中指定的时区的时间。
```

#### date

```python 
代表具体某一天的时间
articles = Article.objects.filter(create_time__date=datetime(year=2019,month=10,day=11))
print(articles.query)
#SQL
SELECT "article"."id", "article"."title", "article"."content", "article"."create_time", "article"."category_id" FROM "article" WHERE django_datetime_cast_date("article"."create_time", 'UTC') = 2019-10-11
注意，因为默认情况下MySQL的表中是没有存储时区相关的信息的。因此我们需要下载一些时区表的文件，然后添加到Mysql的配置路径中。如果你用的是windows操作系统。那么在http://dev.mysql.com/downloads/timezones.html下载timezone_2019c_posix.zip - POSIX standard。然后将下载下来的所有文件拷贝到C:\ProgramData\MySQL\MySQL Server 5.7\Data\mysql中，如果提示文件名重复，那么选择覆盖即可。
如果用的是linux或者mac系统，那么在命令行中执行以下命令：mysql_tzinfo_to_sql /usr/share/zoneinfo | mysql -D mysql -u root -p，然后输入密码，从系统中加载时区文件更新到mysql中。
```

#### year

```python 
根据年份进行查找
articles = Article.objects.filter(create_time__year=2019)
print(articles.query)
SELECT "article"."id", "article"."title", "article"."content", "article"."create_time", "article"."category_id" FROM "article" WHERE "article"."create_time" BETWEEN 2019-01-01 00:00:00 AND 2019-12-31 23:59:59.999999

可以结合gte gt lt lte 方法获取使用
articles = Article.objects.filter(create_time__year__gte=2020)
print(articles.query)
#SQL
SELECT "article"."id", "article"."title", "article"."content", "article"."create_time", "article"."category_id" FROM "article" WHERE "article"."create_time" >= 2020-01-01 00:00:00
```

#### month
 
    同year，根据月份查询
#### day
 
    同year，根据日期查询

#### week_day
 
    同year，根据星期几查询，1表示星期天，7表示星期六，2-6表示周一到周五

#### time

```python 
根据(分时秒)时间进行查询
from datetime import time
    articles = Article.objects.filter(create_time__time=time(hour=13,minute=34))
print(articles.query)
SELECT "article"."id", "article"."title", "article"."content", "article"."create_time", "article"."category_id" FROM "article" WHERE django_datetime_cast_time("article"."create_time", 'UTC') = 13:34:00

注意： time如果要具体到秒，一般比较难匹配，可以使用区间方式进行查询range 方式
start_time = time(hour=17,minute=10,second=27)
end_time = time(hour=17,minute=10,second=28)
articles = Article.objects.filter(create_time__time__range=(start_time,end_time))

更多时间参数
https://docs.djangoproject.com/en/2.0/ref/models/querysets/#range
```

#### isnull

```python 
articles = Article.objects.filter(create_time__isnull=True)#查询create_time数据为空的
ELECT "article"."id", "article"."title", "article"."content", "article"."create_time", "article"."category_id" FROM "article" WHERE "article"."create_time" IS NULL
print(articles.query)
articles = Article.objects.filter(create_time__isnull=False)#查询create_time数据不为空的
print(articles.query)
#SQL
SELECT "article"."id", "article"."title", "article"."content", "article"."create_time", "article"."category_id" FROM "article" WHERE "article"."create_time" IS NOT NULL
```

#### regex
 
    区分大小写
#### iregex

```python 
不区分大小写
articles = Article.objects.filter(create_time__regex=r"\w{4}")
print(articles.query)
SELECT "article"."id", "article"."title", "article"."content", "article"."create_time", "article"."category_id" FROM "article" WHERE "article"."create_time" REGEXP \w{4}
```

#### 聚合函数

* 定义表类型
    
    ```python
    class Author(models.Model):
        """作者模型"""
        name = models.CharField(max_length=100)
        age = models.IntegerField()
        email = models.EmailField()
    
    class Meta:
        db_table = 'author'
    
    class Publisher(models.Model):
        """出版社模型"""
        name = models.CharField(max_length=300)
    
    class Meta:
        db_table = 'publisher'

    class Book(models.Model):
        """图书模型"""
        name = models.CharField(max_length=300)
        pages = models.IntegerField()
        price = models.FloatField()
        rating = models.FloatField()
        author = models.ForeignKey(Author, on_delete=models.CASCADE)
        publisher = models.ForeignKey(Publisher, on_delete=models.CASCADE)
    
    class Meta:
        db_table = 'book'
    
    class BookOrder(models.Model):
        """图书订单模型"""
        book = models.ForeignKey("Book", on_delete=models.CASCADE)
        price = models.FloatField()
    
    class Meta:
        db_table = 'book_order'
    ```    

* 准备数据

  ```python
    INSERT INTO "main"."author" ("id", "name", "age", "email", "ROWID") VALUES (1, '曹雪芹', 35, 'cxq@qq.com', 1);
    INSERT INTO "main"."author" ("id", "name", "age", "email", "ROWID") VALUES (2, '吴承恩', 28, 'wce@qq.com', 2);
    INSERT INTO "main"."author" ("id", "name", "age", "email", "ROWID") VALUES (3, '罗贯中', 36, 'lgz@qq.com', 3);
    INSERT INTO "main"."author" ("id", "name", "age", "email", "ROWID") VALUES (4, '施耐庵', 46, 'sna@qq.com', 4);

    INSERT INTO "main"."book" ("id", "name", "pages", "price", "rating", "author_id", "publisher_id", "ROWID") VALUES (1, '三国演义', 987, 98.0, 4.8, 3, 1, 1);
    INSERT INTO "main"."book" ("id", "name", "pages", "price", "rating", "author_id", "publisher_id", "ROWID") VALUES (2, '水浒传', 967, 97.0, 4.83, 4, 1, 2);
    INSERT INTO "main"."book" ("id", "name", "pages", "price", "rating", "author_id", "publisher_id", "ROWID") VALUES (3, '西游记', 1004, 95.0, 4.85, 2, 2, 3);
    INSERT INTO "main"."book" ("id", "name", "pages", "price", "rating", "author_id", "publisher_id", "ROWID") VALUES (4, '红楼梦', 1007, 99.0, 4.9, 1, 2, 4);

    INSERT INTO "main"."publisher" ("id", "name", "ROWID") VALUES (1, '中国邮电出版社', 1);
    INSERT INTO "main"."publisher" ("id", "name", "ROWID") VALUES (2, '清华大学出版社', 2);

    INSERT INTO "main"."book_order" ("id", "price", "book_id", "ROWID") VALUES (1, 95.0, 1, 1);
    INSERT INTO "main"."book_order" ("id", "price", "book_id", "ROWID") VALUES (2, 85.0, 1, 2);
    INSERT INTO "main"."book_order" ("id", "price", "book_id", "ROWID") VALUES (3, 88.0, 1, 3);
    INSERT INTO "main"."book_order" ("id", "price", "book_id", "ROWID") VALUES (4, 94.0, 2, 4);
    INSERT INTO "main"."book_order" ("id", "price", "book_id", "ROWID") VALUES (5, 93.0, 2, 5);
```

* 聚合函数引入方法

    ```python
    from django.db.models import Avg,Count,Max,Min,Sum
    使用聚合函数必须集合aggregate方法或者annotate方法使用
    aggregate方法: 不会返回一个QuersSet对象，而是返回一个字典并且这个字典的key就是聚合函数的名字，值就是聚合函数返回的执行结果。所以通过聚合函数打印执行sql的时候需要使用connection方法
    from django.db import connection
    
    annotate方法：在原来模型字段的基础上添加一个使用了聚合函数的字段，并且在使用聚合函数的时候，会使用当前这个模型的主键进行分组(group by),并返回一个QuersSet对象

    aggregate 和 annotate：
    相同：
        都可以执行聚合函数
        可以在任何的QuersSet对象上调用
    不同：
        aggregate： 返回一个字典，这个字典中存储的是这个聚合函数执行的结果
        annotate： 返回的是一个QuerySet对象，并且会在这个模型上添加一个聚合函数的属性
            
        aggregate：不会做分组
        annotate: 会使用group by 子句进行分组，只有调用group by 子句，才能对每一条数据求聚合函数的值

    ``` 

* Avg：求平均值

    ```python
    获取所有书的平均价格
    result = Book.objects.aggregate(Avg('price'))
    print(connection.queries)
    [{'sql': 'SELECT AVG("book"."price") AS "price__avg" FROM "book"', 'time': '0.002'}]
    其中price__avg是django自动生成的别名：别名规则是字段名加上函数名
    可以指定自己定义的别名通过指定位置参数的方式
    result = Book.objects.aggregate(priceAvg=Avg('price'))
    [{'sql': 'SELECT AVG("book"."price") AS "priceAvg" FROM "book"', 'time': '0.002'}]

    获取每本书的平均价格
    result = Book.objects.annotate(priceAvg=Avg('bookorder__price'))
    for i in result:
        print(i.name,i.priceAvg)
    print(connection.queries)
    三国演义 89.33333333333333
    水浒传 93.5
    西游记 None
    红楼梦 None
    [{'sql': 'SELECT "book"."id", "book"."name", "book"."pages", "book"."price", "book"."rating", "book"."author_id", "book"."publisher_id", AVG("book_order"."price") AS "priceAvg" FROM "book" LEFT OUTER JOIN "book_order" ON ("book"."id" = "book_order"."book_id") GROUP BY "book"."id", "book"."name", "book"."pages", "book"."price", "book"."rating", "book"."author_id", "book"."publisher_id"', 'time': '0.003'}]

    bookorder__price是通过外键关系查询到的BookOrder表中的price字段
    ```    

* Count： 获取指定的对象的个数

    ```python
    获取图书的总数
    result = Book.objects.aggregate(bookNums=Count('id'))
    print(result)
    print(connection.queries)
    [{'sql': 'SELECT COUNT("book"."id") AS "bookNums" FROM "book"', 'time': '0.003'}]

    通过指定distinct过滤重复的值以后，在做总和计算
    result = Book.objects.aggregate(bookNums=Count('name',distinct=True))
    print(connection.queries)
    [{'sql': 'SELECT COUNT(DISTINCT "book"."name") AS "bookNums" FROM "book"', 'time': '0.004'}]

    获取每本图书售卖的总数
    如果获取的是这个模型的id，这个id可以省略
    result = Book.objects.annotate(bookNums=Count('bookorder__id')) == result = Book.objects.annotate(bookNums=Count('bookorder')) 
    for i in result:
        print(i.name,i.bookNums)
    print(connection.queries)
    三国演义 3
    水浒传 2
    西游记 0
    红楼梦 0
    [{'sql': 'SELECT "book"."id", "book"."name", "book"."pages", "book"."price", "book"."rating", "book"."author_id", "book"."publisher_id", COUNT("book_order"."id") AS "bookNums" FROM "book" LEFT OUTER JOIN "book_order" ON ("book"."id" = "book_order"."book_id") GROUP BY "book"."id", "book"."name", "book"."pages", "book"."price", "book"."rating", "book"."author_id", "book"."publisher_id"', 'time': '0.001'}]

    获取作者中大于30的人数总和
    result = Author.objects.filter(age__gte=30).aggregate(ages=Count('id'))
    print(result)
    print(connection.queries)
    [{'sql': 'SELECT COUNT("author"."id") AS "ages" FROM "author" WHERE "author"."age" >= 30', 'time': '0.001'}]
    ```

* Max 和 Min : 获取最大值和最小值

    ```python 
    获取作者中最大年龄和最新年龄
    result = Author.objects.aggregate(max=Max('age'),min=Min('age'))
    print(result)
    print(connection.queries)
    ```
			
* Sum：获取总和

    ```python 
    获取售卖图书总价格
    result = BookOrder.objects.aggregate(bookSum=Sum('price'))
    print(connection.queries)
    [{'sql': 'SELECT SUM("book_order"."price") AS "bookSum" FROM "book_order"', 'time': '0.001'}]
    
    获取每本图书售卖总和
    result = Book.objects.annotate(bookSum=Sum('bookorder__price'))
    for i in result:
        print(i.name,i.bookSum)
        三国演义 268.0
        水浒传 187.0
        西游记 None
        红楼梦 None
        [{'sql': 'SELECT "book"."id", "book"."name", "book"."pages", "book"."price", "book"."rating", "book"."author_id", "book"."publisher_id", SUM("book_order"."price") AS "bookSum" FROM "book" LEFT OUTER JOIN "book_order" ON ("book"."id" = "book_order"."book_id") GROUP BY "book"."id", "book"."name", "book"."pages", "book"."price", "book"."rating", "book"."author_id", "book"."publisher_id"', 'time': '0.002'}]
    ```

#### 表达式

* F表达式
         
	F表达式是用来优化ORM操作数据库的，动态获取某个字段的值(但是不会生成查询sql)，在对这个值进行操作。比如我们要将公司所有员工的薪水都增加1000元，如果按照正常的流程，应该是先从数据库中提取所有的员工工资到Python内存中，然后使用Python代码在员工工资的基础之上增加1000元，最后再保存到数据库中。这里面涉及的流程就是，首先从数据库中提取数据到Python内存中，然后在Python内存中做完运算，之后再保存到数据库中。示例代码如下：
    ```pyton
    employees = Employee.objects.all()
    for employee in employees:
    employee.salary += 1000
    employee.save()
    而我们的F表达式就可以优化这个流程，他可以不需要先把数据从数据库中提取出来，计算完成后再保存回去，他可以直接执行SQL语句，就将员工的工资增加1000元。示例代码如下：
    
    from djang.db.models import F
    Employee.object.update(salary=F("salary")+1000)
    print(connection.queries[-1]) #打印sql
    F表达式并不会马上从数据库中获取数据，而是在生成SQL语句的时候，动态的获取传给F表达式的值。
    
    比如如果想要获取作者中，name和email相同的作者数据。如果不使用F表达式，那么需要使用以下代码来完成：
    
    authors = Author.objects.all()
    for author in authors:
        if author.name == author.email:
            print(author)
    如果使用F表达式，那么一行代码就可以搞定。示例代码如下：
    
    from django.db.models import F
    authors = Author.objects.filter(name=F("email"))
    ```

* Q表达式
         
	如果想要实现所有价格高于100元，并且评分达到9.0以上评分的图书。那么可以通过以下代码来实现：
    ```python
    books = Book.objects.filter(price__gte=100,rating__gte=9)
    以上这个案例是一个并集查询，可以简单的通过传递多个条件进去来实现。
    但是如果想要实现一些复杂的查询语句，比如要查询所有价格低于10元，或者是评分低于9分的图书。那就没有办法通过传递多个条件进去实现了。这时候就需要使用Q表达式来实现了。示例代码如下：
    
    from django.db.models import Q
    books = Book.objects.filter(Q(price__lte=10) | Q(rating__lte=9))
    以上是进行或运算，当然还可以进行其他的运算，比如有&和~（非）等。一些用Q表达式的例子如下：
    
    from django.db.models import Q
    # 获取id等于3的图书
    books = Book.objects.filter(Q(id=3))
    # 获取id等于3，或者名字中包含文字"记"的图书
    books = Book.objects.filter(Q(id=3)|Q(name__contains("记")))
    # 获取价格大于100，并且书名中包含"记"的图书
    books = Book.objects.filter(Q(price__gte=100)&Q(name__contains("记")))
    # 获取书名包含“记”，但是id不等于3的图书
    books = Book.objects.filter(Q(name__contains='记') & ~Q(id=3))
    ```

####  QuerySet 对象原理解析

```python
    from django.db.models.manager import Manager
    from .models import Author
    def index(request):
        print(type(Author.objects))
        return HttpResponse("sucess")
    输出：
        <class 'django.db.models.manager.Manager'>
    通过查看Manager方法，了解objects对象的详细信息
    class Manager(BaseManager.from_queryset(QuerySet)):
        pass

    class BaseManager:
        # To retain order, track each time a Manager instance is created.
        creation_counter = 0
        
        # Set to True for the 'objects' managers that are automatically created.
        auto_created = False
        
        #: If set to True the manager will be serialized into migrations and will
        #: thus be available in e.g. RunPython operations.
        use_in_migrations = False
        
        def __new__(cls, *args, **kwargs):
            # Capture the arguments to make returning them trivial.
            obj = super().__new__(cls)
            obj._constructor_args = (args, kwargs)
            return obj
        
        def __init__(self):
            super().__init__()
            self._set_creation_counter()
            self.model = None
            self.name = None
            self._db = None
            self._hints = {}
        
        def __str__(self):
            """Return "app_label.model_label.manager_name"."""
            return '%s.%s' % (self.model._meta.label, self.name)
        
        def deconstruct(self):
            """
            Return a 5-tuple of the form (as_manager (True), manager_class,
            queryset_class, args, kwargs).
        
            Raise a ValueError if the manager is dynamically generated.
            """
            qs_class = self._queryset_class
            if getattr(self, '_built_with_as_manager', False):
                # using MyQuerySet.as_manager()
                return (
                    True,  # as_manager
                    None,  # manager_class
                    '%s.%s' % (qs_class.__module__, qs_class.__name__),  # qs_class
                    None,  # args
                    None,  # kwargs
                )
            else:
                module_name = self.__module__
                name = self.__class__.__name__
                # Make sure it's actually there and not an inner class
                module = import_module(module_name)
                if not hasattr(module, name):
                    raise ValueError(
                        "Could not find manager %s in %s.\n"
                        "Please note that you need to inherit from managers you "
                        "dynamically generated with 'from_queryset()'."
                        % (name, module_name)
                    )
                return (
                    False,  # as_manager
                    '%s.%s' % (module_name, name),  # manager_class
                    None,  # qs_class
                    self._constructor_args[0],  # args
                    self._constructor_args[1],  # kwargs
                )
        
        def check(self, **kwargs):
            return []
        
        @classmethod
        def _get_queryset_methods(cls, queryset_class):
            def create_method(name, method):
                def manager_method(self, *args, **kwargs):
                    return getattr(self.get_queryset(), name)(*args, **kwargs)
                manager_method.__name__ = method.__name__
                manager_method.__doc__ = method.__doc__
                return manager_method
        
            new_methods = {}
            for name, method in inspect.getmembers(queryset_class, predicate=inspect.isfunction):
                # Only copy missing methods.
                if hasattr(cls, name):
                    continue
                # Only copy public methods or methods with the attribute `queryset_only=False`.
                queryset_only = getattr(method, 'queryset_only', None)
                if queryset_only or (queryset_only is None and name.startswith('_')):
                    continue
                # Copy the method onto the manager.
                new_methods[name] = create_method(name, method)
            return new_methods
        
        @classmethod
        def from_queryset(cls, queryset_class, class_name=None):
            if class_name is None:
                class_name = '%sFrom%s' % (cls.__name__, queryset_class.__name__)
            return type(class_name, (cls,), {
                '_queryset_class': queryset_class,
                **cls._get_queryset_methods(queryset_class),
            })
        
        def contribute_to_class(self, model, name):
            self.name = self.name or name
            self.model = model
        
            setattr(model, name, ManagerDescriptor(self))
        
            model._meta.add_manager(self)
        
        def _set_creation_counter(self):
            """
            Set the creation counter value for this instance and increment the
            class-level copy.
            """
            self.creation_counter = BaseManager.creation_counter
            BaseManager.creation_counter += 1
        
        def db_manager(self, using=None, hints=None):
            obj = copy.copy(self)
            obj._db = using or self._db
            obj._hints = hints or self._hints
            return obj
        
        @property
        def db(self):
            return self._db or router.db_for_read(self.model, **self._hints)
        
        #######################
        # PROXIES TO QUERYSET #
        #######################
        
        def get_queryset(self):
            """
            Return a new QuerySet object. Subclasses can override this method to
            customize the behavior of the Manager.
            """
            return self._queryset_class(model=self.model, using=self._db, hints=self._hints)
        
        def all(self):
            # We can't proxy this method through the `QuerySet` like we do for the
            # rest of the `QuerySet` methods. This is because `QuerySet.all()`
            # works by creating a "copy" of the current queryset and in making said
            # copy, all the cached `prefetch_related` lookups are lost. See the
            # implementation of `RelatedManager.get_queryset()` for a better
            # understanding of how this comes into play.
            return self.get_queryset()
        
        def __eq__(self, other):
            return (
                isinstance(other, self.__class__) and
                self._constructor_args == other._constructor_args
            )
        
        def __hash__(self):
            return id(self)

    通过from_queryset 以及 _get_queryset_methods方法 实现
    _get_queryset_methods： 将**QuerySet**中的方法拷贝出来

    1. type 动态创建类
        type(class_name,(cls,),class_dict)
        第一个参数用来指定创建类的名字。
        第二个参数用来指定这个类的父类。
        第三个方法用来指定这个类的一些属性和方法
    注意：
        以上代码表示模型名字.objects 是一个django.db.models.manager.Manager对象，而且这个Manager这个类是一个空壳类，他本身没有任何属性和方法，他的方法全部是通过python动态添加的方式，从QuerySet类中拷贝过来的。
```

#### QuerySet 常用方法

在使用QuerySet进行查找操作的时候，可以提供多种操作。比如过滤完后还要根据某个字段进行排序，那么这一系列的操作我们可以通过一个非常流畅的链式调用的方式进行。比如要从文章表中获取标题为123，并且提取后要将结果根据发布的时间进行排序，那么可以使用以下方式来完成：
```python		
articles = Article.objects.filter(title='123').order_by('create_time')
```
可以看到order_by方法是直接在filter执行后调用的。这说明filter返回的对象是一个拥有order_by方法的对象。而这个对象正是一个新的QuerySet对象。因此可以使用order_by方法。
		
那么以下将介绍在那些会返回新的QuerySet对象的方法。
		
1. filter

    filter：将满足条件的数据提取出来，返回一个新的QuerySet。具体的filter可以提供什么条件查询。请见查询操作章节。
    ```python
    通过过两个filter条用的方式在django里面成为链式调用
    articles = Article.objects.filter(title='123').fileter(~Q(id=3))    
    ```
2. exclude

    exclude：排除满足条件的数据，返回一个新的QuerySet。示例代码如下：
    ```python
     Article.objects.exclude(title__contains='hello')
    以上代码的意思是提取那些标题不包含hello的图书。
    ```
3. annotate
    annotate：给QuerySet中的每个对象都添加一个使用查询表达式（聚合函数、F表达式、Q表达式、Func表达式等）的新字段。示例代码如下：
    ```pyhton
     articles = Article.objects.annotate(author_name=F("author__name"))
    以上代码将在每个对象中都添加一个author__name的字段，用来显示这个文章的作者的年龄。
    ```
4. order_by
    order_by：指定将查询的结果根据某个字段进行排序。如果要倒叙排序，那么可以在这个字段的前面加一个负号。示例代码如下：
    ```python
     #根据创建的时间正序排序
     ```python
     articles = Article.objects.order_by("create_time")
     ```
     #根据创建的时间倒序排序
     ```python
     articles = Article.objects.order_by("-create_time")
     ```
     #根据作者的名字进行排序
     ```python
     articles = Article.objects.order_by("author__name")
     ```
     #首先根据创建的时间进行排序，如果时间相同，则根据作者的名字进行排序
     ```python
     articles = Article.objects.order_by("create_time",'author__name')
     ```
    一定要注意的一点是，多个order_by，会把前面排序的规则给打乱，而使用后面的排序方式。比如以下代码：
    ```python
    articles = Article.objects.order_by("create_time").order_by("author__name")
    ```
    他会根据作者的名字进行排序，而不是使用文章的创建时间。

    提前图书数据，根据图书的销量进行排序
    ```python
    books = Book.objects.annotate(order_nums=Count("bookorder")).order_by("order_nums")
    ```

5. values

    values：用来指定在提取数据出来，需要提取哪些字段。默认情况下会把表中所有的字段全部都提取出来，可以使用values来进行指定，并且使用了values方法后，提取出的QuerySet中的数据类型不是模型，而是在values方法中指定的字段和值形成的字典：
    ```python
     articles = Article.objects.values("title",'content')
     for article in articles:
         print(article)

    以上打印出来的article是类似于{"title":"abc","content":"xxx"}的形式。
    如果在values中没有传递任何参数，那么将会返回这个模型中所有的属性。

    获取关联表的数据通过**表明__字段名**的方式，这种方式获取到的key就是author__name，这个key值可以通过F表达式的方式自定义名字
    books = Books.objects.values("id","name","author__name")
    使用F表达式以后获取到的key名就是author_name
    books = Books.objects.values("id","name",author_name=F("author__name"))
    注意：自定义的名字不能和模型上本身拥有的字段一样。

    在values可以用聚合函数行程一个新的字段。
    例如通过Count函数获取图书的销量，
    books = Book.objects.values("id","name","order_nums"=Count("bookorder"))
    ```

6. values_list

    values_list：类似于values。只不过返回的QuerySet中，存储的不是字典，而是元组。示例代码如下：
    ```python
     articles = Article.objects.values_list("id","title")
     print(articles)
    那么在打印articles后，结果为<QuerySet [(1,'abc'),(2,'xxx'),...]>等。
    如果在values_list中只有一个字段。那么你可以传递flat=True来将结果扁平化。示例代码如下：
     articles1 = Article.objects.values_list("title")
     >> <QuerySet [("abc",),("xxx",),...]>
     articles2 = Article.objects.values_list("title",flat=True)
     >> <QuerySet ["abc",'xxx',...]>
    ```

7. all

      all：获取这个ORM模型的QuerySet对象。

8. select_related

    select_related：在提取某个模型的数据的同时，也提前将相关联的数据提取出来。比如提取文章数据，可以使用select_related将author信息提取出来，以后再次使用article.author的时候就不需要再次去访问数据库了。可以减少数据库查询的次数。示例代码如下：
    ```python
     article = Article.objects.get(pk=1)
     >> article.author # 重新执行一次查询语句
     article = Article.objects.select_related("author").get(pk=2)
     >> article.author # 不需要重新执行查询语句了
     >article = Article.objects.select_related("author","publisher") #可以通过传递多个值，获取多个值的结果
    select_related只能用在一对多或者一对一中，不能用在多对多或者多对一中。比如可以提前获取文章的作者，但是不能通过作者获取这个作者的文章，或者是通过某篇文章获取这个文章所有的标签。
    ```

9. prefetch_related

    prefetch_related：这个方法和select_related非常的类似，就是在访问多个表中的数据的时候，减少查询的次数。这个方法是为了解决多对一和多对多的关系的查询问题。比如要获取标题中带有hello字符串的文章以及他的所有标签，示例代码如下：
    ```python
     from django.db import connection
         articles = Article.objects.prefetch_related("tag_set").filter(title__contains='hello')
         print(articles.query) # 通过这条命令查看在底层的SQL语句
        for article in articles:
            print("title:",article.title)
            print(article.tag_set.all())
     #通过以下代码可以看出以上代码执行的sql语句
     for sql in connection.queries:
        print(sql)
    但是如果在使用article.tag_set的时候，如果又创建了一个新的QuerySet那么会把之前的SQL优化给破坏掉。比如以下代码：
     tags = Tag.obejcts.prefetch_related("articles")
     for tag in tags:
        articles = tag.articles.filter(title__contains='hello') #因为filter方法会重新生成一个QuerySet，因此会破坏掉之前的sql优化
     #通过以下代码，我们可以看到在使用了filter的，他的sql查询会更多，而没有使用filter的，只有两次sql查询
     for sql in connection.queries:
        print(sql)
    那如果确实是想要在查询的时候指定过滤条件该如何做呢，这时候我们可以使用django.db.models.Prefetch来实现，Prefetch这个可以提前定义好queryset。示例代码如下：
     Prefetch = Prefetch("articles",queryset=Article.objects.filter(title__contains='hello'))
     tags = Tag.objects.prefetch_related(Prefetch)
     for tag in tags:
        articles = tag.articles.all() #此处不能使用类似filter之类的QuerySet方法，要不然仍会破会之前的优化，如果有条件查询在Prefetch里面执行
     for article in articles:
         print(article)
     for sql in connection.queries:
        print('='*30)
        print(sql)
    因为使用了Prefetch，即使在查询文章的时候使用了filter，也只会发生两次查询操作。
    ```
10. defer 和 only

    这两个方法都会返回一个QuerySet对象，并且这个QuerySet中都是模型，而不是字典
    在查询某个模型的时候，过滤掉某些字段，注意：使用了defer了的字段，如果以后在使用这个字段，会重新发起一次sql请求

    * defer：在一些表中，可能存在很多的字段，但是一些字段的数据量可能是比较庞大的，而此时你又不需要，比如我们在获取文章列表的时候，文章的内容我们是不需要的，因此这时候我们就可以使用defer来过滤掉一些字段。这个字段跟values有点类似，只不过defer返回的不是字典，而是模型。示例代码如下：
    ```python
    articles = list(Article.objects.defer("title"))
    for sql in connection.queries:
        print('='*30)
        print(sql)
        ```
    在看以上代码的sql语句，你就可以看到，查找文章的字段，除了title，其他字段都查找出来了。当然，你也可以使用article.title来获取这个文章的标题，但是会重新执行一个查询的语句。示例代码如下：
    ```python
    articles = list(Article.objects.defer("title"))
    for article in articles:
    # 因为在上面提取的时候过滤了title
    # 这个地方重新获取title，将重新向数据库中进行一次查找操作
        print(article.title)
        for sql in connection.queries:
        print('='*30)
        print(sql)
    ```
    defer虽然能过滤字段，但是有些字段是不能过滤的，比如id，即使你过滤了，也会提取出来。
       
    * only：跟defer类似，只不过defer是过滤掉指定的字段，而only是只提取指定的字段。
        注意：使用了only了的字段，如果以后在使用没有提取某个字段，会重新发起一次sql请求
        
11. get

    get：获取满足条件的数据。这个函数只能返回一条数据，并且如果给的条件有多条数据，那么这个方法会抛出MultipleObjectsReturned错误，如果给的条件没有任何数据，那么就会抛出DoesNotExit错误。所以这个方法在获取数据的只能，只能有且只有一条。

12. create

    create：创建一条数据，并且保存到数据库中。这个方法相当于先用指定的模型创建一个对象，然后再调用这个对象的save方法。示例代码如下：
    ```pythonython
    article = Article(title='abc')
    article.save()

    # 下面这行代码相当于以上两行代码
    article = Article.objects.create(title='abc')
    ```

13. get_or_create

    get_or_create：根据某个条件进行查找，如果找到了那么就返回这条数据，如果没有查找到，那么就创建一个。示例代码如下：
    ```python
    obj,created= Category.objects.get_or_create(title='默认分类')
    如果有标题等于默认分类的分类，那么就会查找出来，如果没有，则会创建并且存储到数据库中。
    这个方法的返回值是一个元组，元组的第一个参数obj是这个对象，第二个参数created代表是否创建的。
    ```

14. bulk_create

    bulk_create：一次性创建多个数据。示例代码如下：
    ```python
    Tag.objects.bulk_create([
        Tag(name='111'),
        Tag(name='222'),
    ])
    ```

15. count

    count：获取提取的数据的个数。如果想要知道总共有多少条数据，那么建议使用count，而不是使用len(articles)这种。因为count在底层是使用select count(*)来实现的，这种方式比使用len函数更加的高效。
    count = Book.ojbects.count()

16. first 和 last

      first和last：返回QuerySet中的第一条和最后一条数据。

17. aggregate

      aggregate：使用聚合函数。

18. exists

    exists：判断某个条件的数据是否存在。如果要判断某个条件的元素是否存在，那么建议使用exists，这比使用count或者直接判断QuerySet更有效得多。示例代码如下：
    ```python
    if Article.objects.filter(title__contains='hello').exists():
    print(True) #比使用count更高效
    if Article.objects.filter(title__contains='hello').count() > 0:
    print(True) #也比直接判断QuerySet更高效
    if Article.objects.filter(title__contains='hello'):
    print(True) #效果最差
    ```

19. distinct

    distinct：去除掉那些重复的数据。这个方法如果底层数据库用的是MySQL，那么不能传递任何的参数。比如想要提取所有销售的价格超过80元的图书，并且删掉那些重复的，那么可以使用distinct来帮我们实现，示例代码如下：
    ```python
    books = Book.objects.filter(bookorder__price__gte=80).distinct()
    需要注意的是，如果在distinct之前使用了order_by，那么因为order_by会提取order_by中指定的字段，因此再使用distinct就会根据多个字段来进行唯一化，所以就不会把那些重复的数据删掉。示例代码如下：

    orders = BookOrder.objects.order_by("create_time").values("book_id").distinct()
    那么以上代码因为使用了order_by，即使使用了distinct，也会把重复的book_id提取出来。
    ```

20. update

    update：执行更新操作，在SQL底层走的也是update命令。比如要将所有category为空的article的article字段都更新为默认的分类。示例代码如下：

    Article.objects.filter(category__isnull=True).update(category_id=3)
    注意这个方法走的是更新的逻辑。所以更新完成后保存到数据库中不会执行save方法，因此不会更新auto_now设置的字段。

21. update_or_create

    根据某个条件或多个进行查找，如果找到了那么根据指定的值更新信息
    ```python
    name="我吃西红柿" 作为过滤条件
    Author.objects.update_or_create(name="我吃西红柿",defaults={"age":20,"email":"ceshi@qq.com"})
    print(connection.queries)
    多个字段做个筛选条件
    Author.objects.update_or_create(name="我吃西红柿",age=21,defaults={"email":"bb@qq.com"})
    print(connection.queries)
    [{'sql': 'BEGIN', 'time': '0.000'}, {'sql': 'SELECT "author"."id", "author"."name", "author"."age", "author"."email" FROM "author" WHERE ("author"."age" = 21 AND "author"."name" = \'我吃西红柿\')', 'time': '0.002'}, {'sql': 'SAVEPOINT "s4332_x1"', 'time': '0.001'}, {'sql': 'INSERT INTO "author" ("name", "age", "email") VALUES (\'我吃西红柿\', 21, \'bb@qq.com\')', 'time': '0.005'}, {'sql': 'RELEASE SAVEPOINT "s4332_x1"', 'time': '0.001'}]
    注意此方法不能返回多个值，如果筛选条件范围多个值，就会报错
    ```

22. delete

    delete：删除所有满足条件的数据。删除数据的时候，要注意on_delete指定的处理方式。
    注意：删除操作尽量不要使用，可以添加一个字段表示已删除，或者把数据更新到历史表里面

23. 切片操作

    切片操作：有时候我们查找数据，有可能只需要其中的一部分。那么这时候可以使用切片操作来帮我们完成。QuerySet使用切片操作就跟列表使用切片操作是一样的。示例代码如下：
    ```python
    books = Book.objects.get_queryset[1:3]
    for book in books:
        print(book)

    可以写成all方法，因为all也是返回一个get_queryset方法
    books = Book.objects.all()[1:3]
    for book in books:
        print(book)
    [{'sql': 'SELECT "book"."id", "book"."name", "book"."pages", "book"."price", "book"."rating", "book"."author_id", "book"."publisher_id" FROM "book"  LIMIT 2 OFFSET 1', 'time': '0.002'}]

    还可以设置步长的方式获取sql
    books = Book.objects.all()[1:3:2]
    for book in books:
        print(book)
    [{'sql': 'SELECT "book"."id", "book"."name", "book"."pages", "book"."price", "book"."rating", "book"."author_id", "book"."publisher_id" FROM "book"  LIMIT 2 OFFSET 1', 'time': '0.002'}]

    切片操作并不是把所有数据从数据库中提取出来再做切片操作。而是在数据库层面使用LIMIE和OFFSET来帮我们完成。所以如果只需要取其中一部分的数据的时候，建议大家使用切片操作。
    ```

24. 什么时候Django会将QuerySet转换为SQL去执行：

    生成一个QuerySet对象并不会马上转换为SQL语句去执行。
    比如我们获取Book表下所有的图书：
    ```python
    books = Book.objects.all()
    print(connection.queries)
    ```
    我们可以看到在打印connection.quries的时候打印的是一个空的列表。说明上面的QuerySet并没有真正的执行。
    在以下情况下QuerySet会被转换为SQL语句执行：

    1. 迭代：在遍历QuerySet对象的时候，会首先先执行这个SQL语句，然后再把这个结果返回进行迭代。比如以下代码就会转换为SQL语句：
        ```python
         for book in Book.objects.all():
             print(book)
        ```
    2. 使用步长做切片操作：QuerySet可以类似于列表一样做切片操作。做切片操作本身不会执行SQL语句，但是如果如果在做切片操作的时候提供了步长，那么就会立马执行SQL语句。需要注意的是，做切片后不能再执行filter方法，否则会报错。

    3. 调用len函数：调用len函数用来获取QuerySet中总共有多少条数据也会执行SQL语句。

    4. 调用list函数：调用list函数用来将一个QuerySet对象转换为list对象也会立马执行SQL语句。

    5. 判断：如果对某个QuerySet进行判断，也会立马执行SQL语句。

#### 数据库迁移

1. 迁移命令

    1. makemigrations 

        将模型生成迁移脚本。模型所在的app，必须放在settings.py中的INSTALLED_APPS中。这个命令有以下几个常用选项：

        * app_label：后面可以跟一个或者多个app，那么就只会针对这几个app生成迁移脚本。如果没有任何的app_label，那么会检查INSTALLED_APPS中所有的app下的模型，针对每一个app都生成响应的迁移脚本。
        * --name：给这个迁移脚本指定一个名字。
        * --empty：生成一个空的迁移脚本。如果你想写自己的迁移脚本，可以使用这个命令来实现一个空的文件，然后自己再在文件中写迁移脚本。
    2. migrate

        将新生成的迁移脚本。映射到数据库中。创建新的表或者修改表的结构。以下一些常用的选项
        * app_label：将某个app下的迁移脚本映射到数据库中。如果没有指定，那么会将所有在INSTALLED_APPS中的app下的模型都映射到数据库中。
        * app_label migrationname：将某个app下指定名字的migration文件映射到数据库中。
        * --fake：可以将指定的迁移脚本名字添加到数据库中。但是并不会把迁移脚本转换为SQL语句，修改数据库中的表。
        * --fake-initial：将第一次生成的迁移文件版本号记录在数据库中。但并不会真正的执行迁移脚本。
        
    3. showmigrations

        查看某个app下的迁移文件。如果后面没有app，那么将查看INSTALLED_APPS中所有的迁移文件。

    4. sqlmigrate

        查看某个迁移文件在映射到数据库中的时候，转换的SQL语句。
2. migrations中的迁移版本和数据库中的迁移版本对不上怎么办？

    找到哪里不一致，然后使用python manage.py --fake [版本名字]，将这个版本标记为已经映射。
    删除指定app下migrations和数据库表django_migrations中和这个app相关的版本号，然后将模型中的字段和数据库中的字段保持一致，再使用命令```python manage.py makemigrations```重新生成一个初始化的迁移脚本，之后再使用命令```python manage.py makemigrations ```--fake-initial来将这个初始化的迁移脚本标记为已经映射。以后再修改就没有问题了。    
3.根据已有的表自动生成模型：

    在实际开发中，有些时候可能数据库已经存在了。如果我们用Django来开发一个网站，读取的是之前已经存在的数据库中的数据。那么该如何将模型与数据库中的表映射呢？根据旧的数据库生成对应的ORM模型，需要以下几个步骤：
    
    1. Django给我们提供了一个inspectdb的命令，可以非常方便的将已经存在的表，自动的生成模型。想要使用inspectdb自动将表生成模型。首先需要在settings.py中配置好数据库相关信息。不然就找不到数据库。示例代码如下：
    ```python
     DATABASES = {
     'default': {
         'ENGINE': 'django.db.backends.mysql',
         'NAME': "migrations_demo",
         'HOST': '127.0.0.1',
         'PORT': '3306',
         'USER': 'root',
         'PASSWORD': 'root'
     }
     }
     ```
    2. 以上代码只是显示在终端。如果想要保存到文件中。那么可以使用>重定向输出到指定的文件。比如让他输出到models.py文件中。示例命令如下：
    ```shell
    python manage.py inspectdb > models.py
    以上的命令，只能在终端执行，不能在pycharm->Tools->Run manage.py Task...中使用。
    ```
     如果只是想要转换一个表为模型。那么可以指定表的名字。示例命令如下：
    ```shell
    python manage.py inspectdb article_article > models.py
    ```
    修正模型：新生成的ORM模型有些地方可能不太适合使用。比如模型的名字，表之间的关系等等。那么以下选项还需要重新配置一下：
    
    3. 模型名：自动生成的模型，是根据表的名字生成的，可能不是你想要的。

    这时候模型的名字你可以改成任何你想要的。
    模型所属app：根据自己的需要，将相应的模型放在对应的app中。放在同一个app中也是没有任何问题的。只是不方便管理。
    模型外键引用：将所有使用ForeignKey的地方，模型引用都改成字符串。这样不会产生模型顺序的问题。另外，如果引用的模型已经移动到其他的app中了，那么还要加上这个app的前缀。
    让Django管理模型：将Meta下的managed=False删掉，如果保留这个，那么以后这个模型有任何的修改，使用migrate都不会映射到数据库中。
    当有多对多的时候，应该也要修正模型。将中间表注视了，然后使用ManyToManyField来实现多对多。并且，使用ManyToManyField生成的中间表的名字可能和数据库中那个中间表的名字不一致，这时候肯定就不能正常连接了。那么可以通过db_table来指定中间表的名字。示例代码如下：
    ```python
    class Article(models.Model):
        title = models.CharField(max_length=100, blank=True, null=True)
        content = models.TextField(blank=True, null=True)
        author = models.ForeignKey('front.User', models.SET_NULL, blank=True, null=True)
     #使用ManyToManyField模型到表，生成的中间表的规则是：article_tags
     #但现在已经存在的表的名字叫做：article_tag
     #可以使用db_table，指定中间表的名字
        tags = models.ManyToManyField("Tag",db_table='article_tag')
    
     class Meta:
        db_table = 'article'
    ```
    表名：切记不要修改表的名字。不然映射到数据库中，会发生找不到对应表的错误。
    执行命令```python manage.py makemigrations```生成初始化的迁移脚本。方便后面通过ORM来管理表。这时候还需要执行命令```python manage.py migrate --fake-initial```，因为如果不使用```--fake-initial```，那么会将迁移脚本会映射到数据库中。这时候迁移脚本会新创建表，而这个表之前是已经存在了的，所以肯定会报错。此时我们只要将这个0001-initial的状态修改为已经映射，而不真正执行映射，下次再migrate的时候，就会忽略他。
    
    4. 将Django的核心表映射到数据库中：

      Django中还有一些核心的表也是需要创建的。不然有些功能是用不了的。比如auth相关表。如果这个数据库之前就是使用Django开发的，那么这些表就已经存在了。可以不用管了。如果之前这个数据库不是使用Django开发的，那么应该使用migrate命令将Django中的核心模型映射到数据库中。

## 高级视图

### 常用的请求method

GET请求: 一般用来向服务器索取数据，但不会想服务器提交数据，不会对服务器的状态进行修改。

POST请求: 主要是用来向服务器提交数据，会对服务器的状态进行更改。
### 限制请求装饰器

Django 内置的视图装饰器可以给视图提供一些限制。

1. django.views.decorators.http.require_http_methods: 

    ```python
    这个装饰器需要传递一个允许访问的方法列表。
    from django.views.decorators.http import require_http_methods

    @require_http_methods(["GET","POST"])
    def my_view(request):
        return HttpResponse("sucess")
    ```

2. django.views.decorators.http.require_GET:

    ```python
    这个装饰器是@require_http_methods(["GET"])的缩写形式
    from  django.views.decorators.http import require_GET
    @require_GET
    def my_view1(request):
        return HttpResponse("sucess")
    ```

3. django.views.decorators.http.require_POST:

    ```python
    from  django.views.decorators.http import require_POST
    @require_POST
    def my_view1(request):
        return HttpResponse("sucess")
```

### 重定向

    重定向分为永久重定向和暂时重定向,
    在页面上体现的操作就是浏览器会从一个页面自动跳转到另外一个页面。
    1. 永久重定向: http的状态码是301，多用于就网址被废弃要转到一个新的网址确保用户访问。
    2. 暂时重定向: http的状态码是302，表示页面的暂时性跳转。

    django 中重定向使用 django.shortcuts.redirect方法实现
    redirect(to,*args,parmanent=False,**kwargs)
    to 表示一个url
    parmanent表示是否是一个永久重定向，默认False
    

### WSGIRequest对象

Django在接收到http请求之后，会各方那就请求携带的参数以及报文信息创建一个WSGIRrequest对象，并且作为视图函数第一个参数传给视图函数，就是我们常见的request参数。
1. WSGIRequest对象常用属性
    
    WSGIRequest对象上大部分的属性都是只读的。因为这些属性是从客户端上传上来的，没必要做任何的修改。以下将对一些常用的属性进行讲解：

    + path：请求服务器的完整“路径”，但不包含域名和参数。比如http://www.baidu.com/xxx/yyy/，那么path就是/xxx/yyy/。
    + method：代表当前请求的http方法。比如是GET还是POST。
    + GET：一个django.http.request.QueryDict对象。操作起来类似于字典。这个属性中包含了所有以?xxx=xxx的方式上传上来的参数。
    + POST：也是一个django.http.request.QueryDict对象。这个属性中包含了所有以POST方式上传上来的参数。
    + FILES：也是一个django.http.request.QueryDict对象。这个属性中包含了所有上传的文件。
    + COOKIES：一个标准的Python字典，包含所有的cookie，键值对都是字符串类型。
    + session：一个类似于字典的对象。用来操作服务器的session。
    + META：存储的客户端发送上来的所有header信息。
    + CONTENT_LENGTH：请求的正文的长度（是一个字符串）。
    + CONTENT_TYPE：请求的正文的MIME类型。
    + HTTP_ACCEPT：响应可接收的Content-Type。
    + HTTP_ACCEPT_ENCODING：响应可接收的编码。
    + HTTP_ACCEPT_LANGUAGE： 响应可接收的语言。
    + HTTP_HOST：客户端发送的HOST值。
    + HTTP_REFERER：在访问这个页面上一个页面的url。
    + QUERY_STRING：单个字符串形式的查询字符串（未解析过的形式）。
    + REMOTE_ADDR：客户端的IP地址。如果服务器使用了nginx做反向代理或者负载均衡，那么这个值返回的是127.0.0.1，这时候可以使用HTTP_X_FORWARDED_FOR来获取，所以获取ip地址的代码片段如下：
    ```python
      if request.META.has_key('HTTP_X_FORWARDED_FOR'):  
          ip =  request.META['HTTP_X_FORWARDED_FOR']  
      else:  
          ip = request.META['REMOTE_ADDR']
    ```
    + REMOTE_HOST：客户端的主机名。
    + REQUEST_METHOD：请求方法。一个字符串类似于GET或者POST。
    + SERVER_NAME：服务器域名。
    + SERVER_PORT：服务器端口号，是一个字符串类型。

2. WSGIRequest对象方法
    + is_secure()：是否是采用https协议。
    + is_ajax()：是否采用ajax发送的请求。原理就是判断请求头中是否存在X-Requested-With:XMLHttpRequest。
    + get_host()：服务器的域名。如果在访问的时候还有端口号，那么会加上端口号。比如www.baidu.com:9000。
    + get_full_path()：返回完整的path。如果有查询字符串，还会加上查询字符串。比如/music/bands/?print=True。
    + get_raw_uri()：获取请求的完整url。

### QueryDict对象

常用的request.GET和request.POST都是QueryDict对象，这个对象继承自dict

1. get 方法

    获取指定key的值，如果没有这个key就返回None，通过指定default设置默认值，如果没有传递参数就是用默认值

    ```python
    def page(request):
        pageNum = request.GET.get("page",default=1)
        print(pageNum)
        return HttpResponse("success")
    ```

2. getlist 方法：如果浏览器上传的key对应的值有多个，就通过此方法获取

### HttpResponse对象

Django服务器接收到客户端发送过来的请求后，会将提交上来的这些数据封装成一个HttpRequest对象传递给视图函数。那么视图函数出来玩相关逻辑后，也需要返回一个相应给浏览器。这个相应必须是HtttpResponseBase或者他的子类对象。
HttpResponse是继承HttpResponseBase用的最多的子类。
1. 常用属性
    + content：返回的内容。
    + status_code：返回的HTTP响应状态码。
    + content_type：返回的数据的MIME类型，默认为text/html。浏览器会根据这个属性，来显示数据。如果是text/html，那么就会解析这个字符串，如果text/plain，那么就会显示一个纯文本。常用的Content-Type如下：
        。 text/html（默认的，html文件）
        。 text/plain（纯文本）
        。 text/css（css文件）
        。 text/javascript（js文件）
        。 multipart/form-data（文件提交）
        。 application/json（json传输）
        。 application/xml（xml文件）
    + 设置请求头：response['X-Access-Token'] = 'xxxx'
2. 常用方法
    + set_cookie：用来设置cookie信息。后面讲到授权的时候会着重讲到。
    + delete_cookie：用来删除cookie信息。
    + write：HttpResponse是一个类似于文件的对象，可以用来写入数据到数据体（content）中。

### JsonResponse 类：

1. 把用来对象dump成json字符串，然后返回将json字符串封装成Response对象返回给浏览器。并且他的Content-Type是application/json。 

    返回json信息
    ```python
    import json
    def json_view(request):
        userInfo = {
            'user': 'root',
            'password': '12345',
        }
        userINfos = json.dumps(userInfo)
        return HttpResponse(userINfos,content_type='application/json')

    使用JsonResponse方法
    from django.http import JsonResponse
    def json_view(request):
        userInfo = {
            'user': 'root',
            'password': '12345',
        }
        return JsonResponse(userInfo)
    ```
2. 默认情况下JsonResponse只能对字典进行dump，如果想要对非字典的数据进行dump，那么需要给JsonResponse传递一个safe=False参数。
    ```python
    from django.http import JsonResponse
    def index(request):
        persons = ['张三','李四','王五']
        return HttpResponse(persons)
    以上代码会报错，应该在使用HttpResponse的时候，传入一个safe=False参数，示例代码如下：
    def index(request):
        persons = ['张三','李四','王五']
        return HttpResponse(persons,safe=False)
    ```

### CSV 文件操作
1. 普通操作写入csv文件 

    生成小的CSV文件，使用Python内置的csv模块来处理csv文件，并且使用HttpResponse来将csv文件返回回去
    ```python
    import csv
    def index(reqeust):
        response = HttpResponse(content_type='text/csv') #指定文件的下载格式
        response['Content-Disposition'] = "attachment;filename=abc.csv" #指定下载文件的名称
        writer =  csv.writer(response)
        writer.writerow(['username','age']) #指定文件内容第一行
        writer.writerow(['ceshi',18])
        return response
    ```
    在初始化HttpResponse的时候，指定了Content-Type为text/csv，这将告诉浏览器，这是一个csv格式的文件而不是一个HTML格式的文件，如果用默认值，默认值就是html，那么浏览器将把csv格式的文件按照html格式输出，这肯定不是我们想要的。
    第二个我们还在response中添加一个Content-Disposition头，这个东西是用来告诉浏览器该如何处理这个文件，我们给这个头的值设置为attachment;，那么浏览器将不会对这个文件进行显示，而是作为附件的形式下载，第二个filename="somefilename.csv"是用来指定这个csv文件的名字。
    我们使用csv模块的writer方法，将相应的数据写入到response中。

2. 定制模板写入csv文件

    可以将csv格式的文件定义成模板，然后使用Django内置的模板系统，并给这个模板传入一个Context对象，这样模板系统就会根据传入的Context对象，生成具体的csv文件。
    ```python
    模板文件abc.txt：
    注意：row.0 必须和for 同一行不然会在开始有一样空行，endfor 必须另起一行表示换行
    {% for row in data %}"{{ row.0|addslashes }}", "{{ row.1|addslashes }}",
    {% endfor %}

    from django.template import loader
    def templateCsv(request):
    response = HttpResponse(content_type='text/csv') #指定文件的下载格式
    response['Content-Disposition'] = "attachment;filename=abc.csv" #指定下载文件的名称
    context = {
        'rows': [
            ['username','age'],
            ['aaa',11]
        ]
    }
    template = loader.get_template('abc.txt')
    csv_template = template.render(context=context)
    response.content = csv_template
    return response
        ```

3. 生成较大的csv文件
    ```python
    from django.http import StreamingHttpResponse
    def largeCsv(request):
        rows = ("Row,{},{}\n".format(idx,idx)  for idx in range(10000))
        response = StreamingHttpResponse(content_type='text/csv')
        response['Content-Disposition'] = "attachment;filename=large.csv"  #指定下载文件的名称
        response.streaming_content = rows
        return response
    ```
    这个类是专门用来处理流数据的。使得在处理一些大型文件的时候，不会因为服务器处理时间过长而到时连接超时。这个类不是继承自HttpResponse，并且跟HttpResponse对比有以下几点区别：

    这个类没有属性content，相反是streaming_content。
    这个类的streaming_content必须是一个可以迭代的对象。
    这个类没有write方法，如果给这个类的对象写入数据将会报错。
    注意：StreamingHttpResponse会启动一个进程来和客户端保持长连接，所以会很消耗资源。所以如果不是特殊要求，尽量少用这种方法。

### 类视图

在写视图的时候，Django除了使用函数作为视图，也可以使用类作为视图。使用类视图可以使用类的一些特性，比如继承等。

1. View：
    * django.views.generic.base.View是主要的类视图，所有的类视图都是继承自他。如果我们写自己的类视图，也可以继承自他。然后再根据当前请求的method，来实现不同的方法。比如这个视图只能使用get的方式来请求，那么就可以在这个类中定义get(self,request,*args,**kwargs)方法。以此类推，如果只需要实现post方法，那么就只需要在类中实现post(self,request,*args,**kwargs)。示例代码如下：
        ```python
        from django.views import View
        class BookDetailView(View):
            def get(self,request,*args,**kwargs):
                return render(request,'detail.html')
        ```
        类视图写完后，还应该在urls.py中进行映射，映射的时候就需要调用View的类方法as_view()来进行转换。示例代码如下：
        ```python
        urlpatterns = [        
            path("detail/<book_id>/",views.BookDetailView.as_view(),name='detail')
        ]
        ```
        除了get方法，View还支持以下方法['get','post','put','patch','delete','head','options','trace']。
        如果用户访问了View中没有定义的方法。比如你的类视图只支持get方法，而出现了post方法，那么就会把这个请求转发给http_method_not_allowed(request,*args,**kwargs)。示例代码如下：
        ```python
        class AddBookView(View):
            def post(self,request,*args,**kwargs):
                return HttpResponse("书籍添加成功！")

            def http_method_not_allowed(self, request, *args, **kwargs):
                return HttpResponse("您当前采用的method是：%s，本视图只支持使用post请求！" % request.method)
        ```
        urls.py中的映射如下：
        ```
        path("addbook/",views.AddBookView.as_view(),name='add_book')
        ```
        如果你在浏览器中访问addbook/，因为浏览器访问采用的是get方法，而addbook只支持post方法，因此以上视图会返回您当前采用的method是：GET，本视图只支持使用post请求！。
    其实不管是get请求还是post请求，都会走dispatch(request,*args,**kwargs)方法。
    * dispatch 
        请求的方法有很多，如果使用 if 来一一判断就要写很多判断语句。但是，实际上使用的并不是条件判断，而是 **反射**。用户发送请求，URL会匹配到类。找类中的方法时，使用getattr方法判断请求的方法是否对应类中的方法。查看源码的时候发现在执行get和post方法之前，还执行了dispatch方法。dispatch方法里执行就是这个操作：
        ```
        def dispatch(self, request, *args, **kwargs):
        # Try to dispatch to the right method; if a method doesn't exist,
        # defer to the error handler. Also defer to the error handler if the
        # request method isn't on the approved list.
        if request.method.lower() in self.http_method_names:
            # 在当前对象中找用户提交的方法request.method.lower(),handler就是找到的方法名
            handler = getattr(self, request.method.lower(), self.http_method_not_allowed)
        else:
            handler = self.http_method_not_allowed
        # handler加括号就是执行这些方法
        return handler(request, *args, **kwargs)
        ```
        示例代码如下：
        ```python
        from django.views import View
        # 这里Home需要继承View
        class Home(View):
            # dispatch方法的功能类似 装饰器 的功能，在执行请求的方法之前和之后做其它操作
            def dispatch(self, request, *args, **kwargs):
                # 调用父类中dispatch方法
                print("before")
                result = super(Home, self).dispatch(request,*args,**kwargs)
                print("after")
                return result
            def get(self,request):
                print(request.method)
                return render(request,"home.html")
            def post(self,request):
                print(request.method)
                return render(request, "home.html")

        请求结果
        [06 / Jun / 2020 01: 32:33] "GET /login.html/ HTTP/1.1" 200 1813
        Before
        get
        After
        [06 / Jun / 2020 01: 32:38] "GET /login.html/ HTTP/1.1" 200 1813
        [06 / Jun / 2020 01: 32:41] "POST /login.html/ HTTP/1.1" 200 15
        Before
        post
        After

        ```
    * CBV装饰器
    CBV中添加装饰器有两种方法，分别是在指定的方法中添加装饰器和在类上添加装饰器。在方法中添加装饰器：
        + 在类方法中添加装饰器
            ```
            from django.views import View
            from django.views.decorators.csrf import csrf_protect
            from django.utils.decorators import method_decorator

            def wrapper(func):
                def inner(*args, **kwargs):
                    return func(*args, **kwargs)
                return inner

            class Login(View):
                @method_decorator(csrf_protect)
                def get(self, request):
                    pass

                @method_decorator(wrapper)
                def post(self, request):
                    pass
            ```
        + 在类上添加装饰器
            ```
            from django.views import View
            from django.views.decorators.csrf import csrf_protect
            from django.utils.decorators import method_decorator

            @method_decorator(csrf_protect, name='get')
            @method_decorator(csrf_protect, name='post')
            class Login(View):
                def get(self, request):
                    pass

                def post(self, request):
                    pass
            ```
        + 为所有类方法加上装饰器,需要添加在dispatch方法上
            ```
            from django.views import View
            from django.views.decorators.csrf import csrf_protect
            from django.utils.decorators import method_decorator

            def wrapper(func):
                def inner(*args, **kwargs):
                    return func(*args, **kwargs)
                return inner

            @method_decorator(csrf_protect, name='dispatch')
            @method_decorator(wrapper, name='dispatch')
            class Login(View):
                def get(self, request):
                    pass

                def post(self, request):
                    pass
            ```



2. TemplateView：

    django.views.generic.base.TemplateView，这个类视图是专门用来返回模版的。在这个类中，有两个属性是经常需要用到的，一个是template_name，这个属性是用来存储模版的路径，TemplateView会自动的渲染这个变量指向的模版。另外一个是get_context_data，这个方法是用来返回上下文数据的，也就是在给模版传的参数的。示例代码如下：
    ```python
    from django.views.generic.base import TemplateView
    class HomePageView(TemplateView):

        template_name = "home.html"

        def get_context_data(self, **kwargs):
            context = super().get_context_data(**kwargs)
            context['username'] = "张三"
            return context
    ```
    在urls.py中的映射代码如下：
    ```pyton
    from django.urls import path
    from myapp.views import HomePageView
    urlpatterns = [
        path('', HomePageView.as_view(), name='home'),
    ]
    ```
    如果在模版中不需要传递任何参数，那么可以直接只在urls.py中使用TemplateView来渲染模版。示例代码如下：
    ```python
    from django.urls import path
    from django.views.generic import TemplateView
    urlpatterns = [
        path('about/', TemplateView.as_view(template_name="about.html")),
    ]
    ```
3. ListView：

    在网站开发中，经常会出现需要列出某个表中的一些数据作为列表展示出来。比如文章列表，图书列表等等。在Django中可以使用ListView来帮我们快速实现这种需求。示例代码如下：
    ```python
    class ArticleListView(ListView):
        model = Article
        template_name = 'article_list.html'
        paginate_by = 10
        context_object_name = 'articles'
        ordering = 'create_time'
        page_kwarg = 'page'

        def get_context_data(self, **kwargs):
            context = super(ArticleListView, self).get_context_data(**kwargs)
            print(context)
            return context

        def get_queryset(self):
            return Article.objects.filter(id__lte=89)

        对以上代码进行解释：
        首先ArticleListView是继承自ListView。
        model：重写model类属性，指定这个列表是给哪个模型的。
        template_name：指定这个列表的模板。
        paginate_by：指定这个列表一页中展示多少条数据。
        context_object_name：指定这个列表模型在模板中的参数名称。
        ordering：指定这个列表的排序方式。
        page_kwarg：获取第几页的数据的参数名称。默认是page。
        get_context_data：获取上下文的数据。
        get_queryset：如果你提取数据的时候，并不是要把所有数据都返回，那么你可以重写这个方法。将一些不需要展示的数据给过滤掉。
    ```

    示例代码如下：
    * 创建数据库模板文件

        ```python
        https://docs.djangoproject.com/zh-hans/2.2/ref/class-based-views/base/
        mode.py
        from django.db import models
        class Author(models.Model):
            name = models.CharField(verbose_name='作者名',max_length=100)
            age = models.CharField(verbose_name="年龄",max_length=10)

            class Meta:
                db_table = 'author'
                verbose_name = '作者'
                verbose_name_plural = verbose_name

            def __str__(self):
                return self.name
        class Book(models.Model):
            name = models.CharField(verbose_name='书名', max_length=100)
            price = models.CharField(verbose_name="售价",max_length=100)
            author = models.ManyToManyField('Author',related_name='author_book',related_query_name='book_author')

            class Meta:
                db_table = 'book'
                verbose_name = '图书'
                verbose_name_plural = verbose_name

            def __str__(self):
                return self.name
        class Publish(models.Model):
            name = models.CharField(verbose_name='出版社',max_length=100)
            book = models.ForeignKey('Book',on_delete=models.CASCADE,max_length='书名',related_name='publish_book',related_query_name='book_publish')

            class Meta:
                db_table = 'publish'
                verbose_name = '出版社'
                verbose_name_plural = verbose_name

            def __str__(self):
                return self.name
        ```
    * 创建admin管理模板文件

        ```python
        from django.contrib import admin
        from .models import Book,Author,Publish
        @admin.register(Author)
        class AuthorAdmin(admin.ModelAdmin):
            list_display = ['id','name','age']

        @admin.register(Book)
        class BookAdmin(admin.ModelAdmin):
            list_display = ['id','name','price','author_name']

            def author_name(self,obj):
                """
                当使用ManyToManyField的时候，如果需要在list_display 中显示字段信息，需要做此配置
                :param obj:
                :return:
                """
                return ', '.join([a.name for a in obj.author.all()])
            author_name.short_description = u'作者'
        @admin.register(Publish)
        class PublishAdmin(admin.ModelAdmin):
            list_display = ['id','name','book_publish']

            def book_publish(self,obj):
                """
                当使用ForeignKey的时候，如果需要在list_display中显示字段信息，需要做此配置
                :param obj:
                :return:
                """
                return u'%s' % obj.book.name
            book_publish.short_description = u'书名'
        ```

    * 创建视图文件模板

        ```python
        from django.shortcuts import render
        from django.views.generic import ListView,DeleteView,DetailView,CreateView
        from django.db.models import Q,F
        from django.http import HttpResponse
        from .models import Book,Author,Publish
        from .forms import BookForm
        from django.urls import reverse_lazy
        from django.db import connection

        class IndexListView(ListView):
            #path('indexlist/',views.IndexListView.as_view(),name='indexlist'),
            model = Book #调用的modles.Book.ojbect.all()返回所有数据如果和get_queryset中的queryset同时存在get_queryset生效
            template_name = 'classView/index.html' #返回的模板
            context_object_name = 'books' #model 模板中的数据在html模板中调用名字(上下文)
            ordering = ('-name') #简单排序功能如果和get_queryset中的ordering同时存在get_queryset生效
            paginate_by = 10 #每页中显示多少条数据
            page_kwarg = 'page' #做翻页的时候模板中参数的名字
            permission_required = 'catalog.can_mark_returned' #权限控制
            #success_url = reverse_lazy('classview:index')  #这个类视图执行完成以后调整到哪个页面和get_success_url功能一样

            #添加额外数据的两种写法
            # 1
            # def get_context_data(self,*args,**kwargs):
            #     context = super(IndexListView,self).get_context_data(*args,**kwargs)
            #     context['other'] = '盘龙'
            #     return context

            # 2
            def get_context_data(self, **kwargs):
                context = {
                    "latest": "Django 开发",
                }
                kwargs.update(context)
                return super().get_context_data(**kwargs)

            #针对返回的数据进行定制，默认model.object.all()
            def get_queryset(self):
                # self.queryset = Book.objects.filter(price__gte=100)#返回价格大于等于100的图书
                # self.queryset = Book.objects.filter(author__age__gte=28)#返回作者年龄大于等于28
                # self.queryset = Book.objects.filter(price__gte=100,author__age__gte=28)#返回作者年龄大于等于28并且价格大于等会100
                # self.queryset = Book.objects.filter(Q(price__gte=100) & Q(author__age__gte=28))#返回作者年龄大于等于28并且价格大于等会100
                self.queryset = Book.objects.filter(Q(price__lte=80) & Q(author__age__gte=28))#返回作者年龄大于等于28或者价格大于等会100
                print(self.queryset.query)
                print(connection.queries)
                return self.queryset

            #这个类视图执行完成以后调整到哪个页面，可以添加参数跳转
            def get_success_url(self):
                return reverse_lazy("classview:index", kwargs=self.kwargs)
        ```
    * 创建html模板文件

        ```html
        <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="UTF-8">
            <title>Class View 方法</title>
        </head>
        <body>
        <table border="1" cellpadding="0" cellspacing="0" width="300">
                <tr>
                    <th>作者</th>
                    <th>书名</th>
                    <th>价格</th>
                    <th>出版社</th>
                </tr>
            {% for book in books %}
                <tr>
                    {# 通过ForeignKey读取数据#}
                    {% for name in book.author.all %}
                        <td>{{ name  }}</td>
                    {% endfor %}
                    <td>{{ book.name }}</td>
                    <td>{{ book.price }}</td>
                    {#通过ManyToManyField读取数据#}
                    {% for name in  book.publish_book.all  %}
                        <td>{{ name }}</td>
                    {% endfor %}
                </tr>
            {% endfor %}
        </table>
        <p>{{ latest }}</p>
        </body>
        </html>
        ```

    *可以通过继承View 可以修改get 和 post 方法

        ```python
        class IndexView(View):
            template_name = 'classView/index.html'

            def get_context(self):
                students = Book.objects.all()
                context = {
                    'students': students,
                }
                return context

            def get(self, request):
                context = self.get_context()
                form = StudentForm()
                context.update({
                    'form': form,
                })
                return render(request, self.template_name, context=context)

            def post(self, request):
                form = StudentForm(request.POST)
                if form.is_valid():
                    cleaned_data = form.cleaned_data
                    student = Student()
                    student.name = cleaned_data['name']
                    student.sex = cleaned_data['sex']
                    student.email = cleaned_data['email']
                    student.profession = cleaned_data['profession']
                    student.qq = cleaned_data['qq']
                    student.phone = cleaned_data['phone']
                    student.save()
                    return HttpResponseRedirect(reverse('index'))
                context = self.get_context()
                context.update({
                    'form': form,
                })
                return render(request, self.template_name, context=context)
        ```
4. FormView
    * 定义模型myapp/models.py：
    ```

    from django.db import models
    from django.urls import reverse

    class Author(models.Model):
        name = models.CharField(max_length=200)

        def get_absolute_url(self):
            return reverse('author-detail', kwargs={'pk': self.pk})
    ```
    * 示例myapp/forms.py：
    ```
    from django import forms

    class ContactForm(forms.Form):
        name = forms.CharField()
        message = forms.CharField(widget=forms.Textarea)

        def send_email(self):
            # send email using the self.cleaned_data dictionary
            pass
    ```
    * 示例myapp/views.py：
    ```    
    from myapp.forms import ContactForm
    from django.views.generic.edit import FormView

    class ContactView(FormView):
        template_name = 'contact.html'
        form_class = ContactForm
        success_url = '/thanks/'

        def form_valid(self, form):
            # This method is called when valid form data has been POSTed.
            # It should return an HttpResponse.
            form.send_email()
            return super().form_valid(form)
    ```
    * 示例myapp/contact.html：
    ```
    <form method="post">{% csrf_token %}
        {{ form.as_p }}
        <input type="submit" value="Send message">
    </form>
    ```
5. TextChoices
    增加一个Choices类，以及他的两个子类：IntegerChoices和TextChoices。
    从名字上我们就可以看出，Choices类适用于选项操作，而子类IntegerChoices是整型数选项，子类TextChoices为字符型选项。
    ```
    from django.db import models

    class Status(models.TextChoices):
        UNPUBLISHED = 'un', '未发布'
        PUBLISHED = 'pu', '已发布'
     
    class Book(models.Model):
     status = models.CharField(max_length=2,
                                  choices=Status.choices,
                                  default=Status.UNPUBLISHED)

    class Article(models.Model):
     status = models.CharField(max_length=2,
                                  choices=Status.choices,
                                  default=Status.UNPUBLISHED)

    #使用QuerySet筛选器查询条件也可以使用Choices类：
    unpub_book= Book.objects.filter(status =
                        Status.UNPUBLISHED)

    ```
6. 常用的类视图
    * 基础类视图：
        View
        TemplateView
        RedirectView

    * 展示类视图：
        DetailView
        ListView

    * 编辑类视图：
        FormView
        CreateView
        UpdateView
        DeleteView

    * 日期类视图：
        ArchiveIndexView
        YearArchiveView
        MonthArchiveView
        WeekArchiveView
        DayArchiveView
        TodayArchiveView
        DateDetailView
    * 枚举类视图
        TextChoices




## 表单

### HTML中的表单：

    单纯从前端的html来说，表单是用来提交数据给服务器的,不管后台的服务器用的是Django还是PHP语言还是其他语言。只要把input标签放在form标签中，然后再添加一个提交按钮，那么以后点击提交按钮，就可以将input标签中对应的值提交给服务器了。

### Django中的表单：
    Django中的表单丰富了传统的HTML语言中的表单。在Django中的表单，主要做以下两件事：
    * 渲染表单
    * 验证数据合法
    每个Django表单的实例都有一个内置的is_valid()方法，用来验证接收的数据是否合法。如果所有数据都合法，那么该方法将返回True，并将所有的表单数据转存到它的一个叫做cleaned_data的属性中，该属性是一个字典类型数据。

### 渲染表单模板
    Django中表单使用流程：
    在讲解Django表单的具体每部分的细节之前。我们首先先来看下整体的使用流程。这里以一个做一个留言板为例。首先我们在后台服务器定义一个表单类，继承自django.forms.Form。示例代码如下：
    ```python
    # forms.py
    class MessageBoardForm(forms.Form):
        title = forms.CharField(max_length=3,label='标题',min_length=2,error_messages={"min_length":'标题字符段不符合要求！'})
        content = forms.CharField(widget=forms.Textarea,label='内容')
        email = forms.EmailField(label='邮箱')
        reply = forms.BooleanField(required=False,label='回复')
    然后在视图中，根据是GET还是POST请求来做相应的操作。如果是GET请求，那么返回一个空的表单，如果是POST请求，那么将提交上来的数据进行校验。示例代码如下：
    
    # views.py
    class IndexView(View):
        def get(self,request):
            form = MessageBoardForm()
            return render(request,'index.html',{'form':form})
        
        def post(self,request):
            form = MessageBoardForm(request.POST)
            if form.is_valid():
                title = form.cleaned_data.get('title')
                content = form.cleaned_data.get('content')
                email = form.cleaned_data.get('email')
                reply = form.cleaned_data.get('reply')
                return HttpResponse('success')
            else:
                print(form.errors)
                return HttpResponse('fail')

    在使用GET请求的时候，我们传了一个form给模板，那么以后模板就可以使用form来生成一个表单的html代码。在使用POST请求的时候，我们根据前端上传上来的数据，构建一个新的表单，这个表单是用来验证数据是否合法的，如果数据都验证通过了，那么我们可以通过cleaned_data来获取相应的数据。在模板中渲染表单的HTML代码如下：

    <form action="" method="post">
    {% csrf_token %}
    <table>
        <tr>
            <td></td>
            <td><input type="submit" value="提交"></td>
        </tr>
    </table>
    </form>
    我们在最外面给了一个form标签，然后在里面使用了table标签来进行美化，在使用form对象渲染的时候，使用的是table的方式，当然还可以使用ul的方式（as_ul），也可以使用p标签的方式（as_p），并且在后面我们还加上了一个提交按钮。这样就可以生成一个表单了
    ```
### 用表单验证数据

    常用的Field：
    使用Field可以是对数据验证的第一步。你期望这个提交上来的数据是什么类型，那么就使用什么类型的Field。
    
    + CharField：
    用来接收文本。
    参数：
        - max_length：这个字段值的最大长度。
        - min_length：这个字段值的最小长度。
        - required：这个字段是否是必须的。默认是必须的。
        - error_messages：在某个条件验证失败的时候，给出错误信息。
    + EmailField：
        - 用来接收邮件，会自动验证邮件是否合法。
        - 错误信息的key：required、invalid。
    + FloatField：
    用来接收浮点类型，并且如果验证通过后，会将这个字段的值转换为浮点类型。
    参数：
        - max_value：最大的值。
        - min_value：最小的值。
        - 错误信息的key：required、invalid、max_value、min_value。
    + IntegerField：
    用来接收整形，并且验证通过后，会将这个字段的值转换为整形。
    参数：
        - max_value：最大的值。
        - min_value：最小的值。
        - 错误信息的key：required、invalid、max_value、min_value。
    
    + URLField：
    用来接收url格式的字符串。
        + 错误信息的key：required、invalid。

    内置表单字段
    BooleanField
    CharField
    ChoiceField
    TypedChoiceField
    DateField
    DateTimeField
    DecimalField
    DurationField
    EmailField
    FileField
    FilePathField
    FloatField
    ImageField
    IntegerField
    GenericIPAddressField
    MultipleChoiceField
    TypedMultipleChoiceField
    NullBooleanField
    RegexField
    SlugField
    TimeField
    URLField
    UUIDField
    ComboField
    MultiValueField
    SplitDateTimeField
    ModelChoiceField
    ModelMultipleChoiceField
        
### 常用验证器：

    在验证某个字段的时候，可以传递一个validators参数用来指定验证器，进一步对数据进行过滤。验证器有很多，但是很多验证器我们其实已经通过这个Field或者一些参数就可以指定了。比如EmailValidator，我们可以通过EmailField来指定，比如MaxValueValidator，我们可以通过max_value参数来指定。以下是一些常用的验证器：
    
    + MaxValueValidator：验证最大值。
    + MinValueValidator：验证最小值。
    + MinLengthValidator：验证最小长度。
    + MaxLengthValidator：验证最大长度。
    + EmailValidator：验证是否是邮箱格式。
    + URLValidator：验证是否是URL格式。
    + RegexValidator：如果还需要更加复杂的验证，
那么我们可以通过正则表达式的验证器：RegexValidator。比如现在要验证手机号码是否合格，那么我们可以通过以下代码实现：

    ```python
    from django.core import validators
    class MyForm(forms.Form):
        telephone = forms.CharField(validators=[validators.RegexValidator("1[345678]\d{9}",message='请输入正确格式的手机号码！')])
    ```
### 自定义验证：

    有时候对一个字段验证，不是一个长度，一个正则表达式能够写清楚的，还需要一些其他复杂的逻辑，那么我们可以对某个字段，进行自定义的验证。比如在注册的表单验证中，我们想要验证手机号码是否已经被注册过了，那么这时候就需要在数据库中进行判断才知道。对某个字段进行自定义的验证方式是，定义一个方法，这个方法的名字定义规则是：clean_fieldname。如果验证失败，那么就抛出一个验证错误。比如要验证用户表中手机号码之前是否在数据库中存在，那么可以通过以下代码实现：
    ```python
    class MyForm(forms.Form):
        telephone = forms.CharField(validators=[validators.RegexValidator("1[345678]\d{9}",message='请输入正确格式的手机号码！')])
        
        def clean_telephone(self):
            telephone = self.cleaned_data.get('telephone')
            exists = User.objects.filter(telephone=telephone).exists()
            if exists:
                raise forms.ValidationError("手机号码已经存在！")
            return telephone
    以上是对某个字段进行验证，如果验证数据的时候，需要针对多个字段进行验证，那么可以重写clean方法。比如要在注册的时候，要判断提交的两个密码是否相等。那么可以使用以下代码来完成：
    
    class MyForm(forms.Form):
        telephone = forms.CharField(validators=[validators.RegexValidator("1[345678]\d{9}",message='请输入正确格式的手机号码！')])
        pwd1 = forms.CharField(max_length=12)
        pwd2 = forms.CharField(max_length=12)
        
        def clean(self):
            cleaned_data = super().clean()
            pwd1 = cleaned_data.get('pwd1')
            pwd2 = cleaned_data.get('pwd2')
            if pwd1 != pwd2:
                raise forms.ValidationError('两个密码不一致！')
    提取错误信息：
    如果验证失败了，那么有一些错误信息是我们需要传给前端的。这时候我们可以通过以下属性来获取：
    
    form.errors：这个属性获取的错误信息是一个包含了html标签的错误信息。
    form.errors.get_json_data()：这个方法获取到的是一个字典类型的错误信息。将某个字段的名字作为key，错误信息作为值的一个字典。
    form.as_json()：这个方法是将form.get_json_data()返回的字典dump成json格式的字符串，方便进行传输。
    上述方法获取的字段的错误值，都是一个比较复杂的数据。比如以下：
    {'username': [{'message': 'Enter a valid URL.', 'code': 'invalid'}, {'message': 'Ensure this value has at most 4 characters (it has 22).', 'code': 'max_length'}]}
    那么如果我只想把错误信息放在一个列表中，而不要再放在一个字典中。这时候我们可以定义一个方法，把这个数据重新整理一份。实例代码如下：
    
    class MyForm(forms.Form):
        username = forms.URLField(max_length=4)
        
        def get_errors(self):
            errors = self.errors.get_json_data()
            new_errors = {}
            for key,message_dicts in errors.items():
                messages = []
                for message in message_dicts:
                    messages.append(message['message'])
                new_errors[key] = messages
            return new_errors
    这样就可以把某个字段所有的错误信息直接放在这个列表中。
    ```

### ModelForm：

    大家在写表单的时候，会发现表单中的Field和模型中的Field基本上是一模一样的，而且表单中需要验证的数据，也就是我们模型中需要保存的。那么这时候我们就可以将模型中的字段和表单中的字段进行绑定。
    比如现在有个Article的模型。示例代码如下：
    ```python
    from django.db import models
    from django.core import validators
    class Article(models.Model):
        title = models.CharField(max_length=10,validators=[validators.MinLengthValidator(limit_value=3)])
        content = models.TextField()
        author = models.CharField(max_length=100)
        category = models.CharField(max_length=100)
        create_time = models.DateTimeField(auto_now_add=True)
    那么在写表单的时候，就不需要把Article模型中所有的字段都一个个重复写一遍了。示例代码如下：
    
    from django import forms
    class MyForm(forms.ModelForm):
        class Meta:
            model = Article
            fields = "__all__"
    MyForm是继承自forms.ModelForm，然后在表单中定义了一个Meta类，在Meta类中指定了model=Article，以及fields="__all__"，这样就可以将Article模型中所有的字段都复制过来，进行验证。如果只想针对其中几个字段进行验证，那么可以给fields指定一个列表，将需要的字段写进去。比如只想验证title和content，那么可以使用以下代码实现：
    
    from django import forms
    class MyForm(forms.ModelForm):
        class Meta:
            model = Article
            fields = ['title','content']
    如果要验证的字段比较多，只是除了少数几个字段不需要验证，那么可以使用exclude来代替fields。比如我不想验证category，那么示例代码如下：
    
    class MyForm(forms.ModelForm):
        class Meta:
            model = Article
            exclude = ['category']
    自定义错误消息：
    使用ModelForm，因为字段都不是在表单中定义的，而是在模型中定义的，因此一些错误消息无法在字段中定义。那么这时候可以在Meta类中，定义error_messages，然后把相应的错误消息写到里面去。示例代码如下：

    class MyForm(forms.ModelForm):
        class Meta:
            model = Article
            exclude = ['category']
            error_messages  ={
                'title':{
                    'max_length': '最多不能超过10个字符！',
                    'min_length': '最少不能少于3个字符！'
                },
                'content': {
                    'required': '必须输入content！',
                }
            }
    ```
    save方法：
    ModelForm还有save方法，可以在验证完成后直接调用save方法，就可以将这个数据保存到数据库中了。示例代码如下：
    ```python
    form = MyForm(request.POST)
    if form.is_valid():
        form.save()
        return HttpResponse('succes')
    else:
        print(form.get_errors())
        return HttpResponse('fail')
    ```
    这个方法必须要在clean没有问题后才能使用，如果在clean之前使用，会抛出异常。另外，我们在调用save方法的时候，如果传入一个commit=False，那么只会生成这个模型的对象，而不会把这个对象真正的插入到数据库中。比如表单上验证的字段没有包含模型中所有的字段，这时候就可以先创建对象，再根据填充其他字段，把所有字段的值都补充完成后，再保存到数据库中。示例代码如下：
    ```python
    form = MyForm(request.POST)
    if form.is_valid():
        article = form.save(commit=False)
        article.category = 'Python'
        article.save()
        return HttpResponse('succes')
    else:
        print(form.get_errors())
        return HttpResponse('fail')
    ```

## Cookie 和 Session

### cookie

    在网站中，http请求是无状态的。也就是说即使第一次和服务器连接后并且登录成功后，第二次请求服务器依然不能知道当前请求是哪个用户。cookie的出现就是为了解决这个问题，第一次登录后服务器返回一些数据（cookie）给浏览器，然后浏览器保存在本地，当该用户发送第二次请求的时候，就会自动的把上次请求存储的cookie数据自动的携带给服务器，服务器通过浏览器携带的数据就能判断当前用户是哪个了。cookie存储的数据量有限，不同的浏览器有不同的存储大小，但一般不超过4KB。因此使用cookie只能存储一些小量的数据。

### session 
    
    session和cookie的作用有点类似，都是为了存储用户相关的信息。不同的是，cookie是存储在本地浏览器，session是一个思路、一个概念、一个服务器存储授权信息的解决方案，不同的服务器，不同的框架，不同的语言有不同的实现。虽然实现不一样，但是他们的目的都是服务器为了方便存储数据的。session的出现，是为了解决cookie存储数据不安全的问题的。

### 存储方式

* 存储在服务端 

    通过cookie存储一个sessionid，然后具体的数据则是保存在session中。如果用户已经登录，则服务器会在cookie中保存一个sessionid，下次再次请求的时候，会把该sessionid携带上来，服务器根据sessionid在session库中获取用户的session数据。就能知道该用户到底是谁，以及之前保存的一些状态信息。这种专业术语叫做server side session。Django把session信息默认存储到数据库中
* 存储到缓存中文件系统中等

    存储在服务器的数据会更加的安全，不容易被窃取。但存储在服务器也有一定的弊端，就是会占用服务器的资源，但现在服务器已经发展至今，一些session信息还是绰绰有余的。
* 将session数据加密存储

    将session数据加密，然后存储在cookie中。这种专业术语叫做client side session。flask框架默认采用的就是这种方式，但是也可以替换成其他形式。


### 在django中操作cookie

* 设置cookie

    设置cookie是设置值给浏览器的。因此我们需要通过response的对象来设置，设置cookie可以通过response.set_cookie来设置，这个方法的相关参数如下：
    ```
    rep = HttpResponse(...)
    rep ＝ render(request, ...)

    rep.set_cookie(key,value,...)
    rep.set_signed_cookie(key,value,salt='加密盐', max_age=None, ...)
    ```
    * key：这个cookie的key。
    * value：这个cookie的value。
    * max_age：最长的生命周期。单位是秒。
    * expires：过期时间。跟max_age是类似的，只不过这个参数需要传递一个具体的日期，比如datetime或者是符合日期格式的字符串。如果同时设置了expires和max_age，那么将会使用expires的值作为过期时间。
    * path：对域名下哪个路径有效。默认是对域名下所有路径都有效。
    * domain：针对哪个域名有效。默认是针对主域名下都有效，如果只要针对某个子域名才有效，那么可以设置这个属性.
    * secure：是否是安全的，如果设置为True，那么只能在https协议下才可用。
    * httponly：默认是False。如果为True，那么在客户端不能通过JavaScript进行操作。

* 删除cookie

    通过delete_cookie即可删除cookie。实际上删除cookie就是将指定的cookie的值设置为空的字符串，然后使用将他的过期时间设置为0，也就是浏览器关闭后就过期。
    ```
    def logout(request):
        rep = redirect("/login/")
        rep.delete_cookie("user")  # 删除用户浏览器上之前设置的usercookie值
        return rep
    ```

* 获取cookie

    获取浏览器发送过来的cookie信息。可以通过request.COOKIES来或者。这个对象是一个字典类型。比如获取所有的cookie，那么示例代码如下：
    ```
    request.COOKIES['key']
    request.COOKIES.get('Key')
    request.get_signed_cookie(key, default=RAISE_ERROR, salt='', max_age=None)
    ```
    * default: 默认值
    * salt: 加密盐
    * max_age: 后台控制过期时间

    获取浏览器发送过来的cookie信息。可以通过request.COOKIES来或者。这个对象是一个字典类型。比如获取所有的cookie，那么示例代码如下：
    ```python
    cookies = request.COOKIES
    for cookie_key,cookie_value in cookies.items():
       print(cookie_key,cookie_value)

    ```

### 在django中操作session

django中的session默认情况下是存储在服务器的数据库中的，在表中会根据sessionid来提取指定的session数据，然后再把这个sessionid放到cookie中发送给浏览器存储，浏览器下次在向服务器发送请求的时候会自动的把所有cookie信息都发送给服务器，服务器再从cookie中获取sessionid，然后再从数据库中获取session数据。但是我们在操作session的时候，这些细节压根就不用管。我们只需要通过request.session即可操作。示例代码如下：
```
def index(request):
   request.session.get('username')
   return HttpResponse('index')
```
* 常用参数

    * get：用来从session中获取指定值。
    * pop：从session中删除一个值。
    * keys：从session中获取所有的键。
    * items：从session中获取所有的值。
    * clear：清除当前这个用户的session数据。
    * flush：删除session并且删除在浏览器中存储的session_id，一般在注销的时候用得比较多。
    * set_expiry(value)：设置过期时间。
    * clear_expired：清除过期的session。Django并不会清除过期的session，需要定期手动的清理，或者是在终端，使用命令行python manage.py clearsessions来清除过期的session。
    * 整形：代表秒数，表示多少秒后过期

        0：代表只要浏览器关闭，session就会过期。
        None：会使用全局的session配置。在settings.py中可以设置SESSION_COOKIE_AGE来配置全局的过期时间。默认是1209600秒，也就是2周的时间。

* 修改session的存储机制

    默认情况下，session数据是存储到数据库中的。当然也可以将session数据存储到其他地方。可以通过设置SESSION_ENGINE来更改session的存储位置，这个可以配置为以下几种方案：
    ```python
    django.contrib.sessions.backends.db：使用数据库。默认就是这种方案。
    django.contrib.sessions.backends.file：使用文件来存储session。
    django.contrib.sessions.backends.cache：使用缓存来存储session。想要将数据存储到缓存中，前提是你必须要在settings.py中配置好CACHES，并且是需要使用Memcached，而不能使用纯内存作为缓存。
    django.contrib.sessions.backends.cached_db：在存储数据的时候，会将数据先存到缓存中，再存到数据库中。这样就可以保证万一缓存系统出现问题，session数据也不会丢失。在获取数据的时候，会先从缓存中获取，如果缓存中没有，那么就会从数据库中获取。
    django.contrib.sessions.backends.signed_cookies：将session信息加密后存储到浏览器的cookie中。这种方式要注意安全，建议设置SESSION_COOKIE_HTTPONLY=True，那么在浏览器中不能通过js来操作session数据，并且还需要对settings.py中的SECRET_KEY进行保密，因为一旦别人知道这个SECRET_KEY，那么就可以进行解密。另外还有就是在cookie中，存储的数据不能超过4k。
    ```
* session的操作

    * 获取session

        ```
        request.session['k1']
        request.session.get('k1',None)
        ```

        #获取session发生的三件事
        * django自动获取浏览器随机字符串取django session表里面比对
        * 如果比对成功 会将当前随机字符串对应的数据赋值给request.session
        * 通过request.session操作该数据(数据不存在也不会影响我们的业务逻辑)

    * 设置session

        ```
        request.session['k1'] = 123
        request.session.setdefault('k1',123) # 存在则不设置
        ```
        #设置session发生的三件事
        * 先生成一个随机的字符串
        * 在django session表中存储该随机字符串与数据的记录
        * 将随机的字符串发送给客户端浏览器（浏览器会设置一个键为sessionid来存放session）
        

## 中间件 和 上下文处理器 

### 上下文处理器

1. 上下文处理器返回数据

    上下文处理器是可以返回一些数据，在全局模板中都可以使用。比如登录后的用户信息，在很多页面中都需要使用，那么我们可以放在上下文处理器中，就没有必要在每个视图函数中都返回这个对象。

    在```settings.TEMPLATES.OPTIONS.context_processors```中，有许多内置的上下文处理器。这些上下文处理器的作用如下：

    - django.template.context_processors.debug：增加一个debug和sql_queries变量。在模板中可以通过他来查看到一些数据库查询。
    - django.template.context_processors.request：增加一个request变量。这个request变量也就是在视图函数的第一个参数。
    - django.contrib.auth.context_processors.auth：Django有内置的用户系统，这个上下文处理器会增加一个user对象。
    - django.contrib.messages.context_processors.messages：增加一个messages变量。
    - django.template.context_processors.media：在模板中可以读取MEDIA_URL。比如想要在模板中使用上传的文件，那么这时候就需要使用settings.py中设置的MEDIA_URL来拼接url。示例代码如下： ```<img src="" />```
    - django.template.context_processors.static：在模板中可以使用STATIC_URL。
    - django.template.context_processors.csrf：在模板中可以使用csrf_token变量来生成一个csrf token。

2. 自定义上下文处理器

    有时候我们想要返回自己的数据。那么这时候我们可以自定义上下文处理器。自定义上下文处理器的步骤如下：

    你可以根据这个上下文处理器是属于哪个app，然后在这个app中创建一个文件专门用来存储上下文处理器。比如你可以根据这个上下文处理器是属于哪个app，然后在这个app中创建一个文件专门用来存储上下文处理器。比如context_processors.py。或者是你也可以专门创建一个Python包，用来存储所有的上下文处理器。
    在你定义的上下文处理器文件中，定义一个函数，这个函数只有一个request参数。这个函数中处理完自己的逻辑后，把需要返回给模板的数据，通过字典的形式返回。如果不需要返回任何数据，那么也必须返回一个空的字典。示例代码如下：
    ```python
    def frontuser(request):
        userid = request.session.get("userid")
        userModel = models.FrontendUser.objects.filter(pk=userid).first()
        if userModel:
            return {'frontuser':userModel}
        else:
            return {}
    ```

    编写好这个函数之后，我们还需要在settings.py中去将我们的上下文处理器添加进去。
    在settings.py.TEMPLATES.OPTIONS.context_processors
    然后添加我们自定义的上下文处理器。
    #appName.定义的上下文件.定义的函数名
    user.context_processors.frontuser
    在模板中直接使用上面定义的变量{{ frontuser }}

### 中间件

中间件是在```request```和```response```处理过程中的一个插件。比如在```request```到达视图函数之前，我们可以使用中间件来做一些相关的事情，比如可以判断当前这个用户有没有登录，如果登录了，就绑定一个user对象到```request```上。也可以在```response```到达浏览器之前，做一些相关的处理，比如想要统一在```response```上设置一些cookie信息等。

1. 自定义中间件

    中间件所处的位置没有规定。只要是放到项目当中即可。一般分为两种情况，如果中间件是属于某个app的，那么可以在这个app下面创建一个python文件用来存放这个中间件，也可以专门创建一个Python包，用来存放本项目的所有中间件。创建中间件有两种方式，一种是使用函数，一种是使用类，接下来对这两种方式做个介绍：
    * 使用函数的中间件

    ```python
    def simple_middleware(get_response):
      # 这个中间件初始化的代码

      def middleware(request):
          # request到达view的执行代码

          response = get_response(request)

          # response到达浏览器的执行代码

          return response

      return middleware
    ```

    * 使用类的中间件

    ```python
    class SimpleMiddleware(object):
      def __init__(self, get_response):
          self.get_response = get_response
          # 这个中间件初始化的代码
          def __call__(self, request):
              # request到达view之前执行的代码

              response = self.get_response(request)

              # response到达用户浏览器之前执行的代码

              return response
    ```

    * 应用中间件

    在写完中间件后，还需要在```settings.MIDDLEWARES```中配置写好的中间件才可以使用。比如我们写了一个在request到达视图函数之前，判断这个用户是否登录，如果已经登录就绑定一个user对象到request上的中间件，这个中间件放在当前项目的middlewares.users下：
    ```python
    def user_middleware(get_response):
      #这个中间件初始化的代码

      def middleware(request):
          # request到达view的执行代码
          userid = request.session.get("userid")
          userModel = FrontUser.objects.filter(pk=userid).first()
          if userModel:
                  setattr(request,'frontuser',userModel)

          response = get_response(request)

          # response到达浏览器的执行代码

          return response

      return middleware
    ```
    那么就可以在settings.MIDDLEWARES下做以下配置：
    ```python
    MIDDLEWARE = [
        'django.middleware.security.SecurityMiddleware',
        'django.contrib.sessions.middleware.SessionMiddleware',
        'django.middleware.common.CommonMiddleware',
        'django.middleware.csrf.CsrfViewMiddleware',
        'django.contrib.auth.middleware.AuthenticationMiddleware',
        'django.contrib.messages.middleware.MessageMiddleware',
        'django.middleware.clickjacking.XFrameOptionsMiddleware',
        'middlewares.users.user_middleware'
    ]
    ```
    中间件的执行是有顺序的，他会根据在MIDDLEWARE中存放的顺序来执行。因此如果有些中间件是需要基于其他中间件的，那么就需要放在其他中间件的后面来执行。

2. Django内置中间件

    1. django.middleware.common.CommonMiddleware：通用中间件。他的作用如下：
        - 限制settings.DISALLOWED_USER_AGENTS中指定的请求头来访问本网站。DISALLOWED_USER_AGENT是一个正则表达式的列表。示例代码如下：
        ```python
          import re
          DISALLOWED_USER_AGENTS = [
              re.compile(r'^\s$|^$'),
              re.compile(r'.*PhantomJS.*')
          ]
        ```
        - 如果开发者在定义url的时候，最后有一个斜杠。但是用户在访问url的时候没有提交这个斜杠，那么CommonMiddleware会自动的重定向到加了斜杠的url上去。
    2. django.middleware.gzip.GZipMiddleware：将响应数据进行压缩。如果内容长度少于200个长度，那么就不会压缩。
    3. django.contrib.messages.middleware.MessageMiddleware：消息处理相关的中间件。
    4. django.middleware.security.SecurityMiddleware：做了一些安全处理的中间件。比如设置XSS防御的请求头，比如做了http协议转https协议的工作等。
    5. django.contrib.sessions.middleware.SessionMiddleware：session中间件。会给request添加一个处理好的session对象。
    6. django.contrib.auth.middleware.AuthenticationMiddleware：会给request添加一个user对象的中间件。
    7. django.middleware.csrf.CsrfViewMiddleware：CSRF保护的中间件。
    8. django.middleware.clickjacking.XFrameOptionsMiddleware：做了clickjacking攻击的保护。clickjacking保护是攻击者在自己的病毒网站上，写一个诱惑用户点击的按钮，然后使用iframe的方式将受攻击的网站（比如银行网站）加载到自己的网站上去，并将其设置为透明的，用户就看不到，然后再把受攻击的网站（比如银行网站）的转账按钮定位到病毒网站的按钮上，这样用户在点击病毒网站上按钮的时候，实际上点击的是受攻击的网站（比如银行网站）上的按钮，从而实现了在不知不觉中给攻击者转账的功能。
    9. 缓存中间件：用来缓存一些页面的。
    ```python
    django.middleware.cache.UpdateCacheMiddleware。
    django.middleware.cache.FetchFromCacheMiddleware。 
    ```   

3. 内置中间件放置的顺序

    1. SecurityMiddleware：应该放到最前面。因为这个中间件并不需要依赖任何其他的中间件。如果你的网站同时支持http协议和https协议，并且你想让用户在使用http协议的时候重定向到https协议，那么就没有必要让他执行下面一大串中间件再重定向，这样效率更高。
    2. UpdateCacheMiddleware：应该在SessionMiddleware, GZipMiddleware, 
    3. LocaleMiddleware之前。
    4. GZipMiddleware。
    5. ConditionalGetMiddleware。
    6. SessionMiddleware。
    7. LocaleMiddleware。
    8. CommonMiddleware。
    9. CsrfViewMiddleware。
    10. AuthenticationMiddleware。
    11. MessageMiddleware。
    12. FetchFromCacheMiddleware。
    13. FlatpageFallbackMiddleware。
    14. RedirectFallbackMiddleware。


## CSRF
    未做记录

## 验证和授权

### 验证和授权概述

Django有一个内置的授权系统。他用来处理用户、分组、权限以及基于cookie的会话系统。Django的授权系统包括验证和授权两个部分。验证是验证这个用户是否是他声称的人（比如用户名和密码验证，角色验证），授权是给与他相应的权限。Django内置的权限系统包括以下方面：

+ 用户。
+ 权限。
+ 分组。
+ 一个可以配置的密码哈希系统。
+ 一个可插拔的后台管理系统。
+ 使用授权系统：
默认中创建完一个django项目后，其实就已经集成了授权系统。那哪些部分是跟授权系统相关的配置呢。以下做一个简单列表：
```python
INSTALLED_APPS：
django.contrib.auth：包含了一个核心授权框架，以及大部分的模型定义。
django.contrib.contenttypes：Content Type系统，可以用来关联模型和权限。
中间件：
SessionMiddleware：用来管理session。
AuthenticationMiddleware：用来处理和当前session相关联的用户。
```

### User模型

User模型是这个框架的核心部分。他的完整的路径是在django.contrib.auth.models.User。以下对这个User对象做一个简单了解：

1. 字段：

    内置的User模型拥有以下的字段：

    * username： 用户名。150个字符以内。可以包含数字和英文字符，以及_、@、+、.和-字符。不能为空，且必须唯一！
    * first_name：歪果仁的first_name，在30个字符以内。可以为空。
    * last_name：歪果仁的last_name，在150个字符以内。可以为空。
    * email：邮箱。可以为空。
    * password：密码。经过哈希过后的密码。
    * groups：分组。一个用户可以属于多个分组，一个分组可以拥有多个用户。groups这个字段是跟Group的一个多对多的关系。
    * user_permissions：权限。一个用户可以拥有多个权限，一个权限可以被多个用户所有用。和Permission属于一种多对多的关系。
    * is_staff：是否可以进入到admin的站点。代表是否是员工。
    * is_active：是否是可用的。对于一些想要删除账号的数据，我们设置这个值为False就可以了，而不是真正的从数据库中删除。
    * is_superuser：是否是超级管理员。如果是超级管理员，那么拥有整个网站的所有权限。
    * last_login：上次登录的时间。
    * date_joined：账号创建的时间。

2. User模型的基本用法：

    * 创建用户：
    通过create_user方法可以快速的创建用户。这个方法必须要传递username、email、password。示例代码如下：
    ```python
    from django.contrib.auth.models import User
    user = User.objects.create_user('ynsy','hynever@ynsy.com','111111')
    # 此时user对象已经存储到数据库中了。当然你还可以继续使用user对象进行一些修改
    user.last_name = 'abc'
    user.save()
    ```
    * 创建超级用户：
    创建超级用户有两种方式。第一种是使用代码的方式。用代码创建超级用户跟创建普通用户非常的类似，只不过是使用create_superuser。示例代码如下：
    ```python
    from django.contrib.auth.models import User
    User.objects.create_superuser('admin','admin@163.com','111111')
    ```
    也可以通过命令行的方式。命令如下：
    ```python
    python manage.py createsuperuser
    ```
    后面就会提示你输入用户名、邮箱以及密码。

    * 修改密码：
    因为密码是需要经过加密后才能存储进去的。所以如果想要修改密码，不能直接修改password字段，而需要通过调用set_password来达到修改密码的目的。示例代码如下：
    ```python
    from django.contrib.auth.models import User
    user = User.objects.get(pk=1)
    user.set_password('新的密码')
    user.save()
    ```

    * 登录验证：

    Django的验证系统已经帮我们实现了登录验证的功能。通过django.contrib.auth.authenticate即可实现。这个方法只能通过username和password来进行验证。示例代码如下：
    ```python
    from django.contrib.auth import authenticate
    user = authenticate(username='ynsy', password='111111')
    # 如果验证通过了，那么就会返回一个user对象。
    if user is not None:
        # 执行验证通过后的代码
    else:
        # 执行验证没有通过的代码。
    ```

### 扩展用户模型

Django内置的User模型虽然已经足够强大了。但是有时候还是不能满足我们的需求。比如在验证用户登录的时候，他用的是用户名作为验证，而我们有时候需要通过手机号码或者邮箱来进行验证。还有比如我们想要增加一些新的字段。那么这时候我们就需要扩展用户模型了。扩展用户模型有多种方式。这里我们来一一讨论下。

1. 设置Proxy模型

    如果你对Django提供的字段，以及验证的方法都比较满意，没有什么需要改的。但是只是需要在他原有的基础之上增加一些操作的方法。那么建议使用这种方式。示例代码如下：
    ```python
    class Person(User):
        class Meta:
            proxy = True

        def get_blacklist(self):
            return self.objects.filter(is_active=False)
    ```
    在以上，我们定义了一个Person类，让他继承自User，并且在Meta中设置proxy=True，说明这个只是User的一个代理模型。他并不会影响原来User模型在数据库中表的结构。以后如果你想方便的获取所有黑名单的人，那么你就可以通过Person.get_blacklist()就可以获取到。并且User.objects.all()和Person.objects.all()其实是等价的。因为他们都是从User这个模型中获取所有的数据。

    注意：如果模型是一个代理模型，不能再这个模型中添加新的Field , 但是可以添加新的属性，局限性较小，不灵活

    视图函数调用:
    ```python
    from  .models import Person
    def index():
        blackList = Person.get_blacklist()
        for persion in blackList:
            print(persion)
        return HttpResponse("proxy")
    ```

2. 一对一外键

    如果你对用户验证方法authenticate没有其他要求，就是使用username和password即可完成。但是想要在原来模型的基础之上添加新的字段，那么可以使用一对一外键的方式。示例代码如下：
    ```python
    from django.db import models
    from django.contrib.auth.models import User
    class UserExtension(models.Model):
        """扩展数据,并指定为OneToOneField的方式"""
        user = models.OneToOneField(User,on_delete=models.CASCADE,related_name='extension')
        telephone = models.CharField(max_length=11)
        schoole = models.CharField(max_length=100)

    from django.dispatch import receiver #receiver一个装饰器接收某个信号，在执行相应的函数
    from django.db.models.signals import post_save #在调用save方法的时候，发送的信号

    #指定receiver接收的信号类型post_save信号，并通过sender指定接收那个方法的信息，sender=User接收User方法的信号
    #编写函数create_user_extension指定接收到信号以后做什么样的操作()
    @receiver(post_save,sender=User)
    def create_user_extension(sender,instance,created,**kwargs):
        #指定的参数是固定的
        if created:
            UserExtension.objects.create(user=instance)
        else:
            instance.extension.save()
    ```
    配置以后需要使用执行数据库生成方式，重新生成sql
    ```python
    python manage.py makemigrations
    python manage.py migrate
    ```
    执行完成以后数据库里面会生成表UserExtension,并且创建和User一对一的关系映射

    函数中使用方式
    ```python
    def one_view(request):
        user = User.objects.create_user(username="ceshi",password="11111")
        user.extension.telephone = '13641230629'
        user.save()
        return HttpResponse("success")
    在调用User save方法的时候,会直接添加对应的数据到UserExtension表中
    ```
    注意：这种方式只能添加字段，不能修改原始的验证方法，可以自定义验证方法
    ```python
    def my_authenticate(telephone,password):
        user = User.objects.filter(extension__telephone=telephone).first()
        if user:
            is_correct = user.check_password(password)
            if is_correct:
                return user
            else:
                return None
        else:
            return None

    def one_view(request):
        user = User.objects.create_user(username="ceshi",password="11111")
        user.extension.telephone = '13641230629'
        user.save()
        telephone = request.GET.get('telephone')
        password = request.GET.get('password')
        user = my_authenticate(telephone,password)
        if user:
            print("验证成功")
        else:
            print("验证失败")
        return HttpResponse("success")
    ```
3. 继承自AbstractUser

* 基础AbstractUser创建User对象

    在不想要修改原来User对象上的一些字段，但是想要增加一些字段，那么这时候可以直接继承自django.contrib.auth.models.AbstractUser，其实这个类也是django.contrib.auth.models.User的父类。比如我们想要在原来User模型的基础之上添加一个telephone和school字段。示例代码如下：
    ```python
    from django.contrib.auth.models import AbstractUser
    class User(AbstractUser):
        telephone = models.CharField(max_length=11,unique=True)
        school = models.CharField(max_length=100)

        # 指定telephone作为USERNAME_FIELD，以后使用authenticate
        # 函数验证的时候，就可以根据telephone来验证
        # 而不是原来的username
        USERNAME_FIELD = 'telephone'
        REQUIRED_FIELDS = []
    ```
    注意：需要在settings中配置好```AUTH_USER_MODEL=youapp.User```。
    这种方式只能在Django第一个运行migrate的时候提前定义好，如果已经运行过migrate生成过表模型这种方式就会报错

* 重构UserManager

    在创建User对象以后，默认继承了AbstractUser,并没有修改object的对象，所以在创建用户的时候还必须要指定username,email,password字段，如果需要指定自己创建字段信息需要自定你UserManager方法
    ```python
    from django.contrib.auth.models import AbstractUser,BaseUserManager

    class UserManager(BaseUserManager):
        def _create_user(self,telephone,username,password,**kwargs):
            if not  telephone:
                raise ValueError("必须传递手机号")
            if not password:
                raise ValueError("必须传递密码")
            user = self.model(telephone=telephone,username=username,**kwargs)
            user.set_password(password)
            user.save()
            return user

        def create_user(self,telephone,username,password,**kwargs):
            kwargs['is_superuser'] =  False
            return self._create_user(telephone=telephone,username=username,password=password,**kwargs)

        def create_superuser(self,telephone,username,password,**kwargs):
            kwargs['is_superuser'] =  True
            return self._create_user(telephone=telephone,username=username,password=password,**kwargs)

    class User(AbstractUser):
        telephone = models.CharField(max_length=11,unique=True)
        school = models.CharField(max_length=100)

        #指定telephone作为USERNAME_FIELD，以后使用authenticate的时候作为验证条件
        #函数验证的时候，就可以根据telephone验证，而不是原来的username
        USERNAME_FIELD = 'telephone'
        REQUIRED_FIELDS = []

        #重新定义Manager对象，在创建user的时候使用telephone和
        #password，而不是使用username和password
        objects = UserManager

    ```

* authenticate验证

    对于Django自带的验证authenticate验证模型,默认使用username=username,
    通过指定USERNAME_FIELD来确定验证的字段信息
    ```python
    #from yourapp.models import User

    from users.models import User
    def inherit_view(request):
        telephone = '17788889999'
        password = '111111'
        username = 'cehsi'
        user = User.objects.create_user(telephone=telephone,username=username,password=password)
        print(user.username)
        return HttpResponse("继承AbstractUser扩展用户")
    ```

    检验验证方式
    ```python
    from django.contrib.auth import authenticate
    def auth(reqiest):
        telephone = '17788889999'
        #此处username 并不是User对象里面的username字段，而是Django内置的一个验证名，这个需要验证的内容可以是自己定义的任何字段这里使用的是telephone
        user = authenticate(reqiest,username=telephone,password='111111')
        if user:
            print("验证成功")
        else:
            print("验证失败")
        return HttpResponse("successs")
    ```

4. 继承自AbstracBaseUser 模型

    如果想修改默认的验证方式，并且对原来User模型上的一些字段不想要，
那么可以自定义一个模型然后继承自AbstracBaseUser,在添加你想要的字段，
这种方式比较麻烦，最好是确定自己对Django比较了解才推荐使用。

* 创建模型。示例代码如下：
```python
class User(AbstractBaseUser,PermissionsMixin):
    email = models.EmailField(unique=True)
    username = models.CharField(max_length=150)
    telephone = models.CharField(max_length=11,unique=True)
    is_active = models.BooleanField(default=True)

    USERNAME_FIELD = 'telephone'
    REQUIRED_FIELDS = ['username']
    EMAIL_FILED = 'email'

    objects = UserManager()

    def get_full_name(self):
        return self.username

    def get_short_name(self):
        return self.username
```
    其中password和last_login是在AbstractBaseUser中已经添加好了的，我们直接继承就可以了。然后我们再添加我们想要的字段。比如email、username、telephone等。这样就可以实现自己想要的字段了。但是因为我们重写了User，所以应该尽可能的模拟User模型：

    * USERNAME_FIELD：用来描述User模型名字字段的字符串，作为唯一的标识。如果没有修改，那么会使用USERNAME来作为唯一字段。必须设置。 设置认证标识，设置成标识的字段 unique=True
    * REQUIRED_FIELDS：一个字段名列表，用于当通过createsuperuser管理命令创建一个用户时的提示。
    * is_active：一个布尔值，用于标识用户当前是否可用。
    * get_full_name()：获取完整的名字。
    * get_short_name()：一个比较简短的用户名。

* 重新定义UserManager

   我们还需要定义自己的UserManager，因为默认的UserManager在创建用户的时候使用的是username和password，那么我们要替换成telephone。示例代码如下：
```python
 class UserManager(BaseUserManager):
     use_in_migrations = True

     def _create_user(self,telephone,password,**extra_fields):
         if not telephone:
             raise ValueError("请填入手机号码！")
         user = self.model(telephone=telephone,*extra_fields)
         user.set_password(password)
         user.save()
         return user

     def create_user(self,telephone,password,**extra_fields):
         extra_fields.setdefault('is_superuser',False)
         return self._create_user(telephone,password)

     def create_superuser(self,telephone,password,**extra_fields):
         extra_fields['is_superuser'] = True
         return self._create_user(telephone,password)
```
在创建了新的User模型后，还需要在settings中配置好。配置AUTH_USER_MODEL='appname.User'。

* 如何使用这个自定义的模型
    比如以后我们有一个Article模型，需要通过外键引用这个User模型，那么可以通过以下两种方式引用。
    - 直接将User导入到当前文件中。示例代码如下：
    ```python
     from django.db import models
     from myauth.models import User
     class Article(models.Model):
         title = models.CharField(max_length=100)
         content = models.TextField()
         author = models.ForeignKey(User, on_delete=models.CASCADE)
    ```

    这种方式是可以行得通的。但是为了更好的使用性，建议还是将User抽象出来，

    - 使用settings.AUTH_USER_MODEL来表示。示例代码如下：

    ```python
     from django.db import models
     from django.conf import settings
     class Article(models.Model):
         title = models.CharField(max_length=100)
         content = models.TextField()
         author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    ```

    - 通过函数调用的方式获取settings.AUTH_USER_MODEL的值返回
    ```python
    from django.contrib.auth import get_user_model
    #get_user_mode 函数模型提取setting文件中的AUTH_USER_MODEL值返回。作用可以动态获取用户模型
    class Article(models.Model):
        title = models.CharField(max_length=100)
        content = models.TextField()
        author = models.ForeignKey(get_user_model(),models.CASCADE)
    ```

注意：因为破坏了原来User模型的表结构，所以必须要在第一次migrate前就先定义好。



### 登录和分组

* 登录
    + 视图函数配置
    ```python
    from django.shortcuts import render
    #Create your views here.
    from django.shortcuts import render,redirect,reverse
    from django.http import HttpResponse
    #from django.contrib.auth.models import User
    from django.contrib.auth import authenticate
    from .models import User
    from .forms import LoginForm
    from django.contrib.auth import login,logout
    from django.contrib.auth.decorators import login_required,permission_required
    from django.contrib.auth.models import Permission,ContentType,Group
    #切记：这里一定不要定义login视图函数
    #可以其他的名字
    def my_login(request):
        if request.method == 'GET':
            return render(request,'login.html')
        else:
            form = LoginForm(request.POST)
            if form.is_valid():
                telephone = form.cleaned_data.get('telephone')
                password = form.cleaned_data.get('password')
                remember = form.cleaned_data.get('remember')
                user = authenticate(request,username=telephone,password=password)
                if user and user.is_active:
                    login(request,user)
                    if remember:
                        # 设置为None，则表示使用全局的过期时间
                        request.session.set_expiry(None)
                    else:
                        request.session.set_expiry(0)
                    next_url = request.GET.get('next')
                    if next_url:
                        return redirect(next_url)
                    else:
                        return HttpResponse('登录成功')
                else:
                    return HttpResponse('手机号码或者密码错误！')
            else:
                print(form.errors)
                return redirect(reverse('login'))
    ```
    + forms配置
    ```python
    #encoding: utf-8
    from django import forms
    from django.contrib.auth import get_user_model
    class LoginForm(forms.ModelForm):
        remember = forms.IntegerField(required=False)
        telephone = forms.CharField(max_length=11)
        class Meta:
            model = get_user_model()
            fields = ['password']
    ```
    + 模板文件
    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>登录</title>
    </head>
    <body>
    <form action="" method="POST">
        <input type="hidden" name="csrfmiddlewaretoken" value="{{ csrf_token }}">
        <table>
            <tbody>
                <tr>
                    <td>用户号码：</td>
                    <td><input type="text" name="telephone"></td>
                </tr>
                <tr>
                    <td>密码：</td>
                    <td><input type="password" name="password"></td>
                </tr>
                <tr>
                    <td>
                        <label for="">
                            <input type="checkbox" name="remember" value="1">记住我
                        </label>
                    </td>
                    <td></td>
                </tr>
                <tr>
                    <td></td>
                    <td><input type="submit" value="登录"></td>
                </tr>
            </tbody>
        </table>
    </form>
    </body>
    </html>
    ```

* 退出
```python
from django.contrib.auth import logout
def my_logout(request):
    logout(request)
    return HttpResponse('成功退出登录！')
```

* 登录限制
    ```python
    #函数视图
    from django.contrib.auth.decorators import login_required
    @login_required(login_url='/login/')
    def my_view(request)
        return HttpResponse('需要登录以后查看')
    #类视图函数
    @method_decorator(login_required, name='dispatch')
    class ProtectedView(TemplateView):
        template_name = 'secret.html'

    #如果modle 模型中启用了is_staff参数，限制只有内部员工可以登录的时候，可以使用下面这个方法
    from django.contrib.admin.views.decorators import staff_member_required

    @staff_member_required(login_url='index')
    def index(request):
        return render(request,'cms/index.html')    
        ```

* 权限
    * 权限解释
        以进行增删改查操作。他不能针对数据级别的，比如对某个表中的某条数据能否进行增删改查操作（如果要实现数据级别的，考虑使用django-guardian）。创建完一个模型后，针对这个模型默认就有三种权限，分别是增/删/改/。可以在执行完migrate命令后，查看数据库中的auth_permission表中的所有权限
        在auth_permission表中字段解释
        + codename: 表示的是权限的名字。
        + name: 表示的是这个权限的作用。
        + content_type_id: 表示关联到django_content_type表中对应的appName下某个模型的id
        + 通过Django源代码查看
        ```python
        from django.contrib.auth.models import Permission
        class Author(models.Model):
            name = models.CharField(_('name'), max_length=255)
            content_type = models.ForeignKey(
                ContentType,
                models.CASCADE,
                verbose_name=_('content type'),
            )
            codename = models.CharField(_('codename'), max_length=100)
            objects = PermissionManager()
        ```
    * 配置权限
        + 通过配置模型的是方式添加权限
        如果我们想要增加新的权限，比如查看某个模型的权限，那么我们可以在定义模型的时候在Meta中定义好。示例代码如下：

        ```python
        class Article(models.Model):
            title = models.CharField(max_length=100)
            content = models.TextField()
            author = models.ForeignKey(get_user_model(),models.CASCADE)

            class Meta:
                permissions = [
                    ('view_article','浏览文章的权限')
                ]
        ```
        
        + 通过代码方式添加权限
        权限都是django.contrib.auth.Permission的实例。这个模型包含三个字段，name、codename以及content_type，其中的content_type表示这个permission是属于哪个app下的哪个models。用Permission模型创建权限的代码如下：
        ```python
        from django.contrib.auth.models import Permission,ContentType
        from .models import Article
        def addPermission(request):
            content_type = ContentType.objects.get_for_model(Article)
            permission = Permission.objects.create(name='可以编辑的权限',codename='edit_article',content_type=content_type)
            return HttpResponse("添加权限成功")
        ```

    * 绑定权限

    权限本身只是一个数据，必须和用户进行绑定，才能起到作用。User模型和权限之间的管理，可以通过以下几种方式来管理：

        * myuser.user_permissions.set(permission_list)：直接给定一个权限的列表。
        * myuser.user_permissions.add(permission,permission,...)：一个个添加权限。
        * myuser.user_permissions.remove(permission,permission,...)：一个个删除权限。
        * myuser.user_permissions.clear()：清除权限。
        * myuser.has_perm('<app_name>.<codename>')：判断是否拥有某个权限。权限参数是一个字符串，格式是app_name.codename。
        * myuser.get_all_permissons()：获取所有的权限。
    示例代码如下：
    ```python
    def operate_permission(request):
        user = User.objects.first()
        content_type = ContentType.objects.get_for_model(Article)
        permissions = Permission.objects.filter(content_type=content_type)
        for permission in permissions:
            print(permission)
        #user.user_permissions.set(permissions)
        #user.save()
        #user.user_permissions.clear() #清除权限
        if user.has_perm('front.view_article'):
            print("判断是否有拥有view_article权限")
        print(user.get_all_permissons()) #打印拥有的所有权限
        return HttpResponse("操作权限的视图")
    ```
    注意：权限和用户对应以后可以通过appName_user_user_permissions表查看具体的对应关系

    * 权限限定
        + 通过代码方式实现
            ```python
            def add_article(request):
                #判断用户是否登录
                if request.user.is_authenticated:
                    print("已经登录")
                    if request.user.has_perm('front.add_article'):
                        return HttpResponse("添加文章")
                    else:
                        return HttpResponse("没有添加文章的权限",status=403)
                else:
                    return HttpResponse('没有登录')

            ```
        + 通过装饰器方式实现
            ```python
            from django.contrib.auth.decorators import login_required,permission_required
            #permission_required装饰器，首先验证用户是否登录，然后验证是否有权限,通过login_url参数指定跳转的页面
            @permission_required('front.add_article',login_url='login')
            def add_article1(request):
                return HttpResponse("添加文章")
            ```

* 分组

    - 创建组
        ```python
        from django.contrib.auth.models import Group
        def operate_group(request):
            group = Group.objects.create(name='运维')
            content_type = ContentType.objects.get_for_model(Article)
            permissions = Permission.objects.filter(content_type=content_type)
            group.permissions.set(permissions)
            group.save()
            return HttpResponse('操作分组')
        ```
    - 把用户添加到某个组
        ```python
        def user_groups(request):
            group = Group.objects.filter(name='运维').first()
            user = User.objects.first()
            user.groups.add(group)
            user.save()
            return HttpResponse("添加用户到组")
        ```
    - 判断拥有的权限
        ```python
        def user_permission(request):
            user = User.objects.first()
            permissions = user.get_group_permissions()
            print(permissions)
            """
            user.has_pem:
            1. 首先判断user.permissions下有没有对应的权限，如果有，就返回True
            2. 如果user.permissions下没有对应的权限，那么会检查它所属的分组下有没有折腾个权限
            如果有返回True
            """
            if user.has_perm('front.add_article'):
                print("有权限")
            else:
                print("没有权限")
            return HttpResponse("判断是否拥有权限")
        ```

* 模板中配置访问权限

    在settings.TEMPLATES.OPTIONS.context_processors下，因为添加了django.contrib.auth.context_processors.auth上下文处理器，因此在模板中可以直接通过perms来获取用户的所有权限。示例代码如下：
    ```html
    {% if perms.front.add_article %}
        <a href='/article/add/'>添加文章</a>
    {% endif %}
    ```
    配置以后，在页面展示，如果没有对应的权限，就不展示相对的链接

## Celery

### celery
1. 安装

    ```python
    pip install celery
    pip install redis
    ```

2. 配置

    在 proj/myproj/ 路径下创建一个 celery.py 文件，用来初始化 Celery 实例

    ```python
    from __future__ import absolute_import, unicode_literals
    import os
    from celery import Celery
    from  django.conf import settings

    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'learn_django.settings')  # 设置django环境
    app = Celery('learn_django')
    app.config_from_object('django.conf:settings', namespace='CELERY') #  使用CELERY_ 作为前缀，在settings中写配置
    app.autodiscover_tasks(lambda: settings.INSTALLED_APPS)  # 发现任务文件每个app下的task.py
    ```

    然后在 proj/myproj/__init__.py 中添加对 Celery 对象的引用，确保 Django 启动后能够初始化 Celery：
    ```python
    from __future__ import absolute_import, unicode_literals
    from .celery import app as celery_app
    __all__ = ['celery_app']
    ```

    为了模拟一个耗时任务，我们直接创建一个方法，使其「睡」10s ，并将其设置为 Celery 的任务：
    proj/myapp/tasks.py
    ```shell
    import time
    from learn_django.celery import app as celery_app

    @celery_app.task
    def waste_time():
        time.sleep(10)
        return "Run function 'waste_time finished'. "

    @celery_app.task
    def waste_time(number):
        time.sleep(number)
        return "Run function 'waste_time finished'. "
    ```

3. 启动 Celery Worker

    Celery 配置完成，并且任务创建成功后，我们以异步任务的模式启动 Celery ：
    ```shell
    celery -A myproj worker -l info
    ```
    注意到我强调了异步模式，是因为 Celery 除了支持异步任务，还支持定时任务，因此启动时候要指明。
    同时要注意，Celery 一旦启动，对 Task(此处为 waste_time) 的修改必须重启 Celery 才会生效。

4. 任务调用

    在请求处理的逻辑代码中，调用上面创建好的任务：
    proj/myapp/views.py

    ```python
    from django.shortcuts import render
    from  django.http import HttpResponse
    from django.views.decorators.http import require_http_methods
    from .tasks import waste_time

    # Create your views here.

    def index(request):
        waste_time.delay()
        return HttpResponse("success")

    def index(request):
        waste_time.delay(20)
        return HttpResponse("success")
    
    from celery.result import AsyncResult
    taskId = waste_time.delay(20)
    res=AsyncResult(taskId) # 参数为task id
    res.result
    ```
通过导入AsyncResult 获取waste_time返回的执行内容
可以在配置waste_time 的时候接收参数，通过deplay调用的时候传入参数
调用 waste_time.delay() 方法后， waste_time 会被加入到任务队列中，等待空闲的 Celery Worker 调用。
5. 效果

    可以通过admin 后台管理查看执行状态


### django-celery-results 

还可以使用Django的orm作为结果存储，当然需要安装依赖插件，这样的好处在于我们可以直接通过django的数据查看到任务状态，同时为可以制定更多的操作，下面介绍如何使用orm作为结果存储

1. 安装

    ```python
    pip install django-celery-results 
    ```
2. settings.py

    ```python
    settings配置
    CELERY_RESULT_SERIALIZER = 'json' # 结果序列化方案
    CELERY_TASK_SERIALIZER = 'json'
    CELERY_RESULT_BACKEND = 'django-db'  #使用django orm 作为结果存储
    CELERYBEAT_SCHEDULER = 'django_celery_beat.schedulers:DatabaseScheduler'
    CELERY_ACCEPT_CONTENT = ['application/json']
    CELERY_BROKER_URL = 'redis://:{}@{}:{}/{}'.format('','127.0.0.1',6379,'0') #
    #password,ip,port,data
    注册INSTALLED_APPS
    INSTALLED_APPS = [
       ....
        'django_celery_results',
    ]
    ```
3. 修改数据库

    ```python
    python3 manage.py migrate django_celery_results
    ```

### django-celery-beat

如果想要在django中使用定时任务功能同样是靠beat完成任务发送功能，当在Django中使用定时任务时，需要安装django-celery-beat插件。

1. 安装

    ```python
    pip install django-celery-beat
    ```
2. settings.py

    ```python
    注册INSTALLED_APPS = [
    INSTALLED_APPS = [
       ....
        'django_celery_beat',
    ]
    ```
3.数据库变更

    ```python
    python3 manage.py migrate django_celery_beat
    ```

4. 分别启动woker和beta

    ```shell
    celery -A proj beat -l info   # 启动beta 调度器使用数据库

    celery worker -A taskproj -l info  #启动woke
    ```

## Logging

### logging中的四个组件
1.Loggers
用户使用的直接接口，将日志传递给Handler
2.Handlers
      控制日志输出到哪里，console，file...，一个logger可以有多个Handler
3.Filters
      控制哪些日志可以从logger流向Handler
4.Formatters
      控制日志的格式

1. Loggers(日志记录器)，系统中的每一条日志都是由该组件进行记录的，每一个记录器都应该有其自己的名称并标记其最低记录的等级；当一条日志给到logger时，logger会对该条信息的级别与自身的级别进行比较，如果该日志级别不低于本身级别，logger就会进行下一步操作。如果日志的信息级别比logger的低，那么就会忽略这条日志。
当logger经过级别的比较之后决定要对某条日志进行处理时，就将该条日志交给了Handlers
2. Handlers(处理器)，用来处理具体每条信息，例如是将日志打印到屏幕还是记录到文件或者发送至某个网络连接；它自己的记录级别，如果日志级别低于handler的级别，handler同样会忽略掉该条日志。
3. Filter(过滤器):提供了传递给handler之前的附加功能。在通常情况下，一条日志信息只要达到logger的级别之后就会传递给handler处理，但是我们可以通过使用filter来对日志进行额外的过滤。
例如我们可以使用某个filter来控制只允许某个特定的源的ERROR级别的日志。
Filter还运行咱们在处理之前修改日志，例如降低或者提高日志的级别。
Filter可以在logger和handler中同时使用，而且多个filter会同时工作。
4. Formatter(格式化):定义了怎么显示内容，因为最终的日志都会是以文本的形式展现，formatter就是描述怎么来做这件事。formatter通常都是使用python格式化字符串的方法来对日志进行格式化

5. logging内置的keys

    属性 格式 描述
    * asctime %(asctime)s 日志产生的时间，默认格式为2003-07-08 16:49:45,896
    * created %(created)f time.time()生成的日志创建时间戳
    * filename %(filename)s 生成日志的程序名
    * funcName %(funcName)s 调用日志的函数名
    * levelname %(levelname)s 日志级别 ('DEBUG', 'INFO', 'WARNING', 'ERROR', 'CRITICAL')
    * levelno %(levelno)s 日志级别对应的数值
    * lineno %(lineno)d 日志所针对的代码行号（如果可用的话）
    * module %(module)s 生成日志的模块名
    * msecs %(msecs)d 日志生成时间的毫秒部分
    * message %(message)s 具体的日志信息
    * name %(name)s 日志调用者
    * pathname %(pathname)s 生成日志的文件的完整路径
    * process %(process)d 生成日志的进程ID（如果可用）
    * processName %(processName)s 进程名（如果可用）
    * thread %(thread)d 生成日志的线程ID（如果可用）
    * threadName %(threadName)s 线程名（如果可用）

6. Django对logging模块进行了一定的扩展，用来满足Web服务器专门的日志记录需求。
    1. 记录器 Loggers
    Django额外提供了几个其内建的logger。
    django： 不要使用这个记录器，用下面的。这是一个被供起来的记录器，^-^
    django.request： 记录与处理请求相关的消息。5XX错误被记录为ERROR消息；4XX错误记录为WARNING消息。接收额外参数：status_code和request
    django.server： 记录开发服务器下处理请求相关的消息。只用于开发阶段。
    django.template: 记录与渲染模板相关的日志。
    django.db.backends: 与数据库交互的代码相关的消息。
    django.security： 记录任何与安全相关的错误。
    django.security.csrf： 记录CSRF验证失败日志。
    django.db.backends.schema： 记录查询导致数据库修改的日志。
    2. 处理器 Handlers
    Django额外提供了一个handler，AdminEmailHandler。这个处理器将它收到的每个日志信息用邮件发送给站点管理员。
    3. 过滤器Filters
    Django还额外提供两个过滤器。
    CallbackFilter(callback)[source]：这个过滤器接受一个回调函数，并对每个传递给过滤器的记录调用它。如果回调函数返回False，将不会进行记录的处理。
    RequireDebugFalse[source]： 这个过滤器只会在settings.DEBUG==False时传递


### settings.py

```python
import logging
import django.utils.log
import logging.handlers

# 创建日志的路径
LOG_PATH = os.path.join(BASE_DIR, 'log')
# 如果地址不存在，则会自动创建log文件夹
if not os.path.isdir(LOG_PATH):
    os.mkdir(LOG_PATH)

LOGGING = {
    # version 值只能为1
    'version': 1,
    # True 表示禁用loggers
    'disable_existing_loggers': False,
    # < 格式化：日志输出格式的定义 >
    'formatters': {
        'standard': { # 标准的日志格式化
            'format': '%(levelname)s %(asctime)s %(module)s %(message)s'
        },
        'error': { # 错误日志输出格式
            'format': '%(levelname)s %(asctime)s %(pathname)s %(module)s %(lineno)d %(filename)s %(funcName)s %(message)s'
        },
        'simple': {
            'format': '%(levelname)s %(asctime)s %(message)s'
        },
    },
    # < 创建过滤器 >
    'filters': {
        'require_debug_true': {
            '()': 'django.utils.log.RequireDebugTrue',
        },
        'require_debug_false': {
                '()': 'django.utils.log.RequireDebugFalse',
        },
    },
    # < 处理器：需要处理什么级别的日志及如何处理 >
    'handlers': {
        'console': { #输出到控制台
            'level': 'DEBUG', #日志级别
            'filters': ['require_debug_true'], #仅当 DEBUG = True 时才发输出，调用上面创建的过滤器
            'class': 'logging.StreamHandler', #使用什么类去处理日志流
            'formatter': 'standard',  #调用上面创建的日志格式
        },
        'mail_admins': { #发送邮件
            'level': 'ERROR',
            'class': 'django.utils.log.AdminEmailHandler',
            'filters': ['require_debug_true'],# 仅当 DEBUG = False 时才发送邮件
            'formatter': 'simple',
            'include_html': False, #是否发送那些回溯信息，因为这些都是很敏感的系统系统，如果被人截获，可能会发生危险，所以要谨慎
        },
        'timefile': {
            'level': 'DEBUG',
            'filters': ['require_debug_true'],
            'class': 'logging.handlers.TimedRotatingFileHandler',
            'filename': '%s/test.log'  % LOG_PATH,  #这是将日志进行切割，
            'formatter': 'error',
            'when': 'D', # 每天一切， 可选值有S/秒 M/分 H/小时 D/天 W0-W6/周(0=周一) midnight/如果没指定时间就默认在午夜
        },
        'debug': {
            'level': 'DEBUG',
            # 指定日志文件大小，若超过指定的文件大小，会再生成一个新的日志文件保存日志信息
            'class': 'logging.handlers.RotatingFileHandler',
            # 指定文件大小
            # 1M=1024kb 1kb=1024b
            'maxBytes': 5 * 1024 * 1024,
            # 文件地址
            'filename': '%s/debug.log' % LOG_PATH,
            # 指定保存格式
            'formatter': 'error',
            # 指定编码格式
            'encoding': 'utf-8',
            #备份份数
            'backupCount': 5,  #备份数为5 xx.log --> xx.log.2018-08-23_00-00-00 --> xx.log.2018-08-24_00-00-00 -->
        },
    },
    # < 创建记录器 >
    'loggers': {
        'django': {
            'handlers': ['console','mail_admins'],
            'level': 'INFO',
            'propagate': True, #日志是否向上级传递。True 向上级传，False 不向上级传。默认为True。
        },
        'django.request': {
            'handlers': ['debug'],
            'level': 'INFO',
            'propagate': False,
        },
        'file': {
            'handlers': ['debug','console'],
            'level': 'DEBUG',
            'propagate': False,
        }
    }
}

```

### 视图函数调用

通过getLogger 获取记录器 'loggers'中指定的名称
```python
logger = logging.getLogger("file")
# Create your views here.

def index(request):
    logger.info("一个更萌的请求过来了。。。。")
    logger.debug("这是app01里面的index视图函数")

    return HttpResponse("OK")
```

## Mail


### setting

```python
#配置邮件发送
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'  #email后端
EMAIL_USE_TLS = False   #是否使用TLS安全传输协议
EMAIL_USE_SSL = True    #是否使用SSL加密，qq企业邮箱要求使用
EMAIL_HOST = 'smtp.163.com'   #发送邮件的邮箱 的 SMTP服务器，这里用了qq企业邮箱
EMAIL_PORT = 465     #发件箱的SMTP服务器端口
EMAIL_HOST_USER = 'ynsymonitor@163.com'    #发送邮件的邮箱地址
EMAIL_HOST_PASSWORD = '3uQs3ZRXBz'         #发送邮件的邮箱密码

```
*   `subject`：一个字符串。
*   `message`：一个字符串。
*   `from_email`：一个字符串。
*   `recipient_list`：字符串列表，每个字符串都是电子邮件地址。每个成员都`recipient_list`将在电子邮件的“收件人：”字段中看到其他收件人。
*   `fail_silently`：布尔值。当它的`False`，`send_mail()`将引发一个[`smtplib.SMTPException`](https://docs.python.org/3/library/smtplib.html#smtplib.SMTPException "（在Python v3.7中）")如果出现错误。请参阅[`smtplib`](https://docs.python.org/3/library/smtplib.html#module-smtplib "（在Python v3.7中）") 文档以获取可能的异常列表，所有这些异常都是其子类 [`SMTPException`](https://docs.python.org/3/library/smtplib.html#smtplib.SMTPException "（在Python v3.7中）")。
*   `auth_user`：用于向SMTP服务器进行身份验证的可选用户名。如果没有提供，Django将使用该[`EMAIL_HOST_USER`](https://docs.djangoproject.com/en/2.1/ref/settings/#std:setting-EMAIL_HOST_USER)设置的值 。
*   `auth_password`：用于向SMTP服务器进行身份验证的可选密码。如果没有提供，Django将使用该[`EMAIL_HOST_PASSWORD`](https://docs.djangoproject.com/en/2.1/ref/settings/#std:setting-EMAIL_HOST_PASSWORD)设置的值 。
*   `connection`：用于发送邮件的可选电子邮件后端。如果未指定，将使用默认后端的实例。有关 更多详细信息，请参阅有关[电子邮件后端](https://docs.djangoproject.com/en/2.1/topics/email/#topic-email-backends)的文档。
*   `html_message`：如果`html_message`被提供，所得到的电子邮件将是一个 *多部分/替代*电子邮件`message`作为 *文本/无格式*内容类型和`html_message`作为 *text / html的*内容类型。

返回值将是成功传递的消息数（可以是`0`或者`1`因为它只能发送一条消息）。

### 视图函数


```python
#引入发送邮件的模块
from django.core.mail import send_mail, send_mass_mail, EmailMultiAlternatives
from django.conf import settings


def mail(request):
    subject = '来自自强学堂的问候'
    text_content = '这是一封重要的邮件.'
    html_content = '<p>这是一封<strong>重要的</strong>邮件.</p>
    from_email = settings.EMAIL_HOST_USER
    recipient_list = ['songbing@ynsy.com']
    msg = EmailMultiAlternatives(subject, text_content, from_email, recipient_list)
    msg.attach_alternative(html_content, "text/html")
    msg.send()
    return HttpResponse("success")

```

## Django多语言
    Django 国际化的本质就是开发者对需要翻译的字符串进行标记，并对字符串进行相应的翻译。当用户访问该 Web 时，Django 内部框架根据用户使用偏好进行 Web 呈现。
    Django 国际化使用的翻译模块是使用 Python 自带的 gettext 标准模块。通过一个到 GNU gettext 消息目录库的接口，这个模块为 Python 程序提供了国际化 (I18N) 和本地化 (L10N)。

### 翻译函数

    ugettext 函数预加载的时候，调度的翻译方法
    ugettext_lazy 函数请求执行的时候调度的翻译方法
    gettext_noop 函数来标记一个不需要立即翻译的字符串。 这个串会稍后从变量翻译。
    使用这种方法的环境是，有字符串必须以原始语言的形式存储（如储存在数据库中的字符串）而在最后需要被翻译出来（如显示给用户时）。
    ungettext()函数包括三个参数： 单数形式的翻译字符串，复数形式的翻译字符串，和对象的个数（将以 count 变量传递给需要翻译的语言）。
### 使用场景

    ugettext_lazy():
        models.py (fields, verbose_name, help_text, methods short_description);
        forms.py (labels, help_text, empty_label);
        apps.py (verbose_name).
        settings.py
        等其他不需要请求执行, 而是在启动执行的模块中
    ugettext():
        views.py
        其他类似于在请求过程中调用的代码
        函数封装的.py文件
        等其他需要请求一次就需要执行一次的模块中

### 代码示例

    ```
    Modle 
    from django.utils.translation import ugettext_lazy as _

    class Asset(models.Model):
        ASSET_STATUS = (
            (1, _("Used")),
            (2, _("Unused")),
            (3, _("Scraped")),
            (4, _("SoldOut"))
        )
        ASSET_TYPE = (
            (1, _("Physical Machine")),
            (2, _("Virtual Machine")),
            (3, _("Switch Board")),
            (4, _("Router")),
            (5, _("Firewall")),
            (6, _("Docker")),
            (7, _("Other"))
        )
        ASSETOS_TYPE=(
            (0,"Centos"),
            (1,"Windows"),
            (2,"Ubuntu"),
            (3,"Debian"),
        )
        id = models.UUIDField(default=uuid.uuid4, primary_key=True)
        hostname = models.CharField(max_length=64, null=True,verbose_name=_("HostName"))
        ip = models.GenericIPAddressField(unique=True, verbose_name=_("IP"))
        other_ip = models.GenericIPAddressField(blank=True,null=True, verbose_name=_("Other_IP"))
        port = models.IntegerField(default=22, verbose_name=_("Port"))
        user = models.CharField(max_length=64,default='root', verbose_name=_("Asset User"))
        password = models.CharField(max_length=100,null=True, verbose_name=_("Asset PassWord"))
        asset_type = models.IntegerField(choices=ASSET_TYPE, null=True, blank=True, verbose_name=_("Asset Type"))
        os_type = models.IntegerField(choices=ASSETOS_TYPE, null=True, blank=True, verbose_name=_("System Type"))
        system_version = models.CharField(max_length=32,null=True, blank=True, verbose_name=_("System Version"))
        mac = models.CharField(max_length=128, null=True, blank=True, verbose_name=_("MAC"))
        cpu_type = models.CharField(max_length=128,  null=True, blank=True, verbose_name=_("CPU Type"))
        cpu_core = models.IntegerField( null=True, blank=True,verbose_name=_("CPU Core Count"))
        cpu_total = models.IntegerField( null=True, blank=True,verbose_name=_("Number of CPUs"))
        sn = models.CharField(max_length=128, null=True, blank=True, verbose_name=_("Asset SN Number"))
        memory = models.CharField(max_length=128, null=True, blank=True, verbose_name=_("Asset Memory"))
        disk_info = models.CharField(max_length=512, null=True, blank=True, verbose_name=_("Disk Information"))
        disk_mount = models.CharField(max_length=512, null=True, blank=True, verbose_name=_("Mount Partition"))
        server_type = models.CharField(max_length=64, null=True, blank=True, verbose_name=_("Server Model"))
        os_kernel = models.CharField(max_length=255, null=True, blank=True, verbose_name=_("System Kernel Version"))
        system_arch = models.CharField(max_length=32, null=True, blank=True, verbose_name=_("System Platform"))
        status = models.IntegerField(choices=ASSET_STATUS, null=True, blank=True, default=1, verbose_name=_("Equipment Status"))
        create_date = models.DateTimeField(blank=True, auto_now_add=True, verbose_name=_("Create By"))
        update_date = models.DateTimeField(blank=True, auto_now=True, verbose_name=_("Update By"))

        project = models.ForeignKey(to="users.Project", related_name='project_asset', default=None, blank=True, null=True,
                                    on_delete=models.SET_NULL, verbose_name=_("User Project"))
        idc = models.ForeignKey(to="Idc", null=True, blank=True, on_delete=models.SET_NULL, verbose_name=_("IDC"))
        product = models.ForeignKey(to="Product", related_name='product_asset', default=None, null=True,
                                    blank=True, on_delete=models.SET_NULL, verbose_name=_("Product"))
        work_env = models.ForeignKey(to="Work_Env", null=True, blank=True, on_delete=models.SET_NULL,
                                     related_name='work_asset', verbose_name=_("Working Environment"))
        use = models.ManyToManyField(to="ServerUse", blank=True, related_name='serveruse_asset', verbose_name=_("Server Use"))



        class Meta:
            db_table = "asset"
            verbose_name = _("Asset Information List")
            verbose_name_plural = _("Asset Information List")

        def __str__(self):
            return self.ip

        @property
        def use_list(self):
            asser = Asset.objects.get(ip=self.ip)
            return asser.use.all()

    ```

    ```
    视图
    from django.utils.translation import ugettext as _
    class AssetAllDel(LoginPermissionRequired,View):
    model = Asset

    def post(self, request):
        ret = {'status': True, 'error': None, }
        try:
            if request.POST.get('nid') :
                id = request.POST.get('nid', None)
                Asset.objects.get(id=id).delete()
            else:
                ids = request.POST.getlist('id', None)
                Asset.objects.filter(id__in=ids).delete()
        except Exception as e:
            ret['status'] = False
            ret['error'] = _('Deletion error，{}'.format(e))
        finally:
            return HttpResponse(json.dumps(ret))
    ```


### settings 中配置
    ```
    MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.locale.LocaleMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'opensa.api.RequestMiddleware',
    ]
    ```
    django.middleware.locale.LocaleMiddleware中间见要出现在
    SessionMiddleware和CacheMiddleware后，其他所有中间件之前,

    ```
    TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],

        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
                'django.template.context_processors.i18n',
            ],
        },
    },
    ]
    ```
    settings.TEMPLATES.OPTIONS.context_processors中要增加django.template.context_processors.i18n

    # https://docs.djangoproject.com/en/2.1/topics/i18n/
    #时区
    ```
    TIME_ZONE = 'Asia/Shanghai'
    DATETIME_FORMAT = 'Y-m-d H:i:s'
    DATE_FORMAT = 'Y-m-d'

    #国际化
    ugettext = lambda s: s
    LANGUAGE_CODE = 'zh-Hans'

    USE_I18N = True
    USE_L10N = True
    USE_TZ = True
    USE_THOUSAND_SEPARATOR = True
    LANGUAGES = (
        ('en', ugettext('Simple Chinese')),
        ('zh-hans', ugettext('English')),

    )

    LOCALE_PATHS = (
        os.path.join(BASE_DIR, 'locale'),
    )
    ```

###  国际化文件生成
    ```
    #makemessages 生成语言文件locale/en/LC_MESSAGES/django.po
    django-admin makemessages -l en 
    #创建信息文件之后，每次对其做了修改，都需要用 django-admin.py compilemessages 编译成“.mo”文件供 gettext 使用
    django-admin compilemessages
    ```
    生成的语言文件

### 模板配置国际化

    在代码中已经加入了国际化模版相关代码，下面 4 点需要注意：

    1 {% load i18n %}
    使模版能够访问到访问到标签。
    2 {% trans str %}
    标记翻译一个常量字符串或 可变内容
    3 模版中的 select 的 name 值必须为"language"
    4 将 testform 的 action 重定向到 /i18n/setlang/，启用了 django.views.i18n.set_language 视图，它的作用是设置用户语言偏好并重定向返回到前一页面。

    Django 模板两种常用模板标签
    {% trans %} 模板标签标记需要翻译的字符串；如果只需要标记字符串而以后再翻译，可以使用 noop 选项。
    在 {% trans %} 中不允许使用模板中的变量，只能使用单引号或双引号中的字符串。如果翻译时需要用到变量（占位符），可以使用 {% blocktrans %}。
    如果需要在 blocktrans 标签内绑定多个表达式，可以用 and 来分隔。
    为了表示单复数相关的内容，需要在 {% blocktrans %} 和 {% endblocktrans %} 之间使用 {% plural %} 标签来指定单复数形式。

    模版标签实现分析

    {% trans %}

    通过 /usr/local/lib/Python2.5/site-packages/django/templatetags/i18n.py 文件 do_translate（）实现的。do_translate（）处理了 {% trans %} 的三种格式：
    1 {% trans "this is a test" %} 
    2 {% trans "this is a test" noop %} 
    3 {% trans variable %}

    用于翻译带参数的文本块
    用法 :

    1 {% blocktrans with foo|filter as bar and baz|filter as boo %} 
    2 This is {{ bar }} and {{ boo }}. 
    3 {% endblocktrans %}

    另外，支持复数 ::
    1 {% blocktrans count var|length as count %} 
    2 There is {{ count }} object. 
    3 {% plural %} 
    4 There are {{ count }} objects. 
    5 {% endblocktrans %}



    ```
    {% load i18n %}
    <!DOCTYPE html>
    <html>

    <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- <meta http-equiv="Content-Security-Policy" content="upgrade-insecure-requests" /> -->
    <title>{% trans 'OpenSa' %}</title>
    </head>
    ```

## Django REST Framework
    https://www.django.cn/course/show-21.html
### 准备工作
    创建一个名为 T_js 的新django项目，然后启动一个名为 Api_T 的新app。
    在创建的虚拟环境中安装 Django 和 Django REST frameworkpip install django
    pip install djangorestframework# 创建一个新项目和一个单个应用
    django-admin.py startproject T_js
    django-admin.py startapp Api_T
    同步数据库：
    python manage.py makemigrations
    python manage.py migrate
    我们还要创建一个名为 admin 的初始用户，密码为 12342qwer
    我们稍后将在该示例中验证该用户。
    python manage.py createsuperuser
    创建初始化用户

### Serializers
    首先我们要定义一些序列化程序。我们创建一个名为 T_js/Api_T/serializers.py的文件，来用作我们的数据表示。

    from django.contrib.auth.models import User, Group
    from rest_framework import serializers

    class UserSerializer(serializers.HyperlinkedModelSerializer):
        class Meta:
            model = User
            fields = ('url', 'username', 'email', 'groups')


    class GroupSerializer(serializers.HyperlinkedModelSerializer):
        class Meta:
            model = Group
            fields = ('url', 'name')
    请注意，在这个例子中我们用到了超链接关系，使用 HyperlinkedModelSerializer。你还可以使用主键和各种其他关系，但超链接是好的RESTful设计。

### Views
    编写视图。打开 T_js/Api_T//views.py 文件开始写代码了。

    from django.contrib.auth.models import User, Group
    from rest_framework import viewsets
    from Api_T.serializers import UserSerializer, GroupSerializer


    class UserViewSet(viewsets.ModelViewSet):
        """
        允许用户查看或编辑的API路径。
        """
        queryset = User.objects.all().order_by('-date_joined')
        serializer_class = UserSerializer


    class GroupViewSet(viewsets.ModelViewSet):
        """
        允许组查看或编辑的API路径。
        """
        queryset = Group.objects.all()
        serializer_class = GroupSerializer

### URLs
    在Api_T/urls.py中开始写连接API的URLs。

    from django.conf.urls import url, include
    from rest_framework import routers
    from Api_T import views

    router = routers.DefaultRouter()
    router.register(r'users', views.UserViewSet)
    router.register(r'groups', views.GroupViewSet)

    # 使用自动URL路由连接我们的API。
    # 另外，我们还包括支持浏览器浏览API的登录URL。
    urlpatterns = [
        url(r'^', include(router.urls)),
        url(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework'))
    ]

### Settings
    我们也想设置一些全局设置。我们想打开分页，我们希望我们的API只能由管理员使用。设置模块都在 T_js/settings.py 中。

    INSTALLED_APPS = (
        ...
        'rest_framework',
    )

    REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.SessionAuthentication',
    ),
    'DEFAULT_PERMISSION_CLASSES': (
        'rest_framework.permissions.IsAuthenticated',
    ),
    'DEFAULT_PARSER_CLASSES': (
        'rest_framework.parsers.JSONParser',
        'rest_framework.parsers.FormParser',
    ),
    }
    访问http://127.0.0.1:8000/users/

### 序列化

##  sitemap功能搜索引擎

sitemap可以用来通知搜索引擎页面的地址，页面的重要性，帮助站点得到比较好的收录。

1. 启用sitemap
    在django的settings.py的INSTALLED_APPS中添加
    ```
    'django.contrib.sites',
    'django.contrib.sitemaps',
    ```
    然后migrate数据库：
    ```
    $ ./manage.py makemigrations
    $ ./manage.py migrate
    ```
    登陆Django后台，修改SITE为你Django网站的域名和名称,然后在settings.py中加入SITE_ID = 1来制定当前的站点。

2. 添加sitemap功能

    - 创建sitemap
    通常，希望搜索引擎抓取工具索引既不是对象详细信息页面也不是列表页的视图(例如index页面)。解决方案是在 items 中显式列出这些视图的网址名称，并在网站地图的 location 方法中调用 reverse()
    创建sitemap.py.内容类似下面的代码：
    ```
    from django.contrib.sitemaps import Sitemap
    from blog.models import Article, Category, Tag
    from accounts.models import BlogUser
    from django.contrib.sitemaps import GenericSitemap
    from django.core.urlresolvers import reverse

    class StaticViewSitemap(Sitemap):
        priority = 0.5 #可选,指定每个对象的优先级,默认0.5
        changefreq = 'daily'  #可选,指定每个对象的更新频率
        def items(self): 
        #返回对象的列表.这些对象将被其他方法或属性调用
            return ['blog:index', ]

        def location(self, item): 
        #可选.返回每个对象的绝对路径.如果对象有get_absolute_url()方法,可以省略location
            return reverse(item)

    class ArticleSiteMap(Sitemap):
        changefreq = "monthly"
        priority = "0.6"
        def items(self):
            return Article.objects.filter(status='p')

        def lastmod(self, obj):
            #可选,该方法返回一个datetime,表示每个对象的最后修改时间
            return obj.last_mod_time

    class CategorySiteMap(Sitemap):
        changefreq = "Weekly"
        priority = "0.6"

        def items(self):
            return Category.objects.all()

        def lastmod(self, obj):
            return obj.last_mod_time

    class TagSiteMap(Sitemap):
        changefreq = "Weekly"
        priority = "0.3"
        def items(self):
            return Tag.objects.all()

        def lastmod(self, obj):
            return obj.last_mod_time

    class UserSiteMap(Sitemap):
        changefreq = "Weekly"
        priority = "0.3"
        def items(self):
            return BlogUser.objects.all()

        def lastmod(self, obj):
            return obj.date_joined
    ```

    - url配置

    url.py中加入:
    ```
    from DjangoBlog.sitemap import StaticViewSitemap, ArticleSiteMap, CategorySiteMap, TagSiteMap, UserSiteMap

    sitemaps = {
        'blog': ArticleSiteMap,
        'Category': CategorySiteMap,
        'Tag': TagSiteMap,
        'User': UserSiteMap,
        'static': StaticViewSitemap
    }

    url(r'^sitemap\.xml$', sitemap, {'sitemaps': sitemaps},
            name='django.contrib.sitemaps.views.sitemap'),
    ```
    至此，全部完成，运行你的django程序，浏览器输入:

    - 访问地址
    http://127.0.0.1:8000/sitemap.xml
    当客服端访问 /sitemap.xml时，这将告诉Django生成一个Sitemap.
    sitemap视图需要一个额外的必需参数:{sitemaps': sitemaps}.sitemaps是一个字典,将小节的标签(例如:new或blog)映射到Sitemap类(例如:NewsSitemap).它也可以映射到Sitemap类的实例(例如: NewsSitemap(some_var)).

3. 通知Google

    当你的sitemap变化的时候，你会想通知Google，以便让它知道对你的站点进行重新索引。 框架就提供了这样的一个函数： django.contrib.sitemaps.ping_google() 。
    ping_google() 有一个可选的参数 sitemap_url ，它应该是你的站点地图的URL绝对地址（例如：
    如果不能够确定你的sitemap URL, ping_google() 会引发 django.contrib.sitemaps.SitemapNotFound 异常。

    我们可以通过模型中的 save() 方法来调用 ping_google() ：
    ```
    from django.contrib.sitemaps import ping_google
     
    class Entry(models.Model):
      # ...
      def save(self, *args, **kwargs):
        super(Entry, self).save(*args, **kwargs)
        try:
          ping_google()
        except Exception:
          # Bare 'except' because we could get a variety
          # of HTTP-related exceptions.
          pass
      ```
    一个更有效的解决方案是用 cron 脚本或任务调度表来调用 ping_google() ，该方法使用Http直接请求Google服务器，从而减少每次调用 save() 时占用的网络带宽。 The function makes an HTTP request to Google's servers, so you may not want to introduce that network overhead each time you call save() .

        Finally, if 'django.contrib.sitemaps' is in your INSTALLED_APPS , then your manage.py will include a new command, ping_google . This is useful for command-line access to pinging. For example:

    ```
    python manage.py ping_google /sitemap.xml
    ```

##  whoosh 全文检索
Django之whoosh搜索引擎

* whoosh 介绍
    1. 搜索引擎介绍#
    django是python语言后台web开发的一个框架,配合一些插件可为web网站实现很方便的搜索功能
    django搜索引擎使用whoosh是一个纯python开发的全文搜索引擎,小巧简单

    2. 搜索引擎作用#
    搜索引擎可以在表中针对某些关键进行全文分析,根据关键词建立索引数据 mu
    索引类似于新华字典的目录,可以快速搜索数据

    #例如
    MacBook:商品1,商品2,商品3
* :搜索引擎框架(haystack)
    1. 作用
    直接在django项目中使用whoosh需要关注一些基础细节问题，而通过haystack这一搜索框架，可以方便地在django中直接添加搜索功能，无需关注索引建立、搜索解析等细节问题。

    haystack支持多种搜索引擎,whoosh,solr,elasticsearch等 虽然whoosh性能相比elasticsearch较低,但是其无二进制包程序不会莫名其妙崩溃,在中小型网站完全适用

    2. 图解
    ![Alt text](./whoosh1.png)

    3. whoosh使用方式

    + 安装依赖包
        ```
        pip install django-haystack #安装haystack框架
        pip install whoosh         #安装whoosh搜索引擎
        ```
    + settings配置文件
        ```
        注册haystack框架
        INSTALLED_APPS = [
             #注册haystack框架
            'haystack'
        ]
        ```
* 配置搜索引擎

    ```
    HAYSTACK_CONNECTIONS = {
        'default': {
            'ENGINE': 'haystack.backends.whoosh_backend.WhooshEngine',
            'PATH': os.path.join(BASE_DIR, 'whoosh_index'),  # whoosh_index 文件夹不需要自己手动创建 会自动创建
        }
    }
    ```
    #添加此项，当数据库改变时，会自动更新索引，非常方便
    ```HAYSTACK_SIGNAL_PROCESSOR = 'haystack.signals.RealtimeSignalProcessor'```

* 全局添加url路由
    ```
    urlpatterns = [
        ...
        url(r'^search/', include('haystack.urls')),
    ]
    ```
* 应用目录下创建search_indexes.py文件(固定名称)
    search_indexes代码
    ```
    from haystack import indexes  # 导入索引
    from . import models   # 导入模型表
    class GoodsIndex(indexes.SearchIndex,indexes.Indexable):

        text = indexes.CharField(document=True,use_template=True)
        '''
        document:指定了将模型类中的哪些字段建立索引
        use_template:在模板文件夹中创建文件夹指明具体的字段建立索引
        '''

        def get_model(self):
            # 为那个模型表建立索引
            return models.GoodInfo

        def index_queryset(self, using=None):
            return self.get_model().objects.all()
    ```

* 模板文件夹创建如下文件

    ![Alt text](./temp.jpg)

    _text.txt指定模型表中那些字段建立索引
    ```
    #指定那些字段建立索引
    {{object.name}}   #商品名称建立索引
    {{object.desc}}   #商品简介建立索引
    ```
    生成索引文件
    ```python manage.py rebuild_index```


* 在templates/search下建立search.html文件#
    搜索出结果后，haystack会把搜索出的结果传递给templates/search目录下的search.html，传递的上下文包括：

    query：搜索关键字

    page：当前页的page对象 –>遍历page对象，获取到的是SearchResult类的实例对象，对象的属性object才是模型类的对象。

    paginator：分页paginator对象

    ```
    <form action="/search" method="get">

        <p>商品搜索:<input type="text" name="q"></p>
        <p>提交:<input type="submit"></p>
    </form>

    <p>搜索关键字:{{ query }}</p>
    <p>当前页page对象:{{ page }}</p>
    <p>分页对象:{{ paginator }}</p>

    <ul>
        {% for item in page %}
          <li> {{ item.object.name }}</li>
          <li> {{ item.object.desc }}</li>
        {% endfor %}
    </ul>
    ```
    通过HAYSTACK_SEARCH_RESULTS_PER_PAGE 可以控制每页显示数量。

* jieba
    1. 安装
        ```
        pip install jieba
        ```
    2. 作用
        whoosh不能很好的进行分词 而使用jieba可以很好的分词

    3. 使用方式
        ```
        import jieba
        split_data = '很好吃的草莓'  #要被切割的数据
        res = jieba.cut(split_data,cut_all=True)
        print(res)  #<generator object Tokenizer.cut at 0x0000000009EA27D8> 拿到一个生成器

        for value in res:
            print(value)
        ```
* Haystack使用方式

    1. 在haystack的安装文件夹下,例如
    安装目录\python3.7\Lib\site-packages\haystack\backends(每个人安装目录不一样 根据自己的安装目录创建)建立ChineseAnalyzer.py文件

    ```
    import jieba
    from whoosh.analysis import Tokenizer, Token
    class ChineseTokenizer(Tokenizer):
        def __call__(self, value, positions=False, chars=False,
                     keeporiginal=False, removestops=True,
                     start_pos=0, start_char=0, mode='', **kwargs):
            t = Token(positions, chars, removestops=removestops, mode=mode,
                      **kwargs)
            seglist = jieba.cut(value, cut_all=True)
            for w in seglist:
                t.original = t.text = w
                t.boost = 1.0
                if positions:
                    t.pos = start_pos + value.find(w)
                if chars:
                    t.startchar = start_char + value.find(w)
                    t.endchar = start_char + value.find(w) + len(w)
                yield t
    def ChineseAnalyzer():
        return ChineseTokenizer()
    ```

    2. 将上面backends目录中的whoosh_backend.py文件，复制一份命名为whoosh_cn_backend.py，然后打开此文件进行替换：

    ```
    #顶部引入刚才添加的中文分词
    from .ChineseAnalyzer import ChineseAnalyzer 

    #在整个py文件中，查找 替换
    analyzer=StemmingAnalyzer()

    analyzer=ChineseAnalyzer()
    ```
    3. 修改settings.py文件中的配置项。

    ```
    HAYSTACK_CONNECTIONS = {
        'default': {
            # 'ENGINE': 'haystack.backends.whoosh_backend.WhooshEngine',  # 原来的默认的
            'ENGINE': 'haystack.backends.whoosh_cn_backend.WhooshEngine', # jieba搜索
            'PATH': os.path.join(BASE_DIR, 'whoosh_index'),  # whoosh_index 文件夹不需要自己手动创建 会自动创建
        }
    }
    ```
    4. 重新生成索引文件

    python manage.py rebuild_index





