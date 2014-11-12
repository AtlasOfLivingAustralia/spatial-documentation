# Creating an environmental layer from DIVA grid data
The layer-ingestion project contains a script named environmental\_load\_diva.sh which will load an environmental layer from DIVA grid format. There are a number of variables defined at the top of the script that need to be modified for each new layer that is being loaded. These are:
  * The layer id (numeric)
  * The short name for the layer
  * The display name of the layer
  * The units used in the layer
  * The path to the directory containing the raw DIVA grid files.

Note that it is assumed that the relevant DIVA grid files have the same name as layer short name (aside from the file extensions)

```
export LAYER_ID=666
export LAYER_SHORT_NAME="new_layer" 
export LAYER_DISPLAY_NAME="description of new layer"
export UNITS="layer units"
export RAW_DIVA_DIR="/data/ala/data/layers/raw/new_diva_layers"
```

The environmental\_load\_diva.sh script does the following things
  1. Copies the diva files to the /data/ala/data/layers/ready/diva directory
  1. Generates an sld legend file from the diva files using the GridLegend tool
  1. Converts the diva files to .bil format
  1. Uses gdal\_translate to convert the .bil data into a a geotiff. WGS 84 datum (EPSG 4326) is assumed here
  1. Uses the EnvironmentalDatabaseEntryCreator java tool (in source control in the layer-ingestion project) to create a minimal layers table entry and a fields table entry in the database for the layer
  1. Uses the EnvironmentalGeoserverLoader java tool to create the layer in geoserver (linked to the geotiff on disk), and define the style to use for the layer (using the .sld file that was generated earlier).

**IMPORTANT:**
  * Only DIVA grids in WGS 84 (EPSG 4326) are supported for ingestion into the spatial portal.
  * The units listed in the diva header (.grd) file are used by the GridLegend tool. If no units are present in that file, no units will be displayed in the spatial portal, regardless of the units value for the layer in the layers table. If the .grd file for a layer does not contain units, it will need to be modified to add the units before ingestion.