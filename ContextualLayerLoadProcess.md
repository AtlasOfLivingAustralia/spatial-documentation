# 1. Create layer from raw data

Contextual layers are typically created from ESRI shape files. See [Creating a contextual layer from an ESRI shapefile](wiki/ContextualLoadShape)

In some cases, a contextual layer may need to be created from gridded (raster) data. In many cases, the dataset can simply be converted into a shapefile by _polygonizing_ - see [Creating a contextual layer from gridded data by polygonizing](wiki/ContextualLoadGriddedPolygonize)

A gridded dataset may be sufficiently large that polygonizing is not feasible. In this situation, A contextual layer can be created using the [GridClassBuilder](https://code.google.com/p/alageospatialportal/source/browse/trunk/layers-store/src/main/java/org/ala/layers/grid/GridClassBuilder.java) tool. See [Creating a contextual layer from gridded data using the GridClassBuilder tool](wiki/ContextualLoadGridClassBuilder).

# 2. Entry of metadata
See [Layer Ingestion Metadata Entry](wiki/LayerIngestionMetadataEntry)

# 3. Edit and or add fields table entries
If the layer was loaded using the [GridClassBuilder tool](wiki/ContextualLoadGridClassBuilder), You may want to edit the fields table entries created by the tools. Specifically, if you wish for the layer to be _tabulated_ you will need to set the _intersect_ value in its field table entry to true. A layer should be tabulated if the layer contains multiple polygons with a reasonable spatial extent. If you wish the objects generated from the fields table entry to be searchable via name search, set the _namesearch_ value to true.

Note that the values of intersect and namesearch are supplied as arguments for the other load methods - [shapefile](wiki/ContextualLoadShape) and [polygonizing](wiki/ContextualLoadGriddedPolygonize)

You may also wish to create additional fields table entries.

See [Fields table entries](wiki/FieldsTableEntries).

# 4. Testing part 1
Do a quick test on the development server to ensure that the layer data has been loaded correctly. Complete steps 1 and 2 from [Testing](wiki/#8._Testing) below.

# 5. Migration of layer to production server
Once a layer has been loaded on the development server and tested there, it is ready to be migrated to the production server.

The [layer-ingestion](wiki/LayerIngestionProcess#Layer_ingestion_tools) project contains a scripts to assist with this. You will need a different script depending on which method was used to create the layer from raw data.

For contextual layers created from shapefiles, or from gridded data by polygonizing, use one of two scripts. Use migrate\_contextual\_shapefile\_with\_sld.sh if a custom sld legend was created for the layer. Otherwise, use migrate\_contextual\_shapefile.sh

Both these scripts do the following:
  * Copy the appropriate ESRI shape files
  * Copy database layer and fields entries
  * Create a database table from the copied shape file
  * Generate database object table entries
  * Create the layer in the production instance of geoserver
In addition, migrate\_contextual\_shapefile\_with\_sld.sh also copies the custom sld from dev to prod.

The top of the script needs to be edit to supply the layer id (numeric) the layer short name, the layer description and the ssh username to use. Copying of files is done using scp with this username. You will be prompted while the script is running to supply the ssh password.

```
export SSH_USERNAME=username
export LAYER_ID=990
export LAYER_SHORT_NAME=short_name
export LAYER_DESCRIPTION="description of layer"
```

These scripts should be run on the **production** server.

No script currently exists to automate the migration of a layer created using the GridClassesBuilder tool. These are the steps that need to be undertaken however:
  * Copy the layer and fields table entries for the layer from dev to prod (the java class MigrateLayerDatabaseEntries in the layer-ingestion project can be used for this)
  * Copy the appropriate files from the shape\_diva folder from dev to prod.

# 6. Background processing of layer
**IMPORTANT** - Tabulation step 1 typically takes an extremely long time to run (i.e. over six weeks!). In practice it may be better to run the lines of the script up to tabulation step 1, and then run the remaining steps once the process running tabulation step 1 is compelete.

Once a layer has been migrated to the production server, some additional processing needs to be done before it can be considered fully ingested. This processing has to be done on the production server due to its heavy memory requirements.

The [layer-ingestion](wiki/LayerIngestionProcess#Layer_ingestion_tools) project contains a script named contextual\_background\_processing.sh.

Note that step 3 only needs to be performed if the layer needs to be tabulated. If that is not the case, simply comment out the relevant lines of the script.

Running this script will do all the required work. The work that is does is described below. The script needs to be edited with each run so that the variable _PATH\_TO\_RECORDS\_CSV_ points to the latest latest longitude, latitude and lsid dump file (described below)

```
export PATH_TO_RECORDS_CSV="/data/ala/data/layers/process/density/built_120523/_records.csv"
```

## 1. Generate layer thumbnails
See [BackendProcesses#Layer\_thumbnails](wiki/BackendProcesses#Layer_thumbnails). This will generate a .jpg thumbmail for each new layer at /data/ala/runtime/output/layerthumbs.

## 2. Run AnalysisLayerUtil java tool
This tool sets layers up for analysis. Run it with the argument "auto" and the JVM argument -DANALYSIS\_RESOLUTIONS=0.5,0.01. This tool creates diva grid files for each layer in subdirectories of /data/ala/data/layers/analysis. All analysis grid files are created with the same origin and grid size, which permits analysis to be done.

## 3. Run TabulationGenerator java tool
See [BackendProcesses#Tabulation](wiki/BackendProcesses#Tabulation). This tool generates tabulations for pairs of contextual layers.

This tool is broken up into a number of steps. A command line argument passed to the tool is used to indicate which step should be run. The steps should be run in this order:

  * Step 1 - Creates the tabulation table entries in the database
  * Step 3 - Deletes invalid objects - removes anything that does not have an area
  * Step 5 - Fills in the species and occurrence counts in the database. Requires the latest longitude, latitude and lsid dump file as an additional command line argument (see below).
  * Step 6 - Same as Step 5, for large grid files for which the process followed in Step 5 would take too long. Outputs a number of .sql files. The SQL in these files then get executed against the database. Requires the latest longitude, latitude and lsid dump file as an additional command line argument (see below).
  * Step 4 - Calculates and fills in the area values for the tabulations table.

Check the output of the TabulationGenerator for error messages. In addition, go to http://spatial.ala.org.au/ws/tabulations and check the html for species and area for any newly tabulated layers. If you receive a 404 message, or the tabulated output contains zeros when it shouldn't, something has gone wrong with the tabulation. One possible cause for such a problem is that the postgis table and shapefile for the layer in question are mismatched. See [below](wiki/ContextualLayerLoadProcess#Mismatched_postgis_table_and_shape_file) for details.

### The longitude, latitude and lsid dump file
This information is extracted weekly from the biocache via a cron job which runs the script /data/ala/data/layers/process/density\_layers.sh. The cron job is run on the production server. Each time the cron job is run, a subdirectory named built` _ ` + _date_ is created under /data/ala/data/layers/process/density on the production server. The longitude, latitude and lsid dump file is located in this subdirectory, in a file named ` _ `records.csv.

# 7. Editing cache settings in geoserver
For every newly loaded layer, the cache settings for the layer need to be edited. This step must be performed manually.
  1. Find the new layer in Layers view in geoserver.
  1. Edit the configuration of the layer
  1. Go to the "publishing" tab
  1. Under "HTTP Settings", check the box marked "Response Cache Headers" and set the "Cache Time (seconds)" to 3600.

# 8. Testing
Complete the following steps to test a newly loaded contextual layer:

  1. Check base metadata and thumbnail in http://spatial.ala.org.au/layers (or http://spatial-dev.ala.org.au/layers)
  1. View layer and associated legend in http://spatial.ala.org.au (or http://spatial-dev.ala.org.au/layers)  to check for display issues.
    * Check map display – extent and colour ranges, registration
    * Check metadata pop-up for layer
    * Check legend for colours and units
    * Check hover tool values
  1. Check Add to Map | Areas | Select area from polygonal layer (click on poly from layer)
  1. Check Add to Map | Areas | Select gazetteer polygon (with class value)
  1. Check Tools | Tabulation to ensure combinations have been calculated
  1. Answer “Is the layer valuable for use in Prediction?”. If yes, then include and test.
  1. Check availability and utility for use in Export | Point sample

# 9. Migration of background processing data to the development server
TODO a script should perhaps be written to automate this.

Finally, if the layer tests successfully on the production server, the results of the background processing need to be copied back to the development server to ensure that both servers are consistent with each other.

Copy the following data to the equivalent locations on the development server using rsync:
  * /data/ala/data/alaspatial/layerDistances.properties as generated by LayerDistanceIndex
  * Analysis files for the layer in subdirectories of /data/ala/data/layers/analysis
  * The layer thumbnails at /data/ala/runtime/output/layerthumbs

In addition, the data in the tabulation table in the database needs to be migrated. Dump the production database's copy of the tabulation table and then restore it on the development server.

# Common problems

## 3d polygons
> 3d polygons cause problems. A 3d polygon can be identified by the following output when converting it to postgis data using shp2pgsql:
```
Output shape: PolygonZ
```
> > This can be fixed by re-exporting the shape file from its associated postgis table using the a pgsql2shp command similar to the following example:
```
pgsql2shp -h ala-devmaps-db.vm.csiro.au -p 5432 -u postgres -P postgres -r layersdb "select ala_name, st_geomfromtext(st_astext(the_geom),4326) as the_geom from \"990\""
st_geomfromtext(st_astext(the_geom), 4326)
```

## Badly formed polygons

Badly formed polygons can cause problems when generating tabulation data. This is because postgis functions for unions and intersections don't work properly with badly formed polygons in some cases. Such a problem may be occurring if an error message is output during the tabulation generation, or if a layer simply does not occur in the list of available layers for tabulation after tabulations have been generated.

Check the postgis table for problems using the ST\_IsValid, ST\_IsValidDetail and ST\_IsValidReason postgis functions. Note that these functions may report issues that do not actually cause problems during the tabulation generation.

Badly formed polygons that are causing problems during tabulation need to be fixed manually using postgis functions (i.e. editing the WKT) and then generating a new shape file to put in layers/ready using psql2shp. If this is not possible or feasible, contact the source organisation and obtain a fixed version of the data.

## Mismatched postgis table and shape file
If the postgis table and shape file for a layer do not contain identical data, problems will occur when generating tabulations for the layer. A mismatch may occur if additional columns are added to the postgis table, or if the data in the postgis table is modified in some way. To fix this problem, a new shapefile needs to be generated from the postgis table using pgsql2shp.

In addition, note that if the gid column is used as the sid, sname or sdescription for one of the layers fields table entries, a shape file will need to be generated using pgsql2shp pgsql2shp must be run with the "-r" flag. This is because the gid values are generated and are not stored in the shapefile by default. See [Object Creation](wiki/ContextualLoadShape#Object_creation).