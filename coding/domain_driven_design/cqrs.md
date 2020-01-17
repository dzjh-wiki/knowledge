# CQRS
  * 命令查询职责分离模式[Command Query Responsibility Segregation].
  * 从业务上分离修改 (Command，增，删，改，会对系统状态进行修改)和查询（Query，查，不会对系统状态进行修改)的行为，即常说的读写隔离。

----
## 什么是CQS
基本思想在于，任何一个对象的方法可以分为两大类：  
  * 命令(Command):不返回任何结果(void)，但会改变对象的状态。
  * 查询(Query):返回结果，但是不会改变对象的状态，对系统没有副作用。
```java
private int i = 0;

// 原来的设计
private int Increase(int value)
{
    i += value;
    return i;
}

// 分离后的设计
private void IncreaseCommand(int value)
{
    i += value;
}
private int QueryValue()
{
    return i;
}
```
  
**CQRS是对CQS模式的进一步改进成的一种简单模式：**  
  * CQRS只是简单的将之前只需要创建**一个对象**拆分成了**两个对象**，这种分离基于方法是执行命令还是执行查询这一原则来定的(这个和CQS的定义一致)。

CQRS使用分离的接口将数据查询操作(Queries)和数据修改操作(Commands)分离开来，这也意味着在查询和更新过程中使用的数据模型也是不一样的，进而将读和写逻辑隔离开来。  