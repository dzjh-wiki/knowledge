# python的魔法方法

----
| 魔法方法 | 调用时机 | 说明 |
| -- | -- | -- |
| `__new__(cls, *args)` | `instance = Class(arg1, arg2)` | `__new__`产生的实例也就是`__init__`里面的的`self` |
| `__init__(self, *args)` | `instance = Class(arg1, arg2)` | `__init__`在实例创建时调用 |
| `__call__(self, *args)` | `Class(arg1, arg2)` | `__call__`允许类的实例跟函数一样表现 |
| `__eq__(self, other)` | `self == other` | 比较操作符 |
| `__ne__(self, other)` | `self != other` | 比较操作符 |
| `__lt__(self, other)` | `self < other` | 比较操作符 |
| `__gt__(self, other)` | `self > other` | 比较操作符 |
| `__le__(self, other)` | `self <= other` | 比较操作符 |
| `__ge__(self, other)` | `self >= other` | 比较操作符 |
| `__pos__(self)` | `+self` | 取正（一元操作符） |
| `__neg__(self)` | `-self` | 取负（一元操作符） |
| `__abs__(self)` | `abs(self)` | 绝对值（一元操作符） |
| `__invert__(self)` | `!self` | 取反（一元操作符） |
| `__add__(self, other)` | `self+other` | 加法（算数操作符） |
| `__sub__(self, other)` | `self-other` | 减法（算数操作符） |
| `__mul__(self, other)` | `self*other` | 乘法（算数操作符） |
| `__floordiv__(self, other)` | `self//other` | 整除（算数操作符） |
| `__truediv__(self, other)` | `self/other` | 除法（算数操作符） |
| `__mod__(self, other)` | `self%other` | 取余（算数操作符） |
| `__pow__(self, other)` | `self**other` | 幂运算（算数操作符） |
| `__and__(self, other)` | `self&other` | 与运算（算数操作符） |
| `__or__(self, other)` | `self|other` | 或运算（算数操作符） |
| `__iadd__(self, other)` | `self+=other` | 赋值操作符 |
| `__isub__(self, other)` | `self-=other` | 赋值操作符 |
| `__imul__(self, other)` | `self*=other` | 赋值操作符 |
| `__ifloordiv__(self, other)` | `self//=other` | 赋值操作符 |
| `__itruediv__(self, other)` | `self/=other` | 赋值操作符 |
| `__imod__(self, other)` | `self%=other` | 赋值操作符 |
| `__str__(self)` | `str(self)` |  |
| `__len__(self)` | `len(self)` |  |


## `__init__`和`__new__`
**`__init__`和`__new__`最主要的区别在于：**  
  * `__init__`通常用于初始化一个新实例，控制这个初始化的过程，比如添加一些属性，做一些额外的操作，发生在类实例被创建完以后。它是实例级别的方法。
  * `__new__`通常用于控制生成一个新实例的过程。它是类级别的方法。

### `__new__`的作用
`__new__`方法主要是当你继承一些不可变的`class`时(比如`int`,`str`,`tuple`)， 提供给你一个自定义这些类的实例化过程的途径。
```py
class PositiveInteger(int):
    def __new__(cls, val):
        return super(PositiveInteger, cls).__new__(cls, abs(val));
```

### 用`__new__`来实现单例
```py
class Singleton(object):
    def __new__(cls):
        if not hasattr(cls, "instance"):
            cls.instance = super(Singleton, cls).__new__(cls);
        return cls.instance;
```


## 属性访问控制
**`__getattr__(self, name)`**  
  * 该方法定义了你试图访问一个**不存在的属性**时的行为。因此，重载该方法可以实现捕获错误拼写然后进行重定向, 或者对一些废弃的属性进行警告。

**`__setattr__(self, name, value)`**  
  * `__setattr__ `是实现封装的解决方案，它定义了你对属性进行赋值和修改操作时的行为。
  * 不管对象的某个属性是否存在,它都允许你为该属性进行赋值,因此你可以为属性的值进行自定义操作。有一点需要注意，实现`__setattr__`时要避免"无限递归"的错误。

**`__delattr__(self, name)`**  
  * `__delattr__`与`__setattr__`很像，只是它定义的是你删除属性时的行为。实现`__delattr__`是同时要避免"无限递归"的错误。

**`__getattribute__(self, name)`**  
  * `__getattribute__`定义了你的属性被访问时的行为，相比较，`__getattr__`只有该属性不存在时才会起作用。
  * 因此，在支持`__getattribute__`的Python版本,调用`__getattr__`前必定会调用 `__getattribute__`。`__getattribute__`同样要避免"无限递归"的错误。

```py
class Foo(object):
    def __init__(self, value):
        self.value = value

    def __getattr__(self, item):
        if item == 'scolia':
            return 'no attr:%s' % item
        elif item in self.__dict__:
            return self.__dict__[item]
        else:
            raise AttributeError('no attr:%s' % item)

    def __setattr__(self, key, value):
        if key == 'good':
            print 'can not set the attr: good'
        else:
            self.__dict__[key] = value

    def __delattr__(self, item):
        if item == 'a':
            print 'no attr: good'
        else:
            del self.__dict__[item]

    def __getattribute__(self, item):
        if item == 'a':
            raise AttributeError('not a')
        return object.__getattribute__(self, item)
```

## 描述器

一个类要成为描述器，必须实现`__get__`, `__set__`, `__delete__`中的至少一个方法。下面简单介绍下:

**`__get__(self, instance, owner)`**  
  * 参数instance是拥有者类（指调用该描述器的类）的实例。参数owner是拥有者类本身。`__get__`在其拥有者对其读值的时候调用。

**`__set__(self, instance, value)`**  
  * `__set__`在其拥有者对其进行修改值的时候调用。

**`__delete__(self, instance)`**  
  * `__delete__`在其拥有者对其进行删除的时候调用。


## 构造自定义容器
在Python中，常见的容器类型有: dict, tuple, list, string。  
其中tuple, string是不可变容器，dict, list是可变容器。  
  * 如果要自定义不可变容器类型，只需要定义`__len__`和`__getitem__`方法;
  * 如果要自定义可变容器类型，还需要在不可变容器类型的基础上增加定义`__setitem__`和`__delitem__`;
  * 如果你希望你的自定义数据结构还支持"可迭代", 那就还需要定义`__iter__`。

**`__len__(self)`**  
  * 需要返回数值类型，以表示容器的长度。该方法在可变容器和不可变容器中必须实现。

**`__getitem__(self, key)`**  
  * 当你执行self[key]的时候，调用的就是该方法。该方法在可变容器和不可变容器中也都必须实现。
  * 调用的时候,如果key的类型错误，该方法应该抛出TypeError；
  * 如果没法返回key对应的数值时,该方法应该抛出ValueError。

**`__setitem__(self, key, value)`**  
  * 当你执行self[key] = value时，调用的是该方法。

**`__delitem__(self, key)`**
  * 当你执行del self[key]的时候，调用的是该方法。

**`__iter__(self)`**  
  * 该方法需要返回一个迭代器(iterator)。当你执行for x in container: 或者使用iter(container)时，该方法被调用。

**`__reversed__(self)`**  
  * 如果想要该数据结构被內建函数reversed()支持,就还需要实现该方法。

**`__contains__(self, item)`**  
  * 如果定义了该方法，那么在执行item in container 或者 item not in container时该方法就会被调用。
  * 如果没有定义，那么Python会迭代容器中的元素来一个一个比较，从而决定返回True或者False。


## 上下文管理
参考[Python 上下文管理器](../coding_language_feature/python.md#上下文管理器)
```py
with open("test.txt") as f:
    print(f.read());
```
需要实现两个魔术方法: `__enter__`和`__exit__`。

**`__enter__(self)`**  
  * `__enter__`会返回一个值，并赋值给as关键词之后的变量。在这里，你可以定义代码段开始的一些操作。

**`__exit__(self, exception_type, exception_value, traceback)`**  
  * `__exit__`定义了代码段结束后的一些操作，可以这里执行一些清除操作，或者做一些代码段结束后需要立即执行的命令，比如文件的关闭，socket断开等。如果代码段成功结束，那么exception_type, exception_value, traceback 三个参数传进来时都将为None。如果代码段抛出异常，那么传进来的三个参数将分别为: 异常的类型，异常的值，异常的追踪栈。
  * 如果`__exit__`返回True, 那么with声明下的代码段的一切异常将会被屏蔽。
  * 如果`__exit__`返回None, 那么如果有异常，异常将正常抛出，这时候with的作用将不会显现出来。


## python中字典的key要求
  * 有`__hash__`方法可以做字典的key，没有则不能作为字典的key;
  * 除了list、dict、set和内部至少带有上述三种类型之一的tuple之外，其余对象均可作为字典的key。

```py
class Mylist(list):
def __hash__(self):
    print("hash(self(0)) = ",hash(self[0]))
    return hash(self[0])

l = Mylist([1,2])
d = {}
d[l] = l # hash(self(0)) =  1
print(d) # {[1, 2]: [1, 2]}
d[1] = 1
print(d) # {[1, 2]: [1, 2], 1: 1}
```

在将对象作为字典的key时，`__hash__`会先执行，找到对应的内存，没有值则直接写入；有值，判断新来的键和原来的键是否相等，相等则认为是一个键，更新值，不相等则开辟空间，自增键值对。  
```py
class Mylist(list):
    def __hash__(self):
        print("hash(self(0)) = ",hash(self[0]))
        print("hash running.")
        return hash(self[0])

    def __eq__(self,other):
        print("eq running.")
        return self[0] == other

def test1():
    l = Mylist([1,2])
    d = {}
    d[l] = l
    print(d)
    d[1] = 1
    print(d)
    # 输出：
    # hash(self(0)) =  1
    # hash running.
    # {[1, 2]: [1, 2]}
    # eq running.
    # {[1, 2]: 1}

def test2():
    l = Mylist([1,2])
    d = {}
    d[1] = 1
    print(d)
    d[l] = l
    print(d)
    # 输出：
    # {1: 1}
    # hash(self(0)) =  1
    # hash running.
    # eq running.
    # {1: [1, 2]}
```