---
layout:       post
title:        "Generating 2D Floor Plan Using Deep Learning"
author:       "Eric"
header-style: text
catalog:      true
tags:
    - Python
    - CV
    - CNN
    - GAN
    - Tensorflow
---

> This is a project to explore how to generate 2D floor plan using different methods.

## Archi-GAN ##
The 1st method I tried is called Archi-GAN, an method posted in 2019.
![Demo](/img/t2d-archi.png)

### Dataset ###
TELL2DESIGN--This is from a research <Tell2Design: A Dataset for Language-Guided Floor Plan Generation> in 2023.
![Demo](/img/t2d-data-look.png)
![Demo](/img/t2d-input-data-example.png)

### Process ###
Load Data --> Resize & Pre-process --> Split to train & test sets --> Train Model (/w 200 epoches & 500 epoches) --> Predict

### Test Record ###
![Demo](/img/t2d-train-test.png)
![Demo](/img/t2d-train-time.png)
With 200 epoches, it cost 250 minutes. With 500 epoches, it cost 700 minutes. (Maybe I need a faster computer)

### Generated Floor Plan ###
![Demo](/img/t2d-test1.png)
![Demo](/img/t2d-test2.png)
![Demo](/img/t2d-test2.png)

As shown in the outputs, the results are really poor :(
I'll try some other methods later.
