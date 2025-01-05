+++
authro = "Kyle Fang"
title = 'Depth'
date = 2023-05-11
math = false
+++

The best explaination about depth in rendering is by [Cyanilux](https://www.cyanilux.com/tutorials/depth/).

# Early Depth Test, Alpha Test, Overdraw

One example using depth to optimize the rendering is the early depth test. The depth value of the incoming pixel is compared to the existing value in the depth buffer. The depth test passes when the comparison satisfies the depth test function. It skips the pixel shading, otherwise. In this way, the GPU rejects many shading calculation of the occluded objects.

Alpha Test checks the pixel's alpha value to determine whether it should discard. In other words, it need to reach the pixel shading first, so the early depth test would be turned off, leading to overdraw.

Overdraw is simly drawing the same pixel  multiple times, because depth test is bypassed by Alpha Test. This increases GPU workload, resulting in poor performance on mobile platform.


# Depth Texture

Depth texture stores the the depth buffer's value but in the range "[0,1]" based on the near and the far clip planes of the camera. Unity's depth texture is `_CameraDepthTexture`. 
Depth texture is created before rendering the translucent objects, so only opaque objects contribute to the depth buffer. That is why only transparent rendering can sample the depth texture in Unity.

**World Space Position Reconstruction**

Unity's URP documentation provideds an [example](https://docs.unity3d.com/6000.0/Documentation/Manual/urp/writing-shaders-urp-reconstruct-world-position.html) of reconstructing the world space position from the depth buffer. This is commonly used in many post-processing effect, like screen space [decal](https://www.youtube.com/watch?v=f7iO9ernEmM)/ambient occlusion/global illumination/reflection, or [edge detection](https://ameye.dev/notes/edge-detection-outlines/), 

Or the detecting/scanning VFX you can see in many games.

{{< youtube KBlqRmpvzhc >}}



# Sources

1. Ameye, Alexander. “Edge Detection Outlines.” _Ameye.dev_, 2022, [ameye.dev/notes/edge-detection-outlines](ameye.dev/notes/edge-detection-outlines)

2. Cyanilux. “Depth.” _Cyanilux.com_, 25 Nov. 2020, [www.cyanilux.com/tutorials/depth](www.cyanilux.com/tutorials/depth)

3. Daniel Ilett. “Decals & Stickers in Unity Shader Graph and URP.” _YouTube_, 26 Sept. 2021, [www.youtube.com/watch?v=f7iO9ernEmM](www.youtube.com/watch?v=f7iO9ernEmM).

4. Unity Technologies. “Unity - Manual: Reconstruct World Space Positions in a Shader in URP.” _Unity3d.com_, 2024, [docs.unity3d.com/6000.0/Documentation/Manual/urp/writing-shaders-urp-reconstruct-world-position.html](docs.unity3d.com/6000.0/Documentation/Manual/urp/writing-shaders-urp-reconstruct-world-position.html)