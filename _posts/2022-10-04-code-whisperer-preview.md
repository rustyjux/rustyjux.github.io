---
layout: post
title:  learning from AI
date:   2022-10-04
description: taking CodeWhisperer for a spin
tags: favorites
categories: programming
---
Just got my preview code from Amazon - time for a quick test of [CodeWhisperer](https://blog.symops.com/2022/08/31/amazon-codewhisperer/).

## Round one
Yesterday I wrote this little function while working on a reporting script:
![prompt](assets/img/20221004083419.png)  
*Context: I was looking to generate a single file for each report. Exporting html to pdf would do this but brings in formatting issues (which is why I switched to html from the original .docx used in the script I modified). base64 allows you to embed image data directly.*

I deleted my function from the code (~600 lines), and then provided this slightly clearer comment:
![](assets/img/20221004083536.png)  
I called for suggestions (ALT+C) and got:
![](assets/img/20221004083521.png)  
Nailed it. Getting more specific:
![](assets/img/20221004083617.png)  
Super. And one more ask:
![](assets/img/20221004083645.png)  

Pretty much identical with my code. Nice work! All of these were the first suggestion CW provided, though you can flip through other suggestions.

Taking it a step beyond what I had initially written (only jpg in my project):
![](assets/img/20221004083843.png)  
No dice! At first, CW wouldn’t provide suggestions (it gives a ‘No suggestions’ message). Then I added the (ie jpg, png) bit and it didn’t do what I asked. Ah well. More time refining the request might have helped

---

## How about some arcpy? 

I started with this comment block:
![](assets/img/20221004083950.png)  
Which had CW lead me through these lines one at a time:
![](assets/img/20221004084005.png)  
Not too shabby – let’s try and get more specific:
![](assets/img/20221004084021.png)  
I got:
![](assets/img/20221004084038.png)  
Good feature class, but no fields. Maybe if this was inside a function? (prompt was the same but I added ‘a function to’ at the start)
![](assets/img/20221004084059.png)  
Very nice! I like that it didn’t include all the optional arguments in CreateFeatureclass()

---

## How about jumping into a larger arcpy script?
![](assets/img/20221004084240.png)  
![](assets/img/20221004084252.png)  
What if we try in a new file? 
Prompt:
![](assets/img/20221004084306.png)  
Suggestions:
#1
![](assets/img/20221004084320.png)  
A few weird things – no need to get the input spatial reference in lines 6-7, why delete an attribute? (lines 11-12), and then the use of DefineProjection() (lines 14-15) which is for when data has no defined spatial reference (had to look that one up).
How about the next suggestion?
#2
![](assets/img/20221004084334.png)  
Better!
#5
![](assets/img/20221004084348.png)  
WoW! Now I see why there was the inclination to check out in the initial spatial reference (duh!) Learning from AI… this is interesting.
