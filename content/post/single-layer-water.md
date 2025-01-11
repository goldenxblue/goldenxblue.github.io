+++
author = "Kyle Fang"
title = 'Single Layer Water'
date = 2024-10-29
math = true
+++

Unreal Engine has a physically-based shading model for its single-layer water material. Instead of using a predefined water color, it calculates the water color through light scattering and absorption.

Some tutorials also mentioned this appraoch:
- [Yet Another Stylised Water Shader](https://halfpastyellow.com/blog/2020/10/01/Yet-Another-Stylised-Water-Shader.html) by Remy.
- [Realistic Water Colour in ShaderGraph](https://www.youtube.com/watch?v=S4RrVKBEaDw) by eleonora

I went through Unreal's shading code and figured the absorption and scattering light calculation part. It's a interesting method so here I am sharing with you if you don't want to read code. Note that this only covers a small portion of the complete shading model.

Before explaining the code parts, we should know two phenomena occur when light travels through the water: scattering and absorption. Scattered light bounce within the water to create visible light, while certain wavelength of light are absorbed and go extinct.

To recreate light scattering and absorption, Unreal used [Beer-Lambert](https://en.wikipedia.org/wiki/Beer%E2%80%93Lambert_law) law to calculate light transmittance:

$$T=e^{−(σ_s​+ σ_a​)⋅d}$$

$T$ is the transmittance. It measures how much light can pass through the water.
Sigma s and sigma a are the scattering and absorption coefficients. The sum of them is the extinction coefficient.
$d$ is the path length or depth of the medium the light travels through.

Scattering and absorption coefficients are user defined. $d$ is the depth difference between the water surface and the geometry surface under the water.

[Stylized Water Shader](https://ameye.dev/notes/stylized-water-shader/) by Alexander Ameye shows two methods for calculating depth difference: camera-relative and world-space.
Unreal appears utilize both approaches.

Unreal applied transmittance in multiple parts of their shading model. Here are two parts: the **scene luminance behind the water** and the **scattered luminance**.

(Side note: I don't know why Unreal call them "luminance", the measurement of light intensity, instead of "light color". Maybe there's a reason hide behind the code that I didn't read yet.)

Multiplying the transmittance by the scene color gives the scene luminance behind the water. We can take it as the light go through the water from the scene behind water.
```
// calcualte the transmittance
const float3 MeanTransmittanceToLightSources = exp(-DistanceFromScenePixelToWaterTop * ExtinctionCoeff);

// get sample the scene color under the water
float3 BehindWaterSceneLuminance = SceneColorWithoutSingleLayerWaterTexture.SampleLevel(SceneColorWithoutSingleLayerWaterSampler, ViewportUV, 0).rgb;

// the result 
BehindWaterSceneLuminance = MeanTransmittanceToLightSources * ResolvedView.OneOverPreExposure * BehindWaterSceneLuminance;
```


Scattering luminance is a bit complicated to understand to me. We can break it down. 
- `ExtinctionCoeff` is the total light loss: scattered and absorbed.
- `1 - Transmittance` is the light that did NOT pass through the water.
- `ScatteringCoeff * (1.0f - Transmittance)` is the scattered light that did NOT pass through the water.
- the final saturated division by `ExtinctionCoeff` gives the percentage of scattered light relative to total light loss. In other words, "if we lost `ExtinctionCoeff` amount of light, what is the percentage of the scattering light?"
- finally, multiplying by the `IncomingLuminance`, we get the scattered light.

```
float3 ExtinctionCoeff = ScatteringCoeff + AbsorptionCoeff;

float3 SafeScatteringAmount = saturate(ScatteringCoeff * (1.0f - Transmittance) / ExtinctionCoeffSafe);

float3 ScatteredLuminance = IncomingLuminance * SafeScatteringAmount;
```

Unreal added them together with many scaling factor multiplication, like opacity, exposure, fresnel, environment BRDF … So this is not complete code. I only took this part because it adds a nice touch of realism.

By setting scattering coefficient to dark blue, and absorption between black and orange, it wil add a touch of green tint to the shallow water:

![no-water](/no-water.png)

![add-water](/add-water.png)

# Sources

1. Works CitedAmeye, Alexander. “Stylized Water Shader.” _Ameye.dev_, [ameye.dev/notes/stylized-water-shader/.eleonora](ameye.dev/notes/stylized-water-shader/.eleonora)

2. “Realistic Water Colour in ShaderGraph.” _YouTube_, 6 Sept. 2023, [www.youtube.com/watch?v=S4RrVKBEaDw](www.youtube.com/watch?v=S4RrVKBEaDw).

3. Wikipedia Contributors. “Beer–Lambert Law.” _Wikipedia_, Wikimedia Foundation, 30 May 2019, [en.wikipedia.org/wiki/Beer%E2%80%93Lambert_law](en.wikipedia.org/wiki/Beer%E2%80%93Lambert_law).

4. “Yet Another Stylised Water Shader - Half Past Yellow | Blog.” _Halfpastyellow.com_, 2020, [halfpastyellow.com/blog/2020/10/01/Yet-Another-Stylised-Water-Shader.html](halfpastyellow.com/blog/2020/10/01/Yet-Another-Stylised-Water-Shader.html).