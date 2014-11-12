# Introduction

About the configuration of Geoserver for the Spatial Portal.


# Dependencies
  * [layers database](wiki/SystemDocumentationLayersDB)


# Configuration
  1. In the geoserver data directory add [scale.xml](http://alageospatialportal.googlecode.com/svn/trunk/database/scale.xml) to a new directory "layout".
  1. Create the workspace "ALA".
  1. Create the postgis store "layersdb".
  1. Create a layer named "world".  This is a base layer available in the webportal.
  1. Create the style "alastyles".  Use [alastyles.sld](http://alageospatialportal.googlecode.com/svn/trunk/database/alastyles.sld).
  1. Create the style "envelope\_style".  Use [envelope\_style.sld](http://alageospatialportal.googlecode.com/svn/trunk/database/envelope_style.sld).
  1. Create the style "distributions\_style".  Use [distributions\_style.sld](http://alageospatialportal.googlecode.com/svn/trunk/database/distributions_style.sld).
  1. Create the layer "Distributions"
    * create from: new SQL view on layersdb
    * sql: select ` * ` from distributions where geom\_idx= %s% limit 1
    * params: name="s", default=10000, validation="^[\w\d\s]+$"
    * attributes: the\_geom column, type=Geometry, SRID=4326
    * bounds: -180, -90, 180, 90
    * style: distributions\_style
  1. Create the layer "Objects"
    * create from: new SQL view on layersdb
    * sql: select ` * ` from objects where pid= '%s%'
    * params: name="s", default=1, validation="^[\w\d\s]+$"
    * attributes: the\_geom column, type=Geometry, SRID=4326
    * bounds: -180, -90, 180, 90
    * style: distributions\_style

All layers are added to the database as part of the [Backend processes](wiki/BackendProcesses).