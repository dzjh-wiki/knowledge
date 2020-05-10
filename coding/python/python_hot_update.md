# python的热更新

----
函数`__import__`会在import声明中被调用。  
import导入一个模块分两步：  
  * 搜索模块，并在必要时进行初始化；
  * 在当前命名空间中定义相关命名。

其中第一步有以下的搜寻过程：`sys.modules` -> `sys.meta_path` -> `sys.path_hooks, sys.path_importer_cache, sys.path`  

## reload
math从缓存sys.modules移除后，__import__会重新load math并添加到sys.modules，导致当前环境中math绑定的math module和sys.modules中不一致，导致reload失败。  
```py
import sys, math

sys.modules.pop('math')
__import__('math')
print(id(math), id(sys.modules.get('math'))) # 两者的id值不一样
reload(math)
# 出现报错
# Traceback (most recent call last):
#   File "<pyshell#4>", line 1, in <module>
#     reload(math)
# ImportError: reload(): module math not in sys.modules
```

一般情况下，所能想到的方法是，通过检测文件修改并使用reload更新，但根本的缺陷是旧的对象不能执行新的代码，需要重新生成新的对象，且指定检测更新模块的方式不灵活，且要求先前导入过被reload的模块。这种方法可以应用于特定少量文件的更新。

当reload(M)被执行后：  
  * M模块将被重新解释字节码。并再执行模块级定义的执行语句(译注：由此应认识到在模块级就编写函数调用和类对象生成是多么坏的习惯呀)。并在M模块内定义一个新的`命名->新对象`的命名空间映射。
  * M模块reload前的所有旧对象，直到它们的引用数量降到0，才可能被gc回收。
  * M模块的命名空间中的命名全部指向了新的对象。
  * 其他模块中对M模块reload前的旧对象的引用，仍然维持旧对象的引用; 如果你希望其他模块对M模块的相关对象引用能同时更新为M中的新对象, 那需要你自己动手。


## 实现的核心思路
### 1. 函数和方法的更新
**Python中，一切皆为对象。**  
`method /staticmethod / classmethod / function`这四种对象类型其实都可以归结到`function object`的更新上(因为`method/staticmethod/classmethod`本质上都是对`function`的一个`wrapper`对象，都有途径获得被`wrap`的`function`)。  

`function object`的功能其实本质上是一个函数块，它主要由`func_code`, `func_defaults`, `func_doc`三个成员组成，那我们用`reload`后的`function`对象相应内容替换到旧的`function`对象中即可。  

`class`则稍微特殊一些，它是由`method / staticmethod / classmethod`， 以及`BASES`关系(+MRO)，数据成员等共同组成的一个对象体。但由于Python中对`BASES tuple`在运行时的替换有`deallocator`相等的限制，使得从Python脚本层次对派生关系重新定义不可行(但是增加基类是可以的:`ClassA.__bases__ += (ClassB, )`，所谓的`Mix-in`)。  

**优点：**  
  * 无论这些function/class以什么方式引用，只要不深入直接引用到func_code/func_default对象，均可动态更新到
  * 只需要更新一个对象，速度非常快

**缺点：**  
  * 不能动态更新class的派生关系相关的信息

```py
RELOAD_MOD_LIST = ('hotfix',)

def do_replace_func(new_func, old_func):
    # 暂时不支持closure的处理
    re_attrs = ('func_doc', 'func_code', 'func_dict', 'func_defaults')
    for attr_name in re_attrs:
        setattr(old_func, attr_name, getattr(new_func, attr_name, None))

def update_type(cls_name, old_mod, new_mod, new_cls):
    old_cls = getattr(old_mod, cls_name, None)
    if old_cls:
        for name, new_attr in new_cls.__dict__.iteritems():
            old_attr = old_cls.__dict__.get(name, None)
            if new_attr and not old_attr:
                setattr(old_cls, name, new_attr)
                continue
            if inspect.isfunction(new_attr) and inspect.isfunction(old_attr):
                do_replace_func(new_attr, old_attr)
                # setattr(old_cls, name, new_attr)
        setattr(new_mod, cls_name, old_cls)

def reload_with_func_replace():
    for mod_name in RELOAD_MOD_LIST:
        # 由于使用reload要事先知道并确定模块，而且只能运用于绑定到模块的变量上，程序运行过程中通过sys.modules拿到的模块都是是str类型的，因此runtime使用reload显然不合适。这里通过sys.modules和__import__来重新加载模块。
        old_mod = sys.modules.pop(mod_name)
        __import__(mod_name)
        new_mod = sys.modules.get(mod_name)
        for name, new_attr in inspect.getmembers(new_mod):
            if new_attr is not type and isinstance(new_attr, type):
                update_type(name, old_mod, new_mod, new_attr)


if __name__ == '__main__':
    cmd = 1
    while cmd == 1:
        reload_with_func_replace()
        cmd = input()
```


### 2. 新对象替换旧对象
模块`M`被热更新后，找出所有对`M`中的`class/function...`有引用的对象，逐个执行新对象替换旧对象的操作。比如`obj.__class__ = class_after_reload`。  

**优点：**  
  * 实现相对简洁
  * 支持class对象的全更新

**缺点：**  
  * 对于将`function/classobj.method`跨模块不可变容器(`tuple`, `frozenset`...)引用的更新不了
  * 如果引用对象众多，比(思路1)处理起来慢许多。