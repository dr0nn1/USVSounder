# USV Sounder
Making an automonus vessel that surveys the seabed, and can detect lobster traps from the data collected from a Multibeam Echo.  
See this [video](https://youtu.be/eW9WCYAyL6I?t=689) for a quick summary of what we have done. PS. Video is in norwegian  

# Table of Contents
- [Group](#group)
- [Balder](#balder)
- [Data](#data)
- [Guidance](#guidance)

# Group
![Group](https://github.com/dr0nn1/USVSounder/blob/master/pictures/group.png)
*From left: Alise Skaar, Erling Tveter, Marthe Linnea Fagerlie, Helene Semb, Petter Drønnen and Sondre Holth Rognlien.*  

## Petter Drønnen
[Github](https://github.com/dr0nn1)  
[LinkedIn](https://www.linkedin.com/in/petter-dr%C3%B8nnen-014b02196/)

## Marthe Linnea Fagerlie
[Github](https://github.com/marthelf)  
[LinkedIn](https://www.linkedin.com/in/marthelinneafagerlie/)

## Erling Tveter
[Github](https://github.com/ErlingTve)  
[LinkedIn](https://www.linkedin.com/in/erling-tveter-40a429142/)

## Helene Semb
[Github](https://github.com/helensem)  
[LinkedIn](https://www.linkedin.com/in/helene-semb-705888160/)

## Sondre Holth Rognlien
[Github](https://github.com/sondrehr)  
[LinkedIn](https://www.linkedin.com/in/sondre-holth-rognlien-68b0b8172/)

## Alise Skaar
[Github](https://github.com/aliseska)  
[LinkedIn](https://www.linkedin.com/in/alise-skaar-452938b8/)


# Balder
![Balder](https://github.com/dr0nn1/USVSounder/blob/master/pictures/balder.png)
*The sounder used called Balder.*

![Multibeam Echo](https://github.com/dr0nn1/USVSounder/blob/master/pictures/echoSounder.png)
*The echo sounder used, model number: 2040P.*

# Data
## Water column
Water column is the raw data from the multibeam which tells how much sound is returned from differents depths.
In water colum it was difficult to spot what the object on the picture was. On the picture below a lobster trap is shown.
![Water column](https://github.com/dr0nn1/USVSounder/blob/master/pictures/waterColumn.png)

*Water column picture that could be used.*  

## Point cloud
Point cloud is a simplification of multiple water column data which visually represents the predicted seafloor.
This could potentially be the best viable option to detect different object on the seafloor. However, due to a limited timeframe of only eight weeks this option was not further explored.
![Point Cloud](https://github.com/dr0nn1/USVSounder/blob/master/pictures/pointCloud.PNG)
*Point cloud picture that could be used.*  

![Point Cloud](https://github.com/dr0nn1/USVSounder/blob/master/pictures/pointCloudResult.PNG)
*Point cloud picture with bounding boxes.*  

## Seabed
Seabed is a simplification of multiple water column data which visually represents the hardness of the seabed.
This option was chosen as this was the easiest data to work with, and had great potential. 
![Seabed](https://github.com/dr0nn1/USVSounder/blob/master/pictures/seabedWithCrabPot.png)
*A seabed image with lobster trap.*  

The picture below shows a prediction from our model. 
![Result](https://github.com/dr0nn1/USVSounder/blob/master/pictures/result.png)
*Seabed picture after detection.*

# Guidance
The guidance-chapter consists of the following subchapters:
- [Surveypath](#surveypath)
- [Waypoints](#waypoints)
- [Inspection path](#inspection-path)
- [Controller](#controller)


## Surveypath
From the API, a convex polygon is sent through an MQTT Broker in the shape of points in latitude and longitude. From this, a surveypath is created.
![Surveypath](https://github.com/dr0nn1/USVSounder/blob/master/pictures/surveypath.png)

*Image of a surveypath covering a possible search area.*

The survey lines are spaced 15 meters apart to ensure a ~50% overlap between each line of sensor data. The angle of the lines can be set according to the wind direction (loaded from a free weather API), but for safety reasons the direction was set to a set angle during demonstration, as the demo was to be "as close as possible" to the shore. For the same reason, the vessel will, in its current setting, always begin at the surveypath's southernmost entrypoint. At the demonstration, this ensured that the vessel would always turn away from shore, making the demo safer. In a non-demo version, the vessel will simply start at the entry point closest to the vessel's initial position. 

The lines are starting 50 meters outside of the search area. This is to help the vessel converge to the desired line before entering the search area. The sensor is at its best when the vessel is not turning, so the convergence to the desired line before entering the search area is important for the system to detect possible lobster traps. 

## Waypoints
The surveypath is translated into point coordinates in a local North-East-Down (NED) coordinate system. 
![Waypoints](https://github.com/dr0nn1/USVSounder/blob/master/pictures/VisualizationOfRun.png)
*Image of an earlier version of the API showing the waypoints, surveylines and the current surveyline (in bold). Note that the search area in this case is the thin, long polygon that is slightly less opaque than the other polygon. The different colors in the wake of the vessel represents the different [modes](#mode) it has been set to. Here, green represents nothing-mode and purple represents survey-mode.*

The coordinates of each point is saved as part of a data structure called a *waypoint*. Each waypoint consists of the coordinates of the point, the desired speed over ground (SoG) with which the vessel should approach the point, the [acceptance distance](#acceptance-distance) and the [mode](#mode) in which the system should be in, approaching the point.

### Acceptance distance
The *acceptance distance* is the distance away from the waypoint, measured along the desired line (the along-track distance), at which the waypoint is said to be "reached". At this point, the next waypoint, with a new desired line, is selected, unless the waypoint turns out to be the last one to be visited. In that case the vessel simply shuts down. 

The acceptance distance of the surveypoints (see [Mode](#mode)) is set to 16 meters, which is twice the length of the USV Sounder. This ensures a smoother transition between the lines.

The acceptance distance of the inspection points is set to 0 meters, as the vessel needs to go all the way to the point of interest in order to inspect it properly.

### Mode
The *mode* of a waypoint is the mode in which the vessel should be in, approaching it. There are four different modes:
-  Nothing - The sensor should not do anything.
-  Survey - The sensor should be running and detections can be made.
-  Inspection - The sensor frequency is increased in order to get a better/different look at the detected object(s) of interest. No new detections can be made.
-  Quit - All parts of the system, except for the API, shuts down. This mode is only set when the last waypoint is reached.

## Inspection path
Once the vessel has reached the end of the surveypath, it checks whether there has been any detections of interest. If so, waypoints with the coordinates of these detections are appended to the list of waypoints the vessel should visit. The waypoints are appended in order of distance from the last survey point.

## Controller
In order to give the right course over ground (CoG) commands to the vessel, an integral line-of-sight (ILOS) controller is utilized. The implementation of this is heavily inspired by the Marine Systems Simulator, [MSS toolbox](https://github.com/cybergalactic/MSS), created by T. I. Fossen and T. Perez (2004). The most substantial change from the implementation in MSS toolbox is that the ILOS controller is programmed in *C++* instead of *MATLAB*.

This particular controller does not suffer greatly from integral windup, while at the same time keeping the positive effects of the integral part, meaning that constant disturbances are attenuated. The controller was tuned using the [good gain method](http://www.techteach.no/publications/articles/good_gain_method/good_gain_method.pdf) for PI controllers. 
