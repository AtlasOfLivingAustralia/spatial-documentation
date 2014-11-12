# Creating an environmental layer from .bil data
The layer-ingestion project contains a script named environmental\_load\_bil.sh which will load an environmental layer from .bil format. There are a number of variables defined at the top of the script that need to be modified for each new layer that is being loaded. These are:
  * The layer id (numeric)
  * The short name for the layer
  * The display name of the layer
  * The units used in the layer
  * The path to the adf header file for the layer

```
export LAYER_ID=666
export LAYER_SHORT_NAME="new_layer" 
export LAYER_DISPLAY_NAME="description of new layer"
export UNITS="layer units"
export RAW_BIL_FILE="/data/ala/data/layers/raw/newlayer/newlayer.bil"
```

The environmental\_load\_adf.sh script does the following things
  1. Uses gdalwarp to reproject the bil file to WGS 84. Output is second .bil file.
  1. Uses the Bil2diva java tool (in source control in the layers-store project) to convert .bil to diva grid format
  1. Uses the GridLegend java tool (in source control in the layers-store project) to generate a .sld legend file for use in geoserver
  1. Uses gdal\_translate to convert .bil into a geotiff file
  1. Uses the EnvironmentalDatabaseEntryCreator java tool (in source control in the layer-ingestion project) to create a minimal layers table entry and a fields table entry in the database for the layer
  1. Uses the EnvironmentalGeoserverLoader java tool to create the layer in geoserver (linked to the geotiff on disk), and define the style to use for the layer (using the .sld file that was generated earlier).