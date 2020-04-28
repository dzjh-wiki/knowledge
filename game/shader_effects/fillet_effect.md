# 圆角特效

----
实现步骤：  
  * 先保留除四个顶点距离边的中点为圆角半径的区域；
  * 接着对于所排除的四个矩形区域，排除掉在`片元对应位置`到`矩形离物体中心最近顶点`的距离大于圆角半径的区域（使用`discard`丢弃片元）。

```csharp
Shader "Custom/FilletEffect" {
    Properties {
        _MainTex ("Base (RGB)", 2D) = "white" {}
        _RADIUSBUCE("_RADIUSBUCE",Range(0,0.5))=0.2
    }
    SubShader {
        pass {

            CGPROGRAM

            #pragma exclude_renderers gles
            #pragma vertex vert
            #pragma fragment frag
            #include "unitycg.cginc"

            float _RADIUSBUCE;
            sampler2D _MainTex;

            struct v2f {
                float4 pos : SV_POSITION ;
                float2 ModeUV: TEXCOORD0;
                float2 RadiusBuceVU : TEXCOORD1;
            };

            v2f vert(appdata_base v) {
                v2f o;
                o.pos=mul(UNITY_MATRIX_MVP,v.vertex); //v.vertex;
                o.ModeUV=v.texcoord;
                o.RadiusBuceVU=v.texcoord-float2(0.5,0.5); //将模型UV坐标原点置为中心原点,为了方便计算
                return o;
            }

            fixed4 frag(v2f i):COLOR {
                fixed4 col;
                col=(0,1,1,0);

                // |x|<(0.5-r)或|y|<(0.5-r)
                if(abs(i.RadiusBuceVU.x)<0.5-_RADIUSBUCE || abs(i.RadiusBuceVU.y)<0.5-_RADIUSBUCE) {
                    col=tex2D(_MainTex,i.ModeUV);
                } else {
                    if(length(abs(i.RadiusBuceVU)-float2(0.5-_RADIUSBUCE,0.5-_RADIUSBUCE)) < _RADIUSBUCE) {
                        col=tex2D(_MainTex,i.ModeUV);
                    } else {
                        discard; // drop the fragment
                    }
                }

                return col;
            }
            
            ENDCG

        }
    }
}
```