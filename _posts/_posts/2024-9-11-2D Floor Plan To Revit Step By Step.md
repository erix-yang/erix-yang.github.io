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
It's time to use Revit API to generate a revit model ;)

