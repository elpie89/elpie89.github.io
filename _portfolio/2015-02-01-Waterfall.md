---
layout: portfolioitem
title: Waterfall Shader
preview: waterfall/preview.png
---
In this project, I had to create two shaders that were operating on transparency with more precision than the standard shaders of Unity.
At the specific request of the Art Director of DiXidiasoft, so I created a couple of shaders, one that blends the transparencies taken input from different textures and another that acts additively concerning the color in the background
<!--more-->
What you see is, therefore, the result visually integrated into the game The Steampunk League.
Everything then was combined with a script C# that has allowed us to give a constant offset to the UV in real-time.

# Waterfall 

```C  
Shader "WaterfallShader" {
    Properties {
    _MainTex("Diffuse (RGB)", 2D) = "white" {}
    _Color ("Color", Color) = (0.5,0.5,0.5)
    _Alpha1 ("Alpha1 (RGB)", 2D) = "white" {}
    _Alpha1CTRL("Alpha1CTRL",Range(0,1))=0.5
    _Alpha2 ("Alpha2 (RGB)", 2D) = "white" {}
    _Alpha2CTRL("Alpha2CTRL",Range(0,1))=0.5
    _Opacity ("Opacità",Range(0,1))=0.5
    }   
 SubShader {
    Tags { "Queue"="Transparent" "RenderType"="Transparent"}
    cull off


    CGPROGRAM
    #pragma surface surf BlinnPhong alpha
    #pragma target 3.0
    sampler2D _Alpha1;
    sampler2D _Alpha2;
    sampler2D _MainTex;
    float _Opacity;
    float _Alpha1CTRL;
    float _Alpha2CTRL;
    float3 _Color;
    struct Input {
        float2 uv_Alpha1;
        float2 uv_Alpha2;
        float2 uv_MainTex;
    };
    void surf (Input IN, inout SurfaceOutput o) {
        fixed4 colore= tex2D (_MainTex,IN.uv_MainTex);
        o.Albedo= colore.rgb*_Color;
        fixed3 first = tex2D (_Alpha1, IN.uv_Alpha1).rgb;
        fixed3 second= tex2D (_Alpha2, IN.uv_Alpha2).rgb;
        fixed3 firstLerp=lerp (first,1,_Alpha1CTRL);
        fixed3 secondLerp=lerp (second,1,_Alpha2CTRL);
        fixed3 product = dot(firstLerp,secondLerp);
        o.Alpha = product.rgb*_Opacity;
    }
    ENDCG
 }
 FallBack "Diffuse"
 }
 ```


# Waterfall Additive
```C 
Shader "WaterfallAdditive" {
     Properties {
          _Color ("Color", Color) = (0.5,0.5,0.5,0.5)
          _MainTex ("Diffuse", 2D) = "white" {}
          _Alpha1 ("Alpha1 (RGB)", 2D) = "white" {}
          _Opacity ("Opacità",Range(0,1))=0.5
          }
    Category {
        Tags { "Queue"="Transparent" "IgnoreProjector"="True" "RenderType"="Transparent" }
        Blend SrcAlpha One
        AlphaTest Greater .01
        ColorMask RGB
        Cull Off Lighting Off ZWrite Off Fog { Color (0,0,0,0) 
        }
    SubShader {
        Pass {
            
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #include "UnityCG.cginc"

            sampler2D _MainTex;
            fixed4 _Color;
            sampler2D _Alpha1;
            uniform float _Alpha1CTRL;
            uniform float _Opacity;

            struct vertexInput {
                float4 vertex : POSITION;
                fixed4 color : COLOR;
                float2 texcoord : TEXCOORD0;
                float2 alpha1 :TEXCOORD1;
            };

            struct vertexOutput {
                float4 vertex : POSITION;
                fixed4 color : COLOR;
                float2 texcoord : TEXCOORD0;
                float2 alpha1 :TEXCOORD1;
            };

            float4 _MainTex_ST;
            float4 _Alpha1_ST;

            vertexOutput vert (vertexInput v){
                vertexOutput o;
                o.vertex = mul(UNITY_MATRIX_MVP, v.vertex);
                o.color = v.color;
                o.texcoord = TRANSFORM_TEX(v.texcoord,_MainTex);
                o.alpha1 = TRANSFORM_TEX(v.alpha1,_Alpha1);
                return o;
            }

            sampler2D _CameraDepthTexture;

            fixed4 frag (vertexOutput i) : COLOR
            {
                float3 mainTex=_Color*tex2D(_MainTex, i.texcoord);
                float3 alpha1Tex=tex2D(_Alpha1, i.alpha1);
                float3 colore=2.0f * i.color * _Color * mainTex * alpha1Tex;
                return float4(colore,_Opacity);
            }
            ENDCG
 
        }
    }
    }
}
```




<iframe src="https://player.vimeo.com/video/118059510" width="1280" height="720" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
<p><a href="https://vimeo.com/118059510">Waterfall Shader</a> from <a href="https://vimeo.com/user1489637">Luca Pierabella</a> on <a href="https://vimeo.com">Vimeo</a>.</p>

<a href="{{ site.baseurl }}/assets/portfolio/waterfall/waterfall_shader.jpg"><img src="{{ site.baseurl }}/assets/portfolio/waterfall/waterfall_shader.jpg" alt="Triumph" style="width: auto;"/>


