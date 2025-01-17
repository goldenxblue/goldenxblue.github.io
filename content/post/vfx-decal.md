+++
author = "Kyle Fang"
title = 'Recreation, Honkai Star Rail VFX Decal'
date = 2025-01-17
+++

I found an interesting decal in the game. At first I thought it was a mesh decal, but I realized I was wrong. I noticed that characters get a red outline when they are near the decal, and the cube VFX in the center also has a red outline. 

![hsr-decal](/hsr-decal.png)

This appears to be created using [edge detection](https://en.wikipedia.org/wiki/Edge_detection), with the edge detection implemented in the decal rather than in general post-processing. Here is my attempt to recreate it.


![hsr-decal-recreate](/hsr-decal-recreate.png)

Alexander Ameye had a [post](https://ameye.dev/notes/rendering-outlines/) about multiple ways to create outline effect.