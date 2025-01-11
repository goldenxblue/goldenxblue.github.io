+++
author = "Kyle Fang"
title = 'Tonemapping'
date = 2024-02-04
math = true
+++

If you don't know the theory and methods behind Tonemapping, it's simply a tool to adjust the color like color grading. But they are not the same thing. Tonemappin's purpose is to conver the High Dynamic Range (HDR) color to Standard Dynamic Range (SDR). 

Video game always uses coor value exceeds the range between 0 and 1, like bloom and or HDR texture. To display these color, Tonemapping is used to remap the color in the post-processing.

# Reinhard
Paper: [Photographic Tone Reproduction for Digital Images](https://www-old.cs.utah.edu/docs/techreports/2002/pdf/UUCS-02-001.pdf)

Reinhard Tonemapping focus on luminance instead of RGB. 

$$
L = 0.2125R + 0.7152G + 0.0722B
$$

Simple Reinhard Tone mapping. High luminance is scaled by $1 / L$ , and the low luminance is scaled by 1. This brings all luminance within a displayable range. The result is not always desirable.
$$
L_d(x, y) = \frac{L(x, y)}{1 + L(x, y)}
$$


Extended Reinhard Tone mapping
$$
L_d(x, y) = \frac{L(x, y) (1 + \frac{L(x,y)}{L^2_{white}})}{1 + L(x, y)}
$$
$L_{white}$ is the white point, where the lowest value remapped to 1 (pure white).



The common approach to color treatment in tone mapping by Schlick is preserving color ratios.

$$
C_{out} = \frac{C_{in}}{L_{in}} L_{out}
$$
Mantiuk implemented other implementation in his [paper](https://www.cl.cam.ac.uk/%7Erkm38/pdfs/mantiuk09cctm.pdf);



**Luminance vs. Luma**

luma = luminance of an sRGB color (gamma-corrected)

Applying tonemapping on luminance could cause hue/saturation shift. It's not always desired. 

Naty Hoffman's [thoughts](https://imdoingitwrong.wordpress.com/2010/08/19/why-reinhard-desaturates-my-blacks-3/#comment-3) on this Reinhard Tonemapping.
> Reinhard was on the wrong track in applying his tone mapping curve to luminance; his curve was inspired by film, but film curves are applied to each color channel separately.


# Filmic Tonemapping

"Filmic" tonemapping emulates real film. 

Three parts: 
- Toe gives crisper black
- Linear stays relatively unchanged
- Shoulder gives softer transition to overexposed highlights.

**Uncharted 2 by [John Hable](http://filmicworlds.com/blog/filmic-tonemapping-operators/)**

``` hlsl
float A = 0.15;
float B = 0.50;
float C = 0.10;
float D = 0.20;
float E = 0.02;
float F = 0.30;
float W = 11.2;

float3 Uncharted2Tonemap(float3 x)
{
   return ((x*(A*x+C*B)+D*E)/(x*(A*x+B)+D*F))-E/F;
}

float4 ps_main( float2 texCoord  : TEXCOORD0 ) : COLOR
{
   float3 texColor = tex2D(Texture0, texCoord );
   texColor *= 16;  // Hardcoded Exposure Adjustment

   float ExposureBias = 2.0f;
   float3 curr = Uncharted2Tonemap(ExposureBias*texColor);

   float3 whiteScale = 1.0f/Uncharted2Tonemap(W);
   float3 color = curr*whiteScale;
   float3 retColor = pow(color,1/2.2);
   return float4(retColor,1);
}
```


Hable later create a [improved curve](http://filmicworlds.com/blog/filmic-tonemapping-with-piecewise-power-curves/) that has intuitive controls.


**ACES**

Academy Color Encoding System. It usually involves matrix transformations.
[Stephen Hill](https://github.com/TheRealMJP/BakingLab/blob/master/BakingLab/ACES.hlsl)'s implementation.

[Krzysztof Narkowicz](https://knarkowicz.wordpress.com/2016/01/06/aces-filmic-tone-mapping-curve/) approximated a simpler curve. 
- Color is multiplied exposure before the tonemapping, and do the gamma after.
- simple luminance only fit and over saturates bright colors. It fits their art direction. For more realistic rendering, Narkowicz suggests Stephen Hill's.

***


# Sources

1. “Film Contrast Characteristics.” _Archive.org_, 2025, [web.archive.org/web/20201031155700/www.sprawls.org/ppmi2/FILMCON/.](web.archive.org/web/20201031155700/www.sprawls.org/ppmi2/FILMCON)

2. “Tone Mapping.” _Github.io_, 2019, [64.github.io/tonemapping/#local-tone-mapping-operators](64.github.io/tonemapping/#local-tone-mapping-operators).


