# 扫光特效

  * [通用流光特效](#通用流光特效)

----
实现步骤：  
  * 在顶点着色器中，将扫光贴图的UV根据速度和当前已花的时间进行偏移；
  * 在片元着色器中，根据扫光贴图、UV偏移值、扫光颜色和大小，获取扫光采样，将采样结果与原图进行混合得到扫光结果，最后将扫光结果添加到原图上。

```csharp
Shader "Custom/UIFlowLight"
{
	Properties
	{
		[PerRendererData]_MainTex ("Texture", 2D) = "white" {} // 隐藏当前属性
		[MaterialToggle] PixelSnap("Pixel Snap",float)=0 // 显示一个Toggle按钮

		// FlowLight
		_FLowLightTex("Move Texture",2D)="white"{}//扫光贴图
		_FLowLightColor("FlowLight Color",Color)=(0,0,0,1)//扫光颜色
		_Power("Power",float)=1//扫光大小
		_SpeedX("SpeedX",float)=1//X轴移动速度
		_SpeedY("SpeedY",float)=0//Y轴移动速度

	}
	SubShader
	{
		Tags 
		{ 
			"Queue"="Transparent"
			"IgnoreProjector"="True"
			"RenderType"="Transparent"
			"PreviewType"="Plane"
			"CanUseSpriteAtlas"="True"
		}

		Cull off
		Lighting off
		ZWrite off
		Blend One OneMinusSrcAlpha


		Pass
		{
			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#pragma multi_compile _PIXELSNAP_ON // 指令将会生成多个Shader变体(variants)，运行时根据材质或是全局的Keyword决定哪个变体起效
			
			#include "UnityCG.cginc"

			struct appdata
			{
				float4 vertex : POSITION;
				float4 color : COLOR;
				float2 texcoord : TEXCOORD0;
			};

			struct v2f
			{
				half2 texcoord : TEXCOORD0;
				float4 vertex : SV_POSITION;
				fixed4 color : COLOR;
				// FlowLight
				half2 texFlowLight :TEXCOORD1;
			};

			// FLowLight
			sampler2D _FLowLightTex;
			fixed4 _FLowLightTex_ST;
			fixed4 _FLowLightColor;
			float _Power;
			fixed _SpeedX;
			fixed _SpeedY;

			sampler2D _MainTex;
			
			v2f vert (appdata IN)
			{
				v2f OUT;
				OUT.vertex = UnityObjectToClipPos(IN.vertex);
				OUT.texcoord=IN.texcoord;

				// FlowLight
				OUT.texFlowLight=TRANSFORM_TEX(IN.texcoord,_FLowLightTex);
				OUT.texFlowLight.x+=_Time.y * _SpeedX;
				OUT.texFlowLight.y+=_Time.y * _SpeedY;

				OUT.color = IN.color;
				#ifdef PixelSnap_ON
				OUT.vertex=UnityPixelSnap(OUT.vertex);
				#endif

				return OUT;
			}
			
			fixed4 frag (v2f IN) : SV_Target
			{
				fixed4 col = tex2D(_MainTex, IN.texcoord);
			
				// FlowLight
				fixed4 cadd=tex2D(_FLowLightTex,IN.texFlowLight)*_Power*_FLowLightColor;
				cadd.rgb*=col.rgb; // 将原图的颜色与扫光图的颜色作融合
				col.rgb +=cadd.rgb; // 与主贴图的颜色相叠加
				// col.rgb *=col.a;

				return col;
			}
			ENDCG
		}
	}
    FallBack "Diffuse"
}
```


## 通用流光特效

```csharp
Shader "Custom/FlashEffect"
{
	Properties
	{
		_MainTex("MainTex(RGB)", 2D) = "white" {}
		_FlashTex("FlashTex", 2D) = "black" {}
		_FlashColor("FlashColor",Color) = (1,1,1,1)
		_FlashFactor("FlashFactor", Vector) = (0, 1, 0.5, 0.5)
		_FlashStrength ("FlashStrength", Range(0, 5)) = 1
	}

	CGINCLUDE

	#include "Lighting.cginc"

	uniform sampler2D _MainTex;
	uniform float4 _MainTex_ST;
	uniform sampler2D _FlashTex;
	uniform fixed4 _FlashColor;
	//改为一个vector4，减少传参次数消耗
	uniform fixed4 _FlashFactor;
	uniform fixed _FlashStrength;

	struct v2f
	{
		float4 pos : SV_POSITION;
		float3 worldNormal : NORMAL;
		float2 uv : TEXCOORD0;
		float3 worldLight : TEXCOORD1;
		float4 worldPos : TEXCOORD2;
	};

	v2f vert(appdata_base v)
	{
		v2f o;
		o.pos = mul(UNITY_MATRIX_MVP, v.vertex);
		o.uv = TRANSFORM_TEX(v.texcoord, _MainTex);
		//顶点转化到世界空间
		o.worldPos = mul(unity_ObjectToWorld, v.vertex);
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
		//通过时间偏移世界坐标对flashTex进行采样
		half2 flashuv = i.worldPos.xy * _FlashFactor.zw + _FlashFactor.xy * _Time.y;
		fixed4 flash = tex2D(_FlashTex, flashuv) * _FlashColor * _FlashStrength;
		fixed4 c;
		//将flash图与原图叠加
		c.rgb = diff * albedo + flash.rgb;
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