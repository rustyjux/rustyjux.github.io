---
layout: post
title:  learning from AI
date:   2022-10-04
description: taking CodeWhisperer for a spin
tags: favorites
categories: programming
---
### *tl;dr - it's a great tool to quickly write generic functions using common packages or APIs, and maybe to learn how to better comment and structure code too*

I just got my preview code from Amazon and was excited to take [CodeWhisperer](https://blog.symops.com/2022/08/31/amazon-codewhisperer/) for a spin.
(apologies for not embedding code directly in this post - next time!)

## Round one
Yesterday I wrote this little function while working on a script that generates HTML reports from field data captured with Esri's [Survey123](https://www.esri.com/en-us/arcgis/products/arcgis-survey123/overview?rsource=%2Fen-us%2Farcgis%2Fproducts%2Fsurvey123%2Foverview):
{% highlight python %}
# take img path an return an html image tag with base64 data
def j64(src, width='', height=''):
    with open(src, "rb") as image_file:
        encoded_string = base64.b64encode(image_file.read())
    img = '<img src="data:image/jpg;base64, ' + str(encoded_string)[2:-1] + f'" width="{width}" height="{height}" />'
    return img
{% endhighlight %}

Some context: I was looking to generate a single html file for each report to simplify archiving. Reports include images and I was looking to embed them. Exporting html to pdf would do this but brings in formatting issues (which is why I switched to html from the original .docx output used in the script I modified) and further dependency requirements (potentially problematic when running on our Jenkins server). [base64](https://codepen.io/jamiekane/pen/YayWOa) allows you to embed image binary data directly with a giant text string.

I deleted my function from the code (~600 lines), and then provided this slightly clearer comment as a prompt for CW:
{% include figure.html path="assets/img/20221004083536.png" title="" class="img-fluid rounded z-depth-1" %}  
I called for suggestions (ALT+C) and got:
{% include figure.html path="assets/img/20221004083521.png" title="" class="img-fluid rounded z-depth-1" %}  
Nailed it. Getting more specific:
{% include figure.html path="assets/img/20221004083617.png" title="" class="img-fluid rounded z-depth-1" %}  
Super. And one more ask:
{% include figure.html path="assets/img/20221004083645.png" title="" class="img-fluid rounded z-depth-1" %}  

Pretty much identical with my code. Nice work! All of these were the first suggestion CW provided, though you can easily flip through more suggestions with the arrow keys.

Taking it a step beyond what I had initially written (only jpg in my project):
{% include figure.html path="assets/img/20221004083843.png" title="" class="img-fluid rounded z-depth-1" %}  
No dice! At first, CW wouldn’t provide suggestions (it gives a ‘No suggestions’ message). Then I added the (ie jpg, png) bit and it didn’t do what I asked. Ah well. More time refining the request might have helped.

Thoughts: It didn't take too long 

---

## How about some arcpy? 

The little bit of reading I did on CW explains that it is trained on a “variety of data sources including Amazon open source code.” Since geospatial and arcpy are a little more niche, I thought it would be interesting to see how CW performed here.

I started with this comment block:
{% include figure.html path="assets/img/20221004083950.png" title="" class="img-fluid rounded z-depth-1" %}  
Which had CW lead me through these lines one at a time:
{% include figure.html path="assets/img/20221004084005.png" title="" class="img-fluid rounded z-depth-1" %}  
Not too shabby. Let’s try and get more specific:
{% include figure.html path="assets/img/20221004084021.png" title="" class="img-fluid rounded z-depth-1" %}  
I got:
{% include figure.html path="assets/img/20221004084038.png" title="" class="img-fluid rounded z-depth-1" %}  
Good feature class, but no fields. Maybe if this was inside a function? (prompt was the same but I added ‘a function to’ at the start)
{% include figure.html path="assets/img/20221004084059.png" title="" class="img-fluid rounded z-depth-1" %}  
Very nice! I like that it didn’t include all the optional arguments in CreateFeatureclass()

---

## How about jumping into a larger arcpy script?
{% include figure.html path="assets/img/20221004084240.png" title="" class="img-fluid rounded z-depth-1" %}  
Alas
{% include figure.html path="assets/img/20221004084252.png" title="" class="img-fluid rounded z-depth-1" %}  
What if we try in a new file? 
Prompt:
{% include figure.html path="assets/img/20221004084306.png" title="" class="img-fluid rounded z-depth-1" %}  
Suggestions:
#1
{% include figure.html path="assets/img/20221004084320.png" title="" class="img-fluid rounded z-depth-1" %}  
A few weird things – no need to get the input spatial reference in lines 6-7, why delete an attribute? (lines 11-12), and then the use of DefineProjection() (lines 14-15) which is for when data has no defined spatial reference (had to look that one up).
How about the next suggestion?
#2
{% include figure.html path="assets/img/20221004084334.png" title="" class="img-fluid rounded z-depth-1" %}  
Better!
#5
{% include figure.html path="assets/img/20221004084348.png" title="" class="img-fluid rounded z-depth-1" %}  
WoW! Now I see why there was the inclination to check out in the initial spatial reference (duh!) Learning from AI… this is interesting.