
# 风格化渲染

## 卡通渲染

### 顶点外扩描边

```c
Properties  
{  
	_OutlineColor("Outline Color", Color) = (0, 0, 0, 0) 
	_OutlineWidth("Outline Width", Range(0, 10)) = 1
}

Pass
{
	Tags
	{
		"LightMode" = "SRPDefaultUnlit"
	}

	Cull Front

	HLSLPROGRAM
	#pragma vertex vert
	#pragma fragment frag

	#include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/Core.hlsl"

	struct Attributes
	{
		float4 positionOS : POSITION;
		float3 normalOS : NORMAL;
	};

	struct Varyings
	{
		float4 positionHCS : SV_POSITION;
	};

	CBUFFER_START(UnityPerMaterial)
		half4 _OutlineColor;
		float _OutlineWidth;
	CBUFFER_END

	Varyings vert(Attributes IN)
	{
		Varyings OUT;
		float4 positionHCS = TransformObjectToHClip(IN.positionOS.xyz);
		float3 normalHCS = TransformObjectToHClip(IN.normalOS);
		positionHCS.xy += normalize(normalHCS.xy) / _ScreenParams.xy * _OutlineWidth * positionHCS.w * 2;
		OUT.positionHCS = positionHCS;
		return OUT;
	}

	half4 frag(Varyings IN) : SV_Target
	{
		return _OutlineColor;
	}
	ENDHLSL
}
```