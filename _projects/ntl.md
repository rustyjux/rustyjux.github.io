---
layout: page
title: a light in the dark
description: using google earth to predict electrification from nighttime lights data
img: assets/img/ntl_thumb.png
importance: 1
category: work
---

Access to electricity is essential not just for lighting, communication, and basic home appliances, but also the potential for enterprise and greater development. Alas, over 500 million people lack access to electricity in sub-Saharan Africa. 

Compounding the challenge of expanding access to electricity is the lack of comprehensive data on the subject. To attempt to predict the extent of electrification based on Nighttime Lights data, I trained a supervised classification model. 

The most exciting part of this project for me was learning to use Google Earth Engine to easily wrangle large datasets and quickly perform analyses. This was my first foray into GEE, as well as my first time writing JavaScript.

[GEE Code Editor](https://code.earthengine.google.com/de1392aeb6e9ea99f20c86c271f87742) (sign-in required)

<div class="row justify-content-sm-center">
    <div class="col-sm-7 mt-3 mt-md-0">
        <video width="100%" controls>
            <source type="video/mp4" src="/assets/img/ntl_postcomp.mp4">
        </video>
    </div>
    <div class="col-sm-5 mt-3 mt-md-0">
        <img src="/assets/img/ntl_annual.jpg" title="Annual outputs" class="img-fluid rounded z-depth-1" data-zoomable/>
    </div>
</div>
<div class="caption">
    Animation showing year to year change (left) and annual electrified area outputs (right)
</div>