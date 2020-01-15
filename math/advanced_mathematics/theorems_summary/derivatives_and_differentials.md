# 导数与微分

----

## 导数

### 定义
设函数$$f(x)$$在点$$x_0$$的某领域内有定义，当自变量$$x$$在$$x_0$$处取得增量$$\Delta x$$（点$$x_0 + \Delta x$$仍在该领域内）时，相应的函数取得增量$$\Delta y = f(x_0 + \Delta x) - f(x_0)$$；如果$$\Delta y$$与$$\Delta x$$之比当$$\Delta x \to 0$$时的极限存在，则函数$$y = f(x)$$在点$$x_0$$处可导，并称这个极限为函数$$y = f(x)$$在点$$x_0$$处的导数，记作$$f'(x_0)$$，即
$$f'(x_0) = f'(x)|_{x=x_0} = \lim_{\Delta x \to 0} \frac{\Delta y}{\Delta x} = \lim_{\Delta x \to 0} \frac{f(x_0 + \Delta x) - f(x_0)}{\Delta x}$$

### 简单函数的导数
  * $$(C)' = 0$$，C为常数
  * $$(x^{\mu})' = \mu x^{\mu - 1}$$
  * $$(sin x)' = cos x$$
  * $$(a^x)' = a^x lna$$
  * $$(log_a x)' = \frac{1}{x ln a}$$

### 注意
函数$$f(x)$$在点$$x_0$$处可导的充分必要条件是左导数$$f'_-(x_0)$$和右导数$$f'_+(x_0)$$都存在且相等。

### 函数可导与连续性关系
函数$$y=f(x)$$在$$x$$处可导，则函数在该点必连续。然而，函数$$y=f(x)$$在$$x$$处连续，却不一定在该点可导。

## 函数的求导法则

### 定理1
如果函数$$u = u(x)$$及$$v = v(x)$$都在点$$x$$具有导数，那么它们的和、差、积、商（除分母为0的点外）都在点$$x$$具有导数，且  
  * (1) $$[u(x) \pm v(x)]' = u'(x) \pm v'(x)$$
  * (2) $$[u(x) v(x)]' = u'(x) v(x) + u(x) v'(x)$$
  * (3) $$[\frac{u(x)}{v(x)}]' = \frac{u'(x) v(x) - u(x) v'(x)}{v^2(x)} (v(x) \neq 0)$$

### 定理2
如果函数$$x = f(y)$$在区间$$I_y$$内单调、可导且$$f'(y) \neq 0$$，则它的反函数$$y = f^{-1}(x)$$在区间$$I_x = {x|x=f(y), y \in I_y}$$内也可导，且$$[f^{-1}(x)]' = \frac{1}{f'(y)} 或 \frac{dy}{dx} = \frac{1}{\frac{dx}{dy}}$$。

### 定理3
如果函数$$u = g(x)$$在点$$x$$可导，而$$y = f(u)$$在点$$u = g(x)$$可导，则复合函数$$y = f[g(x)]$$在点$$x$$可导，且其导数为$$\frac{dy}{dx} = f'(u) \cdot g'(x) 或 \frac{dy}{dx} = \frac{dy}{du} \cdot \frac{du}{dx}$$。

## 高阶导数

### 莱布尼兹公式
$$(uv)^{(n)} = \sum_{k=0}^n C^k_n u^{(n - k)} v^{(k)}$$

格式与二项式定理展开类似：  
$$(u + v)^n = u^n v^0 + n u^{n-1}v^1 + \frac{n(n-1)}{2!} u^{n-2} v^2 + \cdots + u^0 v^n$$
，即
$$(u + v)^{n} = \sum_{k=0}^n C^k_n u^{n - k} v^{k}$$

## 隐函数及参数方程

### 隐函数的显化
如，从方程$$x + y^3 - 1 = 0$$解出$$y = \sqrt[3]{1-x}$$，即将隐函数化成了显函数。

### 求导
#### 隐函数求导法
例子：  
【求由方程$$x - y + \frac{1}{2} \sin y = 0$$所确定的隐函数的二阶导数$$\frac{d^2y}{dx^2}$$。】  
解：应用隐函数的求导方法，得  
$$1 - \frac{dy}{dx} + \frac{1}{2} \cos y \cdot \frac{dy}{dx}$$
于是，$$\frac{dy}{dx} = \frac{2}{2 - \cos y}$$。  
然后上式两边再对$$x$$求导，得  
$$\frac{d^2y}{dx^2} = \frac{-2 \sin \frac{dy}{dx}}{(2 - \cos y)^2} = \frac{-4 \sin y}{(2 - \cos y)^3}$$

#### 对数求导法
例子：  
【求$$y = \sqrt{\frac{(x - 1)(x - 2)}{(x - 3)(x - 4)}}$$的导数。】  
解：先对两边取对数（设$$x > 4$$），得  
$$lny = \frac{1}{2} [ln(x - 1) + ln(x - 2) - ln(x - 3) - ln(x - 4)]$$
，上式两边对$$x$$求导，注意到$$y = y(x)$$，得  
$$y' = \frac{y}{2} (\frac{1}{x - 1} + \frac{1}{x - 2} - \frac{1}{x - 3} - \frac{1}{x - 4})$$
当$$x < 1$$时，$$y = \sqrt{\frac{(1 - x)(2 - x)}{(3 - x)(4 - x)}}$$  
当$$2< x < 3$$时，$$y = \sqrt{\frac{(x - 1)(x - 2)}{(3 - x)(4 - x)}}$$  
用同样的方法可得与上面相同的结果。

#### 参数方程求导
例子：  
【求由摆线的参数方程$$\{^{x = a(t - sint)}_{y = a(1 - cost)}$$，所确定的函数$$y = y(x)$$的二阶导数。】  
解：$$\frac{dy}{dx} = \frac{\frac{dy}{dt}}{\frac{dx}{dt}} = \frac{a sint}{a(1-cost)} = \frac{sint}{1-cost} = cot \frac{t}{2} (t \neq 2n \pi,n \in Z)$$
$$\frac{d^2(y)}{dx^2} = \frac{d}{dt}(cot \frac{t}{2}) \cdot \frac{1}{\frac{dx}{dt}} = - \frac{1}{2 sin^2 \frac{t}{2}} \cdot \frac{1}{a(1 - cost)} = - \frac{1}{a(1- cost)^2} (t \neq 2n \pi,n \in Z)$$

## 函数的微分

### 定义
设函数$$y=f(x)$$在某区间内有定义，$$x_0$$及$$x_0+\Delta x$$在这区间内，如果增量$$\Delta y = f(x_0 + \Delta x) - f(x_0)$$可表示为$$\Delta y = A \Delta x + \omicron(\Delta x)$$，其中A是不依赖于$$\Delta x$$的常数，那么称函数$$y = f(x)$$在点$$x_0$$是可微的，而$$A \Delta x$$叫做函数$$y = f(x)$$在点$$x_0$$相应于自变量增量$$\Delta x$$的微分，记作$$dy$$，即
$$dy = A \Delta x$$

### 函数和、差、积、商的微分法则
| 函数和、差、积、商的求导法则 | 函数和、差、积、商的微分法则 |
| ---- | ---- |
| $$(u \pm v)' = u' \pm v'$$ | $$d(u \pm v) = du \pm dv$$ |
| $$(Cu)' = Cu'$$ | $$d(Cu) = Cdu$$ |
| $$(uv)' = u'v + uv'$$ | $$d(uv) = du v + u dv$$ |
| $$(\frac{u}{v})' = \frac{u' v - u v'}{v^2} (v \neq 0)$$ | $$d(\frac{u}{v}) = \frac{du v - u dv}{v^2} (v \neq 0)$$ |

### 复合函数的微分法则
设函数$$y=f(u)$$及$$u=g(x)$$都可导，则复合函数$$y=f[g(x)]$$的微分为$$dy = y_x' dx = f'(u)g'(x)dx$$
，由于$$g'(x)dx = du$$，所以，复合函数$$y = f[g(x)]$$的微分公式也可以写成$$dy = f'(u)du 或 dy = y_u' du$$。

### 函数的近似计算
如果$$y = f(x)$$在点$$x_0$$处的导数$$f'(x_0) \neq 0$$，且$$|\Delta x|$$很小时，有$$\Delta y \approx dy = f'(x_0) \Delta x$$

### 误差估计
如果某个量的精确值为A，近似值为a，那么$$|A - a|$$叫做a的绝对误差，而$$\frac{|A - a|}{|a|}$$叫做a的相对误差。
