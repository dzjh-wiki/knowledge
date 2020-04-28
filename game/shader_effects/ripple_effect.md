# 波纹效果

----
实现步骤：  
  * 在顶点着色器中，获取贴图的UV坐标；
  * 在片元着色器中，对UV的x坐标进行余弦变换（先缩放x坐标，然后加上时间乘以速度的值，最后求余弦并除以强度值），后将对应UV位置的颜色值，赋给输出的颜色。

```csharp
Shader "Custom/Ripple" {
    Properties {
        _MainTex ("Base (RGB)", 2D) = "white" {}

        _Scale("Scale", Range(0,500.0)) = 3.0
        _Speed("Speed", Range(-50,50.0)) = 1.0
        _Identity("Identity",Range(50,100.0))=80

    }
    SubShader {
        Tags { "RenderType"="Opaque" }
        LOD 200

        Pass
        {

            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #include "UnityCG.cginc"
            sampler2D _MainTex;
            float4 _MainTex_ST;

            struct v2f
            {
                float4 pos : POSITION;
                float2 uv : TEXCOORD0;
            };

            v2f vert(appdata_full v)
            {
                v2f o;
                o.pos = mul(UNITY_MATRIX_MVP, v.vertex);
                o.uv = TRANSFORM_TEX(v.texcoord, _MainTex);
                return o;
            }

            float4 frag(v2f i) : COLOR
            {      
               float4 o;
               half2 uv = i.uv;
               half r = uv.x; // 像素在沿着x轴进行波动
               // half r = sqrt(uv.x*uv.x + uv.y*uv.y); // 像素会沿着原点与该点的向量方向进行往复运动
               half z = cos(_Scale*r + _Time[1] * _Speed) /_Identity;
               o.rgb = tex2D(_MainTex, uv+ float2(z,0)).rgb;
               o.a =1;
               return o;
            }

            ENDCG
        }
    }
    FallBack "Diffuse"
}
```