# Introduction

A simple example showing how to re-use WMS from the spatial portal. More detailed examples to be done. A demo of this is available [here](http://spatial.ala.org.au/ws/examples/specieswms).

## Details

```
<html xmlns="http://www.w3.org/1999/xhtml">
  <head>
    <link rel="stylesheet" href="http://dev.openlayers.org/releases/OpenLayers-2.10/theme/default/style.css" type="text/css" />
    <script type="text/javascript" src="http://maps.google.com/maps/api/js?sensor=true"></script>
    <script src="http://openlayers.org/dev/OpenLayers.js"></script>
    <script type="text/javascript">

        var options, map, layer, speciesLayer;

        function init(){

            options = {
                    projection: "EPSG:900913",
                    units: "m",
                    maxExtent: new OpenLayers.Bounds( -20037508, -20037508, 20037508, 20037508)
            };

            map = new OpenLayers.Map( 'map' , options,  {
                    numZoomLevels: 12
                });
            layer = new OpenLayers.Layer.Google(
                "Google Physical",
                {type: google.maps.MapTypeId.TERRAIN}
            );
            speciesLayer = new OpenLayers.Layer.WMS(
                    "Australian Magpie",
                    "http://biocache.ala.org.au/ws/webportal/wms/reflect",
                    {   layers: 'ALA:occurrences',
                        srs: 'EPSG:900913',
                        format: 'image/png',
                        cql_filter: "Australian Magpie",
                        transparent: true,
                        env:'color:22a467;name:circle;size:3;opacity:0.8',
                        exceptions:'application-vnd.ogc.se_inimage'
                    }
            );
            map.addLayer(layer);
            map.addLayer(speciesLayer);
            //map.setCenter(new OpenLayers.LonLat(lon, lat), zoom);
            map.addControl( new OpenLayers.Control.LayerSwitcher() );

            var proj = new OpenLayers.Projection("EPSG:4326");
            var point = new OpenLayers.LonLat(133, -28);
            point.transform(proj, map.getProjectionObject());
            map.setCenter(point, 4);
        }
    </script>
  </head>
  <body onload="init()">
    <h1 id="title">Australian Magpie</h1>
    <div id="map" class="smallmap"></div>
  </body>
</html>

```

# WMS Details

## Use of ids

  * Keeps URLs short.
  * User uploaded coordinates get an id.
  * User uploaded LSIDs get an id.
  * LSID or id plus an area gets an id.  Used by the new Restrict to an Area option under Add to Map | Species.
  * LSID or id plus one or more environmental layers with min and max extents gets an id.  Used by environmental envelope (map all occurrences in an environmental envelope) and scatterplot.
  * Missing an interface for; LSID or id plus one of shape layer value or occurrence field value or environmental layer with min and max gets an id.  Will be used by faceting and charting.
  * The WMS request format is compatible with the previously used geoserver + postgres implementation for LSIDs only.
  * CQL param does not change.  It could do with an update since the new parts will not work with geoserver + postgres.

## WMS request parameters

Notes on the request parameters...

```
&CQL_FILTER=speciesconceptid='1308110385743'
```

  * Starts looking at the value from 'id=' (ignores speciesconcept bit).  LSID
or id.
  * It will permit a parameter to map all occurrences in an area, but I will
leave it out as it is not in use.  As required there is a process to
substitute long WKT for an id and use as "LAYER(name,id)".

Ignored variables:
```
 &version=1.1.0
 &request=GetMap
 &styles=
 &format=image/png
 &transparent=true
 &SRS=EPSG%3A900913
 &EXCEPTIONS=application%2Fvnd.ogc.se_inimage
```

Always returns as png and EPSG900913.

Untested with anything other than 256
```
 &WIDTH=256
 &HEIGHT=256
```
Must be EPSG900913 coordinates
```
&BBOX=12523443.0512,-2504688.2032,15028131.5936,0.3392000021413
```

```
&ENV=color%3Acd3844%3Bname%3Acircle%3Bsize%3A3%3Bopacity%3A0.8
```
- 'name' parameter must be "circle", or leave it out.

- color defaults to black.

- opacity defaults to 0.

- For the colouring of facets use 'colormode' instead of 'color'.
Some are disabled for colouring issues, valid for LSID and ids not from user uploaded coordinates.

'colormode' values;
```
Lifeform="10", 
Scientific_name="taxon_name", 
Species="species", 
Genus="genus", 
Family="family",
Order="order", 
Class="class", 
Phylum="phylum", 
Kingdom="kingdom",
Uncertainty="u", 
Data Provider="p", 
Institution = "in", 
Year="y",
Collection="c", 
Basis of Record="b", 
Altitude="a",
Depth="d".
```

- If parameter 'uncertainty' is defined a circle with radius uncertainty is drawn around each point.  Legend is in webportal.

- 'highlight' is a parameter used for highlighting a subset of the LSID/id with a red circle.  Used in scatterplot.

## Layers Layers Layers

All the spatial portal layers are listed here

http://spatial.ala.org.au/geoserver/web/?wicket:bookmarkablePage=:org.geoserver.web.demo.MapPreviewPage