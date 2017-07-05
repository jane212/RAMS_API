# RAMS API Instruction

## 1. Introduction

RAMS is an ArcGIS REST Service for Iowa DOT. It is a new version of GIMS system.

Go to the map server page, https://gis.iowadot.gov/ramsa/rest/services/lrs/MapServer, check the layers we previously used in GIMS.

Make sure you try out these functions first to get familiar with how the map server works.

### a. using coordinates to get "measure" and "routeId"

This function is using any coordinates (in Iowa) to get the measure of it on a road. You could consider it as 'Mile Marker', but it has a little difference with actual 'Mile Post' on the road: measures are always increasing along the traveling direction.

Go to this link: https://gis.iowadot.gov/ramsa/rest/services/lrs/MapServer/exts/LRSServer/networkLayers/0/geometryToMeasure

Some example for those fields you should fill in (must be json array):

locations: [{"geometry":{"x":-93.646419, "y":42.004027}, "routeId":"S001920030E"}]

tolerance: 20

inSR: 4326

![](https://github.com/jane212/RAMS_API/blob/master/geotomeasure.png)

Note that x is for longitude and y is for latitude. "routeId" is optional, if you don't know the exact route and direction, leave a larger tolerance (in feet) to make sure it returns all the nearby routes.

In the results, the "measure" field indicates how long your location is away from the route start, which could be considered as Mile Marker. For example, here your measure for your input location is 146.62445701775567.

#### Important!

Getting measure and routeId is fundamental for using this system to get any other attributes you want. ALWAYS check your results on the map to see if your location is on the correct route and direction.

### b. using "measure" and "routeId" to get attributes

After getting the measure and routeId, you could use the above link to query any attributes in RAMS/GIMS. Here is an example for getting AADT.

Use this link https://gis.iowadot.gov/ramsa/rest/services/lrs/MapServer/exts/LRSServer/networkLayers/0/queryAttributeSetd and fill in the fields as follows:

locations: [{"routeId":"S001920030E", "measure": 146.62445701775567}]

attributeSet: [{"layerId":102, "fields":["AADT"]}]

![](https://github.com/jane212/RAMS_API/blob/master/aadt.png)

In the results, the "AADT" field has the data you want. Here we used previous location info we got from function a., the AADT value for that location is 23800.

Below is the list of layers RAMS has, and click on the layer you could access all the fields it contains. Remember use exact words of field name, like AADT, AADT_COUNT_YEAR.

https://gis.iowadot.gov/ramsa/rest/services/lrs/MapServer

![](https://github.com/jane212/RAMS_API/blob/master/layers.png)

... ...

## 2. Using Python to query RAMS

After you get familiar with RAMS and the basic functions, here you could use my programs to query the measure, routeId, and attributes in batch processing.

Note that the system has a limit on number of records it will respond and return, thus, I've put the option in my codes to let you choose how many rows you want in one response. (0<=n<=1500)

First of all, you need to convert your coordinates into measure and routeID pair. There are two programs could be used.

### Program 1A.GetMeasure_without_RouteID

Since RAMS is using their own rules to generate routeID (like "S001920030E" for US 30 Eastbound), you may not know the route and direction for your coordinates. Thus, you should use program 1A to arbitrarily get the measure and routeId pair. 

In program 1A, we are querying all the possible measure and routeId pair for your coordinate within certain tolerance. (Modify the default tolerance (50 ft) in Main function). This program will automatically return you only one pair of measure and routeId by filtering the nearest one with highest priority (There is a priority rule in RAMS, you don't need to know).

#### Important!

For any coordinate which is perfectly located on the correct side of the road, for example, INRIX segment start and end points, this program works fine. But for Wavetronix sensor, sometimes two sensors in different direction have the EXACT SAME coordinate. For example:

CBDS 26 SB: 41.149317	-95.822583

CBDS 26 NB: 41.149317	-95.822583

They are physically at the same location, but you want directional info for each sensor name. Then this program cannot give you the answer you want unless you specify: the first coordinate is on Southbound and the second is on Northbound. To do that, you have to do two things:

#### Check the route and direction for your coordiantes on the map.
#### Then, use program 1B.

But I still recommend you to use 1A first, then you could have an idea about which routeId that sensors are generally belong to. And follow the format of routeId to create your own ones.

### Program 1B.GetMeasure_with_RouteID

Using program 1B is easier, all you need is your coordinates and the correct routeId for them. Then the program will return you exact one accurate measure and routeId pair.

#### Important!

ALWAYS check your results on map! See if the routeId is consistent on one road, the measure is alway increasing along traveling direction!

### Program 2A.RequestAttributes

After getting correct measure and routeId pair, you can use program 2A to query attributes, like AADT, speed limit, etc. Make sure you know your layerID and fields you want from this link:

https://gis.iowadot.gov/ramsa/rest/services/lrs/MapServer

And modify the corresponding fields in the codes.

### Program 2B.RequestReferencePost

Not like general attributes, reference post needs a special way to query. This program is ONLY used for querying reference post of one location.

Reference post is an approximation of mile post on the road (the little green sign on the roadside indicates the mileage). In RAMS, reference post is a point object, rather than a line object. Thus, we need to get two nearest points and linearly interpolate or extrapolate the reference posts. This has been implemented in program 2B.

#### Important!

Check your results on the map! Reference post is not alway increasing along traveling direction, it ONLY increases along MAIN direction (Eastbound or Northbound), then decreases along opposite direction (Westbound or Southbound). 

Also, the direction here is the direction from routeID, NOT from the physical world, NOT the direction column from INRIX or Wavetronix meta data.

Besides, there are several errors in RAMS itself. I've tried many scenarios and handled all the exceptions I met. But if you still find your results are not reasonable, please consult Skylar.

## Final Comments

There are many fields you need to specify regarding your own problems. They have been highlighted in the codes. Please follow the instructions in the codes as well.

If you encounter some basic programing errors, try to run the codes line by line and see the results, until you find the reason of error. Debugging is a fun job. And remember that Google is your friend!








