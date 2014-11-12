# Introduction

Listing of local directory structure and some local files required for operation of Spatial Portal.


# Details
  * /data/ala/data/alaspatial
    * Alaspatial working directory.
    * Referenced by alaspatial.properties.
    * Contains interlayer associations, layerDistances.properties, and temporary cut grid files generated for analysis requests.
  * /data/runtime/output
    * Contains analysis output as ` <method> `/` <analysis id> `/` <analysis output files> `.
    * Referenced by webportal\_config.xml, layer.properties, alaspatial.properties.
    * Mapped to URL /output
  * /data/runtime/files
    * General files location.
    * Referenced by TODO, find reference in alaspatial
    * Mapped to URL /files
  * /data/ala/data/layers
    * Layers directory.  Contains layers/analysis, layers/ready/diva, layers/ready/shapes, layers/ready/shapes\_diva, layers/ready/diva\_cache
    * Top and sub directories referenced by webportal\_config.xml, layer.properties, alaspatial.properties.
    * This is root of PostGIS table column layersdb.layers.path\_orig