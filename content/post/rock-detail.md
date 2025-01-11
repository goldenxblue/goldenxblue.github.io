+++
author = "Kyle Fang"
title = 'Rock Detail'
date = 2022-09-07
+++

Secondary maps, or detail maps, allow us to overlay additional textures on top of base textures. It's a common technique to add details on object to make it more visually interesting.

Rock in video games is often scaled up and placed on terrain to create interesting environments. However, when models are scaled up, their textures are also scaled,  resulting blurry and pixlated appearances. 

![rock-no-detail](/rock-example-no-detail-zoom.png)


Detail maps solve this problem by seamlessly tiling additional textures over the base texture, maintaining visual quality in large scales.

![rock-has-detail](/rock-example-detail-zoom.png)

There's an additional setting I created to control the distance to show details. Based on the view distance in world space, we can lerp the detail with the base textures.

```hlsl
float3 viewDirection = GetWorldSpaceViewDir(inputData.positionWS);
float viewDistance = length(viewDirection);
viewDistance = (viewDistance / (rangeInverse + 0.0001f));
viewDistance = saturate(SafePositivePow(viewDistance, fadePower));
float lerpFactor = 1 - viewDistance;
```

This simply creates a fade mask based on the view distance.

![detail-fade](/detail-range-scale.png)