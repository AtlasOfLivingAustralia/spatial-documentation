# Introduction

The ALA Spatial Portal is made up of 4 web applications. These web applications are separated based on their task and utilise web services to communicate with each other when necessary. However, data is shared between each of the web application as necessary, so a pointer to a global storage is necessary.

The web applications, components, that make up the whole Spatial Portal experience are:
  * ALA Spatial Portal UI (webportal)
  * ALA Spatial Analysis Service (alaspatial)
  * ALA Spatial Layers Service (layers-service)
  * ALA Spatial Actions (actions)

There are a few utilities that are built to be shared between each of the above components. They are:
  * ALA Spatial Layers Store (layers-store)
  * ALA Spatial Utilities (utilities)

The Portal also utilises other ALA web services, such as:
  * BIE
  * Biocache
  * Collectory

The SP uses a standard release of Geoserver to serve environmental, contextual and dynamic analysis output data via WMS protocol. Please refer to Geoserver for more information at http://www.geoserver.com/. A custom, simplified WMS service has been created to serve biodiversity data. This mainly point based occurrence data, with some expert distributions for species served as polygons. Please refer to http://spatial.ala.org.au/ws/ for more information. Example usage of loading biodiversity data is available at http://spatial.ala.org.au/ws/examples/



# Details

### ALA Spatial Portal UI

The Spatial Portal UI is what an end user interacts with to map and analyse biodiversity data along with any environmental, contextual or gazetteer information. It was initially based on IMOS Webportal code, but has been heavily modified to fit the requirements of the Atlas. ZK was chosen as the main framework used to build the UI and help with the browser-server interaction, essentially doing the main UI scaffolding.

With the initial launch, the UI is loaded with information from the Layers Service to provide a list of environmental and contextual layers. Once the user selects a task and provides various inputs, the information is sent to either of the Services (Analysis or Layers) for process. Once completed, the user is able to interact with the output, visualising it as any other layer.

A layer, in terms of the Spatial Portal, is a way to visualise and analyse data. These include Species (point and polygon), Environmental (raster), Contextual (various geometry types) and external WMS layers. Most of the visualisation on the map is done via WMS services using OpenLayers to manage these layers. Only layers that aren't loaded via the WMS protocol are KML and WKT data, which OpenLayers has native support. These are drawn as vectors. A list of layers added to the map are available for more actions on the top left-side bar. Layers can be turned on or off using the checkbox. Some quick options are available next to the layer name for zoom to layer, metadata and delete layer. When a layer is selected, a basic set of options to customise the visual aspect are available in the middle section of the left-side bar. You can control the colour (for contextual layers), opacity and even facet on species and uploaded points data. Another section updated on a layer selection is the Hints area, available at the bottom of the left-side bar. These Hints are dynamic suggestions of various actions and analysis you can perform with the selected layer and any other available layers added to the map.

For detailed information on the UI, various case studies for each of the analysis tools and a general "how to use it", please visit the Getting Started http://www.ala.org.au/spatial-portal-help/getting-started/ page.

For more technical information and setting up the system in your local environment, please visit the  [webportal system documentation](wiki/SystemDocumentationWebportal) page.

### ALA Spatial Analysis Service

The Spatial Analysis Service accepts requests for various analysis and either provides the brains for the analysis or prepares data and forks off the request to external 3rd party packages for analysis and responds to the request with a response. It takes in data from a web request and utilises the layers available on the shared disk to massage the data as necessary and provide it for the analysis tools. It has a queuing mechanism, FIFO, and handles all necessary requests, provides estimates, etc.

Currently, there are two external packages that it forks off the requests too:
  * [MaxEnt](http://www.cs.princeton.edu/~schapire/maxent/)
  * [GDM](http://www.biomaps.net.au/gdm/)

For more technical information and setting up the system in your local environment, please visit the  [alaspatial system documentation](wiki/SystemDocumentationAlaspatial) page.

### ALA Spatial Layers Service

The Layer Services provides access to various features related to environmental and contextual data. More specifically, you can browse all available layers, get metadata, search datasets and even gazetteer services. While these are provided as [HTML](http://spatial.ala.org.au/layers/) output, the main aim is to provide the information via [web services](http://spatial.ala.org.au/ws/).

Another main feature of the Layers Services is the [Tabulation](http://spatial.ala.org.au/ws/tabulations) service. Currently, this is a backend generated dataset for all available contextual data and a more dynamic as per requested tabulation service will be available in the near future.

The Layers Service heavily utilises the Layers-Store library which contains common utilities to be used across various ALA Spatial applications.

For more technical information and setting up the system in your local environment, please visit the  [layers-service system documentation](wiki/SystemDocumentationLayersService) page.

### ALA Spatial Actions

The Actions web application purely provides an overview of how the Spatial Portal has been used. All actions from users are logged within the Actions package. Typically, an action is attached to a user, if they are actively logged into the ALA system. However, in the case of not being logged in, the action is tagged under generic user.

The Actions app provides a dashboard view to the end user as a way to track their movements around the Spatial Portal as well as graphical view of the usage. However, an administrator view of the dashboard displays an enhanced view of all actions done by all the SP users. This should be complimentary to the generic ALA usage dashboard (provided by Google Analytics).

For more technical information and setting up the system in your local environment, please visit the  [actions system documentation](wiki/SystemDocumentationActions) page.

# Cleanup
A cleanup script located at /data/ala/cleanup.sh regularly cleans up files generated by the spatial portal web applications. This script is checked into SVN in the alaspatial project at scripts/cleanup.sh