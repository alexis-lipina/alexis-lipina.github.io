---
layout: post
title: Starwave
description: VR Rhythm Game
image: assets/images/starwave-banner.jpg
---

<div style="position:relative;padding-top:56.25%;">
    <iframe src="https://www.youtube.com/embed/T81YeSQyu7I" frameborder="0" allowfullscreen
      style="position:absolute;top:0;left:0;width:100%;height:100%;"></iframe>
</div>

Starwave is a VR rhythm game that was released on Quest 2 & 3 on September 5th, 2024. I worked on the game from October 2023 to June 2024, plus a week in August to help fix some emergency performance issues pre-ship. I was initially brought on board as a gameplay programmer, but saw an urgent need for VFX improvements, graphical performance problems and an opening to apply my learnings & passion for tech art.

# Optimization
As technical artist for a mobile VR title, reconciling the platform’s hardware limitations with our aesthetic goals was always a concern, especially in the months leading up to release where there was both an increased pressure to impress and urgent framerate targets to hit. 

One avenue for improving performance was finding and reducing overdraw / transparency coverage wherever possible. UE’s optimization viewmodes were extremely useful in tracking down not only what objects are most expensive to render on their own, but also finding assets with large overdraw footprints that could easily be reduced without any significant penalty to visual fidelity. Below is an example where I found that two-sided rendering was active on a translucent object, causing a ton of gross overdraw.

![test image]({{ site.url | absolute_path}}/assets/images/ShaderOptimizationViewmode.png)

### Example : Supernova
For an example of my process, below is a “supernova”, our most common gameplay element. When I first joined the project, this asset consisted of the central opaque sphere mesh, a horizontal Translucent glow flare, a vertical Translucent glow flare, and a large Translucent “targeting reticle”. When our game is at its busiest, there are often a dozen of these coming at you from center-distance, resulting in a lot of overdraw. 

- Combined two flare billboards into a single flare by essentially merging their materials
- Change blend mode for flares to be **Additive instead of Translucent** (additive transparency works great for glow FX and importantly is order-independent - a lot cheaper than Translucent blend mode in VR)
- Turned a big translucent square "reticle" into a masked opaque material with no changes to appearance
- Cut down on a ton of empty space in the flare billboard material by just **skewing the billboard 45 degrees** (better coverage for the cross shape) and **rescaling the material & billboard** to ensure the effect size is roughly the same. I did need to fiddle with the material a bit to ensure all the gradients tapered to 0 before the edge of the quad to avoid visible clipping. Below you can see the old and the new billboards & their footprints. ![test image]({{ site.url | absolute_path}}/assets/images/supernova_overdraw_footprint.png)

<!--
### Cert Deadline Emergency
After being staffed on a different project for several month I was asked to jump back on to help diagnose some performance issues that were causing the project to fail Meta's certification on Quest 2. The game needed to run at a steady 72 FPS, and in many cases during intense gameplay moments it was dipping as low as 30. Cert deadline was a week away and the project needed help fast.

(INSERT IMAGE 4 : video or screenshot of poor performance w/ FPS counter)

Thankfully I was familiar with the project and dfjsdakljfsdaklfjdsaklf
- Fixed planet glows to not be enormous billboards (ugly AND tons of overdraw) but instead be nice additive blended spheres
- Remove high resolution texture lookups in skybox, stardust trails that were not contributing significantly to aesthetics
- converted a particularly expensive fullscreen Wormhole transition visual effect from several layers of translucency to a blend of masked & additive materials (possibly show a before and after?)
- Reduce particle counts for xyz
- what else did I do?-->

### Example : Planet Glows
Level designers can spawn planets with different apprearances and locations during a given song. Planets had a very large glowing "atmosphere" billboard which happened to also be causing a lot of overdraw. There are also some cosmetic issues with using billboards for this kind of effect - it is hard to enmesh with the planet surface without very ad-hoc material work done to blend both, default billboards in VR visibly swing around meshes when you turn your head, etc. 

To remedy these issues I made a spherical atmosphere shader to replace the billboard one. This has a much smaller overdraw footprint *and* more closely mimics the atmospheric scattering people expect to see around planets. I use a fresnel effect to blend color & opacity to hide the silhouette of the mesh & give a subtle dark blue-ish tint to upper-atmosphere parts so the glow's hue isn't so monotone. It's rare to be able to please both art direction and QA with one change so this felt great to pull off.

![test image]({{ site.url | absolute_path}}/assets/images/PlanetComparison.png)


# VFX
Especially earlier in my tenure on this project, a lot of my work was creating & iterating on the look and feel of different objects in the game. Working closely with design & creative direction to solve design problems while working within the visual style of the game is one of my favorite problem spaces to work in. 

...

Section under construction, sorry! 
<!--
### CONSTELLATIONS
Constellations are large objects in the distance that build over the course of the level as you score points and flash into existence. They saw a complete visual revamp during my time on the project & went through several rounds of iteration for visual improvements, reactivity to gameplay, performance, etc.

- talk about stardust bursts that seek the currently-building star
- points and lines VFX
- before & after images
- maybe some earlier wip images
- show the color & texture change ability for material instances before that was scrapped

### STARDUST TRAILS
Stardust Trails are curved paths of stardust energy that the player passes their wands through to collect. This was probably the gameplay element that I spent the most time on, as there were several iterative cycles in collaboration with design to ensure it looks good when inert and when interacted with, and to ensure it responds visually to indicate how well you're interacting with it. Communicating any amount of gameplay information as it flies past you and is only visible for a few seconds was a very difficult challenge. 

- before & after image / video of effect when I joined vs after
- gif/video of a long trail showing waving wand vs not waving


### SKYBOX PULSES
During development we considered a visual effect that causes the sky to pulse & could tie into our audio systems to play pulses on beats. The effect didn't make it into the final product as its performance overhead was too great and there wasn't time enough to improve its performance & curate it for each level, but it was a fun effect to work on and looks pretty cool!

- talk about the ways in which it was modular & extensible
- video of the pulse, maybe some different variations of the effect to show its flexibility

### COMETS
Comets were an existing feature that required a visual upgrade to match some of are new aesthetic goals

- "charge meter"
- rainbowification when charged
- tail changed from static mesh to spline trail
- changed bob/sway to have a bit more random energy to it -->