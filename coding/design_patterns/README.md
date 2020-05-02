# 设计模式

  * [1.策略模式](#1-策略模式)
  * [2.观察者模式](#2-观察者模式)
  * [3.装饰者模式](#3-装饰者模式)
  * [4.工厂模式](#4-工厂模式)
  * [5.单件模式](#5-单件模式)
  * [6.命令模式](#6-命令模式)
  * [7.适配器模式](#7-适配器模式)
  * [8.模板方法模式](#8-模板方法模式)
  * [9.迭代器与组合模式](#9-迭代器与组合模式)

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
  * 只和朋友交谈（最少知识原则，减少对象之间的交互）
  * 父类：别调用我们，我们会调用你（好莱坞原则）
  * 一个类应该只有一个引起变化的原因（设计原则）

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


## 5. 单件模式
**单件模式**（Singleton Pattern）确保一个类只有一个实例，并提供一个全局访问点。
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


## 7. 适配器模式
**适配器模式**将一个类的接口，转换成客户期望的另一个接口。适配器让原本接口不兼容的类可以合作无间。
```csharp
// 两个不同的类
public interface Target {
    void play();
}
public interface Source {
    void hit();
}

// 适配器
public class Adapter : Target {
    Source src;

    public Adapter(Source src) {
        this.src = src;
    }

    public void play() {
        src.hit();
    }
}
```

**外观模式**提供了一个统一的接口，用来访问子系统中的一群接口。外观定义了一个高层接口，让子系统更容易使用。
```csharp
// 两个子系统
public interface SubSys1 {
    void play();
}
public interface SubSys2 {
    void hit();
}

public class Appearance {
    SubSys1 sys1;
    SubSys2 sys2;

    public Appearance(SubSys1 sys1, SubSys2 sys2) {
        this.sys1 = sys1;
        this.sys2 = sys2;
    }

    // 统一play接口
    public void play() {
        sys1.play();
        sys2.hit();
    }
}
```
**最少知识原则**要求在对象的方法中，只应调用以下范围的方法：
  * 该对象本身；
  * 被当作方法的参数而传递进来的对象；
  * 此方法所创建或实例化的任何对象；
  * 对象的任何组件（指被实例变量所引用的任何对象）。

```csharp
// 不推荐以下方式
public float getTestVal() {
    Value valObj = valPool.GetValue("Test");
    return valObj.Get();
}

// 推荐这种方式
public float getTestVal() {
    return valPool.GetTestValue(); // 直接在valPool中实现GetTestValue方法来获取Test的值。以减少所依赖类的数量。
}
```

**模式意图**  
  * 装饰者 -> 不改变接口，但加入责任
  * 适配器 -> 将一个接口转成另一个接口
  * 外观 -> 让接口更简单


## 8. 模板方法模式
**模板方法模式**在一个方法中定义一个算法的骨架，而将一些步骤延迟到子类中。模板方法使得子类可以在不改变算法结构的情况下，重新定义算法中的某些步骤。
```csharp
abstract class AbstractClass {
    // 模板方法
    public sealed override void templateMethod() {
        primitiveOp1();
        primitiveOp2();
        concreteOp();
        hook();
    }

    public abstract void primitiveOp1(); // 子类实现

    public abstract void primitiveOp2(); // 子类实现

    // 抽象类的具体操作
    public sealed override void concreteOp() {
        // 抽象类的实现
    }

    // 钩子方法
    public virtual void hook() {}
}
```
**好莱坞原则**：将决策权放在高层模块中，以便决定如何及合适调用底层模块。  

**策略模式**和**模板方法模式**都封装算法，一个用组合，一个用继承。  
**工厂方法**使模板方法的一种特殊版本。  

配对模式与叙述：
  * 策略 -> 封装可互换的行为，然后使用委托来决定要采用哪一个行为
  * 工厂方法 -> 由子类决定实例化哪个具体类
  * 模板方法 -> 子类决定如何实现算法中的某些步骤


## 9. 迭代器与组合模式
**迭代器模式**提供一种方法顺序访问一个聚合对象中的各个元素，而又不暴露其内部的表示。
```csharp
public interface Iterator {
    bool hasNext();
    Object next();
}
```

当一个模块或一个类被设计成只支持一组相关的功能时，就说其具有高内聚；反之，当被设计成支持一组不相关的功能时，就说其具有低内聚。  

**组合模式**允许将对象组合成树形结构来表现“整体/部分”层次结构。组合能让客户以一致的方式处理个别对象以及对象组合。
```csharp
public abstract class Component {
    ArrayList components = new ArrayList();

    public abstract void add(Component component);
    public abstract void remove(Component component);
    public abstract void getChild(int i);
    public virtual Iterator createIterator() {
        return new CompositeIterator(components.iterator());
    };
}

// 组合迭代器
public class CompositeIterator : Iterator {
    Stack stack = new Stack();

    // 将顶层组合的迭代器（相当于树的根节点）传入
    public CompositeIterator(Iterator iterator) {
        stack.push(iterator);
    }

    public Object next() {
        if (hasNext()) {
            Iterator iterator = (Iterator) stack.peek();
            Component component = (Component) iterator.next();
            stack.push(component.createIterator());
            return component;
        } else {
            return null;
        }
    }

    public bool hasNext() {
        if (!stack.empty()) {
            Iterator iterator = (Iterator) stack.peek();
            if (!iterator.hasNext()) {
                stack.pop();
                return hasNext();
            } else {
                return true;
            }
        }
        return false;
    }
}
```

**模式描述**  
  * 策略 -> 封装可交换的行为，并使用委托决定使用哪一个
  * 适配器 -> 改变一个或多个类的接口
  * 外观 -> 简化一群类的接口
  * 迭代器 -> 提供一个方式来遍历集合，而无须暴露集合的实现
  * 组合 -> 客户可以将对象的集合以及个别的对象一视同仁
  * 观察者 -> 当某个状态改变时，允许一群对象能被通知到