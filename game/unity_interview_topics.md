# Unity面试题

----

## 1. 协同程序（协程？）
  * 在主线程运行的同时开启另一段逻辑处理，来协助当前程序的执行。开启协同程序就是**开启一个（轻量级的）线程**。协程类似多线程，但不是多线程，Unity的协程是在每帧结束之后去检测yield的条件是否满足。
  * 多线程程序同时运行多个线程，而在任一指定时刻只有一个协程在运行，并且这个正在运行的协程只在必要时才被挂起。除主线程外的线程无法访问Unity3D的对象、组件、方法。
  * U3d没有多线程的概念，不过unity提供了StartCoroutine（协程）和LoadLevelAsync（异步加载关卡）后台加载场景的方法。
  * 在Unity3D中，使用StopCoroutine(string methodName)来终止一个协同程序，使用StopAllCoroutines()来终止所有可以终止的协同程序，但这两个方法都只能终止该MonoBehaviour中的协同程序。
  * 还有一种方法可以终止协同程序，即将协同程序所在gameobject的active属性设置为false，当再次设置active为ture时，协同程序并不会再开启；若是将协同程序所在脚本的enabled设置为false则不会生效。这是因为协同程序被开启后作为一个线程在运行，而MonoBehaviour也是一个线程，他们成为互不干扰的模块，除非代码中用调用，他们共同作用于同一个对象，只有当对象不可见才能同时终止这两个线程。
  * 协程是在主线程中去执行的。协程**不是线程(大概是因为机制不一样才这么说的吧)**，也不是异步执行的。协程和MonoBehaviour的Update函数一样也是在MainThread中执行的。使用协程你不用考虑同步和锁的问题。
  * 协程其实就是一个IEnumerator（迭代器），IEnumerator 接口有两个方法 Current 和 MoveNext() ，只有当MoveNext()返回true时才可以访问Current，否则会报错。迭代器方法运行到 yield return 语句时，会返回一个expression表达式并保留当前在代码中的位置。 当下次调用迭代器函数时执行从该位置重新启动。
  * Unity在每帧做的工作就是：调用协程（迭代器）MoveNext() 方法，如果返回 true ，就从当前位置继续往下执行。
  * 协程跟Update()其实一样的，都是Unity每帧对会去处理的函数（如果有的话）。如果MonoBehaviour 是处于激活（active）状态的而且yield的条件满足，就会执行协程方法的后面代码。

### 1.1 协程与线程的区别
  * 协同程序（coroutine）与多线程情况下的线程比较类似：有自己的堆栈，自己的局部变量，有自己的指令指针（IP，instruction pointer），但与其它协同程序共享全局变量等很多信息。
  * 协程(协同程序): 同一时间只能执行某个协程。开辟多个协程开销不大。协程适合对某任务进行分时处理。
  * 线程: 同一时间可以同时执行多个线程。开辟多条线程开销很大。线程适合多任务同时处理。
  * A.协程，即协作式程序，其思想是，一系列互相依赖的协程间依次使用CPU，每次只有一个协程工作，而其他协程处于休眠状态。协程实际上是在一个线程中，只不过每个协程对CUP进行分时，协程可以访问和使用unity的所有方法和component。
  * B.线程，多线程是阻塞式的，每个IO都必须开启一个新的线程，但是对于多CPU的系统应该使用thread，尤其是有大量数据运算的时刻，但是IO密集型就不适合；而且thread中不能操作unity的很多方法和component。
  * 线程和协同程序的主要不同在于：在多处理器情况下，从概念上来讲多线程程序同时运行多个线程；而协同程序是通过协作来完成，在任一指定时刻只有一个协同程序在运行，并且这个正在运行的协同程序只在必要时才会被挂起。

## 2. U3d碰撞器和触发器的区别
  * 碰撞器是触发器的载体，而触发器只是碰撞器身上的一个属性。当`Is Trigger=false`时，碰撞器根据物理引擎引发碰撞，产生碰撞的效果，可以调用`OnCollisionEnter/Stay/Exit`函数；当`Is Trigger=true`时，碰撞器被物理引擎所忽略，没有碰撞效果，可以调用`OnCollisionEnter/Stay/Exit`函数。如果既要检测到物体的接触又不想让碰撞检测影响物体移动或要检测一个物体是否经过空间中的某个区域，这时就需用到触发器。

## 3. 物体发生碰撞的必要条件
  * 两个物体都必须带有碰撞器（Collider），其中一个物体还必须带有Rigidbody刚体，而且必须是运动的物体带有Rigidbody脚本才能检测到碰撞。

## 4. ArrayList和List的主要区别
  * ArrayList存在不安全类型（ArrayList会把所有插入其中的数据都当成Object来处理），装箱拆箱的操作（费时）。List是ArrayList类的泛型等效类，在声明List集合时，我们同时需要为其声明List集合内数据的对象类型。

## 5. OnEnable、Awake、Start运行时发生的顺序，及哪些可在同一个对象周期中反复的发生
  * Awake->OnEnable->Start，OnEnable在同一周期中可以反复地发生。

## 6. 游戏对象消失的三种方法
（`enabled/Destroy/active`）
  * gameObject.renderer.enabled=false是控制一个物体是否在屏幕上渲染或显示，而物体实际还是存在的，只是相当于隐身，而物体本身的碰撞体还依然存在。
  * GameObject.Destroy()表示移除物体的内存上的组件，代表销毁该物体。实际上该物体的内存并没有立即释放，而是在当前帧结束的时候再把物体销毁。想要立即销毁，可调用DestroyImmediate()函数。
  * gameObject.active=false表示是否在场景中停用该物体，同时在场景中find不到该物体。如果该物体有子物体，要用SetActiveRecursively(false)来递归停用该物体。（会导致GameObject.Find方法查找不到）

## 7. MeshRender中material和sharedmaterial的区别
  * 修改sharedMaterial将改变所有物体使用这个材质的外观，并且也改变存储在工程里的材质设置。不推荐修改由sharedMaterial返回的材质。如果想修改渲染器的材质，使用material替代。

## 8. Unity提供的光源
  * 平行光：`Directional Light`
  * 点光源：`Point Light`
  * 聚光灯：`Spot Light`
  * 区域光源：`Area Light`

## 9. 动态加载资源的方式
`Resources.Load()`; `Resources.LoadAll()`;  
`AssetBundle`;
  * Resources.Load 资源必须放在Resources文件夹，只能加载本地文件。
  * AssetBundle和WWW一起使用，可实现从服务器上下载资源，并动态加载。

### 9.1 Unity资源加载的方式
  * instantiate：最简单的一种方式，以实例化的方式动态生成一个物体。
  * Assetbundle：将资源打成asset bundle放在服务器或本地磁盘，通过WWW模块get下来，然后从这个bundle中load某个object。Unity官方推荐也是绝大多数商业化项目使用的一种方式。
  * AssetDatabase.loadasset：这种方式只在editor范围内有效，游戏运行时没有此函数，其通常是在开发中调试用的。
  * Resource.Load：可直接load并返回某个类型的Object，前提是要把这个资源放在Resources命名的文件夹下，Unity不管有没有场景引用，都会将其全部打入安装包中。

### 9.2 AssetBundle
  * AssetBundle的管理总共分为三部分：A. AssetBundle文件； B. AssetBundle内存镜像； C. AssetBundle内存对象实例。

AssetBundle进行内存释放的方式：  
从某个AssetBundle中Load一个prefab并克隆【obj=instantiate(AssetBundle0.Load('testPrefab'))】，之后使用Destroy并不能释放掉该对象，而应在Destroy后调用AssetBundle0.Unload(true)，才能释放干净。  
若这个AssetBundle0是要反复读取的，不方便Unload，可在Destroy后用AssetBundle0.UnloadUnusedAssets()将所有相关的Assets都销毁。

## 10. 使用U3d实现2D游戏的方式
  * 使用本身的GUI；
  * 将摄像机的Projection（投影）值调为Orthographic（正交投影），不考虑z轴；
  * 使用2d插件，如2DToolKit、Uni2D；
  * Unity4.3版本新增的`Native 2D`；

## 11. CharacterController和Rigidbody的区别
  * Rigidbody具有完全真实物理的特性，Unity物理系统最基本的一个组件，包含了常用的物理特性；
  * CharacterController可以说是受限的Rigidbody，具有一定的物理效果但不是完全真实的，是Unity为了使开发者能方便开发第一人称视角的游戏而封装的一个组件。

## 12. prefab的用处
 * 在游戏运行时实例化，prefab相当于一个模板，对已有的素材、脚本、参数做一个默认的配置，以便于以后的修改，同时prefab打包的内容简化了导出的操作，便于团队的交流。

## 13. sealed关键字用于类声明和函数声明时的作用
  * sealed修饰的类成为密封类，类声明时可防止其他类继承此类，在方法中声明则可防止派生类重写此方法。
  * String是sealed类。

## 14. 在物体发生碰撞的整个过程中，对应阶段的函数
  * 三个阶段：OnCollisionEnter -> OnCollisionStay -> OnCollisionExit

## 15. Unity3d脚本从唤醒到销毁的重要方法
  * Awake -> OnEnable -> Start -> Update -> FixedUpdate -> LateUpdate -> OnGUI -> OnDisable -> OnDestroy

## 16. 物理更新一般放在FixedUpdate函数中
  * FixedUpdate，每固定帧绘制时执行一次，和Update不同的是FixedUpdate是渲染帧执行，若渲染效率低下的时候，FixedUpdate调用次数就会跟着下降。
  * FixedUpdate比较适用于物理引擎的计算，因为是跟每帧渲染有关。Update比较适合做控制。

## 17. LOD(Level of detail)
  * 多层次细节，是最常用的游戏优化技术。
  * 其按照模型的位置和重要程度决定物体渲染的资源分配，降低非重要物体的面数和细节度，从而获得高效率的渲染运算。缺点是增加了内存。

## 18. MipMap的作用
  * MinMapping：在三维计算机图形的贴图渲染中有常用的技术，为加快渲染进度和减少图像锯齿，贴图被处理成有一系列被预先计算和优化过的图片组成的文件，这样的贴图被称为MipMap。

## 19. 向量的点乘、叉乘及归一化的意义
  * 点乘描述了两个向量的相似程度，结果越大两向量月相似，还能用于表示投影；
  * 叉乘得到的向量垂直于原来的两个向量；
  * 标准化向量：用在之关系方向，不关心大小的时候。

## 20. 在移动设备上寻求U3D原生GUI的替代方案的原因
  * 不美观，且OnGUI费时、效率不高，且使用不便。

## 21. LightMap
  * LightMap：指在三维软件里实现打好光，然后渲染吧场景各表面的光照输出到贴图上，最后又通过引擎贴到场景上，从而使得物体有光照感。

## 22. ref和out参数
  * ref和out参数的效果一样，都是通过关键字找到定义在主函数里的变量的内存地址，并通过方法体内的语法改变其大小。
  * 不同点是输出参数必须对参数进行初始化。ref必须初始化，out必须在函数内赋值。ref参数是引用，out参数为输出参数。

## 23. 射线检测碰撞物的原理
  * 射线是3D世界中一个点向一个方向发射的一条无终点的线，在发射轨迹中与其他物体发生碰撞时，其将停止发射。

## 24. GC原因及避免方法
  * 原因：堆上产生垃圾；
  * 避免方法：A. 减少new产生对象的次数；B. 使用公用的对象（静态成员）；C. 将String换位StringBuilder。

## 25. 反射的实现原理
在运行时根据程序集及其中的类型得到元数据。  
实现步骤：  
  * 导入using System.Reflection；
  * Assembly.Load("程序集")加载程序集，返回类型是一个Assembly；
  * Type type = assembly.GetType("程序集.类名");获取当前类的类型；
  * Activator.CreateInstance(type);创建此类型实例；
  * MethodInfo mInfo = type.GetMethod("方法名");获取当前方法；
  * mInfo.Invoke(null, 方法参数);

## 26. 移动相机动作的实现位置
  * 在LateUpdate函数中实现，确保所有update操作完成，避免出现视觉中还未有角色的空帧现象。

## 27. 渲染管道
指在显示器上为了显示出图像而经过的一系列必要操作。  
其主要步骤有：  
  * 本地坐标 -> 视图坐标 -> 背面裁剪 -> 光照 -> 裁剪 -> 投影 -> 视图变换 -> 光栅化

## 28. 优化内存
  * 压缩自带类库；
  * 将暂时不用的而以后还需用的物体隐藏起来，而不是直接Destroy；
  * 释放AssetBundle占用的资源；
  * 降低模型的片面数，降低模型的骨骼数量，降低贴图的大小；
  * 使用光照贴图，使用LOD，使用Shader，使用Prefab；
  * 减少代码中产生的临时变量。

## 29. 在移动设备上优化资源的方法
  * 使用AssetBundle，实现资源分离和共享，可实现资源的在线更新；
  * 降低顶点数；
  * 只使用一盏动态光，不使用阴影，不使用光照探头；
  * 裁剪粒子系统、合并同时出现的粒子系统、自行实现轻量级的粒子系统；（粒子系统是cpu的主要消耗）
  * 将不需要跟骨骼动画和动作过渡的地方全部使用animation，控制骨骼数量在30根以下；
  * animator出视野不更新；
  * 删除无意义的animator；
  * animator的初始化很耗时；
  * 除主角外都不要跟骨骼运动`apply root motion`；
  * 禁止掉不带刚体待包围盒的物体（`static collider`）运动；
  * 每帧递归的计算fanalalpha改为只有初始化和变动时计算；
  * 去掉法线计算；
  * 不要每帧计算viewsize和windowssize；
  * filldrawcall时构建顶点缓存使用array.copy；
  * 尽量减少`smooth grop`

## 30. Camera组件的ClearFlags选项
  * 当勾选为Deapth Only，那么摄像机只会渲染看得见的对象，将背景完全透明。这种情况一般用于两个摄像机以上的场景。

## 31. GameObject设置为Static的作用
  * 设置为游戏对象为Static时，这些部分被静态物体挡住而不可见时，将会剔除（或禁用）网格对象。【场景中不会动的物体都应该标记为Static】。

## 32. 1个Terrain贴任意张四标贴图时的渲染速度
  * 没有区别。【任意张贴图搜只渲染一次】

## 33. DrawCall
  * Unity中，每次引擎准备数据并通知GPU的过程称为一次DrawwCall。
  * DrawCall越高对显卡的消耗就越大。

降低DrawCall的方法：  
  * Dynamic Batching;
  * Static Batching;
  * 高级特效Shader降级为统一的低级特性的Shader。
  * 对象池的使用？

## 34. 实时点光源的优缺点
  * 优：可以有cookies——带有alpha通道的立方图（cubemap）纹理。
  * 缺：耗费资源。

## 35. Unity的Shader中，Blend SrcAlpha OneMinusSrcAlpha的含义
  * 作用为Alpha混合。公式：最终颜色=源颜色*源透明值+目标颜色*(1-源透明值)。

## 36. 导航网格
  * NavMesh是3D游戏世界中用于实现动态物体自动寻路的一种技术，将游戏中复杂的结构组织关系简化为带有一定信息的网格，在这些网格的基础上通过一系列的计算来实现自动寻路。
  * 导航时，只需要给导航物体挂载导航组件，导航物体便会自行根据目标点来寻找最直接的路线，并沿着该路线到达目标点。

## 37. 光照贴图（Lightmapping）
  * Lightmapping是一种增强静态场景光照效果的技术，较少的性能消耗是静态场景看上去更真实、丰富以及更具有立体感。
  * Unity使用Autodesk的Beast插件。
使用光照贴图的优点：  
  * 使用光照贴图比使用实时光源渲染要快；
  * 可以降低游戏内存消耗；
  * 多个物体可以使用同一张光照贴图。

## 38. 后期屏幕渲染
U3D的图像特效都在OnRenderImage()函数中，在图像到达颜色缓存进行显示交换前进行的图像处理，任何附加在摄像机上的`Image Effects`脚本都可以通过编辑器代码来修改特效的效果。  
**所有的后期屏幕特效只有pro版本才支持！！！**  
使用方式：将`Image Effects`的脚本组件添加到摄像机上。
  * `Antialiasing(Fullscreen)`脚本抗锯齿全屏特效，NFAA、FXAA3、FXAA2、DLAA较快。具体有边缘阀值、模糊半径、清晰度等设置。
  * `Bloom`脚本泛光特效，是增强版的辉光（类似高光耀斑）、光晕（光晕和模糊）效果。调节泛光的质量等级、模式、混合模式（屏幕是两张图像分别处理，Add叠加模式光晕会更亮）、HDR高动态光照渲染开关、泛光强度、阀值、`Blur iterations`模糊迭代效果。Mask光晕遮蔽图。
  * `Bloom and Lens Flares`泛光和镜头炫光效果。泛光效果下增加镜头炫光（耀斑效果），和Bloom效果类似，只是是3.5版本的。
  * `Blur`脚本模糊特效，迭代次数越多越模糊，模糊半径，模糊shader的选择。
  * `Camera Motion Blur`基于摄像机的运动模糊特效，模拟物体相对于摄像机作快速移动时产生的模糊效果。有运动模糊算法的选择，速度尺度、最小速度、摄像机移动、排除不受模糊计算影响的层、抖动采样设置、最大采样数设置。
  * `Color Correction`色彩校正特效，有曲线类型、渐变纹理类型。
  * `Contrast Enhance`对比度增强特效。使用图像处理领域中非锐化遮蔽图（`unsharp mask`）方式来达到增强对比度的效果，使得图像明亮的更加明亮，暗淡的更加暗淡。
  * `Contrast Stretch`对比度拉伸特效。实现从黑暗空间中到明亮空间时产生的眩目感。
  * `Crease`褶皱特效。使得边缘更加明显粗糙，类似手绘效果。
  * `Depth of field`景深特效。使得边缘朦胧，或者物体朦胧，产生视觉上景深效果。
  * `Tilt Shift`移轴特效。是景深特效的特殊版本，使得闪焦区域的过度更加光滑。
  * `Edge Detection`边缘检测特效（分为颜色和几何）。类似手绘边缘特效。
  * `FishEye`鱼眼效果。
  * `Global Fog`全局雾效果。
  * `Glow`发光效果。
  * `Grayscale`灰度特效。
  * `Motion Blur(Color Accumulation)`运动模糊特效。
  * `Noise`噪波图像特效，老式电影和电视的“哗哗”效果。
  * `Sepia Tone`棕褐色效果。
  * `Screen Space Ambient Occlusion`屏幕空间环境遮挡（SSAO）特效。
  * `SunShafts`阳光射线特效。阳光射线特效用于模拟亮度很高的光源被物体遮挡时所产生的径向光线散射效果，合理运用该特效能有效地提升游戏画面的真实感。
  * `Tonemapping`色调映射特效。该特效只有在摄像机对象启用HDR模式时才能正常工作，可以增加视觉方向的光照细节表现，模拟人眼适应环境明暗交替效果，可以和Bloom配合得到更好效果。
  * `Twirl`扭曲特效。Vortex旋涡图像特效类似扭曲特效。
  * `Vignettinggand Chromatic Aberration`渐晕和色差特效，也有模糊效果。

## 39. 遮挡剔除
  * 该功能可在对象因被其他物体遮挡，当前在相机中无法看到时，禁用对象渲染。该功能不会再三维计算机图形中自动开启，因为在大部分情况下，离相机最远的对象最先渲染，离相机近的对象覆盖先前的物体（该步骤称为“重复渲染（overdraw）”）。遮挡剔除（`Occlusion Culling`）与视锥体（`Frustum Culling`）不同。视锥体剔除只禁用相机视野外的对象渲染，不禁用视野中倍遮挡的任何物体的渲染。注意，使用遮挡剔除功能时，仍将受益于视锥体剔除。

## 40. 常用插件
  * `NGUI`、`2D Toolkit`、`iTween`、`PlayMaker`、特效插件`FxMaker`、`NGJMinMap`（小地图）等
  * 界面制作：`NGUI`
  * 2D游戏制作：`2D Toolkit`
  * 可视化编程：`PlayMaker`
  * 插值插件：`iTween`、`HOTween`
  * 路径搜寻：`Simple Path`