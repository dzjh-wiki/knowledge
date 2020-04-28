# 描边效果

----
实现步骤（在表面着色器中处理）：
  * 将Shader颜色与贴图进行混合，分别更新输出颜色和不透明度；
  * 根据物体法线方向和视图方向的角度，获取边缘基数；
  * 将底为边缘基数，指数为边缘光强度作运算，然后乘以边缘光颜色，来作为物体的自放光。

```csharp
Shader "Custom/RimLighting" {
    Properties {
        _Color ("Color", Color) = (1,1,1,1)
        _MainTex ("Albedo (RGB)", 2D) = "white" {}
        //边缘光颜色
        _RimColor("Rim Color",Color) =(1,1,1,1)
        //边缘光强度
        _RimPower("Rim Power", Range(0.5,8.0)) = 3.0
    }
    SubShader {
        Tags { "RenderType"="Opaque" }
        LOD 200

        CGPROGRAM
        // Physically based Standard lighting model, and enable shadows on all light types
        #pragma surface surf Standard fullforwardshadows

        // Use shader model 3.0 target, to get nicer looking lighting
        #pragma target 3.0

        sampler2D _MainTex;

        struct Input {
            float2 uv_MainTex;
            //法线
            float3 worldNormal;
            //视角方向
            float3 viewDir;
        };

        fixed4 _Color;
        fixed4 _RimColor;
        half _RimPower;

        void surf (Input IN, inout SurfaceOutputStandard o) {
            // Albedo comes from a texture tinted by color
            fixed4 c = tex2D (_MainTex, IN.uv_MainTex) * _Color;
            o.Albedo = c.rgb;
            o.Alpha = c.a;

            half rim = 1.0 - saturate(dot(normalize(IN.viewDir), IN.worldNormal)); // 求相机（视角方向）和法向量的余弦值，夹角越小值越趋近1，夹角越大值越趋近于0，也就是值大小与夹角成反比
            o.Emission = _RimColor.rgb * pow(rim, _RimPower);
        }
        ENDCG
    }
    FallBack "Diffuse"
}
```