---
layout: page
title: "How to use VMD to make quality images"
permalink: /vmd-image-tutorial/
footer: true
project: true
---

To start rendering images in VMD you only need two things:

1. VMD, which you can obtain 
[here](https://www.ks.uiuc.edu/Development/Download/download.cgi?PackageName=VMD)
2. A coordinate file of the thing you want to render an image of

Here I use VMD version 1.9.4 and ZAR1 resistosome, PDB ID: 6J5T, for the coordinates. 
Additionally, we will only use the VMD TkConsole to make the image. The image can be made with
the GUI, but using the Tcl scripting language 
that is packaged with VMD is very powerful and is worthwhile to learn.

Image Making Recipe:
1. Open the TkConsole if not alreay open.
	- On the VMD Main window click on the *Extensions* drop down tab. 
	Then click on the *Tk Console* option from the drop down tab.
	{% include image.html file="/assets/vmd_main.PNG" w="300" %}
	- Note that any GUI window can be opened/closed, and translated
	with the Tk Console
	[`menu`](http://www.ks.uiuc.edu/Research/vmd/current/ug/node139.html) command.
2. Load the coordinates from the [RCSB Protein Data Bank](https://www.rcsb.org/) 
with 
```
mol pdbload 6j5t
```
{% include image.html file="/assets/zar1_loaded.PNG" w="300" %}
3. Let's change the drawing style to QuickSurf and the color to white of the
current representation with
```
mol modstyle 0 0 QuickSurf 0.700000 0.400000 1.200000 1.000000
mol modcolor 0 0 ColorID 8
```
4. Let's change the material of the representation with
```
material add copy AOShiny
material change Outline Material23 2.00
material change OutlineWidth Material23 0.25
mol modmaterial 0 0 Material23
``` 
After steps 3 and 4, the molecule will look like this:
{% include image.html file="/assets/zar1_processed.PNG" w="300" %}
5. Lastly, we render an image with 
```
render TachyonInternal snapshot.bmp
```

This produces the final image:
{% include image.html file="/assets/snapshot.bmp" w="300" %}
There are many other possibilities to explore, for example this 
[wiki](https://github.com/skblnw/mkvmd_render) can provide some ideas.


