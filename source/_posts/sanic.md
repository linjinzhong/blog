---
title: sanic
urlname: sanic
top:
categories:
    - 框架
tags:
    - sanic
author: jesonlin
date: 2023-04-08 14:56:57
updated: 2023-04-08 14:56:57
---

下一代 Python Web 服务器/框架

<!-- more -->

## 简介
1. 是什么
    Sanic 是 Python Web 服务器和 Web 框架，旨在提高性能。  
    Sanic 不仅仅是一个 框架，它还是一个 Web 服务器，具备开箱即用的功能。  
    它可以用于编写，部署和扩展生产级 Web 应用程序。 🚀
    Build fast. Run fast.
2. 目标
    提供一种简单且快速，集创建和启动于一体的方法，来实现一个易于修改和拓展的 HTTP 服务


## 安装
```bash
# centos7 安装 python3.9
cd /usr/local
wget https://www.python.org/ftp/python/3.9.10/Python-3.9.10.tgz
tar xvf Python-3.9.10.tgz
cd Python-3.9.10/
./configure --enable-optimizations
sudo make altinstall
/usr/local/bin/python3.9 -m pip install --upgrade pip
pip39 install virtualenv

# 安装sanic 23.3.0
pip install sanic
# 安装扩展
pip install sanic-ext
```


## 实践-helloworld
1. 编辑server.py
    ```python
    from sanic import Sanic
    from sanic.response import text, json

    app = Sanic("MyHelloWorldApp")

    @app.route("/", methods=["POST", "GET"])
    async def hello_world(request):
        # return text("Hello, world.")
        return json({'hello': 'world'})


    if __name__ == '__main__':
        app.run(
            host="0.0.0.0",
            port=8000,
            workers=1,
            debug=True,
            access_log=True,  # 访问日志开关，进针对自带日志有效，自定义的无效
            auto_reload=True,  # 自动重载代码
            dev=True,  # 等价于debug+auto_reload
        )
    ```
2. 运行
    ```bash
    python server.py

    # 命令行验证
    curl localhost:8000 -i

    # 浏览器请求服务
    http://<服务器IP>:8000
    # 查看OpenAPI和运行的功能
    http://<服务器IP>:8000/docs
    ```


## 实践-docker
1. Dockerfile
    ```Dockerfile
    FROM sanicframework/sanic:3.8-latest

    WORKDIR /sanic

    COPY . .

    RUN pip install -r requirements.txt

    EXPOSE 8000

    CMD ["python", "server.py"]
    ```
2. server.py
    ```python
    #!/usr/bin/env python3
    # -*- coding=utf-8 -*-
    """docker_sanic
    """

    from sanic import Request, Sanic
    from sanic.response import text

    app = Sanic("MySanicApp")

    @app.get('/')
    async def hello(request):
        return text("OK!")

    if __name__ == '__main__':
        app.run(host='0.0.0.0', port=8000)
    ```
3. 启动
    ```bash
    docker run --name mysanic -p 8000:8000 -d my-sanic-image
    # 浏览器输入
    http://<服务器IP>:8000/
    ```
4. nginx_sanic.conf
    ```
    server {
        listen 80;
        listen [::]:80;
        location / {
          proxy_pass http://mysanic:8000/;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection upgrade;
          proxy_set_header Accept-Encoding gzip;
        }
    }
    ```
5. docker-compose.yml
    ```yml
    version: "3"

    services:
      mysanic:
        image: my-sanic-image
        ports:
          - "8000:8000"
        restart: always

      mynginx:
        image: nginx:1.13.6-alpine
        ports:
          - "80:80"
        depends_on:
          - mysanic
        volumes:
          - ./nginx_sanic.conf:/etc/nginx/conf.d/mysanic.conf
        restart: always

    networks:
      default:
        driver: bridge
    ```

6. 启动
    ```bash
    docker-compose up -d
    # 浏览器输入
    http://<服务器IP>:80/
    ```


## 基础
```python
#!/usr/bin/env python3
# -*- coding=utf-8 -*-
"""测试sanic框架
"""

__author__ = "jesonlin"


import os
import time
import multiprocessing
import uuid
import asyncio
from functools import wraps
from sanic import Request, Sanic
from sanic.response import text, empty, file, redirect
from sanic.response import json as rsp_json
from sanic.handlers import ErrorHandler

# from sanic.log import logger
import logging
from config import LOGGING_CONFIG

# from sanic_ext import openapi


# 自定义request.id属性
class MyRequest(Request):
    """重载request加入请求id"""

    @classmethod
    def generate_id(cls, *_):
        """请求id默认自带就是用uuid"""
        return uuid.uuid4()
        # return time.time_ns()


# 自定义错误响应函数
class CustomErrorHandler(ErrorHandler):
    """自定义错误响应函数"""

    def default(self, request, exception):
        """handles errors that have no error handlers assigned"""
        # 自定义的错误处理逻辑
        print("这里发生错误|%s" % exception)
        return super().default(request, exception)


DIR_BASE = os.path.dirname(os.path.abspath(__file__))
DIR_LOG = "{}/logs".format(DIR_BASE)
if not os.path.exists(DIR_LOG):
    os.makedirs(DIR_LOG)
logger = logging.getLogger(__name__)


# 实例化app
app = Sanic("MyHelloWorldApp", request_class=MyRequest, error_handler=CustomErrorHandler(), log_config=LOGGING_CONFIG)
# 上下文对象
# app.ctx.db = Database()

# 通过配置文件加载更新配置
app.update_config("./config.py")


@app.get("/")
# @openapi.no_autodoc  # 忽略生成文档
async def hello_world(request):
    """第一个sanic接口

    这是sanic的第一个接口，这里是接口注释。

    openapi:
    ---
    operationId: fooDots
    tags:
      - API
    parameters:
        - name: name
          in: query
          description: 用户名
          required: True
          schema:
            type: string
            format: str
        - name: age
          in: query
          description: 用户年龄
          required: False
          schema:
            type: integer
            format: int32
    responses:
      '200':
        description: Just some dots
    """
    logger.info("Here is your logssss: hello_world")

    # return text("hello world.")
    # return rsp_json({"hello": "world"})
    return rsp_json(
        {
            "token": request.token,
            "user": request.ctx.user,
            "id": str(request.id),
            "AUTO_RELOAD": app.config.AUTO_RELOAD,
            "REQUEST_TIMEOUT": app.config.REQUEST_TIMEOUT,
            "config": str(app.config),
        }
    )


# ============================================ #
# 响应函数: https://sanic.dev/zh/guide/basics/handlers.html#%E5%85%B3%E4%BA%8E-async-a-word-about-async
# 响应函数(Handlers)，也就是我们通常所说的视图(views)。
# 它至少以一个 request 实例作为参数，并返回一个 HTTPResponse 实例或一个执行其他操作的协同程序作为响应。
# ============================================ #
@app.get("/async")
async def async_handler(request):
    """异步响应接口，请求体"""
    await asyncio.sleep(0.1)

    # json -d '{"foo": "bar"}'
    print(request.json)  # {'foo': 'bar'}
    # raw -d '{"foo": "bar"}'
    print(request.body)  # b'{"foo": "bar"}'
    # form -d 'foo=bar'
    print(request.body)  # b'foo=bar'
    print(request.form)  # {'foo': ['bar']}
    print(request.form.get("foo"))  # bar
    print(request.form.getlist("foo"))  # ['bar']
    # upload -F 'my_file=@/path/to/TEST'
    print(request.body)
    print(request.files)  # {'my_file': [File(type='application/octet-stream', body=b'hello\n', name='TEST')]}
    print(request.files.get("my_file"))  # File(type='application/octet-stream', body=b'hello\n', name='TEST')
    print(request.files.getlist("my_file"))  # [File(type='application/octet-stream', body=b'hello\n', name='TEST')]

    return rsp_json({"async_handlerf": "Done."})


# ============================================ #
# 请求: https://sanic.dev/zh/guide/basics/request.html
# 所有的 响应函数 都必须返回一个 response 对象，中间件 可以自由选择是否返回 response 对象。
# ============================================ #
async def _fetch_user_by_token(token):
    """异步根据token获取用户名"""
    return "jesonlin"


@app.middleware("request")
async def run_before_handler(request):
    """request中间件封装参数进上下文对象
    通常被用来存储服务端通过某些验证后需要临时存储的身份认证信息以及专有变量等内容。
    """
    request.ctx.user = await _fetch_user_by_token(request.token)


@app.route("/ctx")
async def ctx(request):
    """测试上下文对象取值"""
    print("这里是接口内部")
    return text("Hi, my name is {}\napp.ctx is {}".format(request.ctx.user, app.ctx))


# @app.route("/route_tag/<tag:int>")
@app.route(r"/route_tag/<tag:[a-z]{3}.txt>", name="get_route_tag")
async def route_tag(request, tag: str):
    """测试提取路径部分参数
    <tag:int>  路径参数强制转换为int
    """

    return text("tag: %s\n args: %s" % (tag, request.args))


@app.route("/args/")
async def args(request):
    """返回请求参数"""

    # 和上述的 request.form、request.files 对象一样，request.args 同样是少数几种字典之一，每个值都是一个列表。这是因为HTTP允许单个键名被重用以发送多个值。
    print(request.args)
    # curl 'http://localhost:8000/print_args?key1=val1&key2=val2&key1=\[val3\]'
    # {'key1': ['val1', '[val3]'], 'key2': ['val2']}
    # 'val1'
    print(request.args.get("key1"))
    # ['val1', '[val3]']
    print(request.args.getlist("key1"))
    # [('key1', 'val1'), ('key2', 'val2'), ('key1', '[val3]')]
    print(request.query_args)
    # key1=val1&key2=val2&key1=[val3]
    print(request.query_string)
    return text("args - type:{} - value:{}".format(type(request.args), request.args))


# ============================================ #
# 响应: https://sanic.dev/zh/guide/basics/response.html
# 所有的 响应函数 都必须返回一个 response 对象，中间件 可以自由选择是否返回 response 对象。
# ============================================ #
@app.route("/response/")
async def response(request):
    """测试响应函数返回不同类型"""

    # return text("Hi 😎")
    # return rsp_json({"foo": "bar"})
    # return redirect("/hi")
    return await file("A.jpg")
    # return empty()


@app.route("/route", methods=["POST", "GET"])
# @app.post("/route")
# @app.get("/route")
async def route(request):
    """中间件添加路由"""

    return text("OK")


# ============================================ #
# 路由: https://sanic.dev/zh/guide/basics/routing.html#%E6%B7%BB%E5%8A%A0%E8%B7%AF%E7%94%B1-adding-a-route
# ============================================ #
@app.route("/dynamic_route")
async def dynamic_route(request):
    """动态路由"""

    # 您可以传递任意数量的关键字参数，任何非路由参数的部分都会被是做为查询字符串的一部分
    # 同样支持为一个键名传递多个值
    # url = app.url_for("route_tag", tag="abc.txt", arg_one=["one", "two"])
    # 有路由名称name则只能通过路由名称name
    url = app.url_for("get_route_tag", tag="abc.txt", arg_one=["one", "two"])
    return redirect(url)


# ============================================ #
# 监听器: https://sanic.dev/zh/guide/basics/listeners.html
# 允许您将功能挂载到工作进程的生命周期
# ============================================ #
@app.reload_process_start
async def reload_process_start(*_):
    """监听重载进程，只一次"""
    print(">>>>>> 监听重载进程 <<<<<<")


@app.main_process_start
async def main_process_start(*_):
    """监听启动程序进程，只一次"""

    print(">>>>>> 监听主程序启动 <<<<<<")


@app.before_server_start
async def before_server_start(app):  # pylint: disable=redefined-outer-name
    """监听服务启动前"""

    print(">>>>>> 监听服务启动前 <<<<<<")
    app.ctx.db = "DB_INFO"


@app.after_server_start
async def after_server_start(app):  # pylint: disable=redefined-outer-name
    """监听服务启动后"""

    print(">>>>>> 监听服务启动后 <<<<<<")


@app.before_server_stop
async def before_server_stop(app):  # pylint: disable=redefined-outer-name
    """监听服务结束前"""

    print(">>>>>> 监听服务结束前 <<<<<<")


@app.after_server_stop
async def after_server_stop(app):  # pylint: disable=redefined-outer-name
    """监听服务结束后"""

    print(">>>>>> 监听服务结束后 <<<<<<")


@app.main_process_stop
async def main_process_stop(app):  # pylint: disable=redefined-outer-name
    """监听器 设置DB"""

    print(">>>>>> 监听主程序结束 <<<<<<")


# ============================================ #
# 中间件: https://sanic.dev/zh/guide/basics/middleware.html
# 中间件允许您将功能挂载到 HTTP 流的生命周期。
# 可以在执行响应函数之前或者响应函数之后执行中间件
# 如果中间件返回了一个 HTTPResponse 对象， 那么请求将会终止，此对象将会作为最终响应进行返回。
# 返回 None 值来跳过某个中间件的执行
# ============================================ #
# @app.middleware("request")
@app.on_request  # 等价于
async def extract_user(request):
    """请求中间件"""
    return None


@app.on_response
async def prevent_xss(request, response):  # pylint: disable=redefined-outer-name
    """响应中间件"""
    response.headers["X-Request-ID"] = request.id
    return None


# ============================================ #
# 标头: https://sanic.dev/zh/guide/basics/headers.html
# 令牌(Token)：从请求头中解析到的 Token <token> 或者 Bearer <token> 将会被赋值给 request.token
# 请求头、响应头
# ============================================ #
@app.route("/hosts", name="foo")
async def hosts(request):
    """查看hosts 信息"""

    return rsp_json(
        {
            "effective host": request.host,  # "<服务器IP>:8000"
            "host header": request.headers.get("host"),  # "<服务器IP>:8000"
            "forwarded host": request.forwarded.get("host"),  # null
            "you are here": request.url_for("foo"),  # "http://<服务器IP>:8000/hosts"
            "all": list(request.headers.items()),
        }
    )


# ============================================ #
# cookies: https://sanic.dev/zh/guide/basics/cookies.html#%E8%AF%BB%E5%8F%96-reading
# 通过 Request 对象的 cookies 字典来访问 Cookies。
# ============================================ #
@app.route("/cookie")
async def cookie(request):
    """读取cookies"""
    test_cookie = request.cookies.get("test")
    rsp = text("Test cookie: {}".format(test_cookie))
    # 写入cookies
    rsp.cookies["test"] = "cookies insert test"
    return rsp


# ============================================ #
# 后台任务: https://sanic.dev/zh/guide/basics/tasks.html#%E5%88%9B%E5%BB%BA%E4%BB%BB%E5%8A%A1-creating-tasks
# ============================================ #
async def my_task(app):  # pylint: disable=redefined-outer-name
    """后台任务"""

    print(f"MY TASK - PID: {os.getpid()}")
    with open("/data/service/python_project/project_sanic/task.txt", "a", encoding="UTF-8") as f:
        f.write("begint run...\n")
    await asyncio.sleep(2)
    app.add_task(my_task(app))


# 每个子进程都有
# app.add_task(slow_work)
# 通过任务别名查看任务
# task = app.get_task("slow_task")
# print("task=%s" % task)

# 要想只为主进程添加任务，您应该考虑使用 @app.main_process_start添加任务。
@app.before_server_start  # 每个子进程都有
# @app.main_process_start  # 没效果，暂不知道原因
async def my_outer_task(app, loop):
    print("=====>>>>开始添加任务")
    logger.info("=====>>>>开始添加任务")
    app.add_task(my_task(app))


# ============================================ #
# 装饰器: https://sanic.dev/zh/guide/best-practices/decorators.html
# ============================================ #
async def check_request_for_authorization_status(request):
    """验证权限"""
    return True


def authorized():
    """权限装饰器"""

    def decorator(f):
        @wraps(f)
        async def decorated_function(request, *args, **kwargs):
            # run some method that checks the request
            # for the client's authorization status
            is_authorized = await check_request_for_authorization_status(request)

            if is_authorized:
                # the user is authorized.
                # run the handler method and return the response
                rsp = await f(request, *args, **kwargs)
                return rsp
            # the user is not authorized.
            return rsp_json({"status": "not_authorized"}, 403)

        return decorated_function

    return decorator


# ============================================ #
# 日志: https://sanic.dev/zh/guide/best-practices/logging.html
# 默认日志， from sanic.log import logger  logger.info(""")
# 自定义日志
# import logging
# DIR_BASE = os.path.dirname(os.path.abspath(__file__))
# DIR_LOG = "{}/logs".format(DIR_BASE)
# if not os.path.exists(DIR_LOG):
#     os.makedirs(DIR_LOG)
# LOGGING_CONFIG = {
#     "version": 1,
#     "disable_existing_loggers": False,
#     "formatters": {
#         "verbose": {"format": "[%(levelname)s %(asctime)s %(name)s %(lineno)d %(process)d %(thread)d] %(message)s"}
#     },
#     "handlers": {
#         "console": {
#             "level": "INFO",
#             "class": "logging.StreamHandler",
#             "formatter": "verbose",
#         },
#         "backend_file": {
#             "delay": True,
#             "class": "logging.handlers.TimedRotatingFileHandler",
#             "filename": "./logs/main.log",  # 日志输出文件
#             "when": "D",
#             "backupCount": 10,  # 备份份数
#             "formatter": "verbose",  # 使用哪种formatters日志格式
#             "level": "DEBUG",
#         },
#         "backend_err_file": {
#             "delay": True,
#             "class": "logging.handlers.TimedRotatingFileHandler",
#             "filename": "./logs/main_error.log",  # 日志输出文件
#             "when": "D",
#             "backupCount": 10,  # 备份份数
#             "formatter": "verbose",  # 使用哪种formatters日志格式
#             "level": "ERROR",
#         },
#     },
#     "loggers": {
#         "": {
#             "handlers": ["backend_file", "backend_err_file"],
#             "level": "INFO",
#             "propagate": False,
#         }
#     },
# }
# logger = logging.getLogger(__name__)
# app = Sanic("MyHelloWorldApp", log_config=LOGGING_CONFIG)
# ============================================ #


# ============================================ #
# 运行: https://sanic.dev/zh/guide/deployment/running.html#sanic-%E6%9C%8D%E5%8A%A1%E5%99%A8-sanic-server
# 获取系统CPU核心数量
# workers = multiprocessing.cpu_count()
# app.run(host='0.0.0.0', port=8000, workers=workers, fast=True)
# gunicorn: 一个基于 UNIX 操作系统的 WSGI HTTP 服务器。它是从 Ruby 的 Unicorn 项目中移植而来，采用的是 pre-fork worker 模型。
# gunicorn myapp:app --bind 0.0.0.0:1337 --worker-class sanic.worker.GunicornWorker
# 如果您的应用有内存泄漏的困扰，您可以通过配置 Gunicorn 使子进程在处理了一定数量的请求后平滑重启。这种方法可以很方便得减少内存泄漏带来的影响。
# 当通过 gunicorn 运行 Sanic 时，您将失去 async/await 带来的诸多性能优势。对于该种部署方式，请三思而后行。的确，Gunicorn 提供了很多配置选项，但它不是让 Sanic 全速运行的最佳坏境。
# ============================================ #


if __name__ == "__main__":
    app.run(
        host="0.0.0.0",
        port=8000,
        # workers=workers,
        fast=True,  # 自动以系统最大的核心数量来创建工作线程
        debug=True,
        access_log=True,  # 访问日志开关，进针对自带日志有效，自定义的无效
        auto_reload=True,  # 自动重载代码
        # dev=True,  # 等价于debug+auto_reload
    )
```


## REFERENCE
[官网](https://sanic.dev/zh/)
[sanic-自助生成文档](https://sanic.dev/zh/plugins/sanic-ext/openapi/autodoc.html)
