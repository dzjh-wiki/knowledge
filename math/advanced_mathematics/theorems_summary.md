# 定理汇总

----

## 函数与极限


### 数列极限

#### 定义
设$\{x_n\}$为一数列，如果存在常数a，对于任意给定的整数$\varepsilon$（无论它何其小），总存在正整数N，使得当$n > N$时，不等式  
$$|x_n - a|<\varepsilon$$
都成立，则称常数a是**数列${\{x_n\}}$的极限**，或者称数列$\{x_n\}$收敛于a，记为  
$$\lim_{n \to \infty} x_n = a$$
或  
$$x_n \to a (n \to \infty)$$


### 函数极限

#### 定义1
设函数$f(x)$在点$x_0$的某一去心邻域内有定义。如果存在常数A，对于任意给定的正数$\varepsilon$（无论它何其小），总存在正数$\delta$，使得当x满足不等式$0<|x - x_0|<\delta$时，对应的函数值$f(x)$都满足不等式
$$|f(x) - A|<\varepsilon$$
，那么常数A就叫做函数$f(x)$当$x \to x_0$时的极限，记作
$$\lim_{x \to x_0} f(x) = A 或 f(x) \to A(当x \to x_0)$$。

#### 定义2
设函数$f(x)$当$|x|$大于某一正数时有定义。如果存在常数A，对于任意给定的正数$\varepsilon$（无论它何其小），总存在着正数X，使得当x满足不等式$|x| > X$时，对应的函数$f(x)$都满足不等式
$$|f(x) - A|<\varepsilon$$
，那么常数A就叫做函数$f(x)$当$x \to \infty$时的极限，记作
$$\lim_{x \to \infty} f(x) = A 或 f(x) \to A(当x \to \infty)$$。


### 无穷小与无穷大

#### 无穷小定义
如果函数$f(x)$当$x \to x_0（或x \to \infty）$时的极限为零，那么称函数$f(x)$为当$x \to x_0（或x \to \infty）$时的无穷小。

#### 无穷大定义
设函数$f(x)$在点$x_0$的某一去心邻域内有定义（或$|x|大于某一正数时有定义$）。如果对于任意给定的正数M（无论它何其大），总存在正数$\delta$（或正数X），只要x适合不等式$0<|x - x_0|<\delta（或|x|<X）$时，对应的函数值$f(x)$总满足不等式
$$|f(x)|>M$$
，则称函数$f(x)$为当$x \to x_0（或x \to \infty）$时的无穷大。


### 极限运算法则

#### 定理1
有限个无穷小的和也是无穷小。

#### 定理2
有界函数与无穷小的乘积是无穷小。

##### 推论1
常数与无穷小的乘积是无穷小。

##### 推论2
有限个无穷小的乘积也是无穷小。

#### 定理3
如果$\lim f(x) = A，\lim g(x) = B$，那么  
  * (1)$\lim [f(x) \pm g(x)] = \lim f(x) \pm \lim g(x) = A \pm B$;
  * (2)$\lim [f(x) \cdot g(x)] = \lim f(x) \cdot \lim g(x) = A \cdot B$;
  * (3)若又有$B \neq 0$，则$$\lim \frac{f(x)}{g(x)} = \frac{\lim f(x)}{\lim g(x)} = \frac{A}{B}$$.

##### 推论1
如果$\lim f(x)$存在，而c为常数，则$$\lim [c f(x)] = c \lim f(x)$$

##### 推论2
如果$\lim f(x)$存在，而n为正整数，则$$\lim [f(x)]^n = [\lim f(x)]^n$$

#### 定理4
设有数列${x_n}$和${y_n}$。如果$$\lim_{n \to \infty} x_n = A, \lim_{n \to \infty} y_n = B$$，那么  
  * (1)$$\lim_{n \to \infty} (x_n \pm y_n) = A \pm B$$
  * (2)$$\lim_{n \to \infty} x_n \cdot y_n = A \cdot B$$
  * (3)当$y_n \neq0(n = 1,2,...)$且$B \neq 0$时，$$\lim_{n \to \infty} \frac{x_n}{y_n} = \frac{A}{B}$$

#### 定理5
如果$\varphi(x) \geq \psi(x)$，而$\lim \varphi(x) = a, \lim \psi(x) = b$，那么$a \geq b$。

#### 定理6（复合函数的极限运算法则）
设函数$y = f[g(x)]$是由函数$u = g(x)$与函数$y = g(u)$复合而成，$f[g(x)]$在点$x_0$的某去心邻域内有定义，若$\lim_ {x\to x_0} g(x) = u_0, \lim_ {u\to u_0} f(u) = A$，且存在$\delta_0 > 0$，当$x \in \bigcup ^ \circ (x_0, \delta_0)$时，有$g(x) \pm u_0$，则$$\lim_{x \to x_0} f[g(x)] = \lim_{u \to u_0} f(u) = A$$


### 极限存在准则
`准则1`及`准则1'`称为**夹逼准则**。

#### 准则1
如果数列$\{x_n\}$、$\{y_n\}$及$\{z_n\}$满足下列条件：  
(1) 从某项起，即$\exists n_0 \in N$，当$n > n_0$时，有$$y_n \leq x_n \leq z_n$$
(2) $$\lim_{n \to \infty} y_n = a, \lim_{n \to \infty} z_n = a$$
那么数列$\{x_n\}$的极限存在，且$\lim_{n \to \infty} x_n = a$。

##### 准则1'
如果  
(1) $x \in \bigcup ^ \circ (x_0, r)$（或$|x| > M$）时，$$g(x) \leq f(x) \leq h(x)$$
(2) $$\lim_{x \to x_0 (x \to \infty)} g(x) = A, \lim_{x \to x_0 (x \to \infty)} h(x) = A$$
那么$\lim_{x \to x_0 (x \to \infty)} f(x)$存在，且等于A。

#### 准则2
单调有界数列必有极限。

##### 准则2'
设函数$f(x)$在点$x_0$的某个左邻域内单调并且有界，则$f(x)$在$x_0$的左极限$f(x_0^-)$必定存在。

#### 柯西极限存在准则（柯西审敛原理）
数列$\{x_n\}$收敛的充分必要条件是：对于任意给定的正数$\varepsilon$，存在着这样的正整数N，使得当$m > N, n > N$时，就有$$|x_n - x_m| < \varepsilon$$。


### 无穷小的比较

#### 定义
  * 如果$\lim \frac{\beta}{\alpha} = 0$, 就说$\beta$是比$\alpha$**高阶的无穷小**，记作$\beta = \omicron (\alpha)$;
  * 如果$\lim \frac{\beta}{\alpha} = \infty$, 就说$\beta$是比$\alpha$**低阶的无穷小**;
  * 如果$\lim \frac{\beta}{\alpha} = c \neq 0$, 就说$\beta$与$\alpha$是**同阶无穷小**;
  * 如果$\lim \frac{\beta}{\alpha} = c \neq 0, k > 0$, 就说$\beta$是关于$\alpha$的**k阶无穷小**;
  * 如果$\lim \frac{\beta}{\alpha} = 1$, 就说$\beta$与$\alpha$的**等阶无穷小**，记作$\alpha$~$\beta$。

#### 定理1
$\beta$与$\alpha$是等价无穷小的充分必要条件为$$\beta = \alpha + \omicron (\alpha)$$。

#### 定理2
设$\alpha$~$\alpha '$，$\beta$~$\beta '$，且$\lim \frac{\beta '}{\alpha '}$存在，则$$\lim \frac{\beta}{\alpha} = \lim \frac{\beta '}{\alpha '}$$。


### 函数的连续性与间断点

#### 定义1
设函数$y = f(x)$在点$x_0$的某一邻域内有定义，如果$$\lim_{\Delta x \to 0}[f(x_0 + \Delta x) - f(x_0)] = 0$$，那么就称函数$y = f(x)$在点$x_0$连续。  

**函数$y = f(x)$在点$x_0$连续的定义：**  
设函数$y = f(x)$在点$x_0$的某一邻域内有定义，如果$$\lim_{\Delta x \to 0}[f(x)] = f(x_0)$$，那么就称函数$f(x)$在点$x_0$连续。 

#### 定义2
设函数$y = f(x)$在点$x_0$的某去心邻域内有定义。在此前提下，如果函数$f(x)$有下列三种情形之一：
  * (1) 在$x = x_0$没有定义；
  * (2) 虽在$x = x_0$有定义，但$\lim_{x \to x_0} f(x)$不存在；
  * (3) 虽在$x = x_0$有定义，且$\lim_{x \to x_0} f(x)$存在，但$\lim_{x \to x_0} f(x) \neq f(x_0)$，

则函数$f(x)$在点$x_0$为不连续，而点$x_0$称为函数$f(x)$的**不连续点**或**间断点**。


### 连续函数的运算与初等函数的连续性

#### 定理1
设函数$f(x)$和$g(x)$在点$x_0$连续，则它们的和（差）$f \pm g$、积$f \cdot g$及商$\frac{f}{g}$（当$g(x_0) \neq 0$时）都在点$x_0$连续。

#### 定理2
如果函数$y = f(x)$在区间$I_x$上单调增加（或单调减少）且连续，那么它的反函数$x = f^{-1}(y)$也在对应的区间$I_y = \{y|y=f(x), x \in I_x\}$上单调增加（或单调减少）且连续。

#### 定理3
设函数$y = [g(x)]$由函数$u = g(x)$与函数$y = f(u)$复合而成，$U^\circ (x_0) \subset D_{f \cdot g}$。若$\lim_{x \to x_0} g(x) = u_0$，而函数$y = f(u)$在$u = u_0$连续，则$$\lim_{x \to x_0} f[g(x)] = lim_{u \to u_0} f(u) = f(u_0)$$。

#### 定理4
设函数$y = [g(x)]$由函数$u = g(x)$与函数$y = f(u)$复合而成，$U(x_0) \subset D_{f \cdot g}$。若函数$u = g(x)$在$x = x_0$连续，而函数$y = f(u)$在$u = u_0$连续，则复合函数$y = [g(x)]$在$x = x_0$也连续。


### 闭区间上连续函数的性质

#### 定理1
（有界性与最大值最小值定理）  
在闭区间上连续的函数在该区间上有界且一定能取得它的最大值和最小值。

#### 定理2
（零点定理）  
设函数$f(x)$在闭区间$[a, b]$上连续，且$f(a)$与$f(b)$异号（即$f(a) \cdot f(b) < 0$），那么在开区间$(a, b)$内至少有一点$\xi$，使$$f(\xi) = 0$$。

#### 定理3
（介值定理）  
设函数$f(x)$在闭区间$[a,b]$上连续，且在这区间的端点取不同的函数值$f(a) = A$及$f(b) = B$，那么对于A与B之间的任意一个数C，在开区间$(a, b)$内至少有一点$\xi$，使得$$f(\xi) = C (a < \xi < b)$$。

#### 定义
设函数$f(x)$在区间$I$上有定义。若果对于任意给定的正数$\varepsilon$，总存在着正数$\delta$，使得对于区间$I$上的任意两点$x_1、x_2$，当$|x_1 - x_2| < \delta$时，就有$$|f(x_1) - f(x_2)| < \varepsilon$$，那么称函数$f(x)$在区间$I$上是一致连续的。

#### 定理4
（一致连续性定理）  
如果函数$f(x)$在闭区间$[a, b]$上连续，那么它在该区间上一直连续。


## 导数与微分

### 导数

#### 定义
设函数$f(x)$在点$x_0$的某领域内有定义，当自变量$x$在$x_0$处取得增量$\Delta x$（点$x_0 + \Delta x$仍在该领域内）时，相应的函数取得增量$\Delta y = f(x_0 + \Delta x) - f(x_0)$；如果$\Delta y$与$\Delta x$之比当$\Delta x \to 0$时的极限存在，则函数$y = f(x)$在点$x_0$处可导，并称这个极限为函数$y = f(x)$在点$x_0$处的导数，记作$f'(x_0)$，即
$$f'(x_0) = f'(x)|_{x=x_0} = \lim_{\Delta x \to 0} \frac{\Delta y}{\Delta x} = \lim_{\Delta x \to 0} \frac{f(x_0 + \Delta x) - f(x_0)}{\Delta x}$$

#### 简单函数的导数
  * $(C)' = 0$，C为常数
  * $(x^{\mu})' = \mu x^{\mu - 1}$
  * $(sin x)' = cos x$
  * $(a^x)' = a^x lna$
  * $(log_a x)' = \frac{1}{x ln a}$

#### 注意
函数$f(x)$在点$x_0$处可导的充分必要条件是左导数$f'_-(x_0)$和右导数$f'_+(x_0)$都存在且相等。

#### 函数可导与连续性关系
函数$y=f(x)$在$x$处可导，则函数在该点必连续。然而，函数$y=f(x)$在$x$处连续，却不一定在该点可导。

### 函数的求导法则

#### 定理1
如果函数$u = u(x)$及$v = v(x)$都在点$x$具有导数，那么它们的和、差、积、商（除分母为0的点外）都在点$x$具有导数，且  
  * (1) $[u(x) \pm v(x)]' = u'(x) \pm v'(x)$
  * (2) $[u(x) v(x)]' = u'(x) v(x) + u(x) v'(x)$
  * (3) $[\frac{u(x)}{v(x)}]' = \frac{u'(x) v(x) - u(x) v'(x)}{v^2(x)} (v(x) \neq 0)$

#### 定理2
如果函数$x = f(y)$在区间$I_y$内单调、可导且$f'(y) \neq 0$，则它的反函数$y = f^{-1}(x)$在区间$I_x = {x|x=f(y), y \in I_y}$内也可导，且$$[f^{-1}(x)]' = \frac{1}{f'(y)} 或 \frac{dy}{dx} = \frac{1}{\frac{dx}{dy}}$$。

#### 定理3
如果函数$u = g(x)$在点$x$可导，而$y = f(u)$在点$u = g(x)$可导，则复合函数$y = f[g(x)]$在点$x$可导，且其导数为$$\frac{dy}{dx} = f'(u) \cdot g'(x) 或 \frac{dy}{dx} = \frac{dy}{du} \cdot \frac{du}{dx}$$。

### 高阶导数

#### 莱布尼兹公式
$$(uv)^{(n)} = \sum_{k=0}^n C^k_n u^{(n - k)} v^{(k)}$$

格式与二项式定理展开类似：  
$$(u + v)^n = u^n v^0 + n u^{n-1}v^1 + \frac{n(n-1)}{2!} u^{n-2} v^2 + \cdots + u^0 v^n$$
，即
$$(u + v)^{n} = \sum_{k=0}^n C^k_n u^{n - k} v^{k}$$

### 隐函数及参数方程

#### 隐函数的显化
如，从方程$x + y^3 - 1 = 0$解出$y = \sqrt[3]{1-x}$，即将隐函数化成了显函数。

#### 求导
##### 隐函数求导法
例子：  
【求由方程$x - y + \frac{1}{2} \sin y = 0$所确定的隐函数的二阶导数$\frac{d^2y}{dx^2}$。】  
解：应用隐函数的求导方法，得  
$$1 - \frac{dy}{dx} + \frac{1}{2} \cos y \cdot \frac{dy}{dx}$$
于是，$\frac{dy}{dx} = \frac{2}{2 - \cos y}$。  
然后上式两边再对$x$求导，得  
$$\frac{d^2y}{dx^2} = \frac{-2 \sin \frac{dy}{dx}}{(2 - \cos y)^2} = \frac{-4 \sin y}{(2 - \cos y)^3}$$

##### 对数求导法
例子：  
【求$y = \sqrt{\frac{(x - 1)(x - 2)}{(x - 3)(x - 4)}}$的导数。】  
解：先对两边取对数（设$x > 4$），得  
$$lny = \frac{1}{2} [ln(x - 1) + ln(x - 2) - ln(x - 3) - ln(x - 4)]$$
，上式两边对$x$求导，注意到$y = y(x)$，得  
$$y' = \frac{y}{2} (\frac{1}{x - 1} + \frac{1}{x - 2} - \frac{1}{x - 3} - \frac{1}{x - 4})$$
当$x < 1$时，$y = \sqrt{\frac{(1 - x)(2 - x)}{(3 - x)(4 - x)}}$  
当$2< x < 3$时，$y = \sqrt{\frac{(x - 1)(x - 2)}{(3 - x)(4 - x)}}$  
用同样的方法可得与上面相同的结果。

##### 参数方程求导
例子：  
【求由摆线的参数方程$\{^{x = a(t - sint)}_{y = a(1 - cost)}$，所确定的函数$y = y(x)$的二阶导数。】  
解：$$\frac{dy}{dx} = \frac{\frac{dy}{dt}}{\frac{dx}{dt}} = \frac{a sint}{a(1-cost)} = \frac{sint}{1-cost} = cot \frac{t}{2} (t \neq 2n \pi,n \in Z)$$
$$\frac{d^2(y)}{dx^2} = \frac{d}{dt}(cot \frac{t}{2}) \cdot \frac{1}{\frac{dx}{dt}} = - \frac{1}{2 sin^2 \frac{t}{2}} \cdot \frac{1}{a(1 - cost)} = - \frac{1}{a(1- cost)^2} (t \neq 2n \pi,n \in Z)$$

### 函数的微分

#### 定义
设函数$y=f(x)$在某区间内有定义，$x_0$及$x_0+\Delta x$在这区间内，如果增量$$\Delta y = f(x_0 + \Delta x) - f(x_0)$$可表示为$$\Delta y = A \Delta x + \omicron(\Delta x)$$，其中A是不依赖于$\Delta x$的常数，那么称函数$y = f(x)$在点$x_0$是可微的，而$A \Delta x$叫做函数$y = f(x)$在点$x_0$相应于自变量增量$\Delta x$的微分，记作$dy$，即
$$dy = A \Delta x$$

#### 函数和、差、积、商的微分法则
| 函数和、差、积、商的求导法则 | 函数和、差、积、商的微分法则 |
| ---- | ---- |
| $(u \pm v)' = u' \pm v'$ | $d(u \pm v) = du \pm dv$ |
| $(Cu)' = Cu'$ | $d(Cu) = Cdu$ |
| $(uv)' = u'v + uv'$ | $d(uv) = du v + u dv$ |
| $(\frac{u}{v})' = \frac{u' v - u v'}{v^2} (v \neq 0)$ | $d(\frac{u}{v}) = \frac{du v - u dv}{v^2} (v \neq 0)$ |

#### 复合函数的微分法则
设函数$y=f(u)$及$u=g(x)$都可导，则复合函数$y=f[g(x)]$的微分为$$dy = y_x' dx = f'(u)g'(x)dx$$
，由于$g'(x)dx = du$，所以，复合函数$y = f[g(x)]$的微分公式也可以写成$$dy = f'(u)du 或 dy = y_u' du$$。

#### 函数的近似计算
如果$y = f(x)$在点$x_0$处的导数$f'(x_0) \neq 0$，且$|\Delta x|$很小时，有$$\Delta y \approx dy = f'(x_0) \Delta x$$

#### 误差估计
如果某个量的精确值为A，近似值为a，那么$|A - a|$叫做a的绝对误差，而$\frac{|A - a|}{|a|}$叫做a的相对误差。


## 微分中值定理与导数的应用

### 微分中值定理

#### 费马定理
设函数$f(x)$在点$x_0$的某邻域$U(x_0)$内有定义，并且在$x_0$处可导，如果对任意的$x \in U(x_0)$，有$$f(x) \leq f(x_0) (或 f(x) \geq f(x_0))$$，那么$f'(x) = 0$。【通常导数为0的点成为函数的驻点（或稳定点，临界点）】

#### 罗尔定理
如果函数$f(x)$满足
  * (1) 在闭区间$[a, b]$上连续；
  * (2) 在开区间$(a, b)$内可导；
  * (3) 在区间端点处的函数值相等，即$f(a) = f(b)$,
那么在$(a, b)$内至少有一点$\xi (a < \xi < b)$，使得$f'(\xi) = 0$

#### 拉格朗日中值定理
如果函数$f(x)$满足  
  * (1) 在闭区间$[a, b]$上连续；
  * (2) 在开区间$(a, b)$内可导；
那么在$(a, b)$内至少有一点$\xi (a < \xi < b)$，使等式
$$f(a) - f(b) = f'(\xi)(b - a)$$
成立。

#### 柯西中值定理
如果函数$f(x)$及$F(x)$满足  
  * (1) 在闭区间$[a, b]$上连续；
  * (2) 在开区间$(a, b)$内可导；
  * (3) 对任一$x \in (a, b), F'(x) \neq 0$，
那么在$(a, b)$内至少有一点$\xi$，使等式
$$\frac{f(b) - f(a)}{F(b) - F(a)} = \frac{f'(\xi)}{F'(\xi)}$$
成立。

### 洛必达法则

#### 定理1
设  
  * (1) 当$x \to a$时，函数$f(x)$及$F(x)$都趋于零；
  * (2) 在点$a$的某去心邻域内，$f'(x)$及$F'(x)$都存在且$F'(x) \neq 0$；
  * (3) $\lim_{x \to a} \frac{f'(x)}{F'(x)}$存在（或为无穷大），
那么$$\lim_{x \to a} \frac{f(x)}{F(x)} = \lim_{x \to a} \frac{f'(x)}{F'(x)}$$。

#### 定理2
设  
