# Introduction

This page provides an overview of the layer ingestion process.

This page only covers the ingestion of contextual and environmental layers. For the ingestion of expert distribtions, refer to ExpertDistributionsLoadProcess.

# Data acquisition

Before a layer can be ingested, the raw spatial data and associated metadata must be acquired.

The following metadata is required:

  * Layer data, typically a shapefile or set of ESRI grid files
  * **description** - appears as 'Description' on the layer info page - should be 1 line
  * **source** - org who is author or custodian
  * **notes** - a fuller description of the layer, including methodology, lineage of the data, etc
  * **displayname** - the name of the layer used in all lists, etc.
  * **metadatapath** - url link to metadata file hosted by the author/custodian
  * **mddatest** - date that the metadata entered into this table was last modified
  * **citation\_date** - date that the spatial data was published
  * **source\_link** - url link to the source organisation or org's page for this data set
  * licensing details for the layer
  * Scale used for the layer if applicable
  * Units used for the layer if applicable

The licencing details for the layer and a copy of the raw data must be provided to Miles Nicholls in the data management team.

# Initial check of raw data
Perform the following to get familiar with a new set of raw spatial data:

  * Load layer in uDig or another tool such as QuantumGIS
  * Check datum
  * Overlay with Australian basemap - does the shape make sense
  * For a shape file - for each polygon, check that id, name and description can be determined from the shapefile attributes.

If there are any problems, contact the data contact person and get them to fix the data.

# Layer ingestion tools
The layer ingestion process relies on a number of java tools and shell scripts that are located in the **layer-ingestion** project. These tools need to be deployed to both the development and production servers.

Follow the steps below to build and deploy the layer ingestion tools:

  1. If you wish to run analysis and tabulation (discussed below) on the target server, open the following configuration files in the layer-ingestion project source tree. Check the values in these files, especially those that refer to the database, geoserver and geonetwork. Modify these values as appropriate.
    * src/main/resources/spring/app-config.xml
    * src/main/resources/spring/data-config.xml
    * src/main/resources/alaspatial.properties
    * src/main/resources/layer.properties

  1. Copy the zip file **layer-ingestion-1.0-SNAPSHOT-bin.zip** from the output of the latest hudson build of the project to the desired destination on the target server
  1. Extract the zip file. The extracted content is a directory that contains the following:
    * layer-ingestion project main jar
    * lib directory containing dependent jars
    * various shell scripts
  1. Make all shell scripts executable using chmod 777.
  1. Most scripts contain variables (defined using _export_) for database and geoserver passwords. By default these values are simply placeholders. Update the scripts with the correct password values
  1. Finally, each script also contains variables (defined using _export_) for specific values used in the ingestion of an individual layer. The values for these variables will need to be modified for each layer that is ingested. The definitions of such variables are clearly defined at the top of the script.

**NOTE:** The scripts provided in the layer-ingestion project are not intended to be used with all raw spatial datasets without modification. They address the primary use cases, however they may need to be modified slightly in order to be used with specific raw data that is received.

# Select an id for the new layer
Layers are given sequential numeric ids in order of their ingestion. To work out what the id for the layer you are ingesting should be, run the following SQL query:

```
select max(id) + 1 from layers;
```

**NOTE** - For some contextual layers, second fields table entries were created with a '1' in front of the id for the layer, e.g. 'cl1604' for layer 604. This means that even if you choose a valid layer id, you may experience errors when you attempt to create fields table entries for the layer. In this case, it is probably best to just skip over the problematic id.

# Layer ingestion proper
If you have followed the steps described above, you are now ready to ingest a layer.

The layer ingestion process differs depending on whether the layer is _environmental_ or _contextual_. In general raster data is ingested as  environmental layers, and polygonal data is ingested as contextual layers. However in some cases contextual layers are ingested from raster data.

The pages below go through the specifics of how to ingest environmental and contextual layers:

  * [Environmental layer load process](wiki/EnvironmentalLayerLoadProcess)
  * [Contextual layer load process](wiki/ContextualLayerLoadProcess)

# Deprecation of layers
A newly ingested layer may supersede a existing layer. In this situation the old layer will need to be deprecated.

## Deprecation of environmental layers
Complete the following process to deprecate an environmental layer:
  1. Disable the layer's entry in the layers table
  1. Set the follow values to false in the fields table entry for the layer in the fields table:
    * enabled
    * analysis
    * addtomap

This can be achieved by running the following SQL statements against both the development and production databases
```
UPDATE layers set enabled = false WHERE id = <LAYER-ID>;
UPDATE fields set enabled = false, analysis = false, addtomap = false WHERE spid = '<LAYER-ID>';  
```

## Deprecation of contextual layers
Complete the following process to deprecate a contextual layer:
  1. Disable the layer's entry in the layers table
  1. Set the follow values to false in the fields table entry for the layer in the fields table:
    * enabled
    * namesearch
    * intersect
    * analysis
    * addtomap
  1. Set 'namesearch' to false and name\_id to null for all objects associated with the deprecated layer. Clear out and regenerate the obj\_names table.

This can be achieved by running the following SQL statements against both the development and production databases
```
UPDATE layers set enabled = false WHERE id = <LAYER-ID>;
UPDATE fields set enabled = false, namesearch = false, "intersect" = false, analysis = false, addtomap = false WHERE spid = '<LAYER-ID>';

BEGIN TRANSACTION;
DELETE FROM obj_names;
UPDATE objects set namesearch=fields.namesearch,name_id=NULL FROM fields WHERE objects.fid=fields.id AND fields.namesearch <> objects.namesearch;
INSERT INTO obj_names (name) SELECT lower(objects.name) FROM objects LEFT
OUTER JOIN obj_names ON lower(objects.name)=obj_names.name WHERE
obj_names.name IS NULL AND objects.namesearch = true GROUP BY
lower(objects.name);
UPDATE objects SET name_id=obj_names.id FROM obj_names WHERE
lower(objects.name)=obj_names.name AND objects.namesearch=TRUE;
END TRANSACTION;  
```

After having cleared out and regenerated the obj\_names, you may also want to regenerate the indexes that use this table. This is not compulsory but is good practice:
```
DROP INDEX idx_objects_name_id;
DROP INDEX objects_namesearch_idx;
CREATE INDEX idx_objects_name_id ON objects USING btree (name_id);
CREATE INDEX objects_namesearch_idx ON objects USING btree (namesearch);
```