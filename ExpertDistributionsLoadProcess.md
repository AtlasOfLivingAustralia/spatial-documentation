# Data Format
The following process assumes that the expert distribution data will be received in the following format. This is the format used by Tony Rees from CMAR:

  1. A set of zip files - one per expert distribution. These zip files are named CAAB + _spcode_.zip, e.g. CAAB37018024.zip. Each zip file contains an ESRI shapefile (.shp) for the expert distribution, and associated files.
  1. A spreadsheet with metadata for the expert distributions. It will have the following columns in order:
    * spcode
    * scientific name
    * authority\_full
    * common name
    * family
    * genus name
    * specific name
    * min depth
    * max depth
    * pelagic flag
    * estuarine flag
    * coastal flag
    * demersal flag
    * group name
    * genus exemplar
    * family exemplar
    * CAAB species number
    * CAAB species URL
    * CAAB family number
    * CAAB family URL
    * Metadata UUID
    * Metadata URL

# Load Process
  1. Copy the set of zip files to a subdirectory of /data/ala/data/layers/raw on the development server.
  1. Unzip each zipfile to its own directory. You can use the following script to do this. It is located in the layer-ingestion project, as "expert\_distributions\_extract\_zips.sh"
```
#!/bin/bash

for file in ./*
do 
if [[ ${file}  =~ \./(.+)\.zip ]]
then
	mkdir ${BASH_REMATCH[1]}
	unzip ${file} -d ${BASH_REMATCH[1]}
	echo $?
fi
done
```
  1. Convert the shapefile for each expert distribution into postgis data using the shp2pgsql tool. Use the following script to do this, which is located in the layer ingestion project as "expert\_distributions\_convert\_shapefiles\_postgis.sh"
```
#!/bin/bash

first_shp_file_extracted=0

for file in ./CAAB*/*
do
if [[ ${file} =~ \.shp ]]
then
	if [[ $first_shp_file_extracted -eq 0 ]]
	then
		shp2pgsql -c -I -s 4326 ${file} new_distributionshapes
		let first_shp_file_extracted=1
	else
 		shp2pgsql -a -s 4326 ${file} new_distributionshapes
	fi
fi
done		
```
> > redirect the output from this script to layersdb on ala-devmaps-db using psql. It will create a new table named "new\_distributionshapes", with one row per expert distribution.
  1. Copy the geometry data and the scientific name (with "Expert distribution " appended to the front) to the table "distributionshapes":
```
INSERT INTO distributionshapes (id, the_geom, name)
SELECT nextval('distributionshapes_id_seq'), the_geom, 'Expert distribution ' || scientific 
FROM new_distributionshapes
```
> > Run this query against the development database (ala-devmaps-db)
  1. Run the DistributionGenerator tool. This is located in the layer-store project, in the package org.ala.layers.distribution. You will need to modify the class so that the JDBC url points the the development database. This tool calculates and fills in the area\_km values for rows in the distributionshapes table.
  1. Convert the supplied spreadsheet to CSV format. Delete the first line in the file containing the column headers. The CSV file should use ";" as the field delimiter, so the content of fields cannot contain ';'.
  1. Run the ExpertDistributionsDataImportTool. This is located in the layer-ingestion project, in the package au.org.ala.layers.ingestion. This tool takes the CSV file generated in the previous step (the path to this file is the only argument that the tools takes) and inserts the data contained within into the distributionsdata table in the dev database (ala-devmaps-db).
  1. Run the following sql against the dev database. Note that the query below matches using names - if there is any mismatch between the names in the CSV and the names in the shapefiles this will cause problems:
```
UPDATE distributiondata
SET bounding_box = ST_ENVELOPE(distributionshapes.the_geom),
    wmsurl = '<COMMON_GEOSERVER_URL>/wms?service=WMS&version=1.1.0&request=GetMap&layers=ALA:Distributions&format=image/png&viewparams=s:' || distributionshapes.id,
    geom_idx = distributionshapes.id FROM distributionshapes 
WHERE 'Expert distribution ' || scientific = distributionshapes.name AND type='e';

DELETE FROM distributions;

INSERT INTO distributions 
    SELECT d.gid, d.spcode, d.scientific, d.authority_, d.common_nam, d.family, d.genus_name,
        d.specific_n, d.min_depth, d.max_depth, d.pelagic_fl, d.estuarine_fl, d.coastal_fl, d.desmersal_fl,
        d.metadata_u, d.wmsurl, d.lsid, d.family_lsid, d.genus_lsid, d.caab_species_number, d.caab_family_number,
        o.the_geom, o.name AS area_name, o.pid, d.type, d.checklist_name, o.area_km, d.notes, d.geom_idx, d.group_name,
        d.genus_exemplar, d.family_exemplar, d.data_resource_uid, d.image_quality, d.bounding_box
    FROM distributionshapes o
    JOIN distributiondata d ON d.geom_idx = o.id;
```
> > This query copies relevant geometry information from the distributionshapes table to the distributiondata table. It also creates a wmsurl that can be used to obtain the expert distribution from geoserver. After query is run - check any rows in distributiondata that have a null geometry - only rows for checklist data should have a null geometry.
  1. This completes the load process. After successful testing of the new expert distributions, you can drop the table new\_distributionshapes.

# Migration to the production server
Follow the following process to migrate expert distributions data to the production server:

  1. Use pgdump to back up the distributiondata and distributionshapes tables on the production server.
  1. Also use pgdump to dump the distributiondata and distributionshapes tables from the development database to disk
  1. Copy the files generated in the previous step to the production database server
  1. drop the distributiondata and distributionshapes tables from the production database server
  1. Use pgrestore to create new versions of the distributiondata and distributionshapes tables from the data dumped from the development database.