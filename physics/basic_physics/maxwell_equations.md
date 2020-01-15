# 麦克斯韦方程组

  * 描述电场和磁场的方程。

----

## 基础定律及方程

### 洛伦兹力
#### 表达式
$$\vec{F} = q (\vec{E} + \vec{v} \times \vec{B})$$
其中，$$\vec{E}$$表示电场，$$\vec{B}$$表示磁场，q是带电粒子的电荷量，v是带电粒子的速度。

#### 积分形式
$$F = \int_V (\rho \vec{E} + \vec{J} \times \vec{B}) d\tau$$
其中，V是积分的体积，$$\rho$$是电荷密度，J是电流密度，$$d\tau$$是微小体元素。

#### 单位体积的洛伦兹力
即，洛伦兹力密度$$\vec{f}$$
$$\vec{f} = \rho \vec{E} + \rho \vec{v} \times \vec{B} =  \rho \vec{E} + \vec{J} \times \vec{B}$$

### 库仑定律
两个电荷之间的库仑力。
$$\vec{F} = \frac{1}{4 \pi \epsilon_0} \frac{Q_1 Q_2}{r^2} \vec{r}$$
其中，Q是电荷，r是电荷之间的距离，$$\vec{r}$$是表示方向的单位向量。

### 毕奥-萨伐尔定律
适用于计算一个稳定电流所产生的磁场。
$$\vec{B}(\vec{r}) = \frac{\mu_0 I}{4 \pi}\int_{L'} dl' \times \frac{\vec{r} - \vec{r}'}{|\vec{r} - \vec{r}'|^3}$$
其中，$$d^3 r'$$为微小体积元素，$$V'$$是积分的体积。

### 高斯定律
穿越出任意闭合曲面的净电通量等于该闭合曲面内的净电荷除以电容率。该闭合曲面称为高斯曲面。  
  * 积分形式$$\oint_{\partial V} \vec{E} \cdot d \vec{a} = \frac{Q_V}{\epsilon_0}$$
  * 微分形式$$\nabla \cdot \vec{E} = \frac{\rho}{\epsilon_0}$$

### 安培定律
  * 积分形式$$\oint_{\partial S} \vec{B} \cdot d \vec{l} = \mu_0 I_S$$
  * 微分形式$$\nabla \times \vec{B} = \mu_0 \vec{J}$$

#### 安培-麦克斯韦定律
除了电流以外，**变化的电场**也可以产生磁场。  
变换电场产生的磁场为：  
  * 积分形式$$\oint_{\partial S} \vec{B} \cdot d \vec{l} = \mu_0 \epsilon_0 \frac{d}{dt} \int_S \vec{E} \cdot d \vec{a}$$
  * 微分形式$$\nabla \times \vec{B} = \mu_0 \epsilon_0 \frac{\partial}{\partial t} \vec{E}$$

### 法拉第定律
任何封闭电路中感应电动势的大小，等于穿过这一电路磁通量的变化率。
$$\varepsilon = - \frac{d \Phi_B}{dt}$$
其中，$$\varepsilon$$ 是电动势，单位为伏特。
$$\Phi_B$$是通过电路的磁通量，单位为韦伯。
  
变化的磁场是可以产生电场:
  * 积分形式$$\oint_{\partial S} \vec{E} \cdot d \vec{l} = - \frac{d}{dt} \int_S \vec{B} \cdot d \vec{a}$$
  * 微分形式$$\nabla \times \vec{E} = - \frac{\partial}{\partial t} \vec{B}$$


## 麦克斯韦方程组
这里$$\vec{E}$$表示电场，$$\vec{B}$$表示磁场，$$\mu_0$$和$$\epsilon_0 $$为常数。  
积分形式中Q是电荷，I是电流，V表示一块体积，$$\partial V$$表示其表面积，而S表示一块曲面，$$\partial S$$表示其边缘。  
微分形式中$$\rho$$是电荷密度（电荷/体积），$$\vec{J}$$是电流密度（电流/面积），$$\nabla \cdot$$和$$\nabla \times$$是两个不同的算符，基本可以理解为对向量的某种微分。

### 积分形式
  * (1) 【高斯定律：电场$$\vec{E}$$在闭合曲面$$\partial V$$上的通量，等于该曲面包裹住的体积$$V$$内的电荷$$Q$$（乘上系数$$\frac{1}{\epsilon_0}$$）】$$\oint_{\partial V} \vec{E} \cdot d \vec{a} = \frac{Q_V}{\epsilon_0}$$
  * (2) 【法拉第定律：电场$$\vec{E}$$在闭合曲线$$\partial S$$上的环量，等于磁场$$\vec{B}$$在该曲线环住的曲面$$S$$上的通量的变化率（乘上系数-1）】$$\oint_{\partial S} \vec{E} \cdot d \vec{l} = - \frac{d}{dt} \int_S \vec{B} \cdot d \vec{a}$$
  * (3) 【高斯磁定律：磁场$$\vec{B}$$在闭合曲面$$\partial V$$上的通量，等于0】$$\oint_{\partial V} \vec{B} \cdot d \vec{a} = 0$$
  * (4) 【安培麦克斯韦定律：磁场$$\vec{B}$$在闭合曲线$$\partial S$$上的环量，等于该曲线环住的曲面$$S$$里的电流$$I$$（乘上系数$$\mu_0$$），加上电场$$\vec{E}$$在该曲线环住的曲面$$S$$上的通量的变化率（乘上系数$$\mu_0 \epsilon_0$$）】$$\oint_{\partial S} \vec{B} \cdot d \vec{l} = \mu_0 I_s + \mu_0 \epsilon_0 \frac{d}{dt} \int_S \vec{E} \cdot d \vec{a}$$

### 微分形式
  * (1) 【高斯定律：电场$$\vec{E}$$的散度，等于在该点的电荷密度\rho（乘上系数$$\frac{1}{\epsilon_0}$$）】$$\nabla \cdot \vec{E} = \frac{\rho}{\epsilon_0}$$
  * (2) 【法拉第定律：电场$$\vec{E}$$的旋度，等于在该点的磁场$$\vec{B}$$的变化率（乘上系数-1）】$$\nabla \times \vec{E} = - \frac{\partial}{\partial t} \vec{B}$$
  * (3) 【高斯磁定律：磁场$$\vec{B}$$的散度，等于0】$$\nabla \cdot \vec{B} = 0$$
  * (4) 【安培麦克斯韦定律：磁场$$\vec{B}$$的旋度，等于在该点的电流密度$$\vec{J}$$（乘上系数$$\mu_0$$），加上在该点的电场$$\vec{E}$$的变化率（乘上系数$$\mu_0 \epsilon_0$$）】$$\nabla \times \vec{B} = \mu_0 \vec{J} + \mu_0 \epsilon_0 \frac{\partial}{\partial t} \vec{E}$$