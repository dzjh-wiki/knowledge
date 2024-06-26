# python单例模式

----

## Python实现单例模式的6种方法
#### 1.使用模块实现单例
```py

class Singleton(object):
    def foo(self):
        pass
singleton = Singleton()
```
```py
from mysingleton import singleton
print(id(singleton))
```

#### 2.通过装饰器实现单例
```py

def singleeton_func(cls):
    instance={}
    def _singleton(*args, **kwargs):
        if cls not in instance:
            instance[cls] = cls(*args, **kwargs)
        return instance[cls]
    return _singleton
 
@singleeton_func
class Instalce(object):
    def instalce_id(self):
        return id(self)
 
if __name__ == '__main__':
    i = Instalce()
    print(i.instalce_id())
```

#### 3.使用实例化方式实现单例
```py
class SingletonInstance(object):
    def __call__(self, *args, **kwargs):
        return self

if __name__ == '__main__':
    SingletonInstance = SingletonInstance()
    s1 = SingletonInstance()
    print(id(s1))
```

#### 4.使用类装饰器实现单例
```py
class SingletonDecorator(object):
    _instance = None
    def __init__(self, cls):
        self._cls = cls
 
    def __call__(self, *args, **kwargs):
        if self._instance is None:
            self._instance = self._cls(*args, **kwargs)
        return self._instance
 
@SingletonDecorator
class Instance(object):
    def instance_id(self):
        return id(self)
 
if __name__ == '__main__':
    i1 = Instance()
    print(i1.instance_id())
```

#### 5.重写类的__new__方法实现单例
```py
class SingletonClass(object):
    _instance = None
    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            # cls._instance = super(SingletonClass, cls).__new__(cls)  # python2.x中，super()函数的使用语法格式
            cls._instance = super().__new__(cls)  # python3.x中，super()函数的使用语法格式
        return cls._instance
 
    _is_init = False
    def __init__(self):
        if self._is_init is False:
            print('-*-')
            self._is_init = True
 
if __name__ == '__main__':
    s1 = SingletonClass()
    print(id(s1))
```

#### 6.通过元类（metaclass）实现单例
```py

class SingletonMeta(type, object):
    def __init__(self, *args, **kwargs):
        self._instance = None
        super().__init__(*args, **kwargs)
 
    def __call__(self, *args, **kwargs):
        if self._instance is None:
            self._instance = super().__call__(*args, **kwargs)
        return self._instance
 
class Instance(object, metaclass=SingletonMeta):
    def instance_id(self):
        return id(self)
 
if __name__ == '__main__':
    i1 = Instance()
    print(i1.instance_id())
```
