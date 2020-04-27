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
  * 类应该对扩展开放，对修改关闭
  * 要依赖抽象，不要依赖具体类（依赖倒置原则）

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
**工厂方法模式**定义了一个创建对象的接口，但由子类决定要实例化的类是哪一个（即，将实例化对象的抽象方法的实现内容，由子类来实现）。工厂方法让类把实例化推迟到子类。  
```csharp
abstract Product factoryMethod(string type) // 实例化对象的抽象方法
```

**抽象工厂模式**提供一个接口，用于创建相关或依赖对象的家族，而不需要明确指定具体类。  
抽象工厂的方法经常以工厂方法的方式实现，而抽象工厂的任务只是定义一个负责创建一组产品的接口。


## 5. 单件模式（Singleton Pattern）
**单件模式**确保一个类只有一个实例，并提供一个全局访问点。
```csharp
public class Singleton {
    private static Singleton instance = new Singleton();

    public static Singleton getInstance() {
        return instance;
    }
}
```
注意：多线程的处理问题（加锁？）。


## 6. 命令模式
**命令模式**将“请求”封装成对象，以便使用不同的请求、队列或者日志来参数化其他对象。命令模式也支持可撤销的操作。  
```csharp
public interface Command {
    void execute();
    void undo();
}

public class NoCommand : Command {
    public execute() {}
    public undo() {}
}

public class RemoteControlWithUndo {
    Command[] onCommands;
    Command[] offCommands;
    Command undoCommand;

    public int commandCount = 3;

    public RemoteControlWithUndo() {
        onCommands = new Command[commandCount];
        offCommands = new Command[commandCount];

        Command noCommand = new NoCommand();
        for (int i = 0; i< commandCount; i++) {
            onCommands[i] = noCommand;
            offCommands[i] = noCommand;
        }
        undoCommand = noCommand;
    }

    public void setCommand(int index, Command onCommand, Command offCommand) {
        onCommands[index] = onCommand;
        offCommands[index] = offCommand;
    }

    public void onBtnClick(int index) {
        onCommands[index].execute();
        undoCommand = onCommands[index];
    }

    public void offBtnClick(int index) {
        offCommands[index].execute();
        undoCommand = offCommands[index];
    }

    public void undoBtnClick() {
        undoCommand.undo();
    }
}
```

宏命令：初始化时传入多组命令，当执行宏命令时，就会一次性执行所传入的所有命令。  
```csharp
public class MacroCommand : Command {
    Command[] commands;

    public MacroCommand(Command[] commands) {
        this.commands = commands;
    }

    public void execute() {
        for (int i = 0; i < commands.length; i++) {
            commands[i].execute();
        }
    }
}
```

应用场景：  
  * 工作队列：在某一端添加命令，然后在另一端（可以是另一个线程）中弹出一个命令，进行调用（execute）。
  * 日志记录：将所有动作命令都记录在日志中，并能在系统死机之后，重新加载这些动作，通过依次调用动作的execute()方法，恢复到出错的状态，从而快速查找到问题。为了减少日志的记录量，可以定义一些检查点，只将上次检查点之后的所有操作记录下来。