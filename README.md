# RAMS API Instruction

1. Introduction
RAMS is an ArcGIS REST Service for Iowa DOT. It is a new version of GIMS system.
Go to the map server page, https://gis.iowadot.gov/ramsa/rest/services/lrs/MapServer, check the layers we previously used in GIMS.
Make sure you try out these functions first to get familiar with how the map server works.
a. geometry to measure
https://gis.iowadot.gov/ramsa/rest/services/lrs/MapServer/exts/LRSServer/networkLayers/0/geometryToMeasure
This function is using any coordinates (in Iowa) to get the measure of it on a road. You could consider it as Mile Marker, but it has a little difference with actual Mile Post on the road: measures are always increasing along the traveling direction.
https://gis.iowadot.gov/ramsa/rest/services/lrs/MapServer/exts/LRSServer/networkLayers/0/queryAttributeSetd
