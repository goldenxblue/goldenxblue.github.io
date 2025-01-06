+++
authro = "Kyle Fang"
title = 'Stylized Waterfall'
date = 2024-10-24
+++

The waterfall is inspired by Genshin Impact's waterfall.

Typically, we need to set texture tiling manually to prevent unwanted stretching.

{{< youtube  yZI0pg8Zf8k >}}

However, there's a trick to achieve automatic texture tiling under certain conditions. Thatis to use object's scale as factors to adjust the tiling. The scale is stored in the model matrix, which is accessible via the `unity_ObjectToWorld`.

```c
float objXScale = length( float3(unity_ObjectToWorld[0].x, unity_ObjectToWorld[1].x, unity_ObjectToWorld[2].x) );  
float objZScale = length( float3(unity_ObjectToWorld[0].z, unity_ObjectToWorld[1].z, unity_ObjectToWorld[2].z) );
```

The waterfall's texture maintains its proper appearance without stretching, regardless of the object's scale.

{{< youtube WLJkbKaMbfA >}}


A common method to create color variation for water is to lerp color based on the depth, or sample and blend normal textures to create visual waves.
The color variance in this waterfall, however, is lerped by the luminance of a reflection cube.
![reflection-cube](/reflection-trick.png)

As for the foam, it's simply sampling noise textures to create masks.
![foam-masks](/foam-mask.png)