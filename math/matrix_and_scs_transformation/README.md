# 矩阵与空间坐标系变换

----

## 矩阵
  * 以下关于**矩阵**的内容，来自<strong>《工程数学——线性代数》（第五版）</strong>一书。

### 矩阵定义
  * $$由m \times n个数a_{ij}(i = 1, 2,\dots,m;j = 1, 2,\dots,n;)排成的m行n列的数表$$$$\begin{pmatrix}
  a_{11} & a_{12} & \dots & a_{1n} \\
  a_{21} & a_{22} & \dots & a_{2n} \\
  \vdots & \vdots &  & \vdots \\
  a_{m1} & a_{m2} & \dots & a_{mn}
  \end{pmatrix}$$$$称为m行n列矩阵，简称m \times n矩阵。$$

### 矩阵运算
#### 加、减
$$只有当两个矩阵是同型矩阵（同样是m \times n矩阵）时，才能进行加/减运算。$$
  * $$将两个矩阵中对应位置的数据进行加/减运算，得到新的m \times n矩阵。$$

$$A + B = \begin{pmatrix}
  a_{11} + b_{11} & a_{12} + b_{12} & \dots & a_{1n} + b_{1n} \\
  a_{21} + b_{21} & a_{22} + b_{22} & \dots & a_{2n} + b_{2n} \\
  \vdots & \vdots &  & \vdots \\
  a_{m1} + b_{m1} & a_{m2} + b_{m2} & \dots & a_{mn} + b_{mn}
  \end{pmatrix}$$

#### 乘法
#### 数与矩阵相乘
  * $$将数乘以m \times n矩阵中每一项数据，得到新的m \times n矩阵。$$
  
$$\lambda A = \begin{pmatrix}
  \lambda a_{11} & \lambda a_{12} & \dots & \lambda a_{1n} \\
  \lambda a_{21} & \lambda a_{22} & \dots & \lambda a_{2n} \\
  \vdots & \vdots &  & \vdots \\
  \lambda a_{m1} & \lambda a_{m2} & \dots & \lambda a_{mn}
  \end{pmatrix}$$

#### 矩阵与矩阵相乘
  * $$设A = (a_{ij})是一个m \times s矩阵，B = (b_{ij})是一个s \times n矩阵，那么规定矩阵A和矩阵B的乘积是一个m \times n矩阵C = (c_{ij})，其中$$$$c_{ij} = a_{i1}b_{1j} + a_{i2}b_{2j} + \dots + a_{is}b_{sj} = \sum_{k=1}^s a_{ik} b_{kj} \, (i=1,2,\dots,m;j=1,2,\dots,n)$$$$，记作C=AB。$$

#### 转置
  * $$将矩阵A的行换成同序数的列得到一个新矩阵，叫做A的转置矩阵，记作A^T。$$

$$A^T = \begin{pmatrix}
  a_{11} & a_{21} & \dots & a_{m1} \\
  a_{12} & a_{22} & \dots & a_{m2} \\
  \vdots & \vdots &  & \vdots \\
  a_{1n} & a_{2n} & \dots & a_{mn}
  \end{pmatrix}$$

#### 逆矩阵
##### 行列式
n个元素的任一排列中，当某两个元素的先后次序与标准次序（从小到大）不同时，就说有1个**逆序**。一个排列中所有逆序的总数叫做这个**排列的逆序数**。  

**n阶（n x n矩阵）行列式：**$$det(a_{ij}) = \begin{vmatrix}
  a_{11} & a_{12} & \dots & a_{1n} \\
  a_{21} & a_{22} & \dots & a_{2n} \\
  \vdots & \vdots &  & \vdots \\
  a_{n1} & a_{n2} & \dots & a_{nn}
  \end{vmatrix} = \sum (-1)^t a_{1 p_1} a_{2 p_2} \dots a_{n p_n}$$
$$其中p_1 p_2 \dots p_n为自然数1,2,\dots,n$$的**一个排列**，t为这个排列的**逆序数**。

##### 余子式
$$在n阶行列式中，把(i,j)元a_{ij}所在的第i行和第j行划去后，留下的n-1阶行列式叫做(i,j)元a_{ij}的【余子式】，记作：M_{ij}；并将A_{ij} = (-1)^{i+j}M_{ij}叫做(i,j)元a_{ij}的【代数余子式】。$$  

如四阶行列式$$D = \begin{vmatrix}
  a_{11} & a_{12} & a_{13} & a_{14} \\
  a_{21} & a_{22} & a_{23} & a_{24} \\
  a_{31} & a_{32} & a_{33} & a_{34} \\
  a_{41} & a_{42} & a_{43} & a_{44}
  \end{vmatrix}$$
$$中(3,2)元a_{32}$$的**余子式**和**代数余子式**分别为：
$$M_{32} = \begin{vmatrix}
  a_{11} & a_{13} & a_{14} \\
  a_{21} & a_{23} & a_{24} \\
  a_{41} & a_{43} & a_{44}
  \end{vmatrix}$$
$$A_{32} = (-1)^{3+2} M_{32} = -M_{32}$$

##### 伴随矩阵
$$行列式|A|的各个元素的代数余子式A_{ij}所构成的矩阵$$$$A^* = \begin{pmatrix}
  A_{11} & A_{21} & \dots & A_{n1} \\
  A_{12} & A_{22} & \dots & A_{n2} \\
  \vdots & \vdots &  & \vdots \\
  A_{1n} & A_{2n} & \dots & A_{nn}
  \end{pmatrix}$$
称为矩阵A的**伴随矩阵**。

##### 逆矩阵
对于n阶矩阵A，如果有一个n阶矩阵B，使$$AB = BA = E \, (E为n阶单位矩阵，即该矩阵的每个元素都是1)$$则说矩阵A是可逆的，并将矩阵B称为A的**逆矩阵**。

$$当行列式|A| \neq 0，则矩阵A可逆，且可得$$**逆矩阵**$$A^{-1} = \frac{1}{|A|} A^*$$，其中A是n阶矩阵。


### 空间坐标系变换
  * 以下关于**空间坐标系变换**的内容，来自<strong>《机器人学导论》（原书第三版）</strong>一书。

#### 坐标系映射变换
$${}^A\!P = {}_B^A\!T {}^B\!P$$$$，其中{}^A\!P表示空间某点相对于{A}（A坐标系）的描述，{}_B^A\!T表示变换矩阵，{}^B\!P表示空间某点相对于{B}（B坐标系）的描述。$$

**变换矩阵**$${}_B^A\!T = \begin{pmatrix}
    \begin{array}{c|c}
    {}_B^A\!R & {}^A\!P_{BORG} \\
    \hline
    0 \ \  0 \ \  0 & 1
    \end{array}
  \end{pmatrix}$$$$，其中，{}_B^A\!R是变换的旋转矩阵（{B}相对于{A}），{}^A\!P_{BORG}是{B}相对于{A}的矢量偏移。$$  

**旋转矩阵**$${}_B^A\!R = ({}^A\!\widehat{X}_B \ \  {}^A\!\widehat{Y}_B \ \  {}^A\!\widehat{Z}_B) = \begin{pmatrix}
    \widehat{X}_B \cdot \widehat{X}_A & \widehat{Y}_B \cdot \widehat{X}_A & \widehat{Z}_B \cdot \widehat{X}_A \\
    \widehat{X}_B \cdot \widehat{Y}_A & \widehat{Y}_B \cdot \widehat{Y}_A & \widehat{Z}_B \cdot \widehat{Y}_A \\
    \widehat{X}_B \cdot \widehat{Z}_A & \widehat{Y}_B \cdot \widehat{Z}_A & \widehat{Z}_B \cdot \widehat{Z}_A \\
  \end{pmatrix}$$$$，其中{}^A\!\widehat{X}_B、{}^A\!\widehat{Y}_B、{}^A\!\widehat{Z}_B表示{B}在{A}的X轴、Y轴、Z轴的单位方向上的投影。$$  

**变换矩阵的逆矩阵**为：$${}_A^B\!T = \begin{pmatrix}
    \begin{array}{c|c}
    {}_B^A\!R^T & -{}_B^A\!R^T \ {}^A\!P_{BORG} \\
    \hline
    0 \ \  0 \ \  0 & 1
    \end{array}
  \end{pmatrix}$$  

### 旋转矩阵
**一般情况下，若用一个旋转变换矩阵<strong style="color:red;">右乘</strong>一个坐标系的变换，那么产生的旋转是相对于<strong style="color:darkred;">前一个变换后的坐标系</strong>（即当前坐标系）的轴来说的。**  

**若用一个旋转变换矩阵<strong style="color:red;">左乘</strong>一个坐标系的变换，那么产生的旋转是相对于<strong style="color:darkred;">基坐标系</strong>（即一开始的参考坐标系）的轴来说的。**  

#### 角坐标系表示法
  * $$刚体绕X、Y、Z轴旋转\theta角的公式：$$
$$R_X(\theta) = \begin{pmatrix}
    1 & 0 & 0 \\
    0 & cos \theta & -sin \theta \\
    0 & sin \theta & cos \theta \\
  \end{pmatrix}$$

$$R_Y(\theta) = \begin{pmatrix}
    cos \theta & 0 & sin \theta \\
    0 & 1 & 0 \\
    -sin \theta & 0 & cos \theta \\
  \end{pmatrix}$$
  
$$R_Z(\theta) = \begin{pmatrix}
    cos \theta & -sin \theta & 0 \\
    sin \theta & cos \theta & 0 \\
    0 & 0 & 1 \\
  \end{pmatrix}$$

##### 表示法
旋转矩阵的角坐标系表示法，一共有二十四种（具体参考<strong>《机器人学导论》附录B</strong>）。  

这里只列举其中的三种表示法：
###### X-Y-Z固定角坐标系表示法
  * $$将{B}与已知的{A}重合。先将{B}绕\widehat{X}_A旋转\gamma角度（回转角），再绕\widehat{Y}_A旋转\beta角度（俯仰角），最后绕\widehat{Z}_A旋转\alpha角度（偏转角）。$$
  * **注意到的是：该方法的每次旋转都是绕着固定参考坐标系<strong style="color:red;">{A}</strong>的轴。**

$${}_B^A\!R_{XYZ}(\gamma, \beta, \alpha) = R_Z(\alpha) R_Y(\beta) R_X(\gamma)$$
$$= \begin{pmatrix}
    cos \alpha & -sin \alpha & 0 \\
    sin \alpha & cos \alpha & 0 \\
    0 & 0 & 1 \\
  \end{pmatrix} \cdot \begin{pmatrix}
    cos \beta & 0 & sin \beta \\
    0 & 1 & 0 \\
    -sin \beta & 0 & cos \beta \\
  \end{pmatrix} \cdot \begin{pmatrix}
    1 & 0 & 0 \\
    0 & cos \gamma & -sin \gamma \\
    0 & sin \gamma & cos \gamma \\
  \end{pmatrix}$$
$$= \begin{pmatrix}
    cos \alpha \cdot cos \beta  & cos \alpha \cdot sin \beta \cdot sin \gamma - sin \alpha \cdot cos \gamma  & cos \alpha \cdot sin \beta \cdot cos \gamma + sin \alpha \cdot sin \gamma \\
    sin \alpha \cdot cos \beta & sin \alpha \cdot sin \beta \cdot sin \gamma + cos \alpha \cdot cos \gamma & sin \alpha \cdot sin \beta \cdot cos \gamma - cos \alpha \cdot sin \gamma \\
    -sin \beta & cos \beta \cdot sin \gamma & cos \beta \cdot cos \gamma \\
  \end{pmatrix}$$


###### Z-Y-X欧拉角坐标系表示法
  * $$将{B}与已知的{A}重合。先将{B}绕\widehat{X}_B旋转\alpha角度，再绕\widehat{Y}_B旋转\beta角度，最后绕\widehat{Z}_B旋转\gamma角度。$$
  * **注意到的是：该方法的每次旋转都是绕着固定参考坐标系<strong style="color:red;">{B}</strong>的轴。**

经过推导可得：
$${}_B^A\!R_{Z'Y'X'}(\alpha, \beta, \gamma) = {}_B^A\!R_{XYZ}(\gamma, \beta, \alpha)$$


###### 等效轴角坐标系表示法
  * $$将{B}与已知的{A}重合。将{B}绕矢量{}^A\!\widehat{K}按右手定则旋转\theta角度。$$

轴坐标系表示法：  
$$R_K(\theta) = \begin{pmatrix}
    k_x \cdot k_x \cdot v \theta + c \theta & k_x \cdot k_y \cdot v \theta - k_z \cdot s \theta & k_x \cdot k_z \cdot v \theta + k_y \cdot s \theta \\
    k_x \cdot k_y \cdot v \theta + k_z \cdot s \theta & k_y \cdot k_y \cdot v \theta + c \theta & k_y \cdot k_z \cdot v \theta - k_x \cdot s \theta \\
    k_x \cdot k_z \cdot v \theta + k_y \cdot s \theta & k_y \cdot k_z \cdot v \theta + k_x \cdot s \theta & k_z \cdot k_z \cdot v \theta + c \theta \\
  \end{pmatrix}$$
  $$，其中，c \theta = cos \theta, s \theta = sin \theta, v \theta = 1 - cos \theta, {}^A\!\widehat{K} = \begin{pmatrix}
    k_x \\
    k_y \\
    k_z \\
  \end{pmatrix}。
  \theta的符号由右手定则确定，即大拇指指向{}^A\!\widehat{K}的正方向。$$

**轴角坐标系等效思路：**  
$$可先使{B}进行R=R_Z(\theta) R_Y(\theta)旋转后，对齐Z轴和矢量{}^A\!\widehat{K}的方向，然后根据【相似变换原理】，可得轴坐标系表示法和角坐标系表示法的关系：$$  
$$R_A(\theta) = R_Z(\theta) R_Y(\theta) R_Z(\theta) R_Y(-\theta) R_Z(-\theta)$$
$$，其中，首先将Z轴与{}^A\!\widehat{K}对齐【R_Z(\theta) R_Y(\theta)】，然后旋转Z轴【R_Z(\theta)】，最后**逆**Z轴与{}^A\!\widehat{K}对齐的旋转【R_Y(-\theta) R_Z(-\theta)】，即可使{B}坐标系与{A}坐标系重合。$$  

根据以上等式，可得：  
$$\begin{cases}
sin \alpha = \frac{k_y}{\sqrt{k_x^2 + k_y^2}}, \ \ cos \alpha = \frac{k_x}{\sqrt{k_x^2 + k_y^2}} \\
sin \beta = \sqrt{k_x^2 + k_y^2}, \ \ cos \beta = k_z
\end{cases}$$。