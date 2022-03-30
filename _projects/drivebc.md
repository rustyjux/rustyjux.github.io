---
layout: page
title: drivebc to pdf
description: produce maps in arcpy from scraped data
img: assets/img/1.jpg
importance: 1
category: work
github: https://github.com/rustyjux/drive-bc
---

This was the assigned final project for GIS 329, Principles of Programming. The assigned task required downloading data from DriveBC and converting into ArcGIS feature classes using ArcPy. I made use of Pandas to manipulate the scaped data before defining feature geometries. I also extended this project by adding script to export a set of layouts using arcpy.mp, based upon a pre-built layout.

While the map outputs are relatively unimpressive, the Python script shows good attention to writing clear, easily interpreted code, with appropriate use of comments and print statements. 

[GitHub repo](https://github.com/rustyjux/drive-bc) 

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        <img src="/assets/img/drivebc_request.jpg" title="Get data from DriveBC" class="img-fluid rounded z-depth-1" data-zoomable/>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        <img src="/assets/img/drivebc_arcpy.jpg" title="Write feature classes" class="img-fluid rounded z-depth-1" data-zoomable/>
    </div>
    <div class="col-sm mt-3 mt-md-0">
        <img src="/assets/img/drivebc_out.jpg" title="Output example" class="img-fluid rounded z-depth-1" data-zoomable/>
    </div>
</div>
<div class="caption">
    First you get the data, then you write the feature classes, then you export the maps. Capisce?
</div>