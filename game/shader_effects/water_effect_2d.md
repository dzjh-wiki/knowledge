# 2D水体效果
  * 参考[https://github.com/tutsplus/unity-2d-water-effect](https://github.com/tutsplus/unity-2d-water-effect)
----
基于线性渲染器（Line Renderer）、网格渲染器（Mesh Renderer），触发器（Trigger）以及粒子来模拟水体效果。


```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class WaterManager : MonoBehaviour {
    // 所有节点的位置、速度及加速度
    float[] xpositions;
    float[] ypositions;
    float[] velocities;
    float[] accelerations;
    // LineRenderer用于保存所有节点及水体的轮廓
    LineRenderer Body;
    // 使用网格来实现水体，并创建游戏对象来使用这些网格
    GameObject[] meshobjects;
    Mesh[] meshes;
    // 为每个游戏对象添加碰撞器
    GameObject[] colliders;

    // 水流速度、衰减度及传播速度
    const float springconstant = 0.02f;
    const float damping = 0.04f;
    const float spread = 0.05f;
    const float z = -1f; // 水体的显示层次，这里设为-1表示会显示在对象前面

    // 水的维度
    float baseheight;
    float left;
    float bottom;

    // 水波四溅效果的粒子系统
    public GameObject splash;

    // 用于Line Renderer的材质
    public Material mat;

    // 用于模拟水体的网格
    public GameObject watermesh

    void Start() {
        //Spawning our water
        SpawnWater(-10,20,0,-3);
    }

    // 在游戏过程中生成水体
    // 参数分别为水体四周的边长
    public void SpawnWater(float Left, float Width, float Top, float Bottom) {
        // 边和节点数量（对每单位宽度的水体使用5个节点，让整个水体运动看起来更平滑）
        int edgecount = Mathf.RoundToInt(Width) * 5;
        int nodecount = edgecount + 1;

        // 使用LineRenderer组件来渲染水体
        Body = gameObject.AddComponent<LineRenderer>();
        Body.material = mat;
        Body.material.renderQueue = 1000; // 通过渲染队列将材质的渲染顺序设为比水体更高
        Body.SetVertexCount(nodecount);
        Body.SetWidth(0.1f, 0.1f);

        // 初始化前面声明的变量
        xpositions = new float[nodecount];
        ypositions = new float[nodecount];
        velocities = new float[nodecount];
        accelerations = new float[nodecount];
        
        meshobjects = new GameObject[edgecount];
        meshes = new Mesh[edgecount];
        colliders = new GameObject[edgecount];
        
        baseheight = Top;
        bottom = Bottom;
        left = Left;

        // 按节点顺序为各数组赋值。这里将所有的y坐标设为水体上方，让水体各部分紧密排列。速度和加速度都为0表示水体是静止的
        for (int i = 0; i < nodecount; i++) {
            ypositions[i] = Top;
            xpositions[i] = Left + Width * i / edgecount;
            accelerations[i] = 0;
            velocities[i] = 0;
            Body.SetPosition(i, new Vector3(xpositions[i], ypositions[i], z));
        }

        for (int i = 0; i < edgecount; i++) {
            // 使用网格来实现水体
            meshes[i] = new Mesh();
            // 数组的四个元素按顺序分别表示左上角、右上角、左下角和右下角的顶点位置
            Vector3[] Vertices = new Vector3[4];
            Vertices[0] = new Vector3(xpositions[i], ypositions[i], z);
            Vertices[1] = new Vector3(xpositions[i + 1], ypositions[i + 1], z);
            Vertices[2] = new Vector3(xpositions[i], bottom, z);
            Vertices[3] = new Vector3(xpositions[i+1], bottom, z);
            // 网格所需的第二个数据就是UV坐标。UV坐标决定了网格用到的纹理部分。这里简单的使用纹理左上角、右上角、左下角及右下角的部分作为网格显示内容
            Vector2[] UVs = new Vector2[4];
            UVs[0] = new Vector2(0, 1);
            UVs[1] = new Vector2(1, 1);
            UVs[2] = new Vector2(0, 0);
            UVs[3] = new Vector2(1, 0);
            // 网格是由三角形组成的，而一个四边形可由两个三角形组成。按节点顺序，三角形A由节点0、1、3组成，三角形B由节点3、2、0组成
            int[] tris = new int[6] { 0, 1, 3, 3, 2, 0 };
            // 设置网格数据
            meshes[i].vertices = Vertices;
            meshes[i].uv = UVs;
            meshes[i].triangles = tris;
            // 添加游戏对象将其渲染到场景中。利用工程中的watermesh预制创建游戏对象，其中包含Mesh Renderer和Mesh Filter 组件
            meshobjects[i] = Instantiate(watermesh,Vector3.zero,Quaternion.identity) as GameObject;
            meshobjects[i].GetComponent<MeshFilter>().mesh = meshes[i];
            meshobjects[i].transform.parent = transform;

            // 添加盒状碰撞器并统一命名以便于管理，同样将其设为管理器子对象。将碰撞器坐标设为节点中间，设置好大小并添加WaterDetector类
            colliders[i] = new GameObject();
            colliders[i].name = "Trigger";
            colliders[i].AddComponent<BoxCollider2D>();
            colliders[i].transform.parent = transform;
            colliders[i].transform.position = new Vector3(Left + Width * (i + 0.5f) / edgecount, Top - 0.5f, 0);
            colliders[i].transform.localScale = new Vector3(Width / edgecount, 1, 1);
            // 给碰撞盒添加中心和尺寸，用来实现让物体在水中漂流
            // gameObject.GetComponent<BoxCollider2D>().center = new Vector2(Left + Width / 2, (Top + Bottom) / 2);
            // gameObject.GetComponent<BoxCollider2D>().size = new Vector2(Width, Top - Bottom);
            colliders[i].GetComponent<BoxCollider2D>().isTrigger = true;
            colliders[i].AddComponent<WaterDetector>();
    }

    // 控制水体网格的移动
    void UpdateMeshes() {
        for (int i = 0; i < meshes.Length; i++)
        {
 
            Vector3[] Vertices = new Vector3[4];
            Vertices[0] = new Vector3(xpositions[i], ypositions[i], z);
            Vertices[1] = new Vector3(xpositions[i+1], ypositions[i+1], z);
            Vertices[2] = new Vector3(xpositions[i], bottom, z);
            Vertices[3] = new Vector3(xpositions[i+1], bottom, z);
 
            meshes[i].vertices = Vertices;
        }
    }

    // 在FixedUpdate()函数中添加物理特性让水体可以自行流动
    void FixedUpdate() {
        // 结合胡克定律和欧拉方法获取水体新的坐标、加速度及速度
        for (int i = 0; i < xpositions.Length ; i++) {
            // 添加一个与速度成比例的阻尼因子形成水面的阻力
            float force = springconstant * (ypositions[i] - baseheight) + velocities[i]*damping ;
            accelerations[i] = -force;
            ypositions[i] += velocities[i];
            velocities[i] += accelerations[i];
            Body.SetPosition(i, new Vector3(xpositions[i], ypositions[i], z));
        }

        // 实现水浪的传播效果【对于每个节点，都要对比前一个节点与当前节点的高度差并将差值存入leftDeltas】
        float[] leftDeltas = new float[xpositions.Length];
        float[] rightDeltas = new float[xpositions.Length];

        // 比较后一个节点与当前节点的高度差并将差值存入rightDeltas。还需将所有的差值乘以传播速度常量
        for (int j = 0; j < 8; j++) { // 这里将以下代码放在一个循环，共运行八次。这样做的目的是希望多次运行但计算量小，而非计算量过大从而导致效果不够流畅
            for (int i = 0; i < xpositions.Length; i++) {
                if (i > 0)
                {
                    leftDeltas[i] = spread * (ypositions[i] - ypositions[i-1]);
                    velocities[i - 1] += leftDeltas[i];
                }
                if (i < xpositions.Length - 1)
                {
                    rightDeltas[i] = spread * (ypositions[i] - ypositions[i + 1]);
                    velocities[i + 1] += rightDeltas[i];
                }
            }
            // 根据高度差立即改变速度，但此时只需保存坐标差即可。如果立即改变第一个节点的坐标，同时再去计算第二个节点时第一个坐标已经移动了，这样会影响到后面所有节点的计算
            for (int i = 0; i < xpositions.Length; i++) {
                if (i > 0) 
                {
                    ypositions[i-1] += leftDeltas[i];
                }
                if (i < xpositions.Length - 1) 
                {
                    ypositions[i + 1] += rightDeltas[i];
                }
            }
        }
    }

    // 水波飞溅的效果【检测水波的x坐标及入水物体接触水面时的速度】
    public void Splash(float xpos, float velocity) {
        // 确定水波飞溅的位置是在水体范围内
        if (xpos >= xpositions[0] && xpos <= xpositions[xpositions.Length-1]) {
            // 改变水波的x坐标以获取飞溅位置与水体起始位置间的相对坐标
            xpos -= xpositions[0];
            // 找到落水物体碰撞的节点【先获取飞溅位置与水体左边界的坐标差（xpos），后将该差值除以水体宽度，将飞溅发生位置的分数乘以边数后取整，就得到了离飞溅位置最近的节点索引】
            int index = Mathf.RoundToInt((xpositions.Length-1)*(xpos / (xpositions[xpositions.Length-1] - xpositions[0])));
            // 将入水物体的速度赋给该物体所碰撞的节点，这样节点会被物体压入水体。
            velocities[index] = velocity;

            // 设置飞溅的参数，这个参数是受撞击物体的速度影响的
            float lifetime = 0.93f + Mathf.Abs(velocity)*0.07f;
            splash.GetComponent<ParticleSystem>().startSpeed = 8+2*Mathf.Pow(Mathf.Abs(velocity),0.5f);
            splash.GetComponent<ParticleSystem>().startSpeed = 9 + 2 * Mathf.Pow(Mathf.Abs(velocity), 0.5f);
            splash.GetComponent<ParticleSystem>().startLifetime = lifetime;

            // 水体是室内游泳池就需要添加以下代码
            Vector3 position = new Vector3(xpositions[index],ypositions[index]-0.35f,5);
            Quaternion rotation = Quaternion.LookRotation(new Vector3(xpositions[Mathf.FloorToInt(xpositions.Length / 2)], baseheight + 8, 5) - position);
            // 最后添加飞溅对象，该对象会在粒子被摧毁后一段时间再消失，因为粒子系统发射了大量爆裂的粒子，所以粒子消失所需时间至少是Time.time + lifetime，最后的爆裂的粒子甚至需要更久。
            GameObject splish = Instantiate(splash,position,rotation) as GameObject;
            Destroy(splish, lifetime+0.3f);
        }
    }
}
```


```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class WaterDetector : MonoBehaviour {
    void OnTriggerEnter2D(Collider2D Hit) {
        if (Hit.rigidbody2D != null) {
          transform.parent.GetComponent<WaterManager>().Splash(transform.position.x, Hit.rigidbody2D.velocity.y*Hit.rigidbody2D.mass / 40f);
        }
    }
}
```