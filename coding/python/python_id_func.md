# python的id函数

----
一个对象的`id`值在`CPython`解释器里就代表它在内存中的地址。  

`id(object)`返回的是对象的“地址标识”，唯一且不变，但在不重合的生命周期里，可能会出现相同的id值。此处所说的对象应该特指复合类型的对象(如类、`list`等)，对于字符串、整数等类型，变量的`id`是随值的改变而改变的。  

**判断对象相同与否用is，不要用id去比较，is由于不能被重载，因此不用查哈希表，会更快一些。**  

## id值出现相同的原因
  * **函数的函数属性（两个生存周期不相交的不同对象）**：以下例子中，在调用`id(a.f)`时，发现`a.f`不存在，所以临时创建了`bound method`，然后销毁；紧接着在调用`id(b.f)`时，又发现`b.f`不存在，再在同一个地址上，临时创建了另一个`bound method`。
  * **函数的函数属性（不同对象的生存周期有重叠）**：在将`a.f`和`b.f`赋值给变量时，因为有了引用就不会被销毁，从而强行令他们的生存周期有重叠，然后发现他们作为不同对象，其地址（id函数的值）确实是不一样的。
  * **小数值的`int`类型**：为了提高内存利用效率，对于一些简单的对象，如一些数值答较小的`int`对象，`python`采取重用对象内存的办法，如指向`a=2，b=2`时，由于2作为简单的`int`类型且数值小，`python`不会两次为其分配内存，而是只分配一次，将`a`与`b`同时指向已分配的对象。
  * **短长度的字符串**：对于较短长度的字符串，`python`也会采取重用对象内存的办法。
  * **函数传递可变对象**：实际上传递给函数的是列表的首地址，在函数内对列表的值不会影响变量指向首地址，所以对函数内的列表元素进行改变，会影响函数外的列表元素。
  * **函数传递不可变对象**：把变量传递给函数实际上是让函数里的变量也指向存储数字的内存地址，而在函数内对变量进行操作，实际上是让它的指向变成其他数字，不会影响函数外的变量x。

## 示例
```py
import math;

class A(object):
    def __init__(self, val):
        self.__val = val;
    
    @property
    def val(self):
        return self.__val;
    
    @val.setter
    def val(self, val):
        self.__val = val;
    
    def f(self):
        pass;
    
    @staticmethod
    def g(self):
        pass;

def changeA(a):
    a.val = 233;
    print(a.val, id(a), id(a.val));
    pass;

def changeX(x):
    x = 233;
    print(id(x));


if __name__ == "__main__":
    v = A(0);
    a, b = A(v), A(v);
    print("--------------------");
    print(id(a) == id(b), a is b); # False False
    print(id(a.f) == id(b.f), a.f is b.f); # True False
    print(id(a.g) == id(b.g), a.g is b.g); # True True
    print("--------------------");
    c, d = a.f, b.f;
    print(id(c) == id(d), c is d); # False False
    print(id(a.f) == id(b.f), a.f is b.f); # True False
    print("--------------------");
    e, f = a.g, b.g;
    print(id(e) == id(f), e is f); # True True
    print("--------------------");
    print(id(256) == id(256), id(1 * math.pow(10, 6)) == id(1 * math.pow(10, 6))); # True False
    print("--------------------");
    i, j = "abcd", "dbfag";
    print(id(i) == id(j), id(i[0]) == id(j[3])); # False True
    print(id("abcdef") == id("abcdef"), id("abcdef" * int(math.pow(10, 6))) == id("abcdef" * int(math.pow(10, 6)))); # True False
    print("--------------------");
    x = 1;
    print(id(x)); # 140737105978000
    changeX(x); # 140737105985424
    print(id(x)); # 140737105978000
    print("--------------------");
    print(a.val, id(a), id(a.val)); # <__main__.A object at 0x000002B83EBBC080> 3214606844088 3214606844032
    changeA(a); # 233 3214606844088 140737105985424
    
```