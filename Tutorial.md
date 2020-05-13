# Introduction
This python3 exercise is about anonymizing extracting information from pictures. 

## Learn how to ...
 - Extract geolocation data from pictures
 - Visualize the geolocation data on Google Maps
 - Enrich the visualization with more information from pictures

## Preperation

### Step 1: Download the pictures
Please download the pictures from `RESOURCES` to /home/hacker/Downloads

### Step 2
Please run the following commands (e.g. Hacking-Lab LiveCD) and setup your python3 environment.

```
mkdir -p /opt/git
cd /opt/git
git clone https://github.com/ibuetler/p3s-exif-analytics.git
cd /opt/git/p3s-exif-analytics
pipenv --python 3 sync
pipenv --python 3 shell
```

## Task: Extract geolocation data from pictures

In this task our goal is first to receive the geolocation data from the given pictures. 

### Theory about Pillow

The Python Imaging Library, or PIL for short, is one of the core libraries for image manipulation in Python. Unfortunately, its development has stagnated, with its last release in 2009. Hovewer, there is an fork of the old library called Pillow (PIL as well for short) and it supports Python 3, wehereas the old library is only for Python 2.

For the time being we are interested in two imports from the library. These are the following two:

```python
from PIL import Image
from PIL.ExifTags import TAGS
```
With the image import it is possible to open an image, for this the library offers the open() function. This function takes the path to the image and opens it. Here is an example how to open an image:

```python
from PIL import Image
from PIL.ExifTags import TAGS

img = Image.open('Pictures/UNADJUSTEDNONRAW_thumb_4c1d.jpg')

