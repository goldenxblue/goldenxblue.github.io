+++
author = "Kyle Fang"
title = 'Terrain Blending'
date = 2023-10-17
math = true
+++




# Method
A common technique for blending objects with terrain involves using depth as an `lerp` factor. It usually requires first rendering the terrain and saving its color and depth information to separate textures. Then the objects rendered subsequently use the color and depth texture to blend the terrain and objects.

Genshin Impact used this method to blend the terrain and stones. But it somehow had weird shadow?

![terrain-blending but missint shadow 1](/genshin-terrain-blending-problem.png)
![terrain-blending but missint shadow 2](/genshin-missing-shadow.png)

An enhanced approach to this technique, suggested by Wang in GDC 2023, involves rendering the terrain twice: once in the opaque pass and once in the transparent pass. During the initial opaque pass, the terrain is rendered normally and masked by stencil. In the transparent pass, the terrain vertices are slightly shifted towards the camera to create a small seams between the objects and terrain. This seams can use the depth and color texture (or GBuffer) created by pipeline without creating extra textures. This second pass skips pixels already rendered in the first pass, thanks to the stencil test. 

![alt text](/terrain-blending-stencil.png)

# Sources

1. _Cross-Platform Mobile and PC Rendering in “Earth: Revival.”_ 2023, [https://www.gdcvault.com/play/1028751/Cross-Platform-Mobile-and-PC](https://www.gdcvault.com/play/1028751/Cross-Platform-Mobile-and-PC).

2. Simon. _Deus Ex – Alpha Terrain | Simonschreibt._ 28 May 2023, [https://simonschreibt.de/gat/deus-ex-alpha-terrain/](https://simonschreibt.de/gat/deus-ex-alpha-terrain/).