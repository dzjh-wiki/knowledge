# js性能优化

--_-
## 如何避免Reflow和Repaint
注意：Reflow比Repaint的成本更高。  

**repaint(重绘)**：repaint发生更改时，元素的外观被改变，且在没有改变布局的情况下发生，如改变outline,visibility,background color，不会影响到dom结构渲染。  

**reflow(渲染)**：与repaint区别就是他会影响到dom的结构渲染，同时他会触发repaint，他会改变他本身与所有父辈元素(祖先)，这种开销是非常昂贵的，导致性能下降是必然的，页面元素越多效果越明显。  

发生时机：  
  * DOM元素的添加、修改（内容）、删除( Reflow + Repaint)
  * 仅修改DOM元素的字体颜色（只有Repaint，因为不需要调整布局）
  * 应用新的样式或者修改任何影响元素外观的属性
  * Resize浏览器窗口、滚动页面
  * 读取元素的某些属性（offsetLeft、offsetTop、offsetHeight、offsetWidth、 scrollTop/Left/Width/Height、clientTop/Left/Width/Height、 getComputedStyle()、currentStyle(in IE))
  * 当你修改网页的默认字体时当，你修改 CSS 样式的时候

**注：display:none 会触发 reflow，而 visibility:hidden 只会触发 repaint，因为没有发现位置变化。**  

**如何避免**：
1) 把DOM离线后修改：  
  * 先将元素从document中删除，完成修改后再把元素放回原来的位置
  * 将元素的display设置为"none"(有一次 repaint)，然后你想怎么改就怎么改。比如修改 100 次，然后再把他显示出来
  * clone 一个 DOM 节点到内存里，然后想怎么改就怎么改，改完后，和在线的那个的交换一下
  * 如果需要创建多个DOM节点，可以使用DocumentFragment创建完后一次性的加入document 
```js
var fragment = document.createDocumentFragment();
fragment.appendChild(document.createTextNode('keenboy test 111'));
fragment.appendChild(document.createElement('br'));
fragment.appendChild(document.createTextNode('keenboy test 222'));
document.body.appendChild(fragment);
```

2) 集中修改样式：  
  * 尽可能少的修改元素style上的属性
  * 尽量通过修改className来修改样式
```js
// 不好的写法
var left = 10,
top = 10;
el.style.left = left + "px";
el.style.top  = top  + "px";
// 推荐写法
el.className += " theclassname";
```
  * 通过cssText属性来设置样式值
```js
element.style.width="80px"; //reflow
element.style.height="90px"; //reflow
element.style.border="solid 1px red"; //reflow
// 以上就产生多次reflow，调用的越多产生就越多
element.style.cssText="width:80px;height:80px;border:solid 1px red;"; //reflow
```
  * 缓存Layout属性值
```js
var left=elem.offsetLeft; // 多次使用left也就产生一次reflow
```
  * 设置元素的position为absolute或fixed。（元素脱离标准流，也从DOM树结构中脱离出来，在需要reflow时只需要reflow自身与下级元素）
  * 尽量不要用table布局。（table元素一旦触发reflow就会导致table里所有的其它元素 reflow。在适合用table的场合，可以设置table-layout为auto或fixed，这样可以让table一行一行的渲染，这种做法也是为了限制reflow的影响范围）
  * 避免使用expression,他会每次调用都会重新计算一遍(包括加载页面)

3) 为动画的 HTML 元件使用 fixed 或 absoult 的 position，那么修改他们的 CSS 是会大大减少`reflow`  

4) 尽可能的修改层级比较低的 DOM节点。当然，改变层级比较底的 DOM节点有可能会造成大面积的 reflow，但是也可能影响范围很小。  


## 垃圾收集
**对象和数据不再需要使用时，置为null**  

**与闭包相关的内存泄漏**  
```js
function assignHandler(){
  let element = document.getElementById("test");
  element.onclick = function(){
    alert(element.id)    
  }          
}

//以上会导致element的引用数无法被回收，更改如下
function assignHandler(){
  let element = document.getElementById("test");
  let id = element.id;
  
  element.onclick = function(){
    alert(id)
  }          
  element = null;  
}
```

## 事件委托
在js中，添加到页面上的事件处理程序数量会直接关系到页面整体运行运行性能。导致这一问题的原因是多方面的。  
首先函数都是对象，都会占用内存；内存中对象越多，性能就越差。其次，必须事先指定所有事件处理程序而导致的DOM访问次数，会延迟整个页面的交互就绪时间。  

**同类型的事件处理函数过多时，应该结合为一个**  
```js
//html代码
<ul id="myLinks">
    <li id="goSomeWhere">Go somewhere</li>
    <li id="sayHi">Say hi</hi>
</ul>


//分别加上事件处理-JS代码
let item1 = document.getElementById("goSomeWhere");
let item2 = document.getElementById("sayHi");

EventUtil.addHandler(item1, "click", function(event){
  console.log("goSomeWhere")  
}

EventUtil.addHandler(item2, "click", function(event){
  console.log("sayHi");  
}

//改善点即将click事件结合在一起
let list = document.getElementById("myLinks")

EventUtil.addHandler(list, "click", function(event){
  event = EventUtil.getEvent(event);
  let target = EventUtil.getTarget(event);
  
  switch(target.id){
    case "goSomeWhere":
      console.log("goSomeWhere");
      break;
   case "sayHi":
      console.log("sayHi");    
      break;            
  }     
})
```

**内存留有过时不用的“空事件处理程序”也是造成性能问题的主因，两种情况下会造成该问题。运用removeChild()和replaceChild()方法去除节点时；在使用innerHTML替换页面某一部分时，如果带有事件处理程序的元素被innerHTML删除了，那么原有事件处理函数极有可能无法被回收。**  


## 注意作用域
**访问全局变量会比访问局部变量要慢**  
