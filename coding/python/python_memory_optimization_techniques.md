# python的内存优化技巧

----

## 1. 使用生成器
生成器的实现通常有两种方式：含有 `yield` 关键字的函数和生成器表达式（`Generator Expressions`）（将列表推导式的方括号换成圆括号即可）。
```py
import sys

# 生成器函数
def number_generator():
    for number in range(100):
        yield number

# 生成器表达式
gen_exp_numbers = (number for number in range(100))

# 列表推导式
lst_comp_numbers = [number for number in range(100)]

# for 循环
for_loop_numbers = []
for number in range(100):
    for_loop_numbers.append(number)
    
print(f'Generator function: {sys.getsizeof(number_generator)}')  # Generator function: 152
print(f'Generator expression: {sys.getsizeof(gen_exp_numbers)}')  # Generator expression: 200
print(f'List comprehension: {sys.getsizeof(lst_comp_numbers)}')  # List comprehension: 920
print(f'For loop: {sys.getsizeof(for_loop_numbers)}')  # For loop: 920
```

## 2. 尽可能使用内置数据类型
`Python` 的内置数据类型，如整数（`int`）、浮点数（`float`）、字符串（`str`）等，是在 `Python` 的底层（`C`语言层面）实现的。这些类型针对性能和内存使用进行了优化。相比于自定义类或者使用其他复杂数据结构，内置数据类型在处理基本数据操作时通常更高效。

**内置类型优势**：
  * **速度**： 内置类型的操作（如数学运算、字符串操作）在 `C` 语言层面执行，速度快。
  * **内存**： 由于优化过的内存分配策略，内置类型使用的内存通常比自定义数据结构更少。
  * **功能丰富**： `Python`的内置类型自带许多实用的方法和属性，用于数据处理很方便。


## 3. 使用__slots__限制实例属性
每个类的实例默认都有一个`__dict__`属性，这是一个字典，用来存储实例的所有属性。虽然这种动态属性赋值的方式提供了极高的灵活性，但它也使得对象占用更多的内存。通过使用`__slots__`声明，可以在类中明确指定实例属性，这样`Python`会为实例使用一种更为紧凑的内部表示，从而减少内存使用。
在类定义中，通过将`__slots__`设置为一个字符串元组，列出所有允许的属性名称，可以防止动态创建其他属性，并优化内存使用。相当于创建了一个属性“白名单”，不在范围内的属性不允许新增。
```py
# 不使用 __slots__  
class NodeWithoutSlots:
    def __init__(self, node_id, connections):
        self.node_id = node_id
        self.connections = connections

# 使用 __slots__        
class NodeWithSlots:
    # 定义属性“白名单”
    __slots__ = ('node', 'connections')
    def __init__(self, node_id, connections):
        self.node = node_id
        self.connections = connections
        
node = NodeWithoutSlots(111, [333, 444, 555])
node.address = 'Shanghai'
node_with_slots = NodeWithSlots(111, [333, 444, 555])

# 内存使用比较
memory_without_slots = sys.getsizeof(node) + sys.getsizeof(node.__dict__)
memory_with_slots = sys.getsizeof(node_with_slots)
print(f'Memory without slots: {memory_without_slots}')  # Memory without slots: 352
print(f'Memory with slots: {memory_with_slots}')  # Memory with slots: 48
```
因为使用`__slots__`的属性并在底层并不通过字典存储，因此不用维护这个特殊的字典，所需内存自然更少。

在这个例子中，使用`__slots__`明确地声明了实例将具有的属性，从而不再需要为每个实例创建一个`__dict__`。这不仅节省了内存，也提高了属性访问的速度。

**总结**：`__slots__`是一个高级的内存优化技巧，适用于需要创建大量实例且实例属性不会动态变化的情况。这种方法可以显著减少内存使用，并提高属性访问速度。


## 4. 利用内存映射文件支持大文件处理
内存映射文件`I/O`，简称“`mmap`”，是一种操作系统级别的优化。

>它实现了需求分页，因为不会立即从磁盘读取文件内容，因此最初根本不使用任何物理内存。只有在特定位置被访问后，才会以一种惰性方式从磁盘读取。

简单来说，当使用`mmap`技术对文件进行内存映射时，它会在当前进程的虚拟内存空间中直接创建文件的映射，而不是将整个文件加载到内存中。

```py
import mmap

with open('large_log_file.txt', 'r+b') as f:
    # 使用内存映射该文件，size=0表示整个文件
    with mmap.mmap(f.fileno(), 0) as mm:
        # 通过标准方法读取内容
        print(mm.read())
        # 通过切片符号读取内容
        part_content = mm[:10]
        print(part_content.decode('utf-8'))
```
如上所示，`Python`使得内存映射文件`I/O`技术的使用变得很方便。我们需要做的仅仅是应用`mmap.mmap()`方法，然后使用标准文件方法或甚至切片符号来处理打开的对象。


## 5. 非必要不使用全局变量


## 6. 利用逻辑运算符的短路计算原则
```py
result_a = computation_func_a()
result_b = computation_func_b()
result = result_a if result_a else result_b
```
获取相同结果的一种性能更好的方法如下：
```py
result =  computation_func_a() or computation_func_b()
```
由于逻辑运算符遵循短路求值规则，如果`computation_func_b()`为`True`时,以上代码中的`computation_func_a()`不会执行。这样就可以节省不必要的内存使用。


## 7. 选择正确的数据存储格式
**选择更合适的数据类型**：
>对于数值数据，`Python`提供了多种数据类型，如整型`int`，浮点型`float`，以及通过标准库如`numpy`提供的更多具体类型，例如`int32`、`int64`、`float32`、`float64`等。在不需要很大范围或精度的情况下，选择较小的数据类型（如`int32`而非`int64`）可以减少内存使用。

**使用更紧凑的数据结构**：
>例如，使用数组(`array`)代替列表(`list`)来存储大量的数值数据。数组在存储同一数据类型的多项数据时比列表的内存使用效率更高，因为数组为数据分配连续的内存空间，而列表则存储指向各个独立对象的指针。


## 8. 使用interning技术共享字符串
`Interning`是一种用于优化内存使用的技术，主要应用于字符串。在`Python`中，`interning`指的是重用相同的不可变对象，从而避免存储多个相同对象。这样，当多个变量引用相同的字符串时，它们实际上指向内存中的同一个位置，从而减少内存的使用。

字符串`Interning`的原理：

`Python`自动对小的和（或）在编译时遇到的字符串（长度不大于`4096`）进行`interning`。例如，小的字符串和在源代码中硬编码的字符串。但是，对于在运行时通过某些操作（如拼接、用户输入等）生成的字符串（长度超过`4096`），`Python`可能不会自动进行`interning`。因此，`4096`是一个魔法数字，它是`Python`是否将两个字符串自动`interning`的阈值标准。

手动进行`interning`： 可以使用`sys.intern()`函数来强制`intern`任何字符串。这在处理大量重复的字符串数据时特别有用，例如，在处理大量网络数据、日志文件或在`NLP`任务中。

```py
import sys

a = 'abcd' * 1024
b = 'abcd' * 1024
c = 'uvwxyz' * 1000
d = 'uvwxyz' * 1000

print(a == b)  # True, 但是比较的是内容
print(a is b)  # True, 因为二者的长度都为 4096
print(c == d)  # True
print(c is d)  # False, 因为长度大于 4096
# 手动 Interning
print(sys.intern(c) is sys.intern(d))   # True，同一个对象
```
**总结**： 使用字符串interning可以在处理大量重复字符串时节省大量内存。这在处理文本数据集、日志数据或任何包含大量重复字符串的场景中非常有用。
