# Introduction

Provides web services for analysis functions.

Contains classification and density layer functions.


# Dependencies
  * [layers-store](wiki/SystemDocumentationLayersStore)
  * [geoserver](wiki/SystemDocumentationGeoserver)
  * biocache-service
  * [local files](wiki/SystemDocumentationLocalFiles)
  * gdal
  * imagemagick


# Configuration
  * WEB-INF/classes/alaspatial.properties, update for environment
```
        # Directory to contain various analysis related files both permanent and temporary.
	workingdir=/data/ala/data/alaspatial/
	
        # external URL to this instance.
	url=http://localhost/alaspatial/

        # Analysis output directory.  Must contain the subdirectory "output".
        # and external URL path where output.url + "/output/" is directed to output.dir + "/output/"
	output.dir = /usr/local/tomcat/webapps/alaspatial/output
	output.dir = http://localhost/alaspatial/output

        # Or if there is a redirect in place so output.url + "/output" points at /data/ala/runtime/output
	#output.dir=/data/ala/runtime/	
	#output.url=http://localhost	
	
	# Analysis layers directory.  Data is setup by AnalysisLayerUtil in layers-store
        # during the layer ingestion process.
	layer.dir=/data/ala/data/layers/analysis/
	
	# Command line to run ALOC.  Input parameters are provided at runtime.
	aloc.cmd=java -Xmx8000m -cp /usr/local/tomcat/webapps/alaspatial/WEB-INF/classes/.:/usr/local/tomcat/webapps/alaspatial/WEB-INF/lib/\* org.ala.spatial.analysis.service.AlocService
	
	# Command to run GDM.  Input parameters are provided at runtime.
	gdm.cmd=/data/ala/modelling/gdm/DoGdm 
	
	#from www.cs.princeton.edu/~schapire/maxent/
	maxent.cmd=java -Xmx5000m -jar /data/ala/modelling/maxent/maxent.jar
	
        # Path to gdal functions.
	gdal.dir=/data/ala/utils/gdal-1.7.2/apps/
	
        # Path to imagemagick functions.
	imagemagick.dir=/usr/bin/convert -quiet
	
	#internal url for POSTing new layers to geoserver
	geoserver.url=http://localhost:8082/geoserver
	geoserver.username=username
	geoserver.password=password

        # URL to biocache webservices.
        biocache.ws.url=http://biocache.ala.org.au/ws
```
  * layers-store
    * WEB-INF/classes/layer.properties, update for the environment.
    * WEB-INF/spring/data-config.xml, update for Postgres.


# Deployment
  * Deploy as WAR for web services.
  * For classification, LayerDistances or DensityLayers
    1. unpack alaspatial.war
    1. run in unpacked dir.  e.g.

{{ java -Xmx8000m -cp .:lib/**org.ala.spatial.analysis.service.AlocService ` <params> ` }}}**


# Utilities
  * ALOC classification on grid files.
```
org.ala.spatial.analysis.service.AlocService ```
  * Interlayer association distances.
```
org.ala.spatial.analysis.index.LayerDistancesIndex```
  * Build species richness and occurrence density layers using data from biocache-service.
```
org.ala.spatial.analysis.layers.DensityLayers```


# Operation

## Analysis web services
Requesting analysis returns analysis id.  Check the analysis status and get output using the analysis id.

Analysis operates on areas as WKT or envelope. Envelopes can be substituted for WKT. This is preferred when working with envelopes because WKT for envelopes can be very large, taking a long time to generate and to process when using.  Envelopes are defined with a fieldId, min value and max value. Envelopes can be specified for contextual layers using the catagory number as both min and max value.  e.g. "ENVELOPE(cl22,1,1)" for a contextual field or "ENVELOPE(el804,0,1000:el799,0,100)" using ":" to separate multiple layer envelopes to be used together.  See org.ala.spatial.analysis.index.LayerFilter.

Applying an area to layers for analysis involves
1. parsing the area (WKT or envelope)
2. cutting the analysis layers
3. exporting the analysis layers to a working directory
The exported analysis layers are fit to the extents of the requested area with nodatavalue set for grid cells outside.  See org.ala.spatial.util.GridCutter.

alaspatial queue for analysis maximum number of active jobs is set in alaspatial.properties. See org.ala.spatial.util.AnalysisQueue.

## Classification
ALOC is the classification method.  It operates on two or more grid files of the same size and resolution to produce one new layer.

It can be run on the deployed WAR like this:
```
java -Xmx8000m -cp /usr/local/tomcat/webapps/alaspatial/WEB-INF/classes/.:/usr/local/tomcat/webapps/alaspatial/WEB-INF/lib/* org.ala.spatial.analysis.service.AlocService /data/directory_containing_grid_files/ 20 4 /data/output_directory/```
where 20 is the number of groups to identify and 4 are the number of threads to use.

Outputs
  * grid files as ASCII and DIVA grids.
  * sld file for the Geoserver style.
  * classification\_means.csv with group means and the RGB colour values assigned.
  * images of the classification as t\_aloc.png and t\_aloc.tif.
  * extents.txt a file containing width, height and bounds.
  * readme.txt listing the files that are zipped for download when run through the webportal.
  * aloc.log, the log.
  * classification.html, a summary of the analysis.

The webservice is /ws/aloc.  Parameters are
  * gc: target number of groups to produce.
  * area: area as WKT (without additional whitespace) or valid ENVELOPE.
  * res: (optional) target resolution.  Defaults to resolution in alaspatial.properties.
  * envlist: list of two or more environmental layers as fieldIds.  e.g. el804,el799.

Returns the analysisId.
  * Monitor analysis with /ws/job?pid=analysisId&log=true.
  * Output layer is in the geoserver instance as ALA:aloc\_analysisId.  Use http://localhost/geoserver/wms?service=WMS&version=1.1.0&request=GetMap&layers=ALA:aloc_analysisId&FORMAT=image%2Fpng
  * Download zipped output with /ws/download/analysisId

## Sites by Species
Sites by species puts species occurrence records on a grid.  Outputs are optional and can be a species richness grid, occurrence density grid and a tabulated sites by species table.

Outputs
  * occurrence\_density and species\_richness
    * ASCII and DIVA grid files
    * layer images as png and the legends as _legend.png.
    * sld files for geoserver.
  * tabulated sites by species table as SitesBySpecies.csv.
  * metadata as odensity\_meatadata.html, srichness\_metadata.html and sxs\_metadata.html._

The webservice is /ws/sitesbyspecies.  Parameters are
  * qname: output name.
  * speciesq: the biocache query q parameter to retrieve the records.
  * bs: url to the biocache webservices.
  * area: area as WKT (without additional whitespace) or valid ENVELOPE.
  * gridsize: output grid resolution in decimal degrees.  e.g. 0.05.
  * movingaveragesize: size of moving average window.  This only applies to species richness and occurrence density outputs.
  * occurrencedensity: set this parameter to 1 to produce occurrence density output.  Otherwise exclude this parameter.
  * speciesdensity: set this parameter to 1 to produce species richness output.  Otherwise exclude this parameter.
  * sitesbyspecies: set this parameter to 1 to produce tabulated sites by species table output.  Otherwise exclude this parameter.

Returns the analysisId.
  * Monitor analysis with /ws/job?pid=analysisId&log=true.
  * Output layers are in the geoserver instance as ALA:odensity\_analysisId and ALA:srichness\_analysisId.  These have the styles geoserver odensity\_analysisId and srichness\_analysisId.  Use http://localhost/geoserver/wms?service=WMS&version=1.1.0&request=GetMap&layers=ALA:odensity_analysisId&FORMAT=image%2Fpng
  * Download zipped output with /ws/download/analysisId

## Species Richness Layer
The species richness layer is generated by a weekly cron job (root user's crontab) which runs the script species\_richness.sh. This script is located at /data/ala/data/layers/process/. A copy can also be found in source control in the scripts folder in the alaspatial project.

The scripts pulls data generated from the biocache-store via the "endemism data" job in Jenkins (which in turn calls the biocache-store's CalculatedLayerHelper class).

The layer is generated for the entire globe (latitude -90 to 90, longitude -180 to 180) Layer data is output in both ESRI ASCII grid format (.asc) and in DIVA grid format (.gri/.grd). -9999 is used as the no data value.

## Occurrence Density Layer
The occurrence density layer is generated by a weekly cron job (root user's crontab) which runs the script species\_richness.sh. This script is located at /data/ala/data/layers/process/. A copy can also be found in source control in the scripts folder in the alaspatial project.

The scripts pulls data generated from the biocache-store via the "endemism data" job in Jenkins (which in turn calls the biocache-store's CalculatedLayerHelper class).

The layer is generated for the entire globe (latitude -90 to 90, longitude -180 to 180) Layer data is output in both ESRI ASCII grid format (.asc) and in DIVA grid format (.gri/.grd). -9999 is used as the no data value.

## Endemism Layer
The endemism layer is generated by a weekly cron job (root user's crontab) which runs the script endemism.sh. This script is located at /data/ala/data/layers/process/. A copy can also be found in source control in the scripts folder in the alaspatial project.

The scripts pulls data generated from the biocache-store via the "endemism data" job in Jenkins (which in turn calls the biocache-store's CalculatedLayerHelper class).

The layer is generated for the entire globe (latitude -90 to 90, longitude -180 to 180) Layer data is output in both ESRI ASCII grid format (.asc) and in DIVA grid format (.gri/.grd). -9999 is used as the no data value.

## Interlayer Association Distances
This is run as part of the [Backend processes](wiki/BackendProcesses).

Environmental layers distances between each other are measured for the webportal layer list traffic lights and a single association matrix.

Distances can be produced using the deployed WAR and run like this
```
java -Xmx8000m -cp /usr/local/tomcat/webapps/alaspatial/WEB-INF/classes/.:/usr/local/tomcat/webapps/alaspatial/WEB-INF/lib/\* org.ala.spatial.analysis.index.LayerDistanceIndex ```

This will add any missing distances for environmental grid files to layerDistances.properties.

Output is accessed with
  * /ws/layerdistances, for the JSON of each distance in a list.
  * /layers/analysis/inter\_layer\_association\_rawnames.csv, for the output matrix with el row and column labels.
  * /layers/analysis/inter\_layer\_association.csv, for the output matrix with layer display names in row and column labels.