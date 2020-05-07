# CSharp语言特性

----
## 委托
### 定义
```csharp
delegate <return type> <delegate-name> <parameter list>;
```

#### 初始化
```csharp
public delegate void DelTest(string s);

public void printStr(string s) {
    Console.WriteLine("Result: {0}.", s);
}

public void printStr1(string s) {
    Console.WriteLine("Result1: {0}.", s);
}

// 初始化 第一种方式
DelTest dt = new DelTest(printStr);

DelTest dt1;
dt1 = printStr; // 或dt1 += printStr;

// 多播
dt1 += printStr1;

```

### 使用
```csharp
// 在上面的初始化后，可通过跟调用普通方法一样，调用委托函数
dt1("test");
// 输出：
// Result: test.
// Result1: test.
```

### =和+=
在使用`+=`符号时会进行判断：  
  * 如果此时委托还没有实例化（委托实例为`null`），它会自动用`+=`右边的函数实例化委托；
  * 如果此时委托已经实例化，它会只把+=右边的函数注册到委托实例上。

**需要注意的是：如果对注册了函数的委托实例重新使用=号赋值，相当于是重新实例化了委托，之前在上面注册的函数和委托实例之间也不再产生任何关系。**

## 参数修饰符
  * `out`：传参前不需要初始化，在方法内必须设置值（包括初始化）；只能修饰变值（常数、方法和委托不可使用）。
  * `ref`：传参前需要初始化，方法内可以不修改该参数值；只能修饰变值（常数、方法和委托不可使用）。
  * `in`：作为参数传入，不允许方法内进行修改。

当输入参数不带`in`修饰词时，表示允许方法可尝试隐性类型的转换（如int转unit）。  

**需要注意的是：匿名方法（包括Lambda）中不能使用参数修饰符**

## 异步编程
  * `Async`、`Await`和`Task`。

```csharp
private void MainFunc() {
    Console.WriteLine("Before Async Func! Thread ID: "+Thread.CurrentThread.ManagedThreadId);
    var resultTask = AsyncFunc();
    // Console.WriteLine(resultTask.Result); // 此语句会阻塞当前线程
    Console.WriteLine(await resultTask); // 此语句不会造成阻塞
    Console.WriteLine("After Async Func! Thread ID: "+Thread.CurrentThread.ManagedThreadId);
}

private async Task<string> AsyncFunc() {
    var task = TestMethod();
    string result = await TestMethod + ". Thread ID: "+Thread.CurrentThread.ManagedThreadId;
    // 如果在MainFunc中，调用了`Console.WriteLine(resultTask.Result);`，会导致当前线程阻塞，而不会执行以下逻辑！
    Console.WriteLine("Result: {0}.", result);
    return result;
}

private Task<string> TestMethod() {
    var task = Task.Run(() => {
        Console.WriteLine("Before sleeping! Thread ID: "+Thread.CurrentThread.ManagedThreadId);
        Thread.Sleep(5000); // 耗时操作
        Console.WriteLine("After sleeping! Thread ID: "+Thread.CurrentThread.ManagedThreadId);
        return "TestMethod Result";
    });
    return task;
}
```


## Unity的coroutine
```csharp
public class TestCoroutine : MonoBehaviour {
    void Start() {
        StartCoroutine(Calculate(1000));
    }

    IEnumerator Calculate(int times) {
        int num = 0; // 用于控制每帧的计算次数
        for (int i = 0; i < times; i++) {
            Debug.Log(Mathf.Pow(i, 10)); // 计算i的10次方
            if (++num >= 10) {
                num = 0;
                yield return null; // 每帧只计算10次
            }
        }
    }
}
```

**yield return的常见返回值及其作用：**  
  * `yield return new WaitForSeconds(3.0f);`：等待3秒，然后继续从此处开始，常用于做定时器。
  * `yield return null;`：这一帧到此暂停，下一帧再从暂停处继续，常用于循环中。
  * `yield return 1;`：这一帧到此暂停，下一帧再从暂停处继续。这里return什么都是等一帧，后面的返回值没有特殊意义。
  * `yield return new WaitForEndOfFrame();`：等到这一帧的cameras和GUI渲染结束后再从此处继续，即等到这帧的末尾再往下运行。
  * `yield return new WaitForFixedUpdate();`：在下一次执行FixedUpdate的时候继续执行这段代码，即等一次物理引擎的更新。
  * `yield return www;`：等待直至异步下载完成。
  * `yield break;`：直接跳出协程，对某些判定失败必须跳出的时候，比如加载AssetBundle的时候，WWW失败了，后边加载bundle没有必要了，此时可以yield break跳出。
  * `yield return StartCoroutine(methodName);`：等待另一个协程执行完。这是把协程串联起来的关键，常用于让多个协程按顺序逐个运行。