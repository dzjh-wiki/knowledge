# 消融效果

  * [边缘高亮的消失效果](#边缘高亮的消失效果)
  * [添加溶解效果的消失效果](#添加溶解效果的消失效果)

----
这里使用了模型空间坐标采样，其实还可以使用屏幕空间坐标或者视口空间坐标采样等等，几种方式各有各的优点和缺点。  
使用世界空间采样，消失的方向是绝对的，比如是从上向下，那么这个模型如果趴在地上，消失的方向就会是从模型后背到前胸的方向，而且坐标阈值会随着模型处于世界中的位置不同而不同；使用模型空间采样，消失的方向与模型本身有关，比如站着的话消失方向是从头到脚，那么趴着也是从头到脚，而且坐标的阈值与模型的高矮胖瘦有关（也与模型的原点位置有点关系）；使用屏幕空间采样的话，消失的方向就可能会与我们观察的方向有关，这种可能不太可控。


```csharp
Shader "Custom/DissolveEffect"
{
    Properties
    {
        _MainTex("MainTex(RGB)", 2D) = "white" {}
        _DissolveVector("DissolveVector", Vector) = (0,0,0,0)
    }

    CGINCLUDE

    #include "Lighting.cginc"

    uniform sampler2D _MainTex;
    uniform float4 _MainTex_ST;
    uniform float4 _DissolveVector;

    struct v2f
    {
        float4 pos : SV_POSITION;
        float3 worldNormal : NORMAL;
        float2 uv : TEXCOORD0;
        float3 worldLight : TEXCOORD1;
        float4 objPos : TEXCOORD2;
    };

    v2f vert(appdata_base v)
    {
        v2f o;
        o.pos = mul(UNITY_MATRIX_MVP, v.vertex);
        o.uv = TRANSFORM_TEX(v.texcoord, _MainTex);
        //顶点转化到世界空间
        o.objPos = v.vertex;
        o.worldNormal = UnityObjectToWorldNormal(v.normal);
        o.worldLight = UnityObjectToWorldDir(_WorldSpaceLightPos0.xyz);
        return o;
    }

    fixed4 frag(v2f i) : SV_Target
    {
        half3 normal = normalize(i.worldNormal);
        half3 light = normalize(i.worldLight);
        fixed diff = max(0, dot(normal, light));
        fixed4 albedo = tex2D(_MainTex, i.uv);
        //不满足条件的discard
        clip(i.objPos.xyz - _DissolveVector.xyz);
        fixed4 c;
        c.rgb = diff * albedo;
        c.a = 1;
        return c;
    }

    ENDCG

    SubShader
    {
        Pass
        {
            Tags{ "RenderType" = "Opaque" }

            CGPROGRAM

            #pragma vertex vert

            #pragma fragment frag

            ENDCG
        }
    }

    FallBack "Diffuse"
}
```


## 边缘高亮的消失效果
```csharp
Shader "Custom/DissolveEffect"
{
    Properties{
        _Diffuse("Diffuse", Color) = (1,1,1,1)
        _DissolveColor("Dissolve Color", Color) = (0,0,0,0)
        _MainTex("Base 2D", 2D) = "white"{}
        _ColorFactor("ColorFactor", Range(0,1)) = 0.7
        _DissolveThreshold("DissolveThreshold", Float) = 0
    }

    CGINCLUDE

    #include "Lighting.cginc"

    uniform fixed4 _Diffuse;
    uniform fixed4 _DissolveColor;
    uniform sampler2D _MainTex;
    uniform float4 _MainTex_ST;
    uniform float _ColorFactor;
    uniform float _DissolveThreshold;

    struct v2f
    {
        float4 pos : SV_POSITION;
        float3 worldNormal : TEXCOORD0;
        float2 uv : TEXCOORD1;
        float4 objPos : TEXCOORD2;
    };

    v2f vert(appdata_base v)
    {
        v2f o;
        o.pos = mul(UNITY_MATRIX_MVP, v.vertex);
        o.uv = TRANSFORM_TEX(v.texcoord, _MainTex);
        o.worldNormal = mul(v.normal, (float3x3)unity_WorldToObject);
        o.objPos = v.vertex;
        return o;
    }

    fixed4 frag(v2f i) : SV_Target
    {

        float factor = i.objPos.x - _DissolveThreshold;

        clip(factor);

        //Diffuse + Ambient光照计算

        fixed3 worldNormal = normalize(i.worldNormal);

        fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz);

        fixed3 lambert = saturate(dot(worldNormal, worldLightDir));

        fixed3 albedo = lambert * _Diffuse.xyz * _LightColor0.xyz + UNITY_LIGHTMODEL_AMBIENT.xyz;

        fixed3 color = tex2D(_MainTex, i.uv).rgb * albedo;

        //等价于下面注释代码的操作

        fixed lerpFactor = saturate(sign(_ColorFactor - factor));

        return lerpFactor * _DissolveColor + (1 - lerpFactor) * fixed4(color, 1);

        /*

        if (factor < _ColorFactor)

        {

        return _DissolveColor;

        }

        return fixed4(color, 1);*/

    }

    ENDCG

    SubShader
    {
        Tags{ "RenderType" = "Opaque" }

        Pass
        {
            //不让模型穿帮，关掉了背面裁剪
            Cull Off

            CGPROGRAM

            #pragma vertex vert

            #pragma fragment frag

            ENDCG
        }
    }

    FallBack "Diffuse"

}
```

## 添加溶解效果的消失效果

  * [溶解效果](ablation_effect.md)

```csharp
Shader "Custom/DissolveEffectX"
{
    Properties{
        _Diffuse("Diffuse", Color) = (1,1,1,1)
        _DissolveColor("Dissolve Color", Color) = (1,1,1,1)
        _MainTex("Base 2D", 2D) = "white"{}
        _DissolveMap("DissolveMap", 2D) = "white"{}
        _DissolveThreshold("DissolveThreshold", Range(0,1)) = 0
        _DissolveSpeedFactor("DissolveSpeed", Range(0,5)) = 2
        _DissolveControl("ColorFactorB", Float) = 0
    }

    CGINCLUDE

    #include "Lighting.cginc"

    uniform fixed4 _Diffuse;
    uniform fixed4 _DissolveColor;
    uniform sampler2D _MainTex;
    uniform float4 _MainTex_ST;
    uniform sampler2D _DissolveMap;
    uniform float _DissolveThreshold;
    uniform float _DissolveSpeedFactor;
    uniform float _DissolveControl;

    struct v2f
    {
        float4 pos : SV_POSITION;
        float3 worldNormal : TEXCOORD0;
        float2 uv : TEXCOORD1;
        float4 objPos : TEXCOORD2;
    };

    v2f vert(appdata_base v)
    {
        v2f o;
        o.pos = mul(UNITY_MATRIX_MVP, v.vertex);
        o.uv = TRANSFORM_TEX(v.texcoord, _MainTex);
        o.worldNormal = mul(v.normal, (float3x3)unity_WorldToObject);
        o.objPos = v.vertex;
        return o;
    }

    fixed4 frag(v2f i) : SV_Target
    {
        fixed4 dissolve = tex2D(_DissolveMap, i.uv);
        //Diffuse + Ambient光照计算
        fixed3 worldNormal = normalize(i.worldNormal);
        fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz);
        fixed3 lambert = saturate(dot(worldNormal, worldLightDir));
        fixed3 albedo = lambert * _Diffuse.xyz * _LightColor0.xyz + UNITY_LIGHTMODEL_AMBIENT.xyz;
        fixed3 color = tex2D(_MainTex, i.uv).rgb * albedo;
        float factor = i.objPos.x - _DissolveControl;
        if(factor < 0)
        {
            clip(_DissolveThreshold - dissolve.r * abs(factor) * _DissolveSpeedFactor);
        }
        return fixed4(color, 1);
    }

    ENDCG

    SubShader
    {

        Tags{ "RenderType" = "Opaque" }

        Pass
        {
            Cull Off

            CGPROGRAM

            #pragma vertex vert

            #pragma fragment frag

            ENDCG
        }
    }

    FallBack "Diffuse"
}
```