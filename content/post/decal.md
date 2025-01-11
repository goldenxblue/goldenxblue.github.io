+++
author = "Kyle Fang"
title = 'Decal'
date = 2024-05-28
+++

There are two types of decals in video games: mesh-based decals and screen space decals. Mesh-based decals are, as the name suggests, basically meshes that stick tightly to an object's surface. Check Fewes' [MeshDecal](https://github.com/Fewes/MeshDecal/tree/master) for an example. They maintain correct perspective and depth at all angles since they're essentially another opaque object. These work best for decals that need to be viewed from extreme angles, or when depth and perspective are crucial.

Screen space decals are rendered in screen space after the object's rendering pass. Daniel Ilett has an excellent [video](https://www.youtube.com/watch?v=f7iO9ernEmM) explains the resons. They don't have "real" geometry but instead use a "projector." They're cheaper to render since they have fewer vertices, but this can lead to overdraw or visual artifacts at extreme angles. Depth issues can also affect them, which is why they're best used for temporary effects.

Most of the time, artists need to control which objects receive decals and which don't. This is usually handled using stencils. By writing a stencil mask on objects that should receive decals (or vice versa), the decal shader can test against the stencil value to determine whether to render the decal or not. URP used rendering layers for this function, but mobile platform could not afforad too many RTs.

One trick I use in URP is overwriting the stencil state in the pipeline within a specific render queue range. This way, we don't need to write multiple shaders for scene objects, and artists could adjust decal in the render queue.

![receive-decal-or-not](/decal-receive-or-not.png)