---
title: "The retinotopic map of marmoset V1"
date: 2016-05-07T18:51:42+11:00
draft: false
category: [neuroscience]
tags: ["vision", "visualization", "data", "brain mapping"]
thumbnail: "/blog/v1-map.png"
---

{{< figure src="/blog/v1-map.png">}}


I created this figure to illustrate the retinotopic map of the primary visual cortex of the marmoset. The eccentricity contours are plotted in red, and polar angles are in blue. It's very easy to create it in 3D so that you can rotate it around and see it from different angles. This is how you do it:

1. Download [ParaView](https://www.paraview.org), the open source 3D data visualization package.

2. Download the published retinotopic data ([Chaplin, Yu & Rosa, 2013](http://onlinelibrary.wiley.com/doi/10.1002/cne.23215/abstract;jsessionid=785ECD35494A9B810E6AC757C05C4452.f04t02)).  The relevant file is [CNE_23215_sm_SuppDataS5.vtk](http://onlinelibrary.wiley.com/store/10.1002/cne.23215/asset/supinfo/CNE_23215_sm_SuppDataS5.vtk?v=1&s=a3f4021122cf3fe8ad659b7c008a427e2c027a5f).

3. Open the .vtk file in ParaView. Use solid colour to display the cortical surface of V1.

4. Select the .vtk file in the Pipeline Browser, then apply the Contour filter (there is a contour icon just above the Pipeline Browser). For the "Contour By" option, choose "Double_Sech_Polar_angle". Enter a range of polar angle values in the "Isosurfaces" panel (0, 30, 60, -30, -60). Set Representation to "Wireframe" and then adjust the styles of the wireframe (set Line Width to 3). Click "Apply". ParaView automatically makes the main surface invisible. Don't worry, just make it visible again by clicking on the eye icon in the Pipeline Browser.

5. Apply a second Contour filter to the surface (that is, first select the .vtk file, then click on the Contours icon). This time, use "Double_Sech_Eccentricity" for contours.
