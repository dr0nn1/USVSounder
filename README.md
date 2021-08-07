# USV sounder
Making an automonus vessel that survey the seabed, and can detect lobster trap from the data collected from a Multibeam Echo.
# Table of Contents
- [Group](#Group)
- [Balder](#Balder)
- [Data](#Data)

# Group
![Group](https://github.com/dr0nn1/USVSounder/blob/master/pictures/group.png)
*From left: Alise Skaar, Erling Tveter, Marthe Linnea Fagerlie, Helene Semb, Petter Drønnen and Sondre Holth Rognlien*  

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
*The sounder used called Balder*

![Multibeam Echo](https://github.com/dr0nn1/USVSounder/blob/master/pictures/echoSounder.png)
*The echo sounder used, model number: 2040P*

# Data
## Water column
Water column is the raw data from the multibeam which just tells how much sound is return from differents depths.
In water colum it was difficult to spot what the object on the picture was. On the picture below a lobster trap is shown.
![Water column](https://github.com/dr0nn1/USVSounder/blob/master/pictures/waterColumn.png)
*Water column picture that could be used*  

## Point cloud
Point cloud is a simplification of multiple water column data which visually represents the predicted seafloor.
This could potentially be the best viable option to detect different object on the seafloor. However, due to a limited timeframe of only eight weeks this option was not further explored.
![Point Cloud](https://github.com/dr0nn1/USVSounder/blob/master/pictures/pointCloud.PNG)
*Point cloud picture that could be used*  

![Point Cloud](https://github.com/dr0nn1/USVSounder/blob/master/pictures/pointCloudResult.PNG)
*Point cloud picture with bounding boxes*  

## Seabed
Seabed is a simplification of multiple water column data which visually represents the hardness of the seabed.
This option was choosen due to this was the easiest data to work with, and had great potential. 
![Seabed](https://github.com/dr0nn1/USVSounder/blob/master/pictures/seabedWithCrabPot.png)
*A seabed image with lobster trap*  

The picture below shows a prediction from are model. 
![Result](https://github.com/dr0nn1/USVSounder/blob/master/pictures/result.png)
*Seabed picture after detection*
