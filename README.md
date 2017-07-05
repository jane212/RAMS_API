# RAMS API Instruction

## 1. Introduction

RAMS is an ArcGIS REST Service for Iowa DOT. It is a new version of GIMS system.

Go to the map server page, https://gis.iowadot.gov/ramsa/rest/services/lrs/MapServer, check the layers we previously used in GIMS.

Make sure you try out these functions first to get familiar with how the map server works.

### a. using coordinates to get measure and routeId

https://gis.iowadot.gov/ramsa/rest/services/lrs/MapServer/exts/LRSServer/networkLayers/0/geometryToMeasure

This function is using any coordinates (in Iowa) to get the measure of it on a road. You could consider it as 'Mile Marker', but it has a little difference with actual 'Mile Post' on the road: measures are always increasing along the traveling direction.

Some example for those fields (must be json array):

locations: [{"geometry":{"x":-93.646419, "y":42.004027}, "routeId":"S001920030E"}]

tolerance: 20

inSR: 4326

Note that x is for longitude and y is for latitude. "routeId" is optional, if you don't know the exact route and direction, leave a larger tolerance (in feet) to make sure it returns any nearby routes.

In the results, the "measure" field indicates how long your location is away from the route start, which could be considered as Mile Marker. For example, here your measure for your input location is 146.62445701775567.

#### Important!

Getting measure and routeId is fundamental for using this system to get any other attributes you want. ALWAYS check your results on the map to see if your location is on the correct route and direction.

### b. using measure and routeId to get any attributes you want

https://gis.iowadot.gov/ramsa/rest/services/lrs/MapServer/exts/LRSServer/networkLayers/0/queryAttributeSetd

After getting the measure and routeId, you could use it to query any attributes in RAMS/GIMS. Here is an example for getting AADT.

Use the above link and fill in the fields as follows:

locations: [{"routeId":"S001920030E", "measure": 146.62445701775567}]
attributeSet: [{"layerId":102, "fields":["AADT"]}]

In the results, the "AADT" field has the data you want. Here we used previous location info we got from function a., the AADT value for that location is 23800.

https://gis.iowadot.gov/ramsa/rest/services/lrs/MapServer

Above is the list of layers RAMS has, and click on the layer you could access all the fields it contains. Remember use exact words of field name, like AADT, AADT_COUNT_YEAR.

## 2. Using Python to query RAMS

After you get familiar with RAMS and the basic functions, here you could use my programs to
