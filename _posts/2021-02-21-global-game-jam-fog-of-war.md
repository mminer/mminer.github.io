---
title: "Global Game Jam: Fog of War"
---

One challenge I tackled for our [Global Game Jam entry](/2021/02/07/global-game-jam-2021) was fog of war. We wanted to start the player in darkness and slowly reveal the level as they explore.

My first instinct was to tile black sprites above the level then destroy them as the player moves around. Our game uses grid-based movement, so this seemed like a reasonable approach. Making the edges blend smoothly proved difficult though.

Instead I arrived at a solution that overlays a single giant texture above the level then cuts out parts where the player moves. The first step is to spawn sprites at the coordinates the player visits.

![Fog of war sprites](/images/fog-of-war-sprites.png)

Add these sprites to a `Fog` layer that the main camera ignores. Then use an orthographic camera to capture the sprites to a render texture. Set this camera to *only* see the `Fog` layer.

![Fog of war camera](/images/fog-of-war-camera.png)

Finally, use a shader to make opaque parts of the render texture transparent and transparent parts opaque.

![Fog of war cutout](/images/fog-of-war-cutout.png)

This was the most difficult step. The shader is simple --- just invert each pixel's alpha value --- but my HLSL prowess is lacking so it took me a while to crack.

```hlsl
Shader "Unlit/FogOfWarCutout"
{
    Properties
    {
        _Color ("Fog Color", Color) = (1,1,1,1)
        _MainTex ("Texture", 2D) = "white" {}
    }
    SubShader
    {
        Tags {
            "Queue" = "Transparent"
            "IgnoreProjector" = "True"
            "RenderType" = "TransparentCutout"
        }

        Lighting Off
        Blend SrcAlpha OneMinusSrcAlpha

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #pragma target 2.0

            #include "UnityCG.cginc"

            struct appdata_t
            {
                float4 vertex : POSITION;
                float2 texcoord : TEXCOORD0;
                UNITY_VERTEX_INPUT_INSTANCE_ID
            };

            struct v2f
            {
                float4 vertex : SV_POSITION;
                float2 texcoord : TEXCOORD0;
                UNITY_VERTEX_OUTPUT_STEREO
            };

            fixed4 _Color;
            sampler2D _MainTex;

            v2f vert (appdata_t v)
            {
                v2f o;
                UNITY_SETUP_INSTANCE_ID(v);
                UNITY_INITIALIZE_VERTEX_OUTPUT_STEREO(o);
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.texcoord = TRANSFORM_TEX(v.texcoord, _MainTex);
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                fixed4 color = _Color;
                color.a = 1 - tex2D(_MainTex, i.texcoord).a;
                return color;
            }

            ENDCG
        }
    }
}
```

An interesting property of using a render texture is that lowering its resolution causes the fog's edges to appear smoother. Blurring the sprite texture compounds this effect, leaving us with a handsome blend from transparent to opaque.

![Fog of war final result](/images/fog-of-war-final.png)

Smooth as Santana.
