# Introduction

This is the main interface of the Spatial Portal with map and mapping functions. Uses ZK and OpenLayers.


# Dependencies
  * [layers-service](wiki/SystemDocumentationLayersService)
  * [alaspatial](wiki/SystemDocumentationAlaspatial)
  * [geoserver](wiki/SystemDocumentationGeoserver)
  * [actions](wiki/SystemDocumentationActions)
  * BIE
  * biocache-service
  * wkhtmltoimage
  * zk-rangeslider
  * webportal\_config
  * [local files](wiki/SystemDocumentationLocalFiles)


# Configuration
  * run tomcat with -DWEBPORTAL\_CONFIG\_DIR=/data/webportal\_config/dev/
  * /data/webportal\_config/dev/webportal\_config.xml
    * add hosts to ` <tns:proxyAllowedHosts> `
    * set correct geoserver values for ` <geoserver_url> `, ` <geoserver_username> ` and ` <geoserver_password> `
    * set ` <convert_cmd> ` to imagemagick convert
    * set ` <wkhtmltoimage_cmd> ` to wkthtmltoimage path, from http://code.google.com/p/wkhtmltopdf/
    * set ` <print_output_path> ` (local dir) and ` <print_output_url> ` (public url to the same)
    * set ` <print_server_url> ` to this instance including trailing '/'
    * set ` <help_url> `
    * set ` <geonetwork_url> `
    * set ` <session_path> `
    * set ` <sat_url> ` to alaspatial instance
    * set ` <layers_url> ` to layers\_service instance
    * set ` <webportal_url> ` to this instance
    * set ` <sampling_files_path> ` to /data/ala/data/layers/ready/
    * set ` <sampling_files_path> ` to /data/ala/data/layers/ready/
    * remove unavailable contextual layer field ids from ` <shapes_to_cache> `
    * remove unavailable environmental layer names from ` <analysis_layer_sets> `
    * set ` <analysis_output_dir> ` to /data/ala/runtime/output/
    * set ` <collectory_url> ` to http://collections.ala.org.au/ws

# Deployment
Deploy as WAR.

# Reloading
The webportal can have its configuration reloaded (i.e. data pulled from the layers db will be refreshed) by calling /admin/reloadconfig e.g. http://spatial.ala.org.au/webportal/ws/admin/reloadconfig