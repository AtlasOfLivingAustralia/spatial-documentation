# Creating a contextual layer from gridded data by polygonizing

**NOTE:**
This process currently does not work because gdal\_polygonize currently does not work on ala-devmaps. gdal\_polygonize is actually a python script located at /data/ala/utils/gdal-1.7.2/swig/python/scripts. In order to use it, some additional python modules need to be installed. See /data/ala/utils/gdal-1.7.2/swig/python/README.txt. An alternative way to use gdal\_polygonize is to run it on a windows machine via OSGeo4W.

The layer-ingestion project contains a script named contextual\_load\_gridded\_polygonize.sh which will load a layer from raw .adf data by using the gdal\_polygonize utility to first convert the .adf data to an ESRI shapefile  format.

Aside from the initial step to convert the .adf data to an ESRI shapefile, the process used in this script is identical to that used when [Creating a contextual layer from an ESRI shapefile](wiki/ContextualLoadShape).

Like all other scripts, there are some variables at the top of the script file that need to be modified for each layer being ingested. These are:

  * The path to the raw adf header file for the layer
  * The layer id (numeric)
  * The short name for the layer
  * The display name of the layer
  * The field id used to generate objects (see [Object creation](wiki/ContextualLoadShape#Object_creation))
  * The field name used to generate objects (see [Object creation](wiki/ContextualLoadShape#Object_creation))
  * The field description used to generate objects (see [Object creation](wiki/ContextualLoadShape#Object_creation))
  * Name search value - true if objects created from the fields table entry for the layer should be searchable by name.
  * Intersect value - true if the layer should be tabulated using the [TabulationGenerator tool](wiki/ContextualLayerLoadProcess#3._Run_TabulationGenerator_java_tool)
  * The derived column JSON file (see [Defining derived columns using JSON](wiki/ContextualLoadShape#Defining_derived_columns_using_JSON))
  * The sld legend file (see [Defining an sld](wiki/ContextualLoadShape#Defining_an_sld))

```
export ADF_HEADER_FILE="/data/ala/data/layers/raw/newlayer/hdr.adf"
export LAYER_ID="666"
export LAYER_SHORT_NAME="newlayer"
export LAYER_DISPLAY_NAME="description of newlayer"
export FIELDS_SID="sid"
export FIELDS_SNAME="sname"
export FIELDS_SDESCRIPTION="sdescription"
export NAME_SEARCH="true"
export INTERSECT="false"
export DERIVED_COLUMNS_FILE="/data/ala/data/layers/raw/newlayer/newlayer_derived_columns.json"
export LEGEND_FILE="/data/ala/data/layers/raw/newlayer/newlayer.sld
```

Note that the contextual\_load\_gridded\_polygonize.sh script by default uses a json file to create derived columns (see [Defining derived columns using JSON](wiki/ContextualLoadShape#Defining_derived_columns_using_JSON)) and an sld to create a custom legend (see [Defining an sld](wiki/ContextualLoadShape#Defining_an_sld)). Typically you will need to do both of these when creating a contextual from gridded data by polygonizing because:

  * The gridded data typically contains numeric that must be mapped to textual labels
  * The gridded data typically gets converted to polygons that we will want to display as _classes_ (for example the layer "Geomorphology of the Australian Margin and adjacent seafloor" has a set of polygons that represent reefs, a set that represent basins, etc.)

## The SLD File
Supplying an sld file is optional. If you are loading a contextual layer that does not require a legend, just leave the "LEGEND\_FILE" variable empty, comment out the line in the script that copies the sld file to the legend directory, and remove "${LEGEND\_DIR}/${LAYER\_SHORT\_NAME}.sld" as the final argument to the PostgisTableGeoserverLoader tool.