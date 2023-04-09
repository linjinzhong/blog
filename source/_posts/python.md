---
title: python
urlname: python
top: false
categories:
    - 编程语言
tags: 
    - python
author: jesonlin
date: 2022-10-08 19:30:23
updated: 2022-10-08 19:30:23
---

Python 是一种解释型、面向对象、动态数据类型的高级程序设计语言。

<!-- more -->


## 实践1
```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""文档注释

文档功能描述

"""

__author__ = 'Jeson Lin'


import sys


class SampleClass(object):
    """类功能

    类功能详细描述

    Attributes:
        attr1: 属性1描述
        attr2: 属性2描述
    """

    def __init__(self, attr1=False):
        """初始化 SampleClass 用attr1"""
        self.attr1 = attr1
        self.attr2 = 0

    def _inner_methon(self):
        """类内自身调用方法"""
        pass

    def public_method(self):
        """公共方法"""
        pass


def sample_function(param1: str, param2: 'int > 0', param3: bool = False) -> dict:
    """函数功能

    函数功能详细描述

    Args:
        param1: 参数1说明。
        param2: 参数2说明。

    Returns:
        返回值描述
        example:

        {
            "a": 1,
            "b": 2
        }

        返回详细描述

    Raises:
        IOError: 异常描述
    """

    # 函数功能逻辑
    pass
    return None


if __name__ == '__main__':
    print('hello, world')
```


## 安装和配置
1. centos
    ```
    yum install -y python36
    ln -s python3.6 python3
    yum install -y python36-setuptools
    easy_install pip 
    ```
2. mac
    ```
    # 官网下载安装包
    https://www.python.org/downloads/

    # 安装了Homebrew
    brew install python3
    ```
3. 解释器
    安装python后，命令行运行`python`启动python交互解释器。  
    1. CPython  
        用`>>>`作为提示符，用C语言开发
    2. IPython  
        用`In [序号]:`作为提示符，基于CPython之上，增强交互。
    3. PyPy  
        PyPy采用JIT技术，对Python代码进行动态编译（注意不是解释），所以可以显著提高Python代码的执行速度。需要了解PyPy和CPython的不同点。
    4. Jython  
        Jython是运行在Java平台上的Python解释器，可以直接把Python代码编译成Java字节码执行。
4. Shebang
    1. 在计算机科学中，Shebang (也称为Hashbang)是一个由井号和叹号构成的字符串行(`#!`)，其出现在文本文件的第一行的前两个字符。  
    2. 在文件中存在Shebang的情况下， 类Unix操作系统的程序载入器会分析Shebang后的内容，将这些内容作为解释器指令， 并调用该指令，并将载有Shebang的文件路径作为该解释器的参数。  
    3. 例如，以指令`#!/bin/sh`开头的文件在执行时会实际调用`/bin/sh 程序`。)


## 虚拟环境-virtualenv
1. 升级pip
    ```
    # 升级pip
    python3 -m pip install --upgrade --force-reinstall pip

    # pip 位置变成/usr/local/bin/pip
    which pip

    # pip 缓存没更新/usr/bin/pip
    type pip

    # 更新缓存
    hash -r
    ```
2. 安装virtualenv
    ```
    # 安装
    pip install virtualenv

    # 测试
    virtualenv --version

    # 使用
    mkdir project_virtualenv
    cd project_virtualenv
    virtualenv venv

    # 激活虚拟环境
    source venv/bin/activate

    # 虚拟环境安装python包
    pip install requests

    # 虚拟环境查看安装包
    pip freeze

    # 虚拟环境导出已安装包
    pip freeze > requirements.txt

    # 根据requirements.txt重新安装所有包
    pip install -r requirements.txt

    # 退出虚拟环境
    deactivate
    ```


## 基础
1. 编码和字符串
    1. `ASCII`：1个字节，能表示最大整数是2^8-1=255。
    2. `Unicode`：2个字节，能表示最大整数是2^16-1=65535。
    3. `UTF-8`：1-6个字节，可变长编码，英文字母1个字节，汉字3个字节，很生僻字符4~6个字节，基于传输存储空间节约角度。
    ```
    # Python 3版本中，字符串是以Unicode编码的

    # 字符对应的整数
    ord('A')

    # 整数对应的字符
    chr(66)

    # 二进制
    x = b'ABC'

    # str转ascii(bytes)
    'ABC'.encode('ascii')

    # str转utf-8(bytes)
    '中文'.encode('utf-8')

    # bytes转str，用errors忽略无法解码的字节
    b'ABC'.decode('ascii')
    b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8', errors='ignore')

    # 格式化字符串三种方式
    str1 = 'Age: %s. Gender: %s, rate: %.2f' % (25, True, 6.186)
    str2 = 'Age: {0}. Gender: {1}, rate: {2:.2f}'.format(25, True, 6.186)
    rate=6.186
    str3 = f'Age: {25}. Gender: {True}, rate: {rate:.2f}'
    ```
2. list和tuple
    ```
    # list 可变，如下方法
    append(元素)
    insert(位置, 元素)
    pop()  # 弹出最后一个
    pop(0)  # 弹出第一个

    # tuple 指向不可变
    t = (1,)  # tuple定义一个元素
    ```
3. dict和set
    ```
    # dict 删除
    A = {'a':1,'b':2}
    A.pop('c', None)  # key不存在不报错

    # set 删除
    A = {'a','b','c'}
    A.remove('d')  # key不存在报错
    A.discard('d')  # key不存在不报错

    # set 交集、并集、差集
    &、|、-
    ```
4. 生成器
    ```
    # 生成器1-()
    g = (x * x for x in range(10))

    # 迭代
    next(g)
    # 或
    for x in g:
        print(x)

    # e.g. 斐波拉契数列（Fibonacci），除第一个和第二个数外，任意一个数都可由前两个数相加得到：
    def fibonacci(N):
        a, b, n = 0, 1, 0
        while n < N:
            print(b)
            a, b = b, a + b
            n += 1
        return None

    # 生成器2-yield
    def fibonacci(N):
        a, b, n = 0, 1, 0
        while n < N:
            yield b
            a, b = b, a + b
            n += 1
        return None
    ```


## 函数式编程
1. 高阶函数-map/reduce/filter/sorted
    1. map
        ```
        map(func, iterable)

        # e.g.
        list(map(str, [1, 2, 3, 4]))
        ```
    2. reduce
        ```
        reduce(func, iterable)

        reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)

        # e.g. 正整数字符串转正整数
        from functools import reduce
        def str2int(s):
            return reduce(lambda x, y: x * 10 + y, map(lambda x: ord(x) - ord('0'), s))
        ```
    3. filter
        ```
        filter(func, iterable)

        # e.g. 保留list中的奇数
        list(filter(lambda x: x % 2 == 1, [1, 2, 4, 5, 6, 9, 10, 15]))

        # e.g. 保留list中的非空字符串
        list(filter(lambda x: x and x.strip(), ['A', '', 'B', None, 'C', '  ']))
        ```
    4. sorted
        ```
        sorted(iterable, key=abs)

        # e.g. 绝对值排序
        sorted([36, 5, -12, 9, -21], key=abs)

        # e.g. 默认按ascii大小排序
        sorted(['bob', 'about', 'Zoo', 'Credit'])
        # 等价于
        sorted(['bob', 'about', 'Zoo', 'Credit'], key=str)

        # 忽略大小写排序
        sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower)

        # 反向排序加参数reverse=True
        ```
2. 返回函数-返回的是一个函数，闭包
    1. 单个函数返回
        ```
        def lazy_sum(*args):
            def sum():
                ax = 0
                for n in args:
                    ax = ax + n
                return ax
            return sum

        >>> f = lazy_sum(1, 3, 5, 7, 9)
        >>> f
        <function lazy_sum.<locals>.sum at 0x7f4f9c8c3730>

        # f实际是返回的sum函数定义+参数，调用f()才真正执行
        >>> f()
        25
        ```
    2. 多个函数返回
        ```
        def count():
            fs = []
            for i in range(1, 4):
                def f():
                     return i*i
                fs.append(f)
            # 循环3次，fs = [func1+i, func2+i, func3+i]
            # 最终返回是把i变成最后迭代完的3
            return fs

        f1, f2, f3 = count()

        # 所以
        f1() == f2() == f3() == 9
        ```
3. 匿名函数
    ```
    f = lambda x: x * x
    ```
4. 装饰器
    ```
    import functools

    def log(text):
        def decorator(func):
            @functools.wraps(func)
            def wrapper(*args, **kw):
                print('%s %s():' % (text, func.__name__))
                return func(*args, **kw)
            return wrapper
        return decorator

    @log('execute')
    def now():
        print("2020-02-02")

    # 等价于
    now = log('execute')(now)
    ```
5. 偏函数
    ```
    # 把一个函数的某些参数给固定住
    import functools
    func_new = functools.partial(func, *args, **kw)

    # 自动替换默认参数的值
    func_new = functools.partial(func, args1=默认值)

    # 将默认值放在函数最左边
    func_new = functools.partial(func, 默认值)

    # 参数展开
    func_new = functools.partial(func, {arg1:默认值})
    ```


## 模块
1. 结构
    ```
    mycompany  # 包名
    ├─ __init__.py  # 有这个文件才是包目录，本身就是模块，模块名是mycompany
    ├─ abc.py  # 模块名是文件名mycompany.abc
    └─ xyz.py  # 模块名是mycompanyxyz
    ```
2. 使用
    当我们在命令行运行hello模块文件时，Python解释器把一个特殊变量__name__置为__main__，而如果在其他地方导入该hello模块时，if判断将失败，因此，这种if测试可以让一个模块通过命令行运行时执行一些额外的代码，最常见的就是运行测试。
    ```
    if __name__ == '__main__':
        test()
    ```
3. 安装
    ```
    pip install requests
    ```


## 面向对象编程
1. 类和实例
    ```
    class Student(object):
        
        def __init__(self, name, score):
            self.name = name
            self.__score = score  # 私有变量，不能直接访问
                
        def get_score(self):
            return self.__score

        def set_score(self, score):
            if 0 <= score <= 100:
                self.__score = score
            else:
                raise ValueError('bad score')
            self.__score = score

        def get_grade(self):
            if self.__score >= 90:
                return 'A'
            elif self.__score >= 60:
                return 'B'
            else:
                return 'C'

    stu1 = Student('jesonlin', 99)
    ```
2. 继承和多态
    1. 类的定义  
        当我们定义一个class的时候，我们实际上就定义了一种数据类型。
    2. 继承  
        子类继承父类的全部功能，同名功能则覆盖父类功能。
    3. 多态  
        同名功能则覆盖父类功能，父类参数可用子类实例。
    4. 开闭原则  
        对扩展开放，允许增加子类；对修改封闭，不需要修改父类函数，只需要修改新增子类函数。有点动态绑定意思。
    5. 鸭子类型  
        静态语言的父类参数对象必须是父类实例或者子类实例；而动态语言则只要子类有对应方法即可，不一定要求是继承自该父类。这就是动态语言的“鸭子类型”，它并不要求严格的继承体系，一个对象只要“看起来像鸭子，走起路来像鸭子”，那它就可以被看做是鸭子。
3. 类型判断
    ```
    isinstance([1, 2, 3], list)
    isinstance((1, 2, 3), (list, tuple))
    class Person(object):
        def __init__(self, name):
            self.name = name

    person = Person()
    isinstance(person, Person)
    ```
4. 属性/方法
    ```
    # 获取所有属性和方法
    dir('ABC')

    # __xxx__的属性和方法在Python中都是有特殊用途的
    len('ABC') == 'ABC'.__len__()

    # 判断属性或方法
    hasattr(person, 'x')

    # 获取属性或方法
    getattr(person, 'z', '不存在')

    # 设置属性
    setattr(person, 'y')

    # 定义方法
    def set_name(self, name):
        self.name = name

    # 设置类方法
    Person.set_name = set_name

    # 设置实例方法
    from types import MethodType
    person.set_name = MethodType(set_name, person)

    # 限制类属性-仅对当前类实例起作用，子类不起左右，即子类没有继承__slots__
    class Student(object):
        # 用tuple定义允许绑定的属性名称
        __slots__ = ('name', 'age')

    # 方法用属性方式调用，@property
     def Student(object):
        @property
        def score(self):
            return self._score
        @score.setter
        def score(self, value):
            if not isinstance(value, int):
                raise ValueError('score must be an integer!')
            if value < 0 or value > 100:
                raise ValueError('score must between 0 ~ 100!')
            self._score = value
    s = Student()
    s.score = 99  # 实际转换为s.set_score(99)
    s.score  # 实际转换为s.get_score()
    ```
5. 多重继承
    若是父类中有相同的方法名，而在子类使用时未指定，python从左至右搜索。即方法在子类中未找到时，从左到右查找父类中是否包含方法。
6. 定制类
    ```
    # 限制当前类对象属性
    __slots__

    # 作用len(实例)
    __len__()

    # print打印类
    __str__()

    # 直接打印类
    __repr__()

    # 类似列表迭代
    __iter__()
    class Fib(object):
        def __init__(self):
            self.a, self.b = 0, 1 # 初始化两个计数器a，b

        def __iter__(self):
            return self # 实例本身就是迭代对象，故返回自己

        def __next__(self):
            self.a, self.b = self.b, self.a + self.b # 计算下一个值
            if self.a > 100000: # 退出循环的条件
                raise StopIteration()
            return self.a # 返回下一个值
    for n in Fib():
        print(n)
        
    # 类似列表取值
    __getitem__()
    class Fib(object):
    def __getitem__(self, n):
        a, b = 1, 1
        for x in range(n):
            a, b = b, a + b
        return a
    f = Fib()
    f[1]

    # 类似列表切片
    class Fib(object):
        def __getitem__(self, n):
            if isinstance(n, int): # n是索引
                a, b = 1, 1
                for x in range(n):
                    a, b = b, a + b
                return a
            if isinstance(n, slice): # n是切片
                start = n.start
                stop = n.stop
                if start is None:
                    start = 0
                a, b = 1, 1
                L = []
                for x in range(stop):
                    if x >= start:
                        L.append(a)
                    a, b = b, a + b
                return 
    f = Fib()
    f[0:5]
    ```
7. 枚举类
    ```
    from enum import Enum, unique

    # Month类继承Enum，有()成员属性，mth是类实例
    mth = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'))
    # value属性则是自动赋给成员的int常量，默认从1开始计数。
    for name, member in mth.__members__.items():
        print(name, '=>', member, ',', member.value)

    @unique  # 装饰器可以帮助我们检查保证没有重复值。
    class Weekday(Enum):
        Sun = 0 # Sun的value被设定为0
        Mon = 1
        Tue = 2
        Wed = 3
        Thu = 4
        Fri = 5
        Sat = 6

    Weekday.Sun == Weekday['Sun']
    <Weekday.Sun: 0>  # 返回形式
    Weekday.Sun.value
    0  # 返回形式
    for name, member in Weekday.__members__.items():
        print(name, '=>', member)
    for name, member in Weekday.__members__.items():
        print(name, '=>', member.value)
    ```
8. 元类
    ```
    # class的type类型就是type
    type(Person)

    # 通过type创建Hello类
    # 类名，父类的元组形式，方法名称与函数绑定
    Hello = type('Hello', (object,), dict(hello=fn)) # 创建Hello class
    ```


## IO 编程
1. 文件读写
    ```
    # 读取utf-8文本文件
    with open('/path/to/file', 'r') as f:
        # 打印全部
        print(f.read())

        # 按行打印
        for line in f.readlines():
            print(line.strip()) # 把末尾的'\n'删掉

    # 读取二进制文件如图片、视频
    with open('/path/to/file', 'rb') as f:
        print(f.read())

    # 非UTF-8编码的文本文件
    with open('/path/to/file', 'r', encoding='gbk', errors='ignore') as f:
        print(f.read())

    # 写入文件，追加用'a'
    with open('/path/to/file', 'w', encoding='UTF-8') as f:
        f.write('Hello, world!')
    ```
2. 内存读写
    ```
    from io import StringIO
    f = StringIO()
    f.write('hello')  # 返回5
    f.write(' ')  # 返回1
    f.write('world!')  # 返回6
    print(f.getvalue())  # 返回hello world!

    from io import BytesIO
    f = BytesIO()
    f.write('中文'.encode('utf-8'))  # 返回6表示6个字节
    print(f.getvalue())  # 返回b'\xe4\xb8\xad\xe6\x96\x87'
    ```
3. 操作文件和目录
    ```
    import os
    os.name  # 操作系统类型
    os.uname  # 详细的系统信息
    os.environ  # 环境变量
    os.environ.get('PATH')

    os.path.abspath('.')  # 查看当前目录的绝对路径
    os.path.join('/Users/michael', 'testdir')  # 合并路径
    os.mkdir('/Users/michael/testdir')  # 创建目录
    os.rmdir('/Users/michael/testdir')  # 删除目录
    os.path.split('/Users/michael/testdir/file.txt')  # 拆分路径
    os.rename('test.txt', 'test.py')  # 重命名文件
    os.remove('test.py')  # 删除文件

    # shutil模块提供了copyfile()的函数，你还可以在shutil模块中找到很多实用函数，它们可以看做是os模块的补充。

    # 列出当前所有目录
    [x for x in os.listdir('.') if os.path.isdir(x)]
    ```
4. 序列化
    1. pickle-只能在同版本python
        ```
        import pickle
        d = dict(name='jesonlin', age=30, score=99)

        # 序列化
        # 任意对象序列化成一个bytes，然后写入文件
        dd = pickle.dumps(d)
        # 或者直接序列化写入一个-file-like object
        with open('dump.txt', 'wb') as f:
            pickle.dump(d, f)

        # 反序列化
        ddd = pickle.loads(dd)
        # 或者直接从文件反序列化
        with open('dump.txt', 'rb') as f:
            ddd = pickle.load(f)
        ```
    2. json-跨语言传递对象
        ```
        import json
        d = dict(name='jesonlin', age=30, score=99)

        # 序列化
        dd = json.dumps(d)
        # 反序列化
        ddd = json.loads(dd)

        class Student(object):
            def __init__(self, name, age, score):
                self.name = name
                self.age = age
                self.score = score
        stu = Student('jesonlin', 30, 99)

        # 类对象序列化，无法直接序列化，可自定义使用转换函数转换为字典
        def stu2dict(stu):
            return {
                'name': stu.name,
                'age': stu.age,
                'score': stu.score
            }
        dd = json.dumps(stu, default=stu2dict)
        # 通用的
        dd = json.dumps(stu, default=lambda obj: obj.__dict__)

        # 类对象反序列
        def dict2stu(d):
            return Student(d['name'], d['age'], d['score'])
        json.loads(dd, object_hook=dict2stu)
        # 通用的
        json.loads(dd, object_hook=lambda obj: Student(**obj))
        ```


## 进程和线程
0. 进程 vs. 线程
    1. 进程
        1. 优点：稳定性高，一个子进程挂了不影响其他进程；
        2. 缺点：效率低，创建和调度进程开销大，进程数有限；
    2. 线程
        1. 优点：效率高，创建和调度线程开销较小；
        2. 缺点：稳定性低，一个线程挂了可能导致整个进程崩溃，因为所有线程共享进程内存。
1. fork
    Unix/Linux操作系统提供了一个fork()系统调用，它非常特殊。普通的函数调用，调用一次，返回一次，但是fork()调用一次，返回两次，因为操作系统自动把当前进程（称为父进程）复制了一份（称为子进程），然后，分别在父进程和子进程内返回。
    ```
    import os

    print('process (%s) start...' % os.getpid())

    # Only works on Unix/Linux/Mac:
    pid = os.fork()
    
    if pid == 0:
        print('I am child process (%s) and my parent is %s.' % (os.getpid(), os.getppid()))
    else:
        print('I (%s) just created a child process (%s).' % (os.getpid(), pid))
    ```
2. 进程
    1. 单个进程-Process
        ```
        from multiprocessing import Process
        import os, time

        def run_proc(name):
            print('Begin run child process %s (%s)...' % (name, os.getpid()))
            time.sleep(5)
            print('End run child process %s (%s)...' % (name, os.getpid()))

        if __name__ == '__main__':
            print('Parent process %s.' % os.getpid())
            p = Process(target=run_proc, args=('test',))
            print('Child process will start.')
            p.start()  # 开始执行子进程
            p.join()  # 等待子进程结束后再继续往下运行，通常用于进程间的同步
            print('Child process end.')
        ```
    2. 进程池-Pool
        ```
        from multiprocessing import Pool
        import os, time, random

        def long_time_task(name):
            print('Run task %s (%s)...' % (name, os.getpid()))
            start = time.time()
            time.sleep(random.random() * 3)
            end = time.time()
            print('Task %s runs %0.2f seconds.' % (name, (end - start)))

        if __name__ == '__main__':
            print('Parent process %s.' % os.getpid())
            p = Pool(4)
            for i in range(5):
                # 立刻执行，无需等待，异步非阻塞
                p.apply_async(long_time_task, args=(i,))
            print('Waiting for all subprocesses done...')
            p.close()  # 调用close()之后就不能继续添加新的Process了
            p.join()  # 等待所有子进程执行完毕
            time.sleep(2)
            print('All subprocesses done.')
        ```
    3. 子进程-subprocess
        ```
        import subprocess

        # 直接调用命令
        print('$ nslookup www.python.org')
        subprocess.call(['nslookup', 'www.python.org'])
        print('Exit code: %s' % r)

        # 交互调用命令
        print('$ nslookup')
        p = subprocess.Popen(['nslookup'], stdin=subprocess.PIPE, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
        output, err = p.communicate(b'set q=mx\npython.org\nexit\n')
        print(output.decode('utf-8'))
        print('Exit code: %s' % p.returncode)
        ```
    4. 进程间通信
        ```
        from multiprocessing import Process, Queue
        import os, time, random

        # 写数据进程执行的代码:
        def write(q):
            print('Process to write: %s' % os.getpid())
            for value in ['A', 'B', 'C']:
                print('Put %s to queue...' % value)
                q.put(value)  # 队列写入
                time.sleep(random.random())

        # 读数据进程执行的代码:
        def read(q):
            print('Process to read: %s' % os.getpid())
            while True:
                value = q.get(True)  # 队列读出
                print('Get %s from queue.' % value)

        if __name__ == '__main__':
            # 父进程创建Queue，并传给各个子进程：
            q = Queue()
            pw = Process(target=write, args=(q,))
            pr = Process(target=read, args=(q,))
            # 启动子进程pw，写入:
            pw.start()
            # 启动子进程pr，读取:
            pr.start()
            # 等待pw结束:
            pw.join()
            # pr进程里是死循环，无法等待其结束，只能强行终止:
            pr.terminate()
        ```
3. 线程
    多线程中，所有变量都由所有线程共享
    0. 模块
        Python的标准库提供了两个模块：\_thread和threading，\_thread是低级模块，threading是高级模块，对_thread进行了封装。绝大多数情况下，我们只需要使用threading这个高级模块。
    1. 启动线程-传入函数，创建Thread实例，调用start()开始执行：
        ```
        import threading
        import time

        def loop():
            print('thread %s is running...' % threading.current_thread().name)
            n = 0
            while n < 5:
                n = n + 1
                print('thread %s >>> %s' % (threading.current_thread().name, n))
                time.sleep(1)
            print('thread %s ended.' % threading.current_thread().name)

        print('thread %s is running...' % threading.current_thread().name)
        t = threading.Thread(target=loop, name='LoopThread')
        t.start()
        t.join()
        print('thread %s ended.' % threading.current_thread().name)
        ```
    2. 锁
        ```
        import threading
        import time

        # 假定这是你的银行存款:
        balance = 0
        # 锁
        lock = threading.Lock()

        def change_it(n):
            # 先存后取，结果应该为0:
            global balance
            balance = balance + n
            balance = balance - n

        def run_thread(n):
            for i in range(2000000):
                lock.acquire()
                try:
                    change_it(n)
                finally:
                    lock.release()

        t1 = threading.Thread(target=run_thread, args=(5,))
        t2 = threading.Thread(target=run_thread, args=(8,))
        t1.start()
        t2.start()
        t1.join()
        t2.join()
        print(balance)
        ```
    3. GIL锁
        Global Interpreter Lock，任何Python线程执行前，必须先获得GIL锁，然后，每执行100条字节码，解释器就自动释放GIL锁，让别的线程有机会执行。所以，多线程在Python中只能交替执行，即使100个线程跑在100核CPU上，也只能用到1个核。  
        Python虽然不能利用多线程实现多核任务，但可以通过多进程实现多核任务。多个Python进程有各自独立的GIL锁，互不影响。
    4. threading.local()-解决了参数在一个线程中各个函数之间互相传递的问题。
        ```
        import threading

        # 创建全局ThreadLocal对象
        # 全局变量local_school就是一个ThreadLocal对象，每个Thread对它都可以读写student属性，但互不影响。
        # 可以把local_school看成全局变量，但每个属性如local_school.student都是线程的局部变量，可以任意读写而互不干扰，也不用管理锁的问题，ThreadLocal内部会处理。
        # ThreadLocal最常用的地方就是为每个线程绑定一个数据库连接，HTTP请求，用户身份信息等，这样一个线程的所有调用到的处理函数都可以非常方便地访问这些资源。
        local_school = threading.local()

        def process_student():
            # 获取当前线程关联的student:
            std = local_school.student
            print('Hello, %s (in %s)' % (std, threading.current_thread().name))

        def process_thread(name):
            # 绑定ThreadLocal的student:
            local_school.student = name
            process_student()

        t1 = threading.Thread(target=process_thread, args=('A',), name='Thread-A')
        t2 = threading.Thread(target=process_thread, args=('B',), name='Thread-B')
        t1.start()
        t2.start()
        t1.join()
        t2.join()
        ```
4. 分布式进程
    1. 生成任务，放入任务队列，等待从结果队列取结果
        ```
        import random, time, queue
        from multiprocessing.managers import BaseManager

        # 发送任务的队列:
        task_queue = queue.Queue()
        # 接收结果的队列:
        result_queue = queue.Queue()

        # 从BaseManager继承的QueueManager:
        class QueueManager(BaseManager):
            pass

        # 把两个Queue都注册到网络上, callable参数关联了Queue对象:
        QueueManager.register('get_task_queue', callable=lambda: task_queue)
        QueueManager.register('get_result_queue', callable=lambda: result_queue)
        # 绑定端口5000, 设置验证码'abc':
        manager = QueueManager(address=('', 5000), authkey=b'abc')
        # 启动Queue:
        manager.start()
        # 获得通过网络访问的Queue对象:
        task = manager.get_task_queue()
        result = manager.get_result_queue()
        # 放几个任务进去:
        for i in range(10):
            n = random.randint(0, 10000)
            print('Put task %d...' % n)
            task.put(n)
        # 从result队列读取结果:
        print('Try get results...')
        for i in range(10):
            r = result.get(timeout=10)
            print('Result: %s' % r)
        # 关闭:
        manager.shutdown()
        print('master exit.')
        ```
    2. 消费任务，从任务队列获取，将结果放入结果队列
        ```
        import time, sys, queue
        from multiprocessing.managers import BaseManager

        # 创建类似的QueueManager:
        class QueueManager(BaseManager):
            pass

        # 由于这个QueueManager只从网络上获取Queue，所以注册时只提供名字:
        QueueManager.register('get_task_queue')
        QueueManager.register('get_result_queue')

        # 连接到服务器，也就是运行task_master.py的机器:
        server_addr = '127.0.0.1'
        print('Connect to server %s...' % server_addr)
        # 端口和验证码注意保持与task_master.py设置的完全一致:
        m = QueueManager(address=(server_addr, 5000), authkey=b'abc')
        # 从网络连接:
        m.connect()
        # 获取Queue的对象:
        task = m.get_task_queue()
        result = m.get_result_queue()
        # 从task队列取任务,并把结果写入result队列:
        for i in range(10):
            try:
                n = task.get(timeout=1)
                print('run task %d * %d...' % (n, n))
                r = '%d * %d = %d' % (n, n, n*n)
                time.sleep(1)
                result.put(r)
            except Queue.Empty:
                print('task queue is empty.')
        # 处理结束:
        print('worker exit.')
        ```


## 正则表达式
1. 正则表达式规则
    1. 如果直接给出字符，就是精确匹配。用 `\d` 可以匹配一个数字，`\w` 可以匹配一个字母或数字。
    2. `.` 匹配任意字符
    3. `*` 匹配任意个字符（包括0个）
    4. `+` 匹配至少一个字符
    5. `?` 匹配0个或1个字符
    6. `\s` 匹配一个空格
    7. `{n}` 匹配那个字符
    8. `{n-m}` 匹配n-m个前面字符  
        `\d{3}\s+\d{3,8}`
        ```
        \d{3} 表示匹配3个数字
        \s+   表示匹配至少一个空格
        \d{3,8} 表示匹配3-8个数字
        ```
    9. `[]` 表示范围
        ```
        [0-9a-zA-Z\_]+ 可以匹配至少由一个数字、字母或者下划线组成的字符串；
        [a-zA-Z\_][0-9a-zA-Z\_]* 可以匹配由字母或下划线开头，后接任意个由一个数字、字母或者下划线组成的字符串，也就是Python合法的变量；
        [a-zA-Z\_][0-9a-zA-Z\_]{0, 19}更精确地限制了变量的长度是1-20个字符（前面1个字符+后面最多19个字符）；
        ```
    10. `A|B` 表示匹配A或B，所以 `(P|p)ython` 可以匹配 `'Python'` 或者 `'python'`
    11. `^` 表示行的开头，`^\d` 表示必须以数字开头
    12. `$` 表示行的结尾，`\d$` 表示结尾必须以数字结束
2. re 模块
    1. python 也使用 `\` 转义
        ```
        s = 'ABC\\-001' # Python的字符串
        # 对应的正则表达式字符串变成：
        # 'ABC\-001'

        # 建议使用Python的r前缀，就不用考虑转义的问题了
        s = r'ABC\-001' # Python的字符串
        # 对应的正则表达式字符串不变：
        # 'ABC\-001'
        ```
    2. match-是否匹配
        ```
        import re
        # 匹配成功
        re.match(r'^\d{3}\-\d{3,8}$', '010-12345')
        # 匹配成功返回如下
        <_sre.SRE_Match object at 0x7f3d6a6017e8>
        # 匹配失败返回None
        re.match(r'^\d{3}\-\d{3,8}$', '010 12345')
        ```
    3. split-切分字符串
        ```
        >>> 'a b   c'.split(' ')
        # 无法失败联系的空格
        ['a', 'b', '', '', 'c']

        # 正则切割包含连续空格串
        >>> re.split(r'\s+', 'a b   c')
        ['a', 'b', 'c']

        # 正则切割包含联系空格和逗号串
        >>> re.split(r'[\s\,]+', 'a,b, c  d')
        ['a', 'b', 'c', 'd']
        ```

# 协程
1. asyncio
    ```
    import asyncio

    @asyncio.coroutine
    def wget(host):
        print('wget %s...' % host)
        connect = asyncio.open_connection(host, 80)
        reader, writer = yield from connect
        header = 'GET / HTTP/1.0\r\nHost: %s\r\n\r\n' % host
        writer.write(header.encode('utf-8'))
        yield from writer.drain()
        while True:
            line = yield from reader.readline()
            if line == b'\r\n':
                break
            print('%s header > %s' % (host, line.decode('utf-8').rstrip()))
        # Ignore the body, close the socket
        writer.close()

    loop = asyncio.get_event_loop()
    tasks = [wget(host) for host in ['www.sina.com.cn', 'www.sohu.com', 'www.163.com']]
    loop.run_until_complete(asyncio.wait(tasks))
    loop.close()
    ```
2. python3.5以上新写法
    ```
    @asyncio.coroutine
    def hello():
        print("Hello world!")
        r = yield from asyncio.sleep(1)
        print("Hello again!")

    # 新写法
    async def hello():
        print("Hello world!")
        r = await asyncio.sleep(1)
        print("Hello again!")
    ```


## REFERENCE
[官网](https://www.python.org/)
[Python最佳实践指南](https://pythonguidecn.readthedocs.io/zh/latest/)
[廖雪峰-python教程](https://www.liaoxuefeng.com/wiki/1016959663602400)
[谷歌-python风格指南](https://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/contents/)
[简单的http请求和响应服务](https://httpbin.org/)
