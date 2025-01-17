+++
author = "Kyle Fang"
title = '2d Moon Phase'
date = 2021-12-30
+++

A nice trick I learned from [Flogelz](https://x.com/flogelz)!

To create sphere-like lighting on a quad, we first need to calculate the normal. Here's how we do it

```
float2 centerUV = (uv - 0.5) * (-2)
float z = sqrt(1.0 - saturate(dot(centerUV, centerUV)));
float3 normal = float3(centerUV.x, centerUV.y, z);
```

First we shift the UV coordinates to the center, the calculate the z component of the normal based on UVs as x and y. By applying the `NdotL`, it makes the light follows the surface as if it were actually hitting a sphere!

{{< youtube pocSu8-z0b0 >}}