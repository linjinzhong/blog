---
title: django
urlname: django
top:
categories:
    - 框架
tags:
    - django
author: jesonlin
date: 2023-04-08 14:56:51
updated: 2023-04-08 14:56:51
---

Django是一个由Python编写的具有完整架站能力的开源Web框架。

<!-- more -->

## 简介
1. web 框架架构
    ![web 框架架构](/web_frame.png)
2. django 架构
    1. Django本身基于MVC架构，即Model（模型）+View（视图）+ Controller（控制器）设计模式，因此天然具有MVC的出色基因：开发快捷、部署方便、可重用性高、维护成本低等优点。
    2. Django是一个全栈Web框架。所谓全栈框架，是指除了封装网络和线程操作，还提供HTTP请求和响应、数据库读写管理、HTML模板渲染等一系列功能的框架。
    3. 强大的数据库访问API。Django的Model层自带数据库ORM组件。
    4. 丰富的Template模板功能：Django自带类似jinjia的模板语言，不但原生功能丰富，还可以自定义模板标签和过滤器。并且以类似Python的调用机制和视图默契配合。
    5. 自带后台管理应用admin：只需要通过简单的几行配置和代码就可以实现一个完整的后台数据管理控制平台。这是Django最受欢迎的功能。
3. MVC设计模式：
    1. 模型(Model)：用于封装与应用程序的业务逻辑相关的数据及对数据的处理方法，是Web应用程序中用于处理应用程序的数据逻辑的部分，Model只提供功能性的接口，通过这些接口可以获取Model的所有功能。白话说，这个模块就是业务逻辑和数据库的交互层，定义了数据表。
    2. 视图(View)：负责数据的显示和呈现，是对用户的直接输出。
    3. 控制器(Controller)：负责从用户端收集用户的输入，可以看成提供View的反向功能。
4. MTV设计模式
    1. 模型(Model)：和MVC中的定义一样  
    2. 模板(Template)：将模型数据与HTML页面结合起来的引擎  
    3. 视图(View)：负责实际的业务逻辑实现  
5. Django对传统的MVC设计模式进行了修改
    将视图分成View模块和Template模块两部分，将动态的逻辑处理与静态的页面展示分离开。而Model采用了ORM技术，将关系型数据库表抽象成面向对象的Python类，将数据库的表操作转换成Python的类操作，避免了编写复杂的SQL语句。
    ![web 框架架构](/MTV.png)


## 安装
```
# 安装
python3 -m pip install Django
# 或者
pip3 install Django

# 查看版本
python -m django --version
```


# 创建项目过程
1. 创建项目
    ```
    # 创建项目目录
    mkdir -p /data/service/mysite
    cd /data/service/mysite

    # 创建虚拟环境
    virtualenv venv
    # 如果没有virtualenv则按照
    python3 -m pip install virtualenv
    # 激活虚拟环境
    source venv/bin/activate 

    # 安装django
    python3 -m pip install Django

    # 当前目录下生成django项目
    django-admin startproject mysite .
    # 生成如下结构
    mysite/
    ├── manage.py
    ├── mysite
    │   ├── asgi.py
    │   ├── __init__.py
    │   ├── settings.py
    │   ├── urls.py
    │   └── wsgi.py
    └── venv
        ├── bin
        ├── lib
        ├── lib64
        └── pyvenv.cfg
    ```
2. 更新sqlite3(如果需要)
    ```
    # 下载
    wget https://www.sqlite.org/2023/sqlite-autoconf-3410200.tar.gz

    # 解压编译
    tar zxvf sqlite-autoconf-3410200.tar.gz
    cd sqlite-autoconf-3410200/
    ./configure --prefix=/usr/local
    make && make install

    # 删除旧的，关联新的
    mv /usr/bin/sqlite3 /usr/bin/sqlite3_old
    ln -s /usr/local/bin/sqlite3 /usr/bin/sqlite3
    echo '/usr/local/lib' > /etc/ld.so.conf.d/sqlite3.conf
    ldconfig

    # 查看版本
    sqlite3 -version
    ```
3. 运行
    ```
    # 添加远程主机，防火墙也要放开监听端口通过
    vim mysite/settings.py 
    ALLOWED_HOSTS = ['云主机IP']

    # 仅本机浏览器查看，以127.0.0.1:8000这个默认配置启动开发服务器。
    python3 manage.py runserver

    # 远程云主机浏览器也能查看
    python3 manage.py runserver 0:8000

    # 浏览器查看
    http://<服务器IP>:8000
    ```


## 创建投票项目应用
0. 项目内容
    一个可以让公众用户进行投票和查看投票结果的站点  
    一个可以进行增、删、改、查的后台管理界面，也就是我们常说的admin站点
1. 请求和响应
    1. 创建应用
        ```
        python3 manage.py startapp polls

        # 结构如下
        polls/
        ├── admin.py
        ├── apps.py
        ├── __init__.py
        ├── migrations
        │   └── __init__.py
        ├── models.py
        ├── tests.py
        └── views.py
        ```
    2. 修改
        1. 根路由：vim mysite/urls.py
            ```
            from django.contrib import admin
            from django.urls import include, path

            urlpatterns = [
                # include语法相当于多级路由，它把接收到的url地址去除与此项匹配的部分
                path('polls/', include('polls.urls')),
                path('admin/', admin.site.urls),
            ]
            ```
        2. 应用app路由：vim polls/urls.py
            ```
            from django.urls import path
            from . import views

            # 应用名，模版中防止不同应用相同路由
            app_name = 'polls'
            urlpatterns = [
                # ex: /polls/
                path('', views.index, name='index'),

                # ex: /polls/5/
                path('<int:question_id>/', views.detail, name='detail'),

                # ex: /polls/5/results/
                path('<int:question_id>/results/', views.results, name='results'),

                # ex: /polls/5/vote/
                path('<int:question_id>/vote/', views.vote, name='vote'),
            ]
            ```
        3. 模版文件：vim polls/templates/polls/xxx.html
            1. 展示最近5个问题：index.html
                ```
                <!DOCTYPE html>
                <html lang="en">
                <head>
                    <meta charset="UTF-8">
                    <title>index</title>
                </head>
                <body>

                {% if latest_question_list %}
                    <ul>
                    {% for question in latest_question_list %}
                        <li><a href="{%url 'polls:detail' question.id %}">{{ question.question_text }}</a>
                        </li>
                    {% endfor %}
                    </ul>
                {% else %}
                    <p>No polls are available.</p>
                {% endif %}

                </body>
                </html>
                ```
            2. 展示某个问题详情包括问题本身和投票项目
                ```
                <form action="{% url 'polls:vote' question.id %}" method="post">
                {% csrf_token %}
                <fieldset>
                    <legend><h1>{{ question.question_text }}</h1></legend>
                    {% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}
                    {% for choice in question.choice_set.all %}
                        <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}">       <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br>
                    {% endfor %}
                </fieldset>
                <input type="submit" value="Vote">
                </form>               
                ```
            3. 展示某个问题投票后结果
                ```
                <h1>{{ question.question_text }}</h1>

                <ul>
                {% for choice in question.choice_set.all %}
                    <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
                {% endfor %}
                </ul>

                <a href="{% url 'polls:detail' question.id %}">Vote again?</a>
                ```

        4. 视图函数：vim polls/views.py
            ```
            from django.shortcuts import render, get_object_or_404
            from django.http import HttpResponse, HttpResponseRedirect, Http404
            from .models import Question
            from django.urls import reverse

            # Create your views here.


            def index(request):
                """展示最近5个问题"""

                latest_question_list = Question.objects.order_by('-pub_date')[:5]
                context = {
                    'latest_question_list': latest_question_list,
                }
                return render(request, 'polls/index.html', context)
                # return HttpResponse(template.render(context, request))
                # return HttpResponse("hello, world. You're at the polls index.")


            def detail(request, question_id):
                """展示每个问题详情"""

                # try:
                #     question = Question.objects.get(pk=question_id)
                # except Question.DoesNotExist:
                #     raise Http404("Question does not exist")
                question = get_object_or_404(Question, pk=question_id)
                return render(request, 'polls/detail.html', {'question': question})


            def results(request, question_id):
                """投票结果"""

                question = get_object_or_404(Question, pk=question_id)
                return render(request, 'polls/results.html', {'question': question})


            def vote(request, question_id):
                """投票"""

                question = get_object_or_404(Question, pk=question_id)
                try:
                    selected_choice = question.choice_set.get(pk=request.POST['choice'])
                except (KeyError, Choice.DoesNotExist):
                    # 投票选项不存在,返回详情页
                    return render(request, 'polls/detail.html', {
                        'question': question,
                        'error_message': "You didn't select a choice.",
                    })
                else:
                    # 投票选项存在,增加该选项值,返回投票结果页
                    selected_choice.votes += 1
                    selected_choice.save()
                    return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))
            ```
    3. 启动
        ```
        # 浏览器输入
        http://<服务器IP>:8000/polls/
        ```
2. 模型和数据库
    1. 默认数据库sqlite3切换为mysql
        vim mysite/settings.py
        ```
        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': 'mysite',
                'USER': 'root',
                'PASSWORD': 'jesonlin',
                'HOST': '127.0.0.1',
                'OPTIONS': {'init_command': 'set names utf8mb4;', 'charset': 'utf8mb4'},
            }
        }

        # 需要依赖包
        yum install mysql-devel
        yum install python3-devel
        yum install gcc
        pip3 install mysqlclient

        # 为 INSTALLED_APPS 默认应用创建表
        python manage.py migrate
        ```
    2. 其他设置
        vim mysite/settings.py
        ```
        TIME_ZONE = 'Asia/Shanghai'

        INSTALLED_APPS = [
            ...,
            'polls',
        ]
        ```
    3. 创建模型
        ```
        # polls/models.py
        from django.db import models

        class Question(models.Model):
            question_text = models.CharField(max_length=200)
            pub_date = models.DateTimeField('date published')

            def __str__(self):
                return self.question_text
            def was_published_recently(self):
                return self.pub_date >= timezone.now() - datetime.timedelta(days=1)

        class Choice(models.Model):
            question = models.ForeignKey(Question, on_delete=models.CASCADE)
            choice_text = models.CharField(max_length=200)
            votes = models.IntegerField(default=0)

            def __str__(self):
                return self.choice_text       
        ```
    4. 将模型生效到数据库
        ```
        # 生成迁移文件
        python manage.py makemigrations polls
        # 查看具体sql语句
        python manage.py sqlmigrate polls 0001
        # 执行sql语句
        python manage.py migrate polls
        # 有多个数据库时候路由
        # python manage.py migrate polls --database config
        ```
3. django的python交互式命令行
    1. 启动交互式命令行
        ```
        # manage.py 会设置 DJANGO_SETTINGS_MODULE 环境变量
        # 这个变量会让 Django 根据 mysite/settings.py 文件来设置 Python 包的导入路径。
        python manage.py shell
        ```
    2. 通过orm插入DB数据
        ```
        >>> from polls.models import Choice, Question
        >>> Question.objects.all()
        <QuerySet []>
        >>> 
        >>> from django.utils import timezone
        >>> q = Question(question_text="What's new?", pub_date=timezone.now())
        >>> q.save()
        >>> 
        >>> q.id
        1
        >>> q.question_text
        "What's new?"
        >>> q.pub_date
        datetime.datetime(2023, 4, 11, 13, 12, 18, 914351, tzinfo=<UTC>)
        >>> 
        >>> 
        >>> q.question_text = "What's up?"
        >>> q.save()
        >>> 
        >>> Question.objects.all()
        <QuerySet [<Question: What's up?>]>
        >>>
        >>> current_year = timezone.now().year
        >>> Question.objects.get(pub_date__year=current_year)
        <Question: What's up?>
        >>>
        # pk 是primary key的简写
        >>> q = Question.objects.get(pk=1)
        >>> q.was_published_recently()
        True
        >>>
        >>> q.choice_set.all()
        <QuerySet []>
        >>>
        # 插入外键关联的数据
        >>> q.choice_set.create(choice_text='Not much', votes=0)
        <Choice: Not much>
        >>> q.choice_set.create(choice_text='The sky', votes=0)
        <Choice: The sky>
        >>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)
        >>>
        # 被外键关联，查看所有关联项目
        >>> q.choice_set.all()
        <QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
        >>> q.choice_set.count()
        3
        ```
4. amdin 管理页面
    0. 管理页面
        ```
        http://<服务器IP>:8000/admin/
        ```
    1. 创建登录管理页面的超级用户
        ```
        python manage.py createsuperuser
        # 按提示输入超级用户信息
        # 此时数据表auth_user就会插入一条用户信息
        ```
    2. 向管理页面添加投票应用
        ```
        # polls/admin.py
        from django.contrib import admin
        from .models import Question
        # Register your models here.

        admin.site.register(Question)
        ```
5. 模版
    0. 配置
        ```
        # mysite/setting.py
        TEMPLATES = [
            {
                'APP_DIRS': True, # 表示优先找每个app下的templates文件夹
            }
        ]
        ```


## 实践
    商品管理和展示（待实践）


## REFERENCE
[官网](https://www.djangoproject.com/)
[刘江-django教程](https://www.liujiangblog.com/course/django/)