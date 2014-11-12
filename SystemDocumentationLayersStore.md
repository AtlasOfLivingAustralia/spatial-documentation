# Introduction

API and utils for contextual, environmental, distribution and checklist layers.

Utils operate on local files and Postgres/PostGIS.


# Dependencies
  * [geoserver](wiki/SystemDocumentationGeoserver)
  * [geonetwork](wiki/GeonetworkAndAnzmet)
  * [gdal](wiki/SystemDocumentation)
  * [local files](wiki/SystemDocumentationLocalFiles)
  * [layers database](wiki/SystemDocumentationLayersDB)


# Configuration
  * WEB-INF/classes/layer.properties, update for the environment.
    * GEOSERVER\_URL=http://spatial.ala.org.au/geoserver
    * GEONETWORK\_URL=http://spatial.ala.org.au/geonetwork
    * GDAL\_PATH=/usr/local/gdal/apps/
  * WEB-INF/spring/data-config.xml, update for Postgres.


# Deployment
For utilities
  1. unpack layers-store-1.0.0-SNAPSHOT-assembly.jar
  1. configure
  1. run utils from unpacked directory.  e.g. java -Xmx10000m -cp .:lib/**org.ala.layers.grid.GridCacheBuilder ` <params> `**


# Utilities
  * org.ala.layers.grid.GridCacheBuilder
    * build a fresh grid cache using diva grids in ready/diva and output to ready/diva\_cache
  * org.ala.layers.grid.GridCacheReader
    * test grid cache performance
  * org.ala.layers.tabulation.TabulationGenerator
    * progressive steps to update postGIS table layersdb.tabulation
  * org.ala.layers.util.AnalysisLayerUtil
    * Build resolutions standardised grids using layersdb.fields enabled grids and shapes in ready/diva and ready/shape.
    * Outputs diva grid files as analysis/` <resolution> `/` <field.id> `
  * org.ala.layers.util.Bil2diva
    * Convert a .bil/.hdr to .gri/.grd
  * org.ala.layers.util.Diva2bil
    * Convert a .gri/.grd to .bil/.hdr
  * org.ala.layers.util.IntersectUtil
    * Intersect coordinates with layers
  * org.ala.layers.stats.ObjectsStatsGenerator
    * Updates layersdb.objects table with bbox and consistently calculated area sqkm.


# API
Batch sampling example
```
 String points = "-22,132,-23,131";
 String fields = "cl22,el804";
 LayerIntersectDAO layerIntersectDAO = Client.getLayerIntersectDAO();
 IntersectUtil.writeSampleToStream(fields.split(","), points.split(","), layerIntersectDAO.sample(fields, points), System.out);
```

There are 3 types of sampling functions available in LayerIntersectDAO

  1. samplingFull; returns additional information on a single point for a list of field ids (or layer names).  This includes the layer display name, pid, and some other stuff.  Used with the hover tool.
  1. sampling(String fieldIds, String pointsString), sampling(String[wiki/](wiki/) fieldIds, double[wiki/](wiki/)[wiki/](wiki/) points), sampling(IntersectionFile[wiki/](wiki/) intersectionFiles, double[wiki/](wiki/)[wiki/](wiki/) points);
    * These are general batch sampling that operate on a list of points.  Appropriate for large numbers of points. There is a ws that uses these.
    * Webportal should be using this but for now duplicates the functionality here.
    * The output is an array of Strings, with each String being a single \n separated column for the input field ids.
    * This permits operations on a large number of points without as much overhead as java Strings usually imposes.  Any variation on String [wiki/](wiki/)[wiki/](wiki/) has poor performance.
  1. sampling(double longitude, double latitude) and sampling(String fieldIds, double longitude, double latitude); Both of these make use of the GridCache and any configured shapeFileCache (see layer.properties or IntersectConfig for dynamic additions).
    * The first, sampling(long,lat), returns only values in the grid or shape caches.  Its purpose is to provide the fastest method of returning intersections on all layers for a specified point.  It would be used (below an IO determined threshold) in place of the batch sampling for smaller number of occurrences when sampling on all layers.
    * The second, sampling(String fieldIds, double longitude, double latitude) does the same but will still return results for the specified fields if they are not cached.