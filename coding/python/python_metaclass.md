# python元类

----

## 类（object）
`__new__`生成一个对象`self`，`__init__`则对这个对象`self`进行加工，即初始化操作。

`__new__()`必须要有返回值，返回实例化出来的实例，需要注意的是，可以`return`父类`__new__()`出来的实例，也可以直接将`object`的`__new__()`出来的实例返回。

`__init__()`有一个参数`self`，该`self`参数就是`__new__()`返回的实例，`__init__()`在`__new__()`的基础上可以完成一些其它初始化的动作，`__init__()`不需要返回值。

若`__new__()`没有正确返回当前类cls的实例，那`__init__()`将不会被调用，即使是父类的实例也不行。

```py
class MyClass:
    def __new__(cls):
        print("Creating instance")
        instance = super(MyClass, cls).__new__(cls)
        return instance

    def __init__(self, value):
        self.my_attribute = value
        print("Initializing instance")
        
    def my_method(self):
        return self.my_attribute

# 一旦类定义完成，你就可以使用`class`关键字后跟类名和一对圆括号来创建类的实例。这个过程称为实例化。
# 在这个例子中，`MyClass`被实例化为`my_instance`，同时传递了参数`10`给构造方法`__init__`。
my_instance = MyClass(10)

# 当调用my_instance.my_method()时，self指向my_instance
result = my_instance.my_method()

# 输出
# Creating instance
# Initializing instance
```


## 元类（Metaclass）
在面向对象编程的世界里，对象的创建和管理是构建复杂系统的基础。
类本身也是对象，它们是由一种特殊的对象【**元类（`Metaclass`）**】所创建和管理的。

**元类**是面向对象语言中的一个高级概念，它不仅是类的模板，更是类的构造者。
在`Python`等动态语言中，元类扮演着至关重要的角色，它们定义了类的行为和创建过程。

>在`Python`中，元类（`Metaclass`）是创建类的“类”，它们定义了如何创建类和如何构造实例。简而言之，如果你把普通的类比作制造对象的模板，那么元类就是制造这些模板的模板。

所有的类都继承自`object`，而`object`是由`type`创建的。`type`是`Python`中所有新式的元类，它提供了类创建的标准行为。在`Python 3`中，所有的类都是新式的，而`Python 2`中的类默认是旧式的，但可以通过继承`object`来变成新式的。

`type`本身也是一个类，由`type`的元类创建。这种递归的特性使得`Python`中的一切都是对象，包括类本身。

#### 元类工作原理
type也是一个内置的工厂函数，可以用来创建新的类。
```py
# 用type动态创建类
MyClass = type('MyClass', (BaseClass,), {'attribute1': value1, 'method1': function1})
```

#### 自定义元类型
要涉及继承自`type`并重写其`__new__`或者`__init__`方法来控制如何创建或初始化新型态（即新建立一个`class`）
```py
# 定义一个名为 Meta 的元素, 继承自 type
# 这是一个类的类。
class Meta(type):
    def __new__(cls, name, bases, dct):
        print('__new__方法，创建一个类！不是实例！类名：', name)
        # 在类定义中添加一个新方法
        dct['new_method'] = lambda self: 'Hello from metaclass'
        return super(Meta, cls).__new__(cls, name, bases, dct)

    def __init__(cls, name, bases, dct):
        # dct['new_method'] = lambda self: 'Hello from metaclass'
        print('__init__方法，创建一个类！不是实例！类名：', name)
        super().__init__(name, bases, dct)


# 使用 Meta 元素来定制 MyClass 类型
class MyClass(metaclass=Meta):
    pass


# 实例化 MyClass 将会触发 Meta 的 __new__
obj = MyClass()
print(obj.new_method())

# 输出：
# __new__方法，创建一个类！不是实例！类名： MyClass
# __init__方法，创建一个类！不是实例！类名： MyClass
# Hello from metaclass

```
在上面代码中：
  * 1.首先声明了一个名为`Meta`的新型态，并且继承了内置型态`type`
  * 2.然后重写了该型态(`Meta`) 的构造方法(`__new__`)。
  * 3.当声明另外一种名为`MyClass`, 并指定其使用`metaclass=Meta`, `Python`将会在创建该`MyClass`时调用到由`Meta`提供的构造方法。

`dct['new_method'] = lambda...`在`__init__`方法中不起作用，原因如下：
  * 1.**执行时机不同**：元类的`__init__`方法是在类对象已经被创建之后调用的。此时对传入参数`dct`的修改并不会影响到已经完成构造过程的类对象。换句话说，在通过元类型(`Meta`)构造完一个新类型(`MyClass`)之后再去尝试添加或修改属性、方法等内容，并不能改变已经生成好了结构和内容定义的那个类型。
  * 2.**参数差异**：虽然既`__new__`又`__init__`都接收到了名为`dct`的字典参数，但只有在`new()`中对`dct`执行操作才能影响到最终生成的类型结构。当执行到`init()`时候, 类型已经被成功创建, 此刻`dct`参数更多地扮演着提供信息参考角色，并不能通过修改它来改变现有类型结构。

#### 元类的作用
元类可以用来控制类的创建过程。你可以使用元类来修改类的定义，或者在类创建时添加额外的行为。以下是一些元类可能的应用场景：
  * 1.**注册机制**：自动将类注册到某个字典或列表中，以便于查找或管理。
  * 2.**单例模式**：确保一个类只有一个实例。
  * 3.**属性和方法的添加**：在不修改类定义的情况下，为其添加额外的属性或方法。
  * 4.**子类检查**：检查一个类是否是另一个类的子类。
  * 5.**自定义类的行为**：修改类的创建过程，以实现自定义的行为。

#### 旧式类（Old-style classes）
在Python 2中，如果你定义一个类没有明确指定基类，那么它就是旧式类。

#### 新式类（New-style classes）
新式类的主要特点包括：
  * 1.更灵活的属性管理：新式类使用`__slots__`属性来定义类级别的属性，这可以节省内存并防止不必要的属性添加。
  * 2.更好的描述符支持：新式类支持更多的描述符协议，这使得属性和方法的管理更加灵活。
  * 3.统一的基类：新式类提供了一个统一的基类`object`，这使得所有的类都有共同的祖先，简化了继承体系。

