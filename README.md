# ArcGIS Pro workshop for LA 6010, focusing on Coney Island

Workshop 2024-09-25 by Keith Jenkins, GIS Librarian at Mann Library, Cornell University. \
This document is online at: <https://kgjenkins.github.io/la-6010-coney-island/>

For help after this workshop, contact me at kgj2@cornell.edu  
Or set up a Zoom appointment at <https://guides.library.cornell.edu/gis/help>

All the data and documentation for this workshop can be downloaded from: \
<https://github.com/kgjenkins/la-6010-coney-island/archive/main.zip>

After downloading the zip file, be sure to UNZIP it!  (Right-click > Extract All)


## Some GIS terminology and concepts

**GIS** stands for Geographic Information System (or Science)

"GIS data" refers to data with a spatial component -- it can be mapped!

**ArcGIS Pro** is one of several popular GIS programs for mapping and spatial analysis.  It is developed by Esri, and replaces ArcMap, which is being phased out.  ArcGIS Pro is available on the public PCs in Mann Library, but requires a user login in order to run.

**Vector** = points, lines, polygons
Common file formats are GeoPackage (.gpkg) and Shapefile (.shp, .dbf, .prj, etc.)

**Raster** = pixels (images, but also data)
Preferred file format is GeoTIFF (.tif), but .jpg and other formats can also be used.

Vector data does not usually contain any information about how to style the display of the data.

Styles are added in a map project.  The same data could be styled in different ways in different maps, or even in the same map!

The ArcGIS Pro project folder contains several files.  The .aprx file describes your project, including information about which data sources are on your map, and what styles are defined for each layer.  Note that it does not include the data, which exists in separate files.  I recommend keeping everything within a containing folder (there can be subfolders) that you can zip up or save to a device or the cloud.  Something like this:

* myfolder/
  * data/
    * boundaries/
      * counties.shp
    * buildings.gpkg
    * streets.gpkg
  * myarcgisproject
    * project.aprx
    * project.gdb
    * etc.

**Coordinate Reference Systems** (CRS)

There are thousands of coordinate systems in existence, each created to deal with converting the curvature of the surface of a roughly-spherical planet into a flat two-dimensional piece of paper or computer screen.  Each focuses on a specific region of the planet, and each makes choices about whether to prioritize distance, area, shape, or direction.  (It is impossible to preserve all of those.)

Ideally, CRS details are quietly handled behind the scenes, so we barely even need to think about it.

But in the real world, you'll eventually run into CRS puzzles and problems, and CRS becomes even more important when doing distance- or area-based analysis.  Here are some of the CRS you might see in New York:

* EPSG:4326 = WGS 84 -- "common longitude/latitude"
* EPSG:4269 = NAD83 -- longitude/latitude, similar to WGS 84, but based on a 1983 model of the Earth that is only defined for North America
* EPSG:3857 = WGS 84 / Pseudo-Mercator -- also called "Web Mercator", uses false "meters" (only true at equator)
* EPSG:2261 = NAD83 / New York Central (ftUS) -- one of many "state plane" coordinate systems measured in feet
* EPSG:26718 = NAD27 / UTM zone 18N -- meter-based system based on a 1927 model of the Earth only defined for North America
* EPSG:26918 = NAD83 / UTM zone 18N -- meter-based system based on a 1983 model of the Earth only defined for North America
* EPSG:32618 = WGS 84 / UTM zone 18N -- meter-based system based on a 1984 model of the Earth used by GPS satellites


# Create a new project

* Run ArcGIS Pro
* At the opening screen, select "Map" (under "New Project")
* Give it whatever name you want
* For the Location, click the folder and browse to the workshop folder that you unzipped


## Civil Boundaries

Civil boundaries in New York state include things like counties, towns, villages, and cities.  We have these boundaries for all of New York state in a shapefile format, which is probably the most common geospatial data format found on the Internet.  But the shapefile format dates from the early 1990s, which is why there are multiple component files (.shp, .dbf, .shx, .prj, and sometimes others).  To add a shapefile to ArcGIS, we just need to select the .shp file and ArcGIS will take care of the rest.  Shapefiles also have other quirks, mostly notably a 10-character limit for attribute names.  Learn more at <http://switchfromshapefile.org/>

Load the county boundaries:
* Look in the "data/boundaries" folder and drag the "Counties.shp" file onto the ArcGIS map.

Change the layer symbology:
* In the Contents pane (on the left), double-click the color swatch under the layer's name
* Rather than clicking one of the named symbols, click the "Properties" tab at the top of the Symbology pane
* For the Color, click the selector and select "No Color" (at the top)

Notice that these boundaries do not show the natural shape of the shoreline, but rather the legal boundaries that extend out into the ocean.  Zoom into New York City and notice how some of the border polygons contain small islands on the basemap.  While these types of boundaries have their uses, let's use a different shapefile of county boundaries that align with the shorelines.

* From the "boundaries" folder, drag the "Counties_Shoreline.shp" file on the ArcGIS map.
* Change the symbology so that there is no fill color, and a 2pt blue outline

Get information about a polygon:
* Click a polygon on the map

View information about all the polygons as a table:
* Right-click layer in Contents pane > Attribute Table
* Right-click on a column header to sort by that column

The table and map are linked, so select a row from the table (by clicking the row number) to see it highlighted in bright blue on the map.  There is a toolbar button above the table to "Clear" the selection.

Let's add the names of the counties to the map:
* Right-click the layer name > Label
* Right-click the layer name > Label Properties...
* Click "Symbol" near the top of the label panel, and make any adjustments you like

To find out what coordinate system is being used:
* Right-click > Properties...  Source > Spatial Reference


## Save your project!

Save your project.  This updates the .aprx file.


## Streets

OpenStreetMap.org is a great source of streets data, and is widely considered to be the most complete and accurate street map in the world.  But New York State has very good official streets data that it makes available to the public, so we'll use a GeoPackage of streets for Kings county that was clipped from a statewide dataset.  GeoPackage is a modern GIS format that was designed to improve upon the many limitations of shapefiles.

* Look in the "streets" folder, and drag the "nys-streets-seneca-neighbors.gpkg" file onto ArcGIS.

Notice that this does not work!  Only certain types of data files can be drag-and-dropped onto the map.  We'll have to add the GeoPackage data a different way.

* Click on the "Map" menu at top
* Click the yellow "Add Data" button
* Browse to find the workshop folder > data > streets
* Select "nys-streets-kings.gpkg", then "main.streetsegment" (which is the name of the layer within the geopackage)

Explore the data a bit (zoom, click a single line segment, look at the attribute table), and notice the contents of the "SPEED" column.  Is this the speed limit?  To find out, The definitions of this field can be found on page 2 of the **streets_data_dictionary.pdf** file.

We can use these values to control the symbology of each line segment (rather than using the same color and weight for every road).  We'll use thin lines for roads 25 MPH and below, and thicker lines for the faster roads.

* In the Contents panel, double-click the symbol below the name of the streets layer
* At the top of the symbology panel, click the back arrow
* Change "Single symbol" to "Graduated Symbols"
* Set Field = "SPEED"
* Set the number of classes to 2
* Set the Minimum size to 0.5pt, and Maximum size to 2pt
* Double-click the "<= 35" upper value and change it to "<= 25"
* Double-click the symbol for each category and change the color to black (click the back arrow at top to get back to the main symbology settings)

This should have the effect of increasing the visual weight of the major roads, making it easier to recognize the major roads.


## Parcels and Buildings

Use the "Add Data" button to add the parcels and buildings layers, and try setting the symbology of the parcels to outline-only.  Explore the data of each layer.


## Exporting your map to an image file

You may want to spend more time getting your map to look good, but let's just assume it looks perfect already!  Here's how to export the map as an image or PDF file:

* Zoom to the part of the map you want to export
* Click on the "Share" menu (at top)
* Click the "Export Map" button (near the right end of the ribbon)
* Select the file type (PDF, PNG, JPEG, etc.)
* Set the output location and filename
* Under the "Image Size" section, check the box to "Show preview" -- this is very important, as ArcGIS tends to reduce the bounds of the map from what you were viewing.
* Adjust the Width/Height as needed
* Click "Export"

Exporting to PDF is useful if you are going to print your map, or if you want to do further work in a program like Inkscape or Adobe Illustrator.  The PDF will keep separate layers for each data layer.  By default, vector layers will remain vectors, and raster layers will remain raster.

There is also a "Capture to Clipboard" button in the toolbar for quickly copying the current map image and extent -- what you see is what you get.


## Exporting data layers to a DXF file (for CAD software)

Landscape architects often want to export GIS layers for use in a CAD program like Rhino or AutoCAD.  Since DXF is a vector format, only vector layers can be exported -- sorry, no basemaps!  When exporting to CAD, be sure to set an appropriate coordinate system for the exported data, which means one based on meters or feet -- not degrees longitude/latitude.

* Zoom to the area you want to export
* Click the "Analysis" menu (at top)
* Click "Tools" (red toolbox)
* In the Geoprocessing panel, search for "CAD"
* Select the "Export to CAD" tool

Most processing tools look similar -- you select your inputs, set some options, occasionally set some things in the "Environments" tab, and specify where to save the output.

* For "Input Features" select each of the vector layers you would like to export
* Set the "Output File" name and location (make sure you know where it is being saved!)
* Click the "Environments" tab (near the top)
* For the "Output Coordinate System", select the streets layer -- this should select the NAD_1983_UTM_Zone_18N coordinate system (which is based on meters)
* In the "Processing Extent" section, click the first icon ("Current Display Extent") -- this will limit the export to features overlapping the current map view.

If none of your layers have the coordinate system you want, you can always click the globe icon and search for a coordinate system, such as these for New York:
* EPSG:2261 = NAD83 / New York Central (ftUS) -- if you want to work in US feet
* EPSG:32618 = WGS 84 / UTM zone 18N -- if you want to work in meters

Do **NOT** export to DXF using any of the following, or you will see distortions and incorrect measurements:
* EPSG:4326 = WGS 84 -- "common longitude/latitude"
* EPSG:4269 = NAD83 -- longitude/latitude, similar to WGS 84, but only defined for North America
* EPSG:3857 = WGS 84 / Pseudo-Mercator -- also called "Web Mercator", uses false "meters" (only true at equator)


## Georeferencing a scanned map

Historical maps can be great sources of information that pre-dates the computer era.  However, most scanned maps images found online are just images, with no information about the coordinates of the area that the map covers.  To view these maps in ArcGIS with other layers, we will need to do a process called "georeferencing".

There are two maps in the "historical_maps" folder.  One has already been georeferenced:

* Drag and drop the "georef-example.tif" file into ArcGIS
* At the "Build Pyramids" dialog, click "OK"

Since this image has already been georeferenced, the file contains some embedded location information, and it should automatically show up in the right place in ArcGIS.  Explore the map to check the alignment with the basemap or other layers.  Notice the change in the shoreline since 1905.

There are some things we can do in order to see the historical map better:

* Turn off some of the vector layers that are on top.
* Click the "georef" layer name, then the "Raster Layer" menu (at top)
    * Change the "Layer Blend" from "Normal" to "Multiply"
    * Adjust the brightness and contrast as needed (sliders on right side of ribbon)
    * Change the "Resampling Type" to "Bilinear" (this reduces pixelization when zoomed in)

The original version of the historical map is also in the folder.  Notice that it actually has three sections.  Let's georeference the top section, which corresponds to the western portion of Coney Island.

* Drag and drop the "coney-island-map-1905.jpg" file into ArcGIS
* At the "Calculate statistics" dialog, click "Yes"

Notice that an "Unknown Coordinate System" message shows up in the top right of the screen.  This is because there is no location information in the file.  We need to georeference the map by creating a series of control point pairs (one on the historical map image, and one on the basemap) that will allow ArcGIS to calculate an equation to position and warp the map as needed to fit.

* Turn off all layers except for the basemap
* Click the layer name of the "coney-island-map-1905.jpg" layer
* Click the "Imagery" menu (at top)
* Click the "Georeference" button (towards left side of ribbon)
* Zoom to the western end of Coney Island
* Click the "Fit to Display" button (towards left side of ribbon)

The image will appear, but not positioned correctly.  First we need to adjust the display of the image so we can see both it and the basemap at the same time.

* Click the "Raster Layer" menu (at top)
* Set the "Layer Blend" to "Multiply"
* Set the "Resampling Type" to "Bilinear"

Now let's get started with the georeferencing...

* Click the Georeference" menu (at top)
* Click "Add Control Points"
* Click first on the historical image (for example the westernmost point), then on the approximate location on the basemap.
* Repeat a few times -- the image will get repositioned each time, hopefully better!

Sometimes the positioning gets thrown off by a single bad control point.  It can help to review all the points we have thus far.

* Click the "Control Point Table" button in the ribbon
* Look for the row with the highest (worst) residual in the last column.  If this number is significantly higher, it is most likely a bad point.
* Click the bad row and press the "delete" key.  (There is also a button above the table that will delete it, but BEWARE -- it is right next to another, similar-looking button that will delete ALL your control points!)

Good places for control points include the corners of long-standing buildings, or the middle of intersections of roads that have not moved in 100 years.  Bad places include shorelines that have eroded, and features that no longer exist.  Inspect your map to gauge your progress.  Sometimes 4-6 points is enough, but other maps might require dozens or more.  Generally, residuals less than 5 (pixels) are pretty good.  So if your map seems to align pretty well, and the residuals are low, we are ready to finalize the process.

* Click the "Save as New" button in the ribbon
* Set the "Output Raster Dataset" to whatever location and filename you want (make sure it is different than the original!), but use a ".tif" extension
* Set the "Output Format" to "TIFF
* Under "Raster Properties", set the "NoData Value" to 0 -- this will prevent the georeferenced image from having a black collar.  (If the image has any pure black areas, try using a value of 255 instead, which should work unless the image has any pure white areas...)
* Click "Export"
* Adjust the "Layer Blend" and "Resampling Type" as before, in order to assess the accuracy of your work

Georeferencing is a complex process.  It can help to see the process in action, so here is a good demo video that also shows how to digitize features from the georeferenced map, creating a new layer of polygons based on map features: <https://www.youtube.com/watch?v=QWv5nwCeZjA>


## Data Sources

The data for this workshop was derived from the following sources:

* County boundaries: <https://gis.ny.gov/civil-boundaries>
* Streets: <https://gis.ny.gov/streets-addresses#nys-streets-data>
* Parcels: <https://gis.ny.gov/parcels#new-york-state-parcels>
* Buildings: <https://data.gis.ny.gov/datasets/sharegisny::nys-building-footprints-2/explore?location=40.564263%2C-73.968428%2C13.97>
* Historical map: <https://mapcollections.brooklynhistory.org/map/maps-of-coney-island-1905/>

