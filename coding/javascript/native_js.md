# 原生js

----
## 事件流
![js_event_flow.png](img/js_event_flow.png)  
事件流原理图：  
  * 一个完整的JS事件流是从window开始，最后回到window的一个过程
  * 事件流被分为三个阶段(1~5)捕获过程、(5~6)目标过程、(6~10)冒泡过程

**关于事件监听**  
addEventListener是Dom2里添加事件监听的写法可以接受三个参数，第一个参数是事件名称，第二个是事件处理函数，第三个参数是布尔值（true是设置事件捕获，false是设置事件冒泡，默认情况下是false）。  
IE里面添加监听事件是attachEvent但是IE不支持事件捕获，所以attachEvent只有前两个参数，不能设置true和false，同时事件名也要加上on。  

**为了解决和IE的兼容问题同城会将两种写法封装成一个函数进行调用，因为IE不支持事件捕获，所以尽量使用事件冒泡以解决兼容问题。**  
```js
var EventUtil={
    
   addHandler:function(element,type,handler){ //添加事件
      if(element.addEventListener){ 
         element.addEventListener(type,handler,false);  //使用DOM2级方法添加事件
      }else if(element.attachEvent){                    //使用IE方法添加事件
         element.attachEvent("on"+type,handler);
      }else{
         element["on"+type]=handler;          //使用DOM0级方法添加事件
      }
   },  

   removeHandler:function(element,type,handler){  //取消事件
      if(element.removeEventListener){
         element.removeEventListener(type,handler,false);
      }else if(element.detachEvent){
         element.detachEvent("on"+type,handler);
      }else{
         element["on"+type]=null;
      }
   },

   getEvent:function(event){  //使用这个方法跨浏览器取得event对象
      return event?event:window.event;
   },
    
   getTarget:function(event){  //返回事件的实际目标
      return event.target||event.srcElement;
   },
    
   preventDefault:function(event){   //阻止事件的默认行为
      if(event.preventDefault){
         event.preventDefault(); 
      }else{
         event.returnValue=false;
      }
   },

   stopPropagation:function(event){  //立即停止事件在DOM中的传播
                                     //避免触发注册在document.body上面的事件处理程序
      if(event.stopPropagation){
         event.stopPropagation();
      }else{
         event.cancelBubble=true;
      }
   },
        
   getRelatedTarget:function(event){  //获取mouseover和mouseout相关元素
      if(event.relatedTarget){
         return event.relatedTarget;
      }else if(event.toElement){      //兼容IE8-
         return event.toElement;
      }else if(event.formElement){
         return event.formElement;
      }else{
         return null;
      }
   },
        
   getButton:function(event){    //获取mousedown或mouseup按下或释放的按钮是鼠标中的哪一个
      if(document.implementation.hasFeature("MouseEvents","2.0")){
         return event.button;
      }else{
         switch(event.button){   //将IE模型下的button属性映射为DOM模型下的button属性
            case 0:
            case 1:
            case 3:
            case 5:
            case 7:
               return 0;  //按下的是鼠标主按钮（一般是左键）
            case 2:
            case 6:
               return 2;  //按下的是中间的鼠标按钮
            case 4:
               return 1;  //鼠标次按钮（一般是右键）
         }
      }
   },
        
   getWheelDelta:function(event){ //获取表示鼠标滚轮滚动方向的数值
      if(event.wheelDelta){
         return event.wheelDelta;
      }else{
         return -event.detail*40;
      }
   },
        
   getCharCode:function(event){   //以跨浏览器取得相同的字符编码，需在keypress事件中使用
      if(typeof event.charCode=="number"){
         return event.charCode;
      }else{
         return event.keyCode;
      }
   }
        
};
```


## window对象
```js
window.open() //打开新窗口
window.location //在域名后追加地址进入新页面
window.location.href //页面地址
window.location.reload() //页面重载
window.print() //打印页面
window.scrollTo(100,500) //窗口滚动到指定位置
```


## Document对象
**document.URL, document.referrer, location.href的区别：**  
  * 从输出结果上，document.URL 和 windows.location.href 没有区别。
  * 只能读取document.URL的值，不能修改它。windows.location.href的值既可以读取也可以修改，可以使用它进行页面跳转。
  * referrer则是返回前一个来源页面的URL，并不是当前页面。

### 元素选择
第一个返回选中id，其余则是返回一个数组：
  * document.getElementById()
  * document.getElementsByName()
  * document.getElementsByClassName()
  * document.getElementsByTagName()


## Event对象
**获取鼠标的按键，返回按键索引**  
```js
<!DOCTYPE html>
<html>
    <head>
        <script type="text/javascript">
            function swhichButton(event) {
                var result = document.getElementById("result");
                var btnNum = event.button;
                switch(btnNum) {
                    case 0:
                        result.innerHTML = "您点击了鼠标左键！";
                        break;
                    case 1:
                        result.innerHTML = "您点击了鼠标中键！";
                        break;
                    case 2:
                        result.innerHTML = "您点击了鼠标右键！";
                        break;
                    default:
                        result.innerHTML = "您点击了" + btnNum+ "号键！";
                        break;
                }
            }
        </script>
    </head>
    <body onclick="swhichButton(event)">
        <p>请在文档中点击鼠标。</p>
        <p id="result" style="color:red;"></p>
    </body>
</html>
```

**获取光标的坐标**  
```js
let x = event.clientX;
let y = event.clientY;  //获取光标的位置Y轴
```

**获取按键码**  
```js
var key = event.keyCode;
```

**获取光标相对于屏幕的位置**  
```js
let x = event.screenX;
let y = event.screenY;
```

**得到事件元素和事件类型**  
```js
event.target; //返回事件目标元素
event.type;  //返回事件类型 事件名称
```


## Form和Input对象
**复选框**  
```js
document.getElementById("Checkbox").checked=true //选中
```

**input**  
```js
document.getElementById("Input").value="test" //重置value
document.getElementById('Input').focus() //获取焦点
document.getElementById('Input').blur() //失去焦点
```

**form表单相关**  
```js
document.getElementById("Form").reset()
document.getElementById("Form").submit()
```


## Option和Select对象
**启用与禁用select**  
```js
document.getElementById("Select").disabled=true
document.getElementById("Select").disabled=false
```

**设置可以选取多个option**  
```js
document.getElementById("Select").multiple=true
```

**更改选择项**  
```js
document.getElementById("Option1").selected=true;
```


## Location对象
**window.location, window.location.href, window,location.replace的区别：**  
  * window.location是页面的位置对象，页面基于域名后添加新增的url跳转
  * window.location.href是location的一个属性值，页面替换url跳转

如果需要打开新窗口，使用window.open('')方法。  

如果当前页面是c页面，并且c页面是这样跳转过来的（a->b->c）：  
  * b->c 是通过window.location.replace("..xx/c") 此时b页面的url会被c页面代替，并且点击后退按钮时会回退到a页面（最开始的页面）。  
  * b->c是通过window.location.href("..xx/c") 此时b页面的路径会被c页面代替，但是点击回按钮后页面回退的是b页面。  


## Navigator对象
用于检测和获取浏览器相关信息
```js
navigator.appName //appname
navigator.appVersion //浏览器的版本信息
naviigator.appCodeName //浏览器代码 
navigator.cookieEnabled //是否启用了cookie
navigator.userAgent //浏览器的用户代理报头
```
