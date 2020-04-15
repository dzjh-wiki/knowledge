# 扫光特效

----
```csharp
Shader "BanMing/UIFlowLight"
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
				OUT.texFlowLight.x+=_Time * _SpeedX;
				OUT.texFlowLight.y+=_Time * _SpeedY;

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