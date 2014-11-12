# 1. Create layer from raw data

Environmental layers are typically created from raw .adf, .bil or DIVA grid data.

The pages below describe the steps to follow in handling each format. The steps described in those pages should be carried out on the development server.

[Creating an environmental layer from ESRI Grid format (.adf) data](wiki/EnvironmentalLoadAdf)

[Creating an environmental layer from .bil data](wiki/EnvironmentalLoadBil)

[Creating an environmental layer from DIVA grid data](wiki/EnvironmentalLoadDiva)

Data for envrionmental layers may be received in other formats. The processes describe above should be able to be adapted to handle other formats.

# 2. Entry of metadata
See [Layer Ingestion Metadata Entry](wiki/LayerIngestionMetadataEntry)

# 3. Testing part 1
Do a quick test on the development server to ensure that the layer data has been loaded correctly. Complete steps 1 and 2 from [Testing](wiki/#7._Testing) below.

# 4. Migration of layer to production server
Once a layer has been loaded on the development server and tested there, it is ready to be migrated to the production server.

The [layer-ingestion](wiki/LayerIngestionProcess#Layer_ingestion_tools) project contains a script named migrate\_environmental.sh. This script does the following to migrate an environmental layer from the development server to the production server:
  * Copies diva and geotiff files
  * Copies database entries
  * Creates identical record for the layer in the production instance of geoserver.

The top of the script needs to be edit to supply the layer id (numeric) the layer short name, and the ssh username to use. Copying of files is done using scp with this username. You will be prompted while the script is running to supply the ssh password.

```
export SSH_USERNAME=username
export LAYER_ID=990
export LAYER_SHORT_NAME=alwc4
```

The script should be run on the **production** server.

# 5. Background processing of layer
Once a layer has been migrated to the production server, some additional processing needs to be done before it can be considered fully ingested. This processing has to be done on the production server due to its heavy memory requirements. Note that the outputs of the script as discussed below should be copied back to the dev server to permit certain SP operations to be used on the dev server.

The [layer-ingestion](wiki/LayerIngestionProcess#Layer_ingestion_tools) project contains a script named environmental\_background\_processing.sh.

Running this script will do all the required work. The work that is does is described below:

## 1. Generate layer thumbnails
See [BackendProcesses#Layer\_thumbnails](wiki/BackendProcesses#Layer_thumbnails). This will generate a .jpg thumbmail for each new layer at /data/ala/runtime/output/layerthumbs.

## 2. Run GridCacheBuilder java tool

This java tool stitches grid files together so that when you intersect on a single point you don't need to
open so many files - this is done because disk seek time was causing problems with sampling (intersecting).

When running the tool, delete the contents of /data/ala/data/layers/ready/diva\_cache and run the tool - this will
repopulate the directory.

## 3. Run AnalysisLayerUtil java tool
This tool sets layers up for analysis. Run it with the argument "auto" and the JVM argument -DANALYSIS\_RESOLUTIONS=0.5,0.01,0.025. This tool creates diva grid files for each layer in subdirectories of /data/ala/data/layers/analysis. All analysis grid files are created with the same origin and grid size, which permits analysis to be done.

## 4. Run LayerDistanceIndex java tool
This tool generates "distance calculations" for pairs of layers. The distance calculation for a pair of layers is measure of similarity between the layers. These values are used to generate the "traffic light" colouring when adding layers from the list in the spatial portal.

The tool's output is written to a file named layerDistances.properties. This file is placed at /data/ala/data/alaspatial (the "workingdir") defined in alaspatial.properties in the alaspatial project.

# 6. Edit cache settings in geoserver
For every newly loaded layer, the cache settings for the layer need to be edited. This step must be performed manually, on both the development and production servers.
  1. Find the new layer in Layers view in geoserver.
  1. Edit the configuration of the layer
  1. Go to the "publishing" tab
  1. Under "HTTP Settings", check the box marked "Response Cache Headers" and set the "Cache Time (seconds)" to 3600.

# 7. Testing
Complete the following steps to test a newly loaded environmental layer:

  1. Check base metadata and thumbnail in http://spatial.ala.org.au/layers (or http://spatial-dev.ala.org.au/layers)
  1. View layer and associated legend in http://spatial.ala.org.au (or http://spatial-dev.ala.org.au) to check for display issues.
    * Check map display – extent and colour ranges, registration
    * Check metadata pop-up for layer
    * Check legend for colours and units
    * Check hover tool values
  1. Check availability and utility for use in Add to Map | Areas | Environmental envelope
  1. Check availability and utility for use in Scatterplot
  1. Check availability and utility for use in Classification
  1. Check availability and utility for use in Prediction
  1. Check availability and utility for use in GDM
  1. Check availability and utility for use in Export | Point sample

# 8. Migration of background processing data to the development server
TODO a script should perhaps be written to automate this.

Finally, if the layer tests successfully on the production server, the results of the background processing need to be copied back to the development server to ensure that both servers are consistent with each other.

Copy the following data to the equivalent locations on the development server using rsync:
  * /data/ala/data/alaspatial/layerDistances.properties as generated by LayerDistanceIndex
  * Analysis files for the layer in subdirectories of /data/ala/data/layers/analysis
  * The contents of the /data/ala/data/layers/ready/diva\_cache directory
  * The layer thumbnails at /data/ala/runtime/output/layerthumbs

# Common problems

  * Grid files must be less than 2GB in size if they use float values. This is due to a limitation of the tools developed by the ALA to assist in loading environmental layers.

  * When loading NETCDF data, the conversion from NETCDF to bil format does not translate the nodata value. Refer to the file /data/ala/data/layers/process on ala-devmaps to see how to address this. Note that there is currently not an automated process to ingest NETCDF data.

  * gdal tools will not properly reproject .bil files that are missing an associated .prj file.