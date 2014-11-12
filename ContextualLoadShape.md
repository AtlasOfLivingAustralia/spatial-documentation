# Creating a contextual layer from an ESRI shapefile (.shp)
The layer-ingestion project contains a script named contextual\_load\_shapefile.sh which will load a layer from raw .shp format. There are a number of variables defined at the top of the script that need to be modified for each new layer that is being loaded. These are:
  * The path to the raw shape file for the layer
  * The layer id (numeric)
  * The short name for the layer
  * The display name of the layer
  * The field id used to generate objects (see below)
  * The field name used to generate objects (see below)
  * The field description used to generate objects (see below)
  * Name search value - true if objects created from the fields table entry for the layer should be searchable by name.
  * Intersect value - true if the layer should be tabulated using the [TabulationGenerator tool](wiki/ContextualLayerLoadProcess#3._Run_TabulationGenerator_java_tool)
```
export SHAPEFILE="/data/ala/data/layers/raw/newlayer/newlayer.shp"

export LAYERID="666"
export LAYER_SHORT_NAME="newlayer"
export LAYER_DISPLAY_NAME="description of newlayer"

export FIELDSSID="sid"
export FIELDSSNAME="sname"
export FIELDSSDESCRIPTION="sdescription"

export NAME_SEARCH="true"
export INTERSECT="false"
```

The contextual\_load\_shapefile.sh script does the following things
  1. uses ogr2ogr (a gdal commandline tool) to reproject the layer to WGS 84
  1. Uses the ContextualFromShapefileDatabaseLoader java tool (in source control in the layer-ingestion project) to create a minimal layers table entry and a fields table entry in the database for the layer. This tool also uses shp2pgsql to convert the shape file to a database table. The name of the database table will be the numeric id for the layer, e.g. "666".
  1. Uses the ContextualObjectCreator java tool (in source control in the layer-ingestion project) to create "object" table entries for the layer in the database. See below for a discussion of object creation. For any layers whose objects are name-searchable (as indicated by the "namesearch" column in the fields table), entries for the object names will be added to the obj\_names table. If a previously loaded layer has an identicial object name, the existing entry in the "obj\_names" table will be used.
  1. Uses the PostgisTableGeoserverLoader java tool to create the layer in geoserver (linked to the database table created from the shapefile using shp2pgsql), and define the style to use for the layer.

### Object creation
Shapefiles loaded for contextual layers will contain one or more polygons. The "objects" database table is used to associate a name, and optionally a description with either:

  * Each individual polygon, or
  * Each _class_ or category of polygons, for example the layer "Geomorphology of the Australian Margin and adjacent seafloor" has a set of polygons that represent reefs, a set that represent basins, etc.

This allows individual polygons or classes of polygons to have their name and description shown in the hover tool in the spatial portal, and allows them to be found via a gazeteer search.

The field id, name and description variables used in the script refer to polygon attributes. Choose the polygon attributes that you want to use for the object ids, names and descriptions respectively. The ContextualObjectCreator tool uses these attributes to generate object table entries. It examines the database table created from the shape file. In this database table, each polygon attribute will be a column in the table. Note that the field id, name and description fields can refer to "derived columns" that are created by the ContextualFromShapefileDatabaseLoader. See [Defining derived columns using JSON](wiki/#Defining_derived_columns_using_JSON) below.

If the shapefile contains a single polygon, or a "derived columns file" is supplied to the ContextualObjectCreator tool, this postgis table will be modified, and the shapefile will be regenerated from the postgis table using pgsql2shp. See [Defining derived columns using JSON](wiki/#Defining_derived_columns_using_JSON) below.

**NOTE:** If the "gid" column in the shape's database table is used in the layer's fields table entry, the shapefile must also be regenerated using pgsql2shp. **THIS IS NOT DONE AUTOMATICALLY**. This is because the gid values are generated and are not stored in the shapefile by default. In this situation, pgsql2shp must be run with the "-r" flag.

### Defining derived columns using JSON

In some cases, the shapefile will not contain the necessary polygon attributes. For example it may simply have a numeric id for each polygon, and the names that match these numeric ids are provided elsewhere e.g. in a text file. In this situation, it is necessary to edit the database table created from the shapefile to add the required information. Using the example if of the numeric ids, it would be necessary to create a new column for the name, and add the name that matches each numeric id. After this has been done, the reprojected shape file on disk must be regenerated from the modified database table. This is done using pgsql2shp:
```
sudo pgsql2shp -f newlayer.shp -h ala-devmaps-db.vm.csiro.au -u postgres -P password layersdb 666
```

This must be done because some of the tools that perform background processing on contextual layers assume that the data in the database and the shapefiles is identical.

In the case of a shapefile that contains a single polygon, the ContextualFromShapefileDatabaseLoader does this process automatically, using the layer name for the object name. No additional action is required. Otherwise, any derived columns must be defined using a JSON file, passed as an optional argument to the ContextualFromShapefileDatabaseLoader. The JSON must take the same format as the following example:

```
[
	{
		"sourceColumn":"dn", 
		"derivedColumn":"class",
		"valueMap": { 
						"1": "Shelf",
						"2": "Slope",
						"3": "Rise",
						"4": "Abyssal plain (includes deep ocean floor)",
						"5": "Bank/shoal",
						"6": "Trench/trough",
						"7": "Deep/hole/valley",
						"8": "Plateau",
						"9": "Saddle",
						"10": "Apron/fan",
						"11": "Escarpment",
						"12": "Basin",
						"13": "Reef",
						"14": "Canyon",
						"15": "Knoll",
						"16": "Ridge",
						"17": "Seamount/guyot",
						"18": "Pinnacle",
						"19": "Sill",
						"20": "Terrace",
						"21": "Sandwave/sandbank",
					}
	}
]
```

The definition for a derived column consists of the following:
  * The name of the source column - the column whose values we want to convert
  * The name of the derived column - the column that is being created
  * The mapping of values from the source to the derived column.

The JSON file can contain multiple derived column defintions.

See the layer-ingestion project script contextual\_load\_gridded\_polygonize.sh to see an example of a JSON file being passed to the ContextualFromShapefileDatabaseLoader tool.

**NOTE:** Where appropriate, the values of the "sid" and "sname" should match the names of generated derived columns. In the example above, both "sid" and "sname" should be "class".

### Defining an sld
By default, contextual layers do not have a legend created for them. In some cases you may want to define one, for example where the object creation (see above) created objects for _classes_ rather than for individual polygons.

An sld file must be created, and passed to the PostgisTableGeoserverLoader as an optional argument.

Here is an extract of the sld file for the layer "Geomorphology of the Australian Margin and adjacent seafloor":

```
<?xml version="1.0" encoding="UTF-8"?>
<sld:StyledLayerDescriptor xmlns="http://www.opengis.net/sld" xmlns:sld="http://www.opengis.net/sld" xmlns:ogc="http://www.opengis.net/ogc" xmlns:gml="http://www.opengis.net/gml" version="1.0.0">
  <sld:NamedLayer>
    <sld:Name>raster</sld:Name>
    <sld:UserStyle>
      <sld:Name>raster</sld:Name>
      <sld:Title>Class attribute based style</sld:Title>
      <sld:Abstract>Class attributes based style</sld:Abstract>
      <sld:FeatureTypeStyle>
        <sld:Name>name</sld:Name>
        <sld:Rule>
          <sld:Name>Shelf</sld:Name>
          <sld:Title>Shelf</sld:Title>
          <ogc:Filter>
            <ogc:PropertyIsEqualTo>
              <ogc:PropertyName>class</ogc:PropertyName>
              <ogc:Literal>Shelf</ogc:Literal>
            </ogc:PropertyIsEqualTo>
          </ogc:Filter>
          <sld:PolygonSymbolizer>
            <sld:Fill>
              <sld:CssParameter name="fill">#00657E</sld:CssParameter>
            </sld:Fill>
          </sld:PolygonSymbolizer>
        </sld:Rule>
        <sld:Rule>
          <sld:Name>Slope</sld:Name>
          <sld:Title>Slope</sld:Title>
          <ogc:Filter>
            <ogc:PropertyIsEqualTo>
              <ogc:PropertyName>class</ogc:PropertyName>
              <ogc:Literal>Slope</ogc:Literal>
            </ogc:PropertyIsEqualTo>
          </ogc:Filter>
          <sld:PolygonSymbolizer>
            <sld:Fill>
              <sld:CssParameter name="fill">#3E8FAC</sld:CssParameter>
            </sld:Fill>
          </sld:PolygonSymbolizer>
        </sld:Rule>
        <sld:Rule>
          <sld:Name>Rise</sld:Name>
          <sld:Title>Rise</sld:Title>
          <ogc:Filter>
            <ogc:PropertyIsEqualTo>
              <ogc:PropertyName>class</ogc:PropertyName>
              <ogc:Literal>Rise</ogc:Literal>
            </ogc:PropertyIsEqualTo>
          </ogc:Filter>
          <sld:PolygonSymbolizer>
            <sld:Fill>
              <sld:CssParameter name="fill">#7FB3C9</sld:CssParameter>
            </sld:Fill>
          </sld:PolygonSymbolizer>
        </sld:Rule>
      </sld:FeatureTypeStyle>
    </sld:UserStyle>
  </sld:NamedLayer>
</sld:StyledLayerDescriptor>
```

The lines ` <ogc:PropertyName>class</ogc:PropertyName> ` refer to the column from the postgis table generated from the shapefile that is used the define the legend. The lines like ` <ogc:Literal>Rise</ogc:Literal> ` refer to specific values within that column. For more information, refer to the [Geoserver SLD Cookbook](http://docs.geoserver.org/latest/en/user/styling/sld-cookbook/index.html).

See the layer-ingestion project script contextual\_load\_gridded\_polygonize.sh to see an example of an sld file being passed to the PostgisTableGeoserverLoader tool.
