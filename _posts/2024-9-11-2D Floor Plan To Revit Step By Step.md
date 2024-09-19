---
layout:       post
title:        "Converting 2D Floor Plan To Revit Step By Step"
author:       "Eric"
header-style: text
catalog:      true
tags:
    - Python
    - CV
    - Revit API
    - C#
---

> This is a personal project to study and practice converting 2D floor plan to a Revit.

## Home Sweet Home ##
![Demo](/img/myhome.png)

This is my little cozy home in Nanjing, China.

## OpenCV Processing ##
Contour Detection
![Demo](/img/cv-contour.png)

Wall Detection
![Demo](/img/cv-walls.png)

Rooms Detection
![Demo](/img/cv-rooms.png)

## CNN Detection ##
Room Detection
![Demo](/img/room_segmentation.png)

Elements Detection 
![Demo](/img/icon_segmentation.png)

## Coordination Extraction ##
Here, I get the coordination of the walls, doors, windows, etc. 

### Walls ###
Wall 1 Polygon Coordinates:

[[ 10 876]
 [ 39 876]
 [ 39 892]
 [ 10 892]]
 
Wall 2 Polygon Coordinates:

[[518 346]
 [526 346]
 [526 438]
 [518 438]]
 
Wall 3 Polygon Coordinates:

[[199 876]
 [238 876]
 [238 892]
 [199 892]]
 
……

### Doors ###
Door 1 Polygon Coordinates:

[[162 568]
 [170 568]
 [170 618]
 [162 618]]
 
Door 2 Polygon Coordinates:

[[273 252]
 [287 252]
 [287 301]
 [273 301]]
 
Door 3 Polygon Coordinates:

[[663 550]
 [714 550]
 [714 558]
 [663 558]]
 
Door 4 Polygon Coordinates:

[[536 522]
 [587 522]
 [587 530]
 [536 530]]
 
……

### Windows ###
Window 1 Polygon Coordinates:

[[543 876]
 [746 876]
 [746 892]
 [543 892]]
 
Window 2 Polygon Coordinates:

[[790 452]
 [806 452]
 [806 511]
 [790 511]]
 
Window 3 Polygon Coordinates:

[[515 892]
 [531 892]
 [531 952]
 [515 952]]
 
 ……

### Label ###
I can label the coordination information on an image!
![Demo](/img/polygon_label.png)

There we go! Coordinates of the walls, windows, doors are extracted and saved in JSON files. 
![Demo](/img/coordinates.png)


## Revit API ##
It's time to do some calculation and use Revit API to generate a revit model ;)

Lots of time were spent on configuring the .NET development environments and also the startup REVIT API lessons.

Here, I can click button on the External Tools button in Revit, and my developed addin program will run (coded with C# in Visual Studio).  
![Demo](/img/addin.png)

And then, with only ONE click, BOOM! Here come the Revit floor plan generated (only walls for now)!
![Demo](/img/revit-floorplan.png)

Also we can check this model in 3D.
![Demo](/img/revit-3d-rough.png)

I will work a little bit more to make it better...

To be continued…
