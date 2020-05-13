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


