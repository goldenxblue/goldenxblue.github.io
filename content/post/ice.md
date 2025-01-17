+++
author = "Kyle Fang"
title = 'Ice'
date = 2022-11-13
+++

Refraction is a key optical property of transparent materials like ice, glass, water, and plastic. [Snell's law](https://en.wikipedia.org/wiki/Snell%27s_law) provides the accurate mathematical formula for refraction, but we often avoid implementatin true refraction due to the performance consideration.

Transparen object's can lead to overdraw, so expensive calculation should be minimized. Additionally, artists typically prefer simpler workflows and are refluctant to work with technical parameters like medium IOR values.

A common approximation technique for refraction invovles offseting the UV coordinates based on surface normal.

```
float3 normalVS = mul(normalWS, (float3x3) UNITY_MATRIX_I_V);  
screenUV += normalVS.xy * 0.05f;
// or
screenUV += normalWS.xy * 0.05f;
// or both
```

Notice that the view-space normals create a dynamic refraction effect that responds to camera rotation.

{{< youtube Y3gY1DF2oZ4 >}}