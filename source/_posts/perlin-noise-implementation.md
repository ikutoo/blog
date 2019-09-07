---
title: Perlin Noise Implementation
date: 2019-09-07 16:23:27
tags:
---


<div style="text-align: center;">
	<iframe width="800" height="450" frameborder="0" src="https://www.shadertoy.com/embed/lt3BWM?gui=true&t=10&paused=true&muted=false" allowfullscreen></iframe>
</div>


<!-- more -->

<br><br>

## Common Micro

```
#define fade(t) (t*t*(3.0-2.0*t))
```

## 1D Perlin Noise

```
float hash(float p)
{
    p = fract(p * .1031);
    p *= p + 33.33;
    p *= p + p;
    return fract(p);
}

float noise(float p) // 1d perlin noise
{
    float pi = floor(p), pf = fract(p), w = fade(pf);
    
    return mix ( dot ( hash(pi+0.0), pf-0.0 ),
               	 dot ( hash(pi+1.0), pf-1.0 ), w);
}
```

## 2D Perlin Noise

```
vec2 hash(vec2 p)
{
	vec3 p3 = fract(vec3(p.xyx) * vec3(.1031, .1030, .0973));
    p3 += dot(p3, p3.yzx+33.33);
    return fract((p3.xx+p3.yz)*p3.zy);
}

float noise(vec2 p) // 2d perlin noise
{
    vec2 pi = floor(p), pf = fract(p), w = fade(pf);

    return mix( mix( dot( hash(pi+vec2(0.0,0.0)), pf-vec2(0.0,0.0) ), 
                     dot( hash(pi+vec2(1.0,0.0)), pf-vec2(1.0,0.0) ), w.x),
                mix( dot( hash(pi+vec2(0.0,1.0)), pf-vec2(0.0,1.0) ), 
                     dot( hash(pi+vec2(1.0,1.0)), pf-vec2(1.0,1.0) ), w.x), w.y);
}
```

## 3D Perlin Noise

```
vec3 hash(vec3 p3)
{
	p3 = fract(p3 * vec3(.1031, .1030, .0973));
    p3 += dot(p3, p3.yxz+33.33);
    return fract((p3.xxy + p3.yxx)*p3.zyx);
}

float noise(vec3 p) // 3d perlin noise
{
    vec3 pi = floor(p), pf = fract(p), w = fade(pf);
    
    return mix( mix( mix( dot( hash(pi+vec3(0.0,0.0,0.0)), pf-vec3(0.0,0.0,0.0) ), 
                          dot( hash(pi+vec3(1.0,0.0,0.0)), pf-vec3(1.0,0.0,0.0) ), w.x),
                     mix( dot( hash(pi+vec3(0.0,1.0,0.0)), pf-vec3(0.0,1.0,0.0) ), 
                          dot( hash(pi+vec3(1.0,1.0,0.0)), pf-vec3(1.0,1.0,0.0) ), w.x), w.y),
                mix( mix( dot( hash(pi+vec3(0.0,0.0,1.0)), pf-vec3(0.0,0.0,1.0) ), 
                          dot( hash(pi+vec3(1.0,0.0,1.0)), pf-vec3(1.0,0.0,1.0) ), w.x),
                     mix( dot( hash(pi+vec3(0.0,1.0,1.0)), pf-vec3(0.0,1.0,1.0) ), 
                          dot( hash(pi+vec3(1.0,1.0,1.0)), pf-vec3(1.0,1.0,1.0) ), w.x), w.y), w.z);
}
```

## 4D Perlin Noise

```
vec4 hash(vec4 p4)
{
	p4 = fract(p4  * vec4(.1031, .1030, .0973, .1099));
    p4 += dot(p4, p4.wzxy+33.33);
    return fract((p4.xxyz+p4.yzzw)*p4.zywx);
}

float noise (vec4 p) // 4d perlin noise
{
	vec4 pi = floor(p), pf = fract(p), w = fade(pf);
    
    return mix( mix (mix (mix ( dot( hash(pi+vec4(0.0,0.0,0.0,0.0)), pf-vec4(0.0,0.0,0.0,0.0) ),
                                dot( hash(pi+vec4(1.0,0.0,0.0,0.0)), pf-vec4(1.0,0.0,0.0,0.0) ), w.x),
                          mix ( dot( hash(pi+vec4(0.0,1.0,0.0,0.0)), pf-vec4(0.0,1.0,0.0,0.0) ),
                                dot( hash(pi+vec4(1.0,1.0,0.0,0.0)), pf-vec4(1.0,1.0,0.0,0.0) ), w.x), w.y),
                     mix (mix ( dot( hash(pi+vec4(0.0,0.0,1.0,0.0)), pf-vec4(0.0,0.0,1.0,0.0) ),
                                dot( hash(pi+vec4(1.0,0.0,1.0,0.0)), pf-vec4(1.0,0.0,1.0,0.0) ), w.x),
                          mix ( dot( hash(pi+vec4(0.0,1.0,1.0,0.0)), pf-vec4(0.0,1.0,1.0,0.0) ),
                                dot( hash(pi+vec4(1.0,1.0,1.0,0.0)), pf-vec4(1.0,1.0,1.0,0.0) ), w.x), w.y), w.z),
                mix (mix (mix ( dot( hash(pi+vec4(0.0,0.0,0.0,1.0)), pf-vec4(0.0,0.0,0.0,1.0) ),
                                dot( hash(pi+vec4(1.0,0.0,0.0,1.0)), pf-vec4(1.0,0.0,0.0,1.0) ), w.x),
                          mix ( dot( hash(pi+vec4(0.0,1.0,0.0,1.0)), pf-vec4(0.0,1.0,0.0,1.0) ),
                                dot( hash(pi+vec4(1.0,1.0,0.0,1.0)), pf-vec4(1.0,1.0,0.0,1.0) ), w.x), w.y),
                     mix (mix ( dot( hash(pi+vec4(0.0,0.0,1.0,1.0)), pf-vec4(0.0,0.0,1.0,1.0) ),
                                dot( hash(pi+vec4(1.0,0.0,1.0,1.0)), pf-vec4(1.0,0.0,1.0,1.0) ), w.x),
                          mix ( dot( hash(pi+vec4(0.0,1.0,1.0,1.0)), pf-vec4(0.0,1.0,1.0,1.0) ),
                                dot( hash(pi+vec4(1.0,1.0,1.0,1.0)), pf-vec4(1.0,1.0,1.0,1.0) ), w.x), w.y), w.z), w.w);
}
```
