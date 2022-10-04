---
layout: post
title:  learning from AI
date:   2022-10-04
description: taking CodeWhisperer for a spin
tags: favorites
categories: programming
---
*tl;dr - CodeWhisperer is a decent tool for a beginner/intermediate coder like myself to quickly write generic functions using common packages or APIs, and maybe to learn how to better comment and structure code too*

I just got my preview code from Amazon and was excited to take [CodeWhisperer](https://blog.symops.com/2022/08/31/amazon-codewhisperer/) for a spin. Here's what happened.


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

I deleted my function from the existing code, and then provided this slightly clearer comment as a prompt for CW:
{% highlight python %}
# a function that takes an image path as an argument and returns a base64 version of the image
{% endhighlight %}
I called for suggestions (ALT+C) and got:
{% highlight python %}
def encode_image(image_path):
    with open(image_path, 'rb') as img_file:
        return base64.b64encode(img_file.read())
{% endhighlight %}
Nailed it. Getting more specific:
{% highlight python %}
# a function that takes an image path as an argument and returns an html image
# containing the base64 version of the image

def img_to_html(img_path):
    """
    Takes an image path as an argument and returns an html image
    containing the base64 version of the image
    """
    with open(img_path, 'rb') as img_file:
        encoded_string = base64.b64encode(img_file.read())
    return '<img src="data:image/jpeg;base64, {}">'.format(encoded_string.decode()) 
{% endhighlight %}
Super. This actually improved on my code by using a method I wasn't aware of - `decode()` - to get rid of a couple little tags which I had stumbled over yesterday and resolved via string indexing. 

And one more ask:
{% highlight python %}
# a function that takes an image path and dimensions as arguments and returns an html image
# containing the base64 version of the image that can be used in a web page

def make_img_tag(img_path, width, height):
    # open the image
    with open(img_path, 'rb') as image_file:
        encoded_string = base64.b64encode(image_file.read())
    # create the html tag
    img_tag = f'<img src="data:image/jpeg;base64,{encoded_string.decode()}" width="{width}" height="{height}"/>'
    return img_tag
{% endhighlight %} 

Pretty much identical with my code. All of these were the first suggestion CW provided, though you can easily flip through more suggestions with the arrow keys. Flipping through the suggestions shows some interesting differences in commenting and style. This could be due to the variation within my script or the training data. How about f-strings every time, please?

Taking it a step beyond what I had initially written (only jpg in my project):
{% highlight python %}
# a function that takes an image path and dimensions as arguments and returns an html image
# containing the base64 version of the image and correct image type that can be used in a web page

def make_img_tag(img_path, width, height):
    # open the image
    img = Image.open(img_path)
    # set the image dimensions
    img.thumbnail((width, height))
    # convert the image to base64
    img_str = base64.b64encode(img.tobytes())
    # create the html image tag
    img_tag = '<img src="data:image/png;base64, {}">'.format(img_str)
    return img_tag 
{% endhighlight %} 
No dice. At first. But then I added (jpeg or png) to the comment, flipped through a few suggestions, and voila: 
{% highlight python %}
# a function that takes an image path and dimensions as arguments and returns an html image
# containing the base64 version of the image and correct image type (jpeg or png) that can be used in a web page

def make_img_tag(img_path, width, height):
    # get the file extension
    ext = os.path.splitext(img_path)[1]
    # make the base64 version of the image
    with open(img_path, 'rb') as image_file:
        encoded_string = base64.b64encode(image_file.read())
    # make the html image tag
    img_tag = '<img src="data:image/{};base64,{}" width="{}" height="{}"/>'.format(ext[1:],
                                                                                  encoded_string.decode('utf-8'),
                                                                                  width,
                                                                                  height)
    return img_tag
{% endhighlight %} 
I guess I will have to forgive the `format()` method. Maybe if I had always used f-strings in my script, then the suggestion would have done so as well?

---

## How about some arcpy? 

The little bit of reading I did on CW explains that it is trained on a “variety of data sources including Amazon open source code.” Since geospatial and arcpy are a little more niche, I thought it would be interesting to see how CW performed here.

I started with this comment block in a new file:
{% highlight python %}
'''
Open an ArcGIS Pro project and create a new feature class
'''
{% endhighlight %} 
Which led CW to give me these lines one at a time:
{% highlight python %}
import arcpy

# Set the workspace

arcpy.env.workspace = r"C:\Users\bwc\Documents\ArcGIS\Projects\TestProject'

# Create a new feature class

arcpy.CreateFeatureclass_management(r"C:\Users\bwc\Documents\ArcGIS\Projects\TestProject'","test_fc", "POINT", "", "", "", "DISABLED", "DISABLED", "")
{% endhighlight %} 
Not too shabby. Repeating the process it was funny to see the different filler filepaths that were suggested. 

Let’s try and be more specific, modifying only the last comment:
{% highlight python %}
# Create a new polygon feature class, with fields for 'date' and 'number of cats spotted'
{% endhighlight %} 
I got:
{% highlight python %}
arcpy.CreateFeatureclass_management("C:/Temp/donnees.gdb", "cats", "POLYGON", "", "", "", "date", "number of cats spotted")
{% endhighlight %} 
Hmm not so sure those last couple arguments would do what we wanted. Maybe if this was inside a function? (prompt was the same but I added ‘a function to’ at the start)
{% highlight python %}
def create_fc(fc_name, fc_type):
    # create a new feature class
    arcpy.CreateFeatureclass_management(r"C:\Users\bwc\Documents\ArcGIS\Projects\TestProject'","test_fc", fc_type)
    # add fields
    arcpy.AddField_management("test_fc", "date", "TEXT")
    arcpy.AddField_management("test_fc", "number_of_cats_spotted", "SHORT")
    # return the path to the new feature class
    return arcpy.Describe("test_fc").catalogPath
{% endhighlight %} 
Pretty nice. I like that it didn’t include all the optional arguments in `CreateFeatureclass()`. But why not use `DATE` as the field type for a field called date? 

---

## How about jumping into a larger arcpy script?
{% highlight python %}
# 
{% endhighlight %} 
Alas
{% include figure.html path="assets/img/20221004084252.png" title="" class="img-fluid rounded z-depth-1" %}  
What if we try in a new file? 
Prompt:
{% highlight python %}
import arcpy

# a function to project a feature class to Web Mercator before publishing
{% endhighlight %} 
Suggestion:
{% highlight python %}
def projectFeatureClass(inFC, outFC):
    # get the coordinate system of the input feature class
    inDesc = arcpy.Describe(inFC)
    inCS = inDesc.spatialReference
    # get the coordinate system of the output feature class
    outCS = arcpy.SpatialReference(3857)
    # project the input feature class to Web Mercator
    arcpy.Project_management(inFC, outFC, outCS)
    # return the projected feature class
    return outFC
{% endhighlight %} 
My first thought was that is no need to get the input spatial reference
Flipping through a few suggestions, I found
{% highlight python %}
def project_fc(fc, out_fc):
    """
    Project a feature class to Web Mercator.

    Parameters
    ----------
    fc : str
        Path to the input feature class.
    out_fc : str
        Path to the output feature class.
    sr : str
        Spatial reference of the output feature class.

    Returns
    -------
    None

    """
    # get the coordinate system of the input feature class
    in_sr = arcpy.Describe(fc).spatialReference
    # if the input coordinate system is already Web Mercator, return
    if in_sr.name == "WGS_1984_Web_Mercator_Auxiliary_Sphere":
        return
    # otherwise, project the feature class to Web Mercator
    arcpy.Project_management(fc, out_fc)
    # set the coordinate system of the output feature class to Web Mercator
    arcpy.DefineProjection_management(out_fc)
    # return
    return
{% endhighlight %} 
The very rigorous style used here is a little funny because of the inclusion of blank return statements. But wait - now I see why there was the inclination to check out in the initial spatial reference (duh!). Learning from AI… this is interesting. 
