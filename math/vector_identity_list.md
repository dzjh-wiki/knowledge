# 向量恒等式列表

----

## 前言
向量与其量值分别用粗体与斜体表示；例如，$$|\vec{A}| = A$$

## 三重积
  * $$\vec{A} \times (\vec{B} \times \vec{C}) = (\vec{C} \times \vec{B}) \times \vec{A} = \vec{B}(\vec{A} \cdot \vec{C}) - \vec{C}(\vec{A} \cdot \vec{B})$$
  * $$\vec{A} \cdot (\vec{B} \times \vec{C}) = \vec{B} \cdot (\vec{C} \times \vec{A}) = \vec{C} \cdot (\vec{A} \times \vec{B})$$

## 其他乘积
  * $$(\vec{A} \times \vec{B}) \cdot (\vec{A} \times \vec{B}) = A^2 B^2  - (\vec{A} \cdot \vec{B})^2 = \vec{B} \cdot (\vec{A} \times (\vec{B} \times \vec{A}))$$
  * $$(\vec{A} \times \vec{B}) \times (\vec{C} \times \vec{D}) = (\vec{A} \cdot \vec{B} \times \vec{D}) \vec{C} - (\vec{A} \cdot \vec{B} \times \vec{C}) \vec{D}$$

## 乘积定则
  * $$\nabla (fg) = f(\nabla g) + g(\nabla f)$$
  * $$\nabla (\vec{A} \cdot \vec{B}) = \vec{A} \times (\nabla \times \vec{B}) + \vec{B} \times (\nabla \times \vec{A}) + (\vec{A} \cdot \nabla)\vec{B} + (\vec{B} \cdot \nabla)\vec{A}$$
  * $$\nabla (\vec{A} \cdot \vec{B}) = (\vec{A} \times \nabla) \times \vec{B} + (\vec{B} \times \nabla) \times \vec{A} + \vec{A} (\nabla \cdot \vec{B}) + \vec{B} (\nabla \cdot \vec{A})$$
  * $$\nabla \cdot (f \vec{A}) = f(\nabla \cdot \vec{A}) + \vec{A}(\nabla f)$$
  * $$\nabla \cdot (\vec{A} \times \vec{B}) = \vec{B} \cdot (\nabla \times \vec{A}) - \vec{A} \cdot (\nabla \times \vec{B})$$
  * $$\nabla \times (f \vec{A}) = f(\nabla \times \vec{A}) + (\nabla f) \times \vec{A}$$
  * $$\nabla \times (\vec{A} \times \vec{B}) = (\vec{B} \cdot \nabla)\vec{A} - (\vec{A} \cdot \nabla) \vec{B} + \vec{A}(\nabla \cdot \vec{B}) - \vec{B} (\nabla \cdot \vec{A})$$
  * $$\nabla \times (\vec{A} \times \vec{B}) = \vec{A} \times (\nabla \times \vec{B}) - \vec{B} \times (\nabla \times \vec{A}) - (\vec{A} \times \nabla) \times \vec{B} + (\vec{B} \times \nabla) \times \vec{A}$$
  * $$\nabla (\frac{1}{|\vec{r} - \vec{r}'|}) = - \nabla ' (\frac{1}{|\vec{r} - \vec{r}'|}) = - \frac{\vec{r} - \vec{r}'}{|\vec{r} - \vec{r}'|^3}$$
  * $$\nabla^2 (\frac{1}{|\vec{r} - \vec{r}'|}) = - 4 \pi \delta(\vec{r} - \vec{r}')$$

## 二次微分
  * $$\nabla \cdot (\nabla \times \vec{A}) = 0$$
  * $$\nabla \times (\nabla f) = \vec{0}$$
  * $$\nabla^2 (\nabla \cdot \vec{A}) = \nabla \cdot (\nabla^2 \vec{A})$$
  * $$\nabla \times (\nabla \times \vec{A}) = \nabla (\nabla \cdot \vec{A}) - \nabla^2 \vec{A}$$
  
这里，$$\nabla^2 \vec{A}$$ 应被理解为对 $$\vec{A}$$ 的每个分量取拉普拉斯算子，即向量值函数的拉普拉斯算子。

## 积分
  * $$\oint_S \vec{A} \cdot d \vec{S} = \int_V (\nabla \cdot \vec{A})dV$$ （散度定理）
  * $$\oint_S \psi d \vec{S} = \int_V \nabla \psi dV$$
  * $$\oint_S (\hat{\vec{n}} \times \vec{A}) \cdot d \vec{S} = \int_V (\nabla \times \vec{A}) dV$$
  * $$\oint_C \vec{A} \cdot d \vec{l} = \int_S (\nabla \times \vec{A}) \cdot d\vec{S}$$ （斯托克斯定理）
  * $$\oint_C \psi d \vec{l} = \int_S (\hat{\vec{n}} \times \nabla \psi) dS$$

## 格林恒等式
### 格林第一恒等式
$$\int_U (\psi \nabla^2 \phi + \nabla \phi \cdot \nabla \psi) dV = \oint_{\partial U} \psi \frac{\partial \phi}{\partial n} dS$$

### 格林第二恒等式
$$\int_U (\psi \nabla^2 \phi - \phi \nabla^2 \psi) dV = \oint_{\partial U} (\psi \frac{\partial \phi}{\partial n} - \phi \frac{\partial \psi}{\partial n}) dS$$

### 格林第三恒等式
$$\psi (x) - \int_U [G(x, x') \nabla'^2 \psi(x')] dV' = \oint_{\partial U}[\psi(x') \frac{\partial G(x, x')}{\partial n'} - G(x, x') \frac{\partial \psi(x')}{\partial n'}] dS'$$