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