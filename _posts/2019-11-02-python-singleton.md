---
layout: post
title: Python单例模式
categories: Python
description: 程序运行时只能生成一个实例，避免对同一资源产生冲突的访问请求。
keywords: Python
---

Python创建单例模式4种方式：

1. 文件导入

   ```python
    in  single.py
        class Singleton():
            def __init__(self):
                pass
        site = Singleton()

    类似：
    import time  第一次已经把导入的time模块，放入内存
    import time  第二次内存已有就不导入了

    in  app.py
        from single.py import site #第一次导入，实例化site对象并放入内存

    in  views.py
        from single.py import site #第二次导入，直接从内存拿。
   ```

1. 装饰器

   ```python
    def Singleton(cls):
        _instance = {}
        def _singleton(*args, **kargs):
            if cls not in _instance:
                _instance[cls] = cls(*args, **kargs)
            return _instance[cls]
        return _singleton

    @Singleton
    class A(object):
        a = 1
        def __init__(self, x=0):
            self.x = x

    a1 = A(2)
    a2 = A(3)
   ```

1. 类方式

   ```python
    缺点：改变了单例的创建方式
        obj = Singleton.instance()

    # 单例模式：无法支持多线程情况
    import time
    class Singleton(object):

        def __init__(self):
            import time
            time.sleep(1)

        @classmethod
        def instance(cls, *args, **kwargs):
            if not hasattr(Singleton, "_instance"):
                Singleton._instance = Singleton(*args, **kwargs)
            return Singleton._instance

    # # 单例模式：支持多线程情况，加锁。未加锁部分并发执行，加锁部分串行执行，速度降低，但是保证了数据安全
    import time
    import threading
    class Singleton(object):
        _instance_lock = threading.Lock()
        def __init__(self):
            time.sleep(1)

        @classmethod
        def instance(cls, *args, **kwargs):
            if not hasattr(Singleton, "_instance"):
                with Singleton._instance_lock:
                    if not hasattr(Singleton, "_instance"):
                        Singleton._instance = Singleton(*args, **kwargs)
            return Singleton._instance
   ```

1. 基于__new__方式实现。实例化一个对象时，是先执行了类的```__new__```方法，实例化对象；然后再执行类的```__init__```方法，对这个对象进行初始化，可以基于这个，实现单例模式

   ```python
    obj1 = Singleton()
    obj2 = Singleton()

    import time
    import threading
    class Singleton(object):
        _instance_lock = threading.Lock()
 
        def __init__(self):
            pass

        def __new__(cls, *args, **kwargs):
            if not hasattr(Singleton, "_instance"):
                with Singleton._instance_lock:
                    if not hasattr(Singleton, "_instance"):
                        Singleton._instance = object.__new__(cls, *args, **kwargs)
            return Singleton._instance
   ```

1. 基于metaclass方式实现

   基于metaclass方式实现的原理：
   1. 对象是类创建，创建对象时候类的__init__方法自动执行，对象()执行类的 ``__call__`` 方法
   1. 类是type创建，创建类时候type的__init__方法自动执行，类() 执行type的 ``__call__``方法

   ```python
    obj1 = Foo()
    obj2 = Foo()

    import threading
    class SingletonType(type):
        _instance_lock = threading.Lock()
        def __call__(cls, *args, **kwargs):
            if not hasattr(cls, "_instance"):
                with SingletonType._instance_lock:
                    if not hasattr(cls, "_instance"):
                        cls._instance = super(SingletonType,cls).__call__(*args, **kwargs)
            return cls._instance

    class Foo(metaclass=SingletonType):
        def __init__(self):
            pass
   ```
