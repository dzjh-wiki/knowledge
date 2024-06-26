# JavaScript语言特性

----
## 原型链
每个构造函数(`constructor`)都有一个原型对象(`prototype`)，原型对象都包含一个指向构造函数的指针，而实例(`instance`)都包含一个指向原型对象的内部指针。  

如果试图引用对象(实例`instance`)的某个属性，会首先在对象内部寻找该属性，直至找不到，然后才在该对象的原型(`instance.prototype`)里去找这个属性。  

```js
function Father() {
    this.name = "Father";
}
Father.prototype.getName = function() {
    return this.name;
}

function Son() {
    this.sonName = "Son";
}
Son.prototype = Father();
Son.prototype.getSonName = function() {
    return this.sonName;
}

var instance = new Son();
alert(instance.getName()); // Father
```

**注意: 此时`instance.constructor`指向的是`Father`,这是因为`Son.prototype`中的`constructor`被重写的缘故。**

### 判断原型和实例的继承关系
  * 1) 使用`instanceof`操作符，只要用这个操作符来测试实例(`instance`)与原型链中出现过的构造函数,结果就会返回true。  
  * 2) 使用`isPrototypeOf()`方法，同样只要是原型链中出现过的原型，`isPrototypeOf()`方法就会返回true。

### 原型链的问题
  * 问题一: 当原型链中包含引用类型值的原型时，该引用类型值会被所有实例共享;
  * 问题二: 在创建子类型(例如创建Son的实例)时，不能向超类型(例如Father)的构造函数中传递参数。

### 借用构造函数
```js
function Father() {
    this.nameList = ["Base", "Father"];
}

function Son() {
    Father.call(this);
}
var instance1 = new Son();
instance1.nameList.push("Son");
console.log(instance1.nameList); // ["Base", "Father", "Son"]
		
var instance2 = new Son();
console.log(instance2.nameList); // ["Base", "Father"]
```

### new 运算符
```js
var obj  = {};
obj.__proto__ = F.prototype;
F.call(obj);
```

使用`new`操作符调用构造函数的时候，函数内部实际上发生以下变化：
  * 1) 创建一个空对象，并且`this`变量引用该对象，同时还继承了该函数的原型。
  * 2) 属性和方法被加入到`this`引用的对象中。
  * 3) 新创建的对象由`this`所引用，并且最后隐式的返回`this`。

### 属性查找
若想避免原型链查找，请使用`hasOwnProperty`方法。因为`hasOwnProperty`是`JavaScript`中唯一一个处理属性但是不查找原型链的函数。


## 闭包
闭包是一种保护私有变量的机制，在函数执行时形成私有的作用域，保护里面的私有变量不受外界干扰。  
可将闭包简单理解成"定义在一个函数内部的函数"。

**注意点：**  
  * 1) 由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。
  * 2) 闭包会在父函数外部，改变父函数内部变量的值。所以，若将父函数当作对象（`object`）使用，把闭包当作它的公用方法（`Public Method`），把内部变量当作它的私有属性（`private value`），这时一定要小心，不要随便改变父函数内部变量的值。