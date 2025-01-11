+++
author = "Kyle Fang"
title = 'Metal'
date = 2022-12-15
draft = true
+++

It's fine to use common PBR calculation for metal in video game, but specular occlusion, or reflectance occlusion, could add a little more touch to it.

Unity's HDRP added this to its "Specular Oclcusion Mode" in shader graph:

```hlsl
// Ref: Moving Frostbite to PBR - Gotanda siggraph 2011  
// Return specular occlusion based on ambient occlusion (usually get from SSAO) and view/roughness info  
real GetSpecularOcclusionFromAmbientOcclusion(real NdotV, real ambientOcclusion, real roughness)  
{  
    return saturate(PositivePow(NdotV + ambientOcclusion, exp2(-16.0 * roughness - 1.0)) - 1.0 + ambientOcclusion);  
}
```

It's a scale factor for reflectance factor, aka, specular.

One way to micmic this is to simply multiply the reflectance by ambient occlusion. 

```hlsl
surfaceData.albedo *= ao;
```

Some metal has iridescent effect. This  can be achieved by utilizing fresnel effect and rainbow gradient map.

```hlsl
half fresnelMask = FresnelEffect();
half3 gradient = SampleTexture(_GradientMap, float2(frac(iridescentMask), 0));
surfaceData.albedo = lerp(albedo, gradient, _IridescentStrength);
```

![metal](/metal.png)

![metal](/metal-iridescent.png)

The key is to use fresnel mask as the uv to sample the gradient, using `frac()` function could control the color's repetativeness.

Brushed metal uses anistropical lighting to create the specular following the brushed metal 