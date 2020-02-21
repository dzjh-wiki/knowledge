# 设计模式

----

## 前言
### OO基础
  * 抽象
  * 封装
  * 多态
  * 继承

### OO原则
  * 封装变化
  * 多用组合，少用继承
  * 针对接口编程，不针对实现编程
  * 为交互对象之间的松耦合设计而努力
  * 对扩展开放，对修改关闭

## 1. 策略模式
定义算法族，分别封装起来，让它们之间可以相互替换，此模式让算法的变化独立于使用算法的客户。

```csharp
public interface IBehavior {
    void toDo(); // 行为
}
public class Test {
    private IBehavior behavior;

    // 设置行为
    public void setBehavior(IBehavior bhv) {
        behavior = bhv;
    }

    // 执行行为
    public void toDoSomething() {
        behavior.toDo();
    }
}
```

## 2. 观察者模式
在对象之间定义一对多的依赖。当一个对象改变状态，依赖它的对象都会收到通知，并自动更新。

```csharp
// 被观察者
public interface Subject {
    public void addObserver(Observer obj);
    public void removeObserver(Observer obj);
    public void notifyObservers();
}

// 观察者
public interface Observer {
    public void update(Subject obj, Object arg);
}

public class TestData : Subject {
    private List<Observer> observers;

    // 构造函数
    public void TestData() {
        observers = new List<Observer>();
    }

    // 添加观察者
    public void addObserver(Observer obj) {
        observers.Add(obj);
    }

    // 移除观察者
    public void removeObserver(Observer obj) {
        if (observers.Exists(obj)) {
            observers.Remove(obj);
        }
    }

    // 通知观察者
    public void notifyObservers() {
        foreach (Observer obj in observers) {
            obj.update(this, null);
        }
    }
}
```


## 3. 装饰者模式
动态地将责任附加到对象上。想要扩展功能，装饰者提供有别于继承的另一种选择。

```csharp
// 基础类
public abstract class BaseObj {
    protected string desc = "base";

    public virtual string getDeac() {
        return desc;
    }
    
    public abstract int getID();
}

// 测试类
public class TestObj : BaseObj {
    public TestObj() {
        desc = "test"; 
    }

    public override int getID() {
        return 233;
    }
}

// 修饰实现类
public class DecorateTestObj : BaseObj {
    private BaseObj baseObj;

    public DecorateTestObj(BaseObj baseObj) {
        this.baseObj = baseObj; 
    }

    public override string getDeac() {
        return "Decorate[" + baseObj.getDeac() + "]";
    }

    public override int getID() {
        return baseObj.getID() * 10;
    }
}

class Program
{
    static void Main(string[] args)
    {
        TestObj testObj = new TestObj();
        Console.WriteLine("Desc: {0}, ID: {1}.", testObj.getDeac(), testObj.getID());

        // 进行装饰
        DecorateTestObj newTestObj = new DecorateTestObj(testObj);
        Console.WriteLine("Desc: {0}, ID: {1}.", newTestObj.getDeac(), newTestObj.getID());
        Console.ReadLine();
    }
}
```


## 4. 工厂模式