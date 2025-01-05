+++
authro = "Kyle Fang"
title = 'Exposure'
date = 2024-08-13
math = true
+++

# Exposure Value

Physical camera use aperture size (f-number), shutter speed, and sensitivity to represent an **Exposure Value** (EV). 

$$
EV_{100} = \log_2{\frac{N^2}{t}} + \log_2 \frac{S}{100}
$$
N = f-number, t = shutter speed in second. Sensitivity is 100 per convention. 

**EV100** is a standardized measurement for exposure value, but I don't understand how it actually achieve this "standardized measurement". Anyway, keep on.

In game development, EV is abused by using as a light unit (Lagarde and Rousiers, 27):
$$
EV​ = \log_2​​ \frac{L_{\text{avg}} \cdot S}{K}​
$$
$L_{avg}$ = average scene luminance. 
S = ISO = sensor sensitivity = 100
K = reflected-light meter calibration constant, Cannon, Nikon and Sekonic all use 12.5.

By taking K = 12.5, S = 100, the formula is simplified to:
$$
L_{avg} = 0.125 \cdot 2^{EV} = 2^{EV - 3}
$$

# Luminous Exposure

**Luminous/Photometric Exposure** (H) is different from EV. Luminous describes the scene luminance reaching the sensor.
$$
H = \frac{qt}{N^2} L = t E
$$
q = lens and vignette attenuation, Unity and many renderers used 0.65 (Guy and Agopian), UE used  0.78

ISO defined 3 ways to relate H and S: Standard Output Sensitivity (SOS), Saturation Based Sensitivity (SBS), Noise Based Sensitivity (NSB). According to the Lagarde and Rousiers, SBS is "the easiest one to understand and is the maximum possible exposure that does not lead to a clipped or bloomed camera output":
$$
H_{sbs} = \frac{78}{S_{sbs}}
$$

So connecting H and S, we have:
$$
\frac{qt}{N^2} L_{max} = \frac{78}{S}
$$

By simplification, we have:
$$
L_{\text{max}} = \frac{78 \cdot  2^{EV_{100}}}{qS}​
$$

# Auto Exposure

Unreal and Unity both use {{L_{max}} as the normalized factor to adjust the scene's exposure
```c++
// Unreal 5.4
const float TargetExposureScale = 1.0f / max(0.0001f, TargetExposure);

// Unity HDRP
float maxLuminance = exposureScale * pow(2.0, EV100);
return 1.0 / maxLuminance;
```

A common method to calculate the average luminance is through Histogram. Unity and Unreal engine both have their own histogram implementation. Unity's HDRP 14.0 used a step-by-step approach, while Unreal 5.4 is likely convert EV100 to luminance first. But the core is very similar: `compensation + (weight * curve * luminance)`

Auto exposure could not use one set of parameters to adjust every scenario, for example:
- camera changing will make the scene luminance inconsistent,
- particle effects is brighter than the environment
- Day/Night cycle makes luminance constantly change in the whole game world's every corner


# Source

1. Lagarde, Sebastien, and Charles de Rousiers. _Moving Frostbite to Physically Based Rendering 2.0_. _Zotero_, [https://media.contentapi.ea.com/content/dam/eacom/frostbite/files/course-notes-moving-frostbite-to-pbr-v2.pdf](https://media.contentapi.ea.com/content/dam/eacom/frostbite/files/course-notes-moving-frostbite-to-pbr-v2.pdf).

2. Narkowicz, Krzysztof. “Automatic Exposure.” _Krzysztof Narkowicz_, 9 Jan. 2016, [https://knarkowicz.wordpress.com/2016/01/09/automatic-exposure/](https://knarkowicz.wordpress.com/2016/01/09/automatic-exposure/).

3. Romain Guy and Mathias Agopian. _Physically Based Rendering in Filament_. [https://google.github.io/filament/Filament.html](https://google.github.io/filament/Filament.html). Accessed 12 Aug. 2024.