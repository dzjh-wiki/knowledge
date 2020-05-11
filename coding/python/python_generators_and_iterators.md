# python的生成器和迭代器

----
## 迭代器
Python中一个实现了`__iter__`方法和`__next__`方法的类对象，就是迭代器。  
`__iter__(self)`只会被调用一次,而`__next__(self)`会被调用`n`次，直到出现`StopIteration`异常。  
```py
class Fib(object):
    def __init__(self, max):
        super(Fib, self).__init__()
        self.max = max

    def __iter__(self):
        self.a = 0
        self.b = 1
        return self

    def __next__(self):
        fib = self.a
        if fib > self.max:
            raise StopIteration
        self.a, self.b = self.b, self.a + self.b
        return fib

# 定义一个main函数，循环遍历每一个菲波那切数
def main():
    # 20以内的数
    fib = Fib(20)
    for i in fib:
        print(i)

# 测试
if __name__ == '__main__':
    main()
```


## 生成器
**生成器是只能遍历一次的一类特殊迭代器。**  

### 生成器表达式
  * 使用生成器表达式取代列表推导式可以同时节省 cpu 和 内存(RAM)。

```py
# 一个列表
xiaoke=[2,3,4,5]
# 生成器generator，类似于list，但是是把[]改为()
gen=(a for a  in xiaoke)
for  i  in gen:
    print(i)
```

### 生成器函数
```py
# 菲波那切数列
def Fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return '没有数据了...'
# 调用方法，生成出10个数来
f=Fib(10)
# 使用一个循环捕获最后return 返回的值，保存在异常StopIteration的value中
while  True:
    try:
        x=next(f)
        print("f:",x)
    except StopIteration as e:
        print("生成器最后的返回值是：",e.value)
        break
```

**关于send函数**  
```py

def foo():
    print("starting...")
    while True:
        res = yield 4
        print("res:",res)
g = foo()
print(next(g))
print("*"*20)
print(g.send(7))

# 输出
# starting...
# 4
# ********************
# res: 7
# 4
```
`send()`是发送一个参数给`res`的，因为在`yield`的时候，并没有把`4`赋值给`res`，下次执行的时候继续执行赋值操作，只好把`res`赋值为`None`了，而如果用`send()`的话，开始执行的时候，先接着上一次（`yield 4`之后）执行，先把`7`赋值给了`res`，然后执行`next`的作用，遇见下一回的`yield`，return出结果后结束。