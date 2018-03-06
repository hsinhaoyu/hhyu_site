---
title: "The marmoset visual area DM for dummies"
date: 2016-05-16T20:07:26+11:00
draft: false
categories: ["science"]
tags: ["brain mapping", "visualization", "vision"]
---
{{< figure src="/blog/dmmap1.jpg">}}

The visuotopic map of the marmoset dorsomedial visual cortex (area DM; see references) is sufficiently complex that it is probably worthwhile to design a few visual aides to make it more intuitive. The figure above is my first attempt. In this figure, DM is divided into three segments, with the most medial segment representing central upper visual field (nose and eye of Sir Isaac Newton), the middle segment representing the entire lower visual field, and the medial segment representing the peripheral upper visual field (the forehead and hair of Newton).

It's a useful figure that conveys the general configuration of the visuotopic map, but it doesn't faithfully reproduce the locations of the horizontal meridian (HM) and the vertical meridian (VM) because the images are not non-linearly "warped". I wrote a simple program that tessellates the images into polygons, and I manually moved the vertices of the polygons around. Now we have a figure showing how different segments of the map are spatially related to each other.

{{< figure src="/blog/dmmap2.jpg">}}

Note: Using the likeness of Sir Isaac Newton to illustrate visuotopic map was inspired by Wandell et al. (2007) Visual field map in human cortex. Neuron 56, 366-383.

### References
- Rosa & Schmid (1995) Visual areas in the dorsal and medial extrastriate cortices of the marmoset. J. Comp. Neurol. 359, 272-299.
- Rosa et al. (2005) Resolving the organization of the new world monkey third visual complex: the dorsal extrastriate cortex of the marmoset (Callithrix bacchus). J. Comp. Neurol. 483, 161-191.
- Angelucci & Rosa (2015) Resolving the organization of the third tier visual cortex in primates: a hypothesis-based approach. Vis. Neurosci. 32, e10.
