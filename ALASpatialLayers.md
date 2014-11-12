**NOTE: Due to licensing issues, we currently only support WMS externally (to the ALA), not WFS or WCS.** We will support WFS and WCS when layers in the library move to a Creative Commons license.

# WMS Services

http://spatial.ala.org.au/geoserver - our primary geoserver instance

http://spatial.ala.org.au/geoserver/ows?service=wms&version=1.3.0&request=GetCapabilities - WMS GetCapabilities request

http://spatial.ala.org.au/geonetwork - our master geonetwork instance hosting layer metadata records and biological collections-level metadata not able to be hosted by the data provider. At some point, we may incorporate (link/download/sync) externally hosted metadata to enable GeoNetwork web services.

http://spatial.ala.org.au/layers - listing of all available ALA spatial layers (including metadata subset and links to external metadata records)

http://spatial.ala.org.au/layers.csv - csv representation of ALA layers

http://spatial.ala.org.au/layers.json - json representation of ALA layers

http://spatial.ala.org.au/layers.xml - xml representation of ALA layers

http://spatial.ala.org.au/layers/?q=cars  - example search

# WMS Clients

You can use a WMS client such as uDig or OpenLayers to connect and render layers from geoserver.

# Species Data
Species data does not correlate directly with layers stored in geoserver, instead the WMS layers are generated dynamically from a spatial index. Example usage and documentation is available at [SimpleWMSExample](wiki/SimpleWMSExample).

# Analysis Layers
Note that layers starting with **species or _aloc_** are layers generated as part of user created prediction and classification.