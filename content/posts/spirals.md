---
title: "Let's make some spirals"
date: 2018-06-07T15:18:22+10:00
draft: false
categories: ["computation"]
tags: ["math", "hack", "code"]
---
[Disk point picking](http://mathworld.wolfram.com/DiskPointPicking.html) is an elementary problem in geometry. To evenly distribute points on a disk, an intuitive idea is to sample pairs of numbers in polar coordinate (radius and angle) randomly from uniform distributions. After all, "uniformly" is just another word for "evenly", isn't it? But it doesn't work. When the points are sampled this way, the density of the dots fall off linearly with the distance to the origin. This is because the area element of the polar coordinate grows linearly with the radius. With a little calculus, it can be shown that this uneven density can be corrected by taking the square root of the uniformly-sampled radius.

However, I ran into a situation where I do want the density to fall off, and I want to control the gradient of the fall-off. In addition, I also want the points distributed regularly, rather than randomly. With a little hack, I came up with this little animation:

{{< figure src="/blog/spiral.gif">}}

The spiral pattern was created with the Vogel method. The formula for varying the density was based on a technical report ([Point Picking and Distributing on the Disc and Sphere](http://www.arl.army.mil/arlreports/2015/ARL-TR-7333.pdf)) by Mary Arthur.

Here's the python code that I used:

{{< highlight python >}}
import numpy as np

def spiral(c, r0, r1, n, shuffleP=True):
    """
        Make a spiral with n points using Vogel's method.
        the radius ranges from r0 to r1
        the density of the points falls off according to r^c, where r is radius
        c should be a negative number
    """

    def f(r):
        return np.sqrt(np.power(r, 1.0-c)/(1.0-c))

    n0 = np.power(r0 * r0 * (1.0-c), 1.0/(1.0-c))
    n1 = np.power(r1 * r1 * (1.0-c), 1.0/(1.0-c))

    lst = []
    for k in range(1, n+1):
        d = f( (n1-n0)/(n-1)*k + n0-(n1-n0)/(n-1) )
        theta = 3.1415926 * (3.0 - np.sqrt(5.0)) * (k-1.0)
        x = d * np.cos(theta)
        y = d * np.sin(theta)
        lst.append((x,y))
    res = np.asarray(lst, dtype=np.float64)
    if shuffleP:
        np.random.shuffle(res)
    return res

zz = spiral(-0.5, 0.2, 40, 500)
{{< / highlight >}}
