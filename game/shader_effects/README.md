# Shader特效

----

Unity Shader中的时间：  
```csharp
_Time float4 Time (t/20, t, t*2, t*3) // 每次切完场景会重新变化计数

// shader中一般用_Time.y或_Time[1]来表示时间
```

**主要特效：**
  * [扫光特效](flow_light.md)
  * [描边特效](rim_lighting.md)
  * [2D水体效果](water_effect_2d.md)
  * [圆角特效](fillet_effect.md)
  * [波纹效果](ripple_effect.md)