# C#面试题

----
## 1. .Net的垃圾回收（GC）
  * 垃圾回收器跟踪并回收托管内存中分配的对象，定期执行垃圾回收，以回收分配给没有有效引用的对象的内存。当可用内存不能满足内存请求时，GC会自动进行。
  * 在进行垃圾回收时，垃圾回收器会首先搜索内存中的托管对象，然后从托管代码中搜索被引用的对象并标记为有效，接着释放没有被标记为有效的对象并收回内存，最后整理内存将有效的对象挪动到一起。
  * 主动请求垃圾回收的接口：System.gc(); Runtime.getRuntime().gc();

## 2. HashMap和Hashtable的区别
  * HashMap是Hashtable的轻量级实现，非线程安全的实现。
  * 都实现了map接口，主要区别是HashMap的键值可以为空(null)，效率高于Hashtable。
  * C#中已没有HashMap。

## 3. Collection和Collections的区别
  * Collection是集合类的上级接口，Collections是针对集合类的一个帮助类，其提供一些列静态方法来实现对各种集合的搜索、排序、线程安全化操作。

## 4. Override和Overload的区别
  * Override（重写）表示重写基类的方法，而且方法的名称、返回类型、参数类型、参数个数要与基类相同。
  * Overload（重载）也表示重写基类的方法，但只要方法名相同，其他的可以不同。

## 5. 进程和线程
  * 进程是比线程大的程序运行单位。
  * 一个程序中至少要有一个进程，一个进程中，至少要有一个线程，线程的划分尺度要比进程小，进程拥有独立的内存单元，线程是共享内存，从而极大地提高了程序的运行效率，同一个进程中的多个线程可以并发执行。
  * 多线程不一定比单线程快，尤其是在进行I/O操作的时候，因为浪费了多线程切换的时间。多线程只是为了充分利用空闲的CPU，提高计算效率。

## 6. 能用foreach遍历访问的对象需实现的方法
  * 需实现IEnumerable接口和GetEnumerator()方法。

## 7. Heap和Stack的差别
  * Heap是堆，空间是由手动操作分配和释放的，其内存储区是很大的自由存储区。
  * Stack是栈，是由操作系统自动分配和释放的，栈上的空间是有限的。程序在编译期间变量和函数分配内存都是在栈上进行的，且在运行时函数调用的参数传递也是在栈上进行的。

## 8. abstract class和interface的区别
声明方法的存在而不去实现它的类称为抽象类（`abstract class`），它用于要创建一个体现某些基本行为的类，并为该类声明方法，但不能在该类中实现该类的情况。  
  * 不能创建abstract类的实例。
  * 可以创建一个变量，其类型是一个抽象类，并让它指向具体子类的一个实例。
  * 不能有抽象构造函数或抽象静态方法。
  * abstract类的子类为它们父类中的所有抽象方法提供实现，否则它们也是抽象类。

接口（`interface`）是抽象类的变体。  
  * 在接口中，所有方法都是抽象的。
  * 多继承性可通过实现这样的接口而获得。
  * 接口只能定义`static final`成员变量。
  * 接口的实现与子类相似，除了该实现类不能从接口定义中继承行为。
  * 它可以在实现了该接口的类的任何对象上调用接口的方法。
  * 引用可以转换到接口类型或从接口类型转换，instanceof运算符可以用来决定某对象的类是否实现了接口。

## 9. 一个对象的一个synchronized方法只能由一个线程访问

## 10. abstract的method不能同时是static、native、synchronized

## 11. List、Set继承自Collection接口

## 12. .Net的错误机制
  * .Net错误处理机制采用`try->catch->finally`结构，发生错误时，层层上抛，直至找到匹配的catch为止。

## 13. C#的委托和事件
  * 委托可以把一个方法作为参数代入另一个方法；
  * 委托可以理解为指向一个函数的指针；
  * 委托和事件没有可比性，因为委托是类型，事件是对象；

委托的对象（用委托方式实现的事件）和（标准的event方式实现）事件的区别：  
  * 事件的内部是用委托实现的。因为对于事件来讲，外部只能“注册自己+=、注销自己-=”，外界不可以注销其他的注册者，外界不可以主动触发事件，因此如果用Delegate就没法进行上面的控制，因此诞生了事件这种语法。
  * 事件是用来阉割委托实例的，类比用一个自定义类阉割List。事件只能add、remove自己，不能赋值。事件只能+=、-=，不能 = 。
  * 事件内部就是一个private的委托和add、remove两个方法。

## 14. CTS、CLS、CLR
  * CTS：Common Type System 通用系统类型。Int32、Int16→int、String→string、Boolean→bool
  * CLS：Common Language Specification 通用语言规范。不同语言语法的不同。
  * CLR：Common Language Runtime 公共语言运行时，就是.Net提供的那些 类。

## 15. 在.Net中类(class)与结构(struct)的异同
  * Class可以被实例化,属于引用类型,是分配在内存的堆上的。类是引用传递的。
  * Struct属于值类型,是分配在内存的栈上的。结构体是复制传递的。Int32、Boolean等都属于结构体。

## 16. String类是sealed类故不可以继承

## 17. new关键字用法
  * new 运算符 用于创建对象和调用构造函数；
  * new 修饰符 用于向基类成员隐藏继承成员；
  * new 约束 用于在泛型声明中约束可能用作类型参数的参数类型。

## 18. 将Array复制到ArrayList里
  * 实现1：string[] s ={ "111", "22222" }; ArrayList list = new ArrayList(); list.AddRange(s);
  * 实现2：string[] s ={ "111", "22222" }; ArrayList list = new ArrayList(s);

## 19. int、DateTime、string是否可以为null?
  * int、DateTime不能，因为其为Struct类型，而结构属于值类型，值类型不能为null,只有引用类型才能被赋值null。
  * string可以为null。

## 20. using关键字有什么用？什么是IDisposable？
  * using可以声明namespace的引入，还可以实现非托管资源的释放，实现了IDisposiable的类在using中创建，using结束后会自动调用该对象的Dispose方法，释放资源。

## 21. 应用程序域、受管制的代码、托管代码、强类型系统、装箱和拆箱、重载
  * 应用程序域为安全性、可靠性、版本控制以及卸载程序集提供了隔离边界。应用程序域通常由运行库宿主创建，运行库宿主负责在运行应用程序之前引导公共语言运行库。应用程序域提供了一个更安全、用途更广的处理单元，公共语言运行库可使用该单元提供应用程序之间的隔离。

  * 受管制的代码：在.Net环境中运行的任何代码都是受管制的代码（managed code），.Net外部的代码也运行在windows上，这些代码称为未受管制的代码（unmanaged code）。

  * 使用基于公共语言运行库的语言编译器开发的代码称为托管代码；托管代码具有许多优点，例如：跨语言集成、跨语言异常处理、增强的安全性、版本控制和部署支持、简化的组件交互模型、调试和分析服务等。

  * 强类型语言是能够禁止任何违反类型系统的代码的语言，或者说是能够捕获所有违反类型系统的错误的语言。我们说C++相对于C是强类型的，是因为C++禁止了一些C中的隐式转换，比如将void*转换为任意的指针类型。

  * 装箱和拆箱使值类型能够被视为对象。对值类型装箱将把该值类型打包到 Object 引用类型的一个实例中。这使得值类型可以存储于垃圾回收堆中。拆箱将从对象中提取值类型。

  * 每个类型成员都有一个唯一的签名。方法签名由方法名称和一个参数列表（方法的参数的顺序和类型）组成。只要签名不同，就可以在一种类型内定义具有相同名称的多种方法。当定义两种或多种具有相同名称的方法时，就称作重载。

## 22. 接口是一种引用类型，在接口中可以声明方法、属性、索引器和事件，但不可以声明公有的域或私有的成员变量

## 23. const与readonly的区别
  * const 关键字用于修饰字段或局部变量的声明。它指定字段或局部变量的值不能被修改。常数声明引入给定类型的一个或多个常数。
  * const数据成员的声明式必须包含初值，且初值必须是一个常量表达式。因为它是在编译时就需要完全评估。
  * const成员可以使用另一个const成员来初始化，前提是两者之间没有循环依赖。
  * readonly在运行期评估赋值，使我们得以在确保“只读访问”的前提下，把object的初始化动作推迟到运行期进行。
  * readonly 关键字与 const 关键字不同：const 字段只能在该字段的声明中初始化。readonly 字段可以在声明或构造函数中初始化。因此，根据所使用的构造函数，readonly 字段可能具有不同的值。另外，const 字段是编译时常数，而 readonly 字段可用于运行时常数。
  * readonly 只能在声明时或者构造函数里面初始化，并且不能在 static 修饰的构造函数里面初始化。

## 24. 使用虚(virtual)方法的情况，其与接口的不同之处
  * 子类重新定义父类的某一个方法时，必须把父类的方法定义为virtual；
  * 在定义接口中不能有方法体，虚方法可以。

与接口的不同之处：  
  * 实现时,子类可以不重新定义虚方法，但如果一个类继承接口，那必须实现这个接口。