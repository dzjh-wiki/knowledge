# 关于Pythonic的方式

## 1. 使用enumerate()而不是range()进行迭代
```py
nums = [1,2,3,4,5]
for i, num in enumerate(nums):
	if num % 2 == 0:
		nums[i] = "even";

# 使用可选的start参数来设置偏移值
for i, num in enumerate(nums, start=10):
	print(i, num);
	# 11, 1
	# 12, 2
	# ...

```

## 2. 使用递推式构造列表而不是map()和filter()
  * 调用`map()`方法：

```py
nums = [1,2,3,4,5]
def square(x):
	return x*x;

ret = list(map(square, nums))
# 等同于
ret = [square(x) for x in nums]
```

  * 调用`filter()`方法：

```py
nums = [1,2,3,4,5]
def is_odd(x):
	return bool(x%2);

ret = list(filter(is_odd, nums))
# 等同于
ret = [x for x in nums if is_odd(x)]
```

## 3. 使用断点breakpoint()调试而不是print()
  * 若使用的是`Python3.7`，则无需导入任何内容，只需在代码中要放入调试器的位置调用`breakpoint()`;
  * `Python3.6-`版本，需显示导入`pdb`：

```py
import pdb; pdb.set_trace();
```

## 4. 使用f-String格式化字符串
  * 参考[Python3特性](python3_feature.md)中的第1项。

## 5. 使用sorted()对复杂列表进行排序
  * 默认情况下，`sorted()`已按升序对输入进行排序，而`reverse`关键字参数则按降序排序。
  * 可选关键字`key`允许在排序之前指定将在每个元素上调用的函数。添加函数允许自定义排序规则。

```py
data = [
	{"type" : "int", "value" : 5},
	{"type" : "str", "value" : "test"},
	{"type" : "list", "value" : []}
]
sorted(data, key=lambda data: data["type"]);
```