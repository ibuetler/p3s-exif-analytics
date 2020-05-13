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

## Task: Extract GPS data from pictures

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

```
Now, the exif data of the picture should be obtained. Exchangeable Image File Format (EXIF) is a standard that defines specific information related to an image or other media captured by a digital camera. It is capable of storing such important data as camera exposure, date/time the image was captured, and even GPS location. We can use a function called \_getexif() with Pillow to get all Exif data of the image.

```python
from PIL import Image
from PIL.ExifTags import TAGS

img = Image.open('Pictures/UNADJUSTEDNONRAW_thumb_4c1d.jpg')
exif_data = img._getexif()

```
The getexif() function returns a dictionary with the numeric code of the exif information with the corresponding value. If we print the dictionary on the console the following structure appears:

```python
{36864: b'0231', 37121: b'\x01\x02\x03\x00', 37377: (111625, 10737), 36867: '2020:02:04 12:07:50', 36868: '2020:02:04 12:07:50', 37378: (54823, 32325), 37379: (253137, 27052), 37380: (0, 1), 37383: 5, 37385: 24, 37386: (17, 4), 40961: 65535, 40962: 768, 41989: 26, 41990: 0, 271: 'Apple', 40963: 1024, 37521: '619', 37522: '619', 272: 'iPhone 11 Pro Max', 37396: (2002, 1505, 2213, 1324), 531: 1, 41495: 2, 33434: (1, 1348), 282: (72, 1), 283: (72, 1), 33437: (9, 5), 41729: b'\x01', 34850: 2, 34853: {1: 'N', 2: ((13, 1), (26, 1), (2826, 100)), 3: 'E', 4: ((103, 1), (51, 1), (3172, 100)), 5: b'\x00', 6: (393983, 10059), 12: 'K', 13: (0, 1), 16: 'T', 17: (1110289, 4096), 23: 'T', 24: (1110289, 4096), 31: (1020238, 1567)}, 34855: 32, 296: 2, 41986: 0, 40960: b'0100', 41987: 0, 305: '13.3', 42034: ((807365, 524263), (6, 1), (9, 5), (12, 5)), 42035: 'Apple', 42036: 'iPhone 11 Pro Max back triple camera 4.25mm f/1.8', 306: '2020:02:04 12:07:50', 34665: 200, 37500: b'Apple iOS\x00\x00\x01MM\x00\x1b\x00\x01\x00\t\x00\x00\x00\x01\x00\x00\x00\x0b\x00\x02\x00\x07\x00\x00\x02.\x00\x00\x01X\x00\x03\x00\x07\x00\x00\x00h\x00\x00\x03\x86\x00\x04\x00\t\x00\x00\x00\x01\x00\x00\x00\x01\x00\x05\x00\t\x00\x00\x00\x01\x00\x00\x00\xc2'}

```

The key is always a certain information of the image as a numerical code. For example, the geolocation data corresponds to the code 34853. A complete documentation of the codes would be found here: https://www.exiv2.org/tags.html

But we can write our own function in Python that parses the Exif tags to their corresponding value. For this we now use our second import the TAGS module. With TAGS.get(code) we get the corresponding value for an Exif code. For example for the code 34853 we get the value 'GPSInfo'. Here is a function that takes an Exif dictionary and returns a new dictionary with the resolved codes:

```python
from PIL import Image
from PIL.ExifTags import TAGS

def get_structured_exif(exif_dictionary):
    structured_dictionary = {}
    for (key, val) in exif_dictionary.items():
        structured_dictionary[TAGS.get(key)] = val

    return structured_dictionary
```

If we print the new dictionary to the console, it is now more readable:

```
{'ExifVersion': b'0231', 'ComponentsConfiguration': b'\x01\x02\x03\x00', 'ShutterSpeedValue': (111625, 10737), 'DateTimeOriginal': '2020:02:04 12:07:50', 'DateTimeDigitized': '2020:02:04 12:07:50', 'ApertureValue': (54823, 32325), 'BrightnessValue': (253137, 27052), 'ExposureBiasValue': (0, 1), 'MeteringMode': 5, 'Flash': 24, 'FocalLength': (17, 4), 'ColorSpace': 65535, 'ExifImageWidth': 768, 'FocalLengthIn35mmFilm': 26, 'SceneCaptureType': 0, 'Make': 'Apple', 'ExifImageHeight': 1024, 'SubsecTimeOriginal': '619', 'SubsecTimeDigitized': '619', 'Model': 'iPhone 11 Pro Max', 'SubjectLocation': (2002, 1505, 2213, 1324), 'YCbCrPositioning': 1, 'SensingMethod': 2, 'ExposureTime': (1, 1348), 'XResolution': (72, 1), 'YResolution': (72, 1), 'FNumber': (9, 5), 'SceneType': b'\x01', 'ExposureProgram': 2, 'GPSInfo': {1: 'N', 2: ((13, 1), (26, 1), (2826, 100)), 3: 'E', 4: ((103, 1), (51, 1), (3172, 100)), 5: b'\x00', 6: (393983, 10059), 12: 'K', 13: (0, 1), 16: 'T', 17: (1110289, 4096), 23: 'T', 24: (1110289, 4096), 31: (1020238, 1567)}, 'ISOSpeedRatings': 32, 'ResolutionUnit': 2, 'ExposureMode': 0, 'FlashPixVersion': b'0100', 'WhiteBalance': 0, 'Software': '13.3', 'LensSpecification': ((807365, 524263), (6, 1), (9, 5), (12, 5)), 'LensMake': 'Apple', 'LensModel': 'iPhone 11 Pro Max back triple camera 4.25mm f/1.8', 'DateTime': '2020:02:04 12:07:50', 'ExifOffset': 200, 'MakerNote': b'Apple iOS\x00\x00\x01MM\x00\x1b\x00\x01\x00\t\x00\x00\x00\x01\x00\x00\x00\x0b\x00\x02\x00\x07\x00\x00\x02.\x00\x00\x01X\x00\x03\x000\xbc\x00\x07\x00\t\x00\x00\x00\x01\x00\x00\x00\x01\x00\x08\x00\n\x00\x00\x00\x03\x00\x00\x03\xee\0'}

```

### Theory about glob

To solve this task efficiently, another library called glob is used. To solve this task efficiently, another library called glob is used. In the glob library there is a module glob, which allows us to iterate very efficiently over all files in a folder. The glob module finds all the pathnames matching a specified pattern according to the rules used by the Unix shell, although results are returned in arbitrary order. Since in our task all pictures in the folder end with '.jpg'. We can use glob in the following way to iterate over all pictures:

```python
import glob
import os

path = '../Pictures/'
for filename in glob.glob(os.path.join(path, '*.jpg')):
 img = Image.open(filename)

```

**Task:** Now, you should have all the knowledge to iterate over all pictures and create a dictionary which holds as the key the number of the picture (First picture = 1) and as the value the GPSinfo. For example the first entry of the dictionary could look like this:

```python
{1: {1: 'N', 2: ((7, 1), (59, 1), (5064, 100)), 3: 'E', 4: ((98, 1), (17, 1), (3186, 100)), 5: b'\x00', 6: (9547, 1127), 7: ((10, 1), (6, 1), (25, 1)), 12: 'K', 13: (0, 1), 16: 'T', 17: (5929, 18), 23: 'T', 24: (5929, 18), 29: '2017:02:23', 31: (5, 1)}, 2:
```
### Converting the GPSinfo into latitude and longitude

To visualize the location of our images on Google Maps, we need the GPS information as longitude and latitude. Pillow returns the GPS information as sexagesimal degree format. This means they are given as degrees, minutes, and seconds : 40° 26′ 46″ N 79° 58′ 56″ W.

There are 60 minutes in a degree and 60 seconds in a minute. Therefore, to convert from a degrees minutes seconds format to a decimal degrees format, one may use the formula:

decimal degrees = degrees + (minutes/60) + (seconds/3600)

In our dictionary from the previous task we can receive the latitude and longitude in the degrees minutes and seconds format in the following way: (example is for the picture with the key 1)

```python
  latitude = dict_gps[1][2]
  longitude = dict_gps[1][4]

```
For example for the latitude we have now the following data:

```python
((7, 1), (59, 1), (5064, 100))
```

The first pair is for the degree, the second for minutes and the third for seconds. To receive the actual value the first value ot the pair must be divided by the second. Example for degrees = 7/1.

Afterwards the formula from above can be used:

decimal degrees = degrees + (minutes/60) + (seconds/3600)

**Optional Task:** You can try to create a function on your own, for converting the two formats. Otherwise click below for a solution.

<details><summary>Solution for conversion</summary>
<p>

```python
get_float = lambda x: float(x[0]) / float(x[1])
def convert_to_degrees(value):
    d = get_float(value[0])
    m = get_float(value[1])
    s = get_float(value[2])
    return d + (m / 60.0) + (s / 3600.0)
```
</p>
</details>


Hovewer this is still not the actual value for the latitude and longitude. The reference value of them is neede. This meands compared to the equatorial plane, the latitude is completed by a letter N (hemisphere) or S depending on whether one is located in the northern or southern hemisphere. Compared to the Greenwich meridian, the longitude is completed by a letter W or E depending on whether you are located in the west or east.

