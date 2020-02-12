---
layout: portfolioitem
title: Vegetation Shader
preview: vegetation/preview.png
---
This is a simple demonstration of how I structured a vegetation shader in the real-time rendering engine Unity
<!--more-->
<iframe src="https://player.vimeo.com/video/118272044" width="1280" height="720" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
<p><a href="https://vimeo.com/118272044">Vegetation Shader</a> from <a href="https://vimeo.com/user1489637">Luca Pierabella</a> on <a href="https://vimeo.com">Vimeo</a>.</p>

<a href="{{ site.baseurl }}/assets/portfolio/vegetation/vegetationInspector.jpg"><img src="{{ site.baseurl }}/assets/portfolio/vegetation/vegetationInspector.jpg" alt="Vegetation" style="width: auto;"/>

The purpose of this shader is to simulate the wind force acting on leaves and the vegetation in general
To achieve this effect I chose a classic approach to working directly on the vertices
The shader I wrote in very few words takes as input a force and a direction to simulate wind and apply them at each vertex
To be able to give more control to the artist can give color information at each vertex influencing so differently for different points

<a href="{{ site.baseurl }}/assets/portfolio/vegetation/vertexcolor.jpg"><img src="{{ site.baseurl }}/assets/portfolio/vegetation/vertexcolor.jpg" alt="Vegetation" style="width: auto;"/>

# Vegetation 

```C  
Shader "Custom/VegetationShader"
{
    Properties
    {
        _MainTex ("Base (RGB)", 2D) = "white" {}
        _AlphaTex("Alpha (A)",2D) = "white" {}
        //_BumpTex ("Bumpmap", 2D) = "bump" {}
       
        _Opacity ("Opacity",Range(0,1))=1
       
        _winddescription("_WIND_PARAMETERS_",Range(0,0))=0
       
        _WindDirection("WindDirection(from 0 to 1)",Vector)=(1,1,1,1)
        _WindStrength ("WindForce",Range(0,100))=1
        _WindSpeed ("Velocity",Range(0,100))=1
       
        _maindescription("_MAIN_ANIMATION_",Range(0,0))=0
       
        //_MainBendingOffset("MainBendingOffset",Vector)=(1,1,1)
        _MainBendingStrength("MainBendingStrength",Range(-1,0))=1
       
        _edgedescription("_EDGE_ANIMATION_",Range(0,0))=0
 
        _EdgeDeformationStrength("EdgeStrength",Range(0,1))=0.1
        _EdgeDeformationSpeed ("EdgeSpeed",Range(0,100))=1
        _EdgeDeformationWaveSize ("EdgeSize",Range(0,10))=1
        _EdgeResistency("EdgeResistency",Range(0,20))=1
       
        _leafdescription("_LEAF_ANIMATION_",Range(0,0))=0
 
        _PerLeafBendWaveSize("PerLeafWave",Range(0,10))=1
        _PerLeafSpeed("PerLeafSpeed",Range(0,10))= 1
        _PerLeafBendStrength("PerLeafBendStrength",Range(0,1))=0.1
       
        _switchdescription("_BLEND_ANIMATION_",Range(0,0))=0
 
        _SwitchEdge("SwitchEdge",Range(0,1))=1
        _SwitchLeaf("SwitchLeaf",Range(0,1))=1
       
    }
   
    SubShader
    {
        Tags {"Queue"="Transparent" "RenderType"="Transparent"}
        LOD 200
        Cull Back
       
        CGPROGRAM
        #pragma surface surf Lambert alpha vertex:vert
        #include "UnityCG.cginc"
       
       
 
        sampler2D _MainTex;
        sampler2D _AlphaTex;
        //sampler2D _BumpTex;
        float _Opacity;
       
        float  _WindSpeed;
        float3 _WindDirection;
        float  _WindStrength;
       
        float _MainBendingStrength;
        float4 _MainBendingOffset;
       
        float _EdgeDeformationSpeed ;
        float _EdgeDeformationWaveSize;
        float _EdgeDeformationStrength;
        float _EdgeResistency;
 
        float _PerLeafSpeed;
        float _PerLeafBendStrength;
        float _PerLeafBendWaveSize;
 
        float _SwitchEdge;
        float _SwitchLeaf;
       
        struct Input
        {      
            float2 uv_MainTex;
            float2 uv_AlphaTex;
            //float2 uv_BumpTex;
            float3 color : COLOR;
            float4 pos : SV_POSITION;
            float4 posWorld : TEXCOORD0;
            float3 worldNormal: TEXCOORD1;
        };
 
       
        void vert (inout appdata_full v,out Input o)
        {
            _MainBendingOffset=(0,0,0,0);
            UNITY_INITIALIZE_OUTPUT(Input,o);
            o.pos =  mul(UNITY_MATRIX_MVP, v.vertex);
            o.posWorld = mul(_Object2World, v.vertex);
            o.worldNormal= mul((float3x3)_Object2World, v.normal);
            float4 timeNode= sin(3.141592654+_Time.g)*0.016;
             
            ///main
            float4 objPos = mul (_Object2World, float4(0,0,0,1) );
            float3 killY=(1,0,1);
            float3 mainBending;
            float4 main= distance(objPos+_MainBendingOffset,o.posWorld)/256;
            mainBending = clamp((sin(normalize(_WindDirection.xyz)*_WindSpeed *timeNode)),0,1)*_WindStrength*killY*main*v.texcoord.y;
            mainBending = _MainBendingStrength*mainBending;
           
            //edge
            float3 a=normalize(_WindDirection.xyz);
            float3 b=(_EdgeDeformationSpeed*_WindSpeed)+(o.posWorld/_EdgeDeformationWaveSize);
            float3 c =a*b;
            //qui sommo dopo
            float temp=_WindStrength*(clamp(sin(c),0,1))*killY*timeNode;
            float3 x= o.worldNormal*v.color.r;
            float3 edge =x*temp*_EdgeDeformationStrength*_SwitchEdge;
 
            //per leaf
            float y=(o.posWorld/_PerLeafBendWaveSize)+(_PerLeafSpeed*_WindSpeed);
            //qui sommo dopo
            float3 keepY=(0,0,2.5);
            keepY=((clamp(sin(y),0,1))*_WindStrength*keepY*timeNode)*(v.color.b);
            float3 leaf=_PerLeafBendStrength*keepY*_SwitchLeaf;
 
            //gloabal leaf
            //float leafVariation =v.color.g*_LeafVariation;
 
            v.vertex.xyz += mainBending+edge+leaf;
        }
 
        void surf (Input IN, inout SurfaceOutput o)
        {
            half4 col = tex2D (_MainTex, IN.uv_MainTex);
            half4 alpha = tex2D (_AlphaTex,IN.uv_AlphaTex);
            //half3 bump = UnpackNormal (tex2D (_BumpTex, IN.uv_BumpTex));
            o.Albedo = col.rgb;
            o.Alpha  = alpha.rgb * _Opacity ;
 ```

By analyzing the code you see that it is possible to define a particular behavior for vertices blue, in my case I used it for the leaves
While on the other hand with the red vertices react differently, in the specific case I used to define the behavior of the branches of the leaves

