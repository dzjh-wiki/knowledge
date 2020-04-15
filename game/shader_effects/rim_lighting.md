# 描边效果

----
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