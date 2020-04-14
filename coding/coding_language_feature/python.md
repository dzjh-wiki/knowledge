# Python语言特性

----
## 装饰器
```py
def decorate(func):
    def wrapper(*args, **kwargs):
        print("decorated!");
        return func(*args, **kwargs);
    return wrapper;

@decorate
def printStr():
    print("test");

# 输出结果：
# decorated!
# test
```

## Pythonic

### 上下文管理器
with语句
```py
class Resource():
    def __enter__(self):
        print("Enter to resource.");
        return self;
        
    def __exit__(self, exc_type, exc_val, exc_tb):
        print("Exit from resource.");
        return self;
    
    def do(self):
        print("Do something.");
        pass;

with Resource() as res:
    res.do();

# 输出结果
# Enter to resource.
# Do something.
# Exit from resource.
```

`__exit__`函数的参数解析：  
  * exc_type：异常类型
  * exc_val：异常值
  * exc_tb：异常的错误栈信息

### 列表推导式
```py
variable = [out_exp_res for out_exp in input_list if do_condition(out_exp)]
```

### 描述器
`__get__`、`__set__`和`__delete__`。
```py
class AbsNum(object):
    def __init__(self, val):
        self.__val = abs(val);
    
    def __get__(self, obj, dtype):
        print("__get__ => obj: {}, dtype: {}".format(obj, dtype));
        return self.__val;
        
    def __set__(self, obj, val):
        print("__set__ => obj: {}, val: {}".format(obj, val));
        self.__val = abs(val);
        
    def __delete__(self, obj):
        print("__delete__ => obj: {}".format(obj));


class Test(object):
    num = AbsNum(233);

    def __init__(self):
        self.num = -233;
        print("result: {}".format(self.num));

if __name__ == '__main__':
	Test()

# 输出结果：
# __set__ => obj: <__main__.Test object at 0x0347DE10>, val: -233
# __get__ => obj: <__main__.Test object at 0x0347DE10>, dtype: <class '__main__.Test'>
# result: 233
```

## 多线程和锁
```py
import threading;

def printStr(lock, args):
    if lock.acquire():
        try:
            print("result: {}".format(args));
        finally:
            lock.release();

l1 = threading.Lock();
tt = threading.Thread(target=printStr, args=(l1, "test val."));
```