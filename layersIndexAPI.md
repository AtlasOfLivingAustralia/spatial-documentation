# layers-index API Documentation

layers-index is a new spatial services suite that will replace many of the existing gazetteer services.

(TODO: Add table of contents.)

## Layer Services

Layer services are used to retrieve information about spatial layers. It can be called to return only environmental (grids), or vector (contextual) layers.

### List all layers

**/layers**

```
[{"id":"761","name":"trngm","description":"Mean annual diurnal temperature range (Degrees C)","type":"Environmental","source":"CSIRO Ecosystem Sciences","path":"\/mnt\/transfer\/williams\/arc\/trngm.tif","extents":"null","minlatitude":"-43.80000","minlongitude":"112.90000","maxlatitude":"-9.00000","maxlongitude":"153.64000","notes":"null","enabled":"t","displayname":"Temperature - mean annual diurnal range","displaypath":"http:\/\/spatial.ala.org.au\/geoserver\/gwc\/service\/wms?service=WMS&version=1.1.0&request=GetMap&layers=ALA:trngm&format=image\/png&styles=","scale":"null","environmentalvaluemin":"0","environmentalvaluemax":"16.838","environmentalvalueunits":"degrees C","lookuptablepath":"","metadatapath":"null","classification1":"Climate","classification2":"Temperature","uid":"761","mddatest":"2010-07","citation_date":"null","datalang":"eng","mdhrlv":"null","respparty_role":"null","licence_level":"1","licence_link":"null","licence_notes":"Permission required to re-distribute derivative works. Please contact Dr. Kristen Williams - kristen.williams@csiro.au","source_link":"http:\/\/www.csiro.au\/org\/ces.html","path_orig":"trngm","path_1km":"null","path_250m":"null","pid":"null"},...]
```

### List a single layer

**/layer/{id}**

Returns a single layer provided a layer id

## Fields Services

Fields services are used to retrieve information about gazetteer fields. Fields are used to determine what layer attributes are returned in search and intersect operations. A layer can have one or more associated field records (representing different views of the data - including shape unions based on a field attribute).

### List all fields

The url to invoke this service has the form:

**/fields**

Example:

**/fields**

Returns:

```
[{"name":"Vegetation types - native","id":"cl617","desc":"Vegetation types - native","type":"c","spid":"617","sid":"gid","sname":"class","sdesc":"null","indb":"t","enabled":"t","last_update":"null","nameSearch":"f","defaultLayer":"f"},{"name":"NPP Mean","id":"el676","desc":"NPP Mean","type":"e","spid":"676","sid":"null","sname":"null","sdesc":"null","indb":"t","enabled":"t","last_update":"null","nameSearch":"f","defaultLayer":"f"},{"name":"Hunter Areas Of Interest","id":"cl907","desc":"Hunter Areas Of Interest","type":"c","spid":"907","sid":"elem_text","sname":"elem_text","sdesc":"null","indb":"t","enabled":"t","last_update":"null","nameSearch":"t","defaultLayer":"t"},{"name":"Collaborative Australian Protected Areas Database","id":"cl1896","desc":"Collaborative Australian Protected Areas Database","type":"c","spid":"896","sid":"iucn","sname":"icun","sdesc":"null","indb":"t","enabled":"t","last_update":"null","nameSearch":"f","defaultLayer":"t"},...]
```

### Get all DB fields

Only inDb=true fields from /indexed/fields - this will only return fields that will be indexed within the biocache.

The url to invoke this service has the form:

**/fieldsdb**

For Example:

**/fieldsdb**

Returns:

```
[{"name":"Vegetation types - native","id":"cl617","desc":"Vegetation types - native","type":"c","spid":"617","sid":"gid","sname":"class","sdesc":"null","indb":"t","enabled":"t","last_update":"null","nameSearch":"f","defaultLayer":"f"},{"name":"NPP Mean","id":"el676","desc":"NPP Mean","type":"e","spid":"676","sid":"null","sname":"null","sdesc":"null","indb":"t","enabled":"t","last_update":"null","nameSearch":"f","defaultLayer":"f"},{"name":"Hunter Areas Of Interest","id":"cl907","desc":"Hunter Areas Of Interest","type":"c","spid":"907","sid":"elem_text","sname":"elem_text","sdesc":"null","indb":"t","enabled":"t","last_update":"null","nameSearch":"t","defaultLayer":"t"},{"name":"Collaborative Australian Protected Areas Database","id":"cl1896","desc":"Collaborative Australian Protected Areas Database","type":"c","spid":"896","sid":"iucn","sname":"icun","sdesc":"null","indb":"t","enabled":"t","last_update":"null","nameSearch":"f","defaultLayer":"t"},{"name":"K2C Management Regions","id":"cl908","desc":"K2C Management Regions","type":"c","spid":"908","sid":"name","sname":"name","sdesc":"null","indb":"t","enabled":"t","last_update":"null","nameSearch":"t","defaultLayer":"t"},...]
```

Return Data Dictionary:

**_TODO_**

### Get single field

Provided a field id, this service returns all objects for the field.

The url to invoke this service has the form:

**/field/{fid}**

For Example:

**/field/cl22**

Returns:

```
{"objects":[{"description":"Australian Capital Territory, Territory","fid":"cl22","fieldname":"States","id":"Australian Capital Territory","name":"Australian Capital Territory","pid":"3742602"}, ...
```

Return Data Dictionary:

See above.

## Objects Services

### Get single object details

**/object/{oid}**

Example:

**/object/3742602**

Returns:

```
{"description":"Australian Capital Territory, Territory","fid":"cl22","fieldname":"States","id":"Australian Capital Territory","name":"Australian Capital Territory","pid":"3742602"}
```

## Shapes Services

### Get object as GeoJSON

**/shape/geojson/{oid}**

Example:

**/shape/geojson/3742602**

Returns:
```
{"type":"MultiPolygon","coordinates":[[[[149.126878000000147,-35.128149999999948],[149.127542000000062,-35.129535999999973],[149.127564000000007,-35.129581999999971],[149.128356999999937,-35.129230999999947],[149.130492000000004,-35.130618999999967],[149.132384000000116,-35.130159999999989],[149.135621000000015,-35.129371999999989],[149.137577999999962,-35.13000599999998],[149.137618999999972,-35.130018999999891],[149.137436999999977,-35.131007999999895]...
```

### Get object as WKT

**/shape/wkt/{oid}**

Example:

**/shape/geojson/3742602**

Returns:
```
MULTIPOLYGON(((149.126878 -35.1281499999999,149.127542 -35.129536,149.127564 -35.129582,149.128357 -35.1292309999999,149.130492 -35.130619,149.132384 -35.13016,149.135621 -35.129372,149.137578 -35.130006,149.137619 -35.1300189999999,149.137437 -35.1310079999999...
```

### Get object as KML

**/shape/kml/{oid}**

Example:

**/shape/geojson/3742602**

Returns:
```
<MultiGeometry><Polygon><outerBoundaryIs><LinearRing><coordinates>149.126878000000147,-35.128149999999948 149.127542000000062,-35.129535999999973 149.127564000000007 ...
```

### Get object as Shapefile **_(TODO)_**

**/shape/shapefile/{uuid}**

## Uuid Service **_TODO_**

**/info/uuid**

shape file; layer table record, shape stats (e.g. extents, #shapes, total area)

grid file; layer table record, header info (e.g. extents, resolution, total area)

object; uuid, name, description, layer uuid, field id, shape stats (e.g. extents, area)

## Intersect Service

**/intersect/{uuid1},{uuid2}**  **_TODO_**

- uuids are shape layer or shape object

**/intersect/{ids}/{lat}/{lng}**

- intersects a single point and one or more fields.

**/intersect/batch?type="uuid" or "value"&fields=[id, ...]&points=[{lat, lng} , ... ](wiki/)&absences=true**  **_TODO_**

- (default) type="value" return as x,y,value

- type="uuid" return as x,y,object uuid

- absences=false (default) or true to return unsuccessful intersections as x,y

## Distribution Service **_TODO_**

Presence/absence distribution intersection

**/intersect/distributions/?uuid=object uuid**

**/intersect/distributions/?wkt=wkt**

## Search Service

**/search?q="search string"&types="grid,shape,object"**

- types is optional to restrict to a subset of layers

- grid to include grids in the layer table

- shape to include shapes in the layer table

- object to include objects in indexed fields

# API Calls
This API Reference is for direct calls to the java API. Some services are restricted to this method.

## Layers
` Layer[] getLayers() `

` Layer getLayer(String pid) `

## Fields
` Field[] getFields() `

` Field[] getFieldsDb() `

` Field[] getFieldsForLayer(String lid) `
pid might want to be interchangeable with layer.name and layer.id during the transition.

` Field getField(String fid) `
Noting that 'fid' is a user entered value with a prefix 'el' or 'cl' where 'el' is for numeric values after an intersection and 'cl' is for string values after an intersection. (Environmental or Contextual Layer)

## Objects
` Object[] getObjects(String fid) `

` Object getObject(String oid) `

` String getShapeGeoJson(String oid) `

` String getShapeKml(String oid) `

` String getShapeWkt(String oid) `

` byte[] getShapeFile(String oid) `

## Shape Registration
` String registerShape(String name, String wkt) `
Not sure what I put in the 'uploads' table.  It should have minimal additional fields.  It may require a 'metadata\_url' or equivelant.

## Intersection
` String[] intersect(ArrayList<String> fids,double latitude, double longitude) `
Intersection of a single point should be for storage (display) instead of analysis.  Therefore output can be String.

` Map[] intersect(ArrayList<String> fids,double latitude, double longitude) `
This is closer to what the WS does in the returned json.

` ArrayList<String []> intersect(ArrayList<String> fids, double[][] points) `
Useful for biocache and webportal user coordinates.   double [wiki/](wiki/)[wiki/](wiki/) should be double [wiki/](wiki/), but because the existing Grid and SimpleShapeFile classes are already [wiki/](wiki/)[wiki/](wiki/) that's how I have written it.

## Distribution
` boolean intersectDistribution(String oid, String did) `

` String[] intersectDistributionWkt(String wkt) `

## Search
` SearchResult [] search(String term, boolean searchObjects, boolean searchGrids, boolean searchShapes) `

# Tables Required

## Layers

uuid, string, layer UUID

uuid\_time, date/time, when uuid was created

keywords, string, searchable keywords for layers

- carry over existing layer fields

- fields in issue 410 (on Google Code)

## Fields

name, string, nice name for this field

id, string, datatype character + short number that makes this field unique

desc, string, description for this field

s\_uuid, string, source layer UUID, shape or environmental layer

type, byte, text = 0 or number = 1, depending on the data source, categorical or continuous

s\_id, string, name of the source field in text/contextual/shape table that makes objects in this field unique

s\_value, string, name of the source field in text/contextual/shape table for the value

s\_desc, string, name of the source field in text/contextual/shape table for the description

keywords, string, keywords for searching

indb, boolean, true when this field is to be included against all occurrences

enabled, boolean, true when this field is in use
last\_update, date/time of last field change

- All text fields are searchable by s\_value.

- inDb=true for fields required to be attached to the occurrence record by intersection.  This would include grid files and shape fields, excluding point objects.  It makes the fields available in filtering queries.

## Objects

id, string, value that makes this object unique in s\_uuid

name, string, nice name for this field built from s\_value

desc, string, description for this field built from field s\_desc

uuid, string, source layer UUID, shape or environmental layer

uuid\_time, date/time, when uuid was created

s\_id, string, indexed field id

s\_uuid, string, source layer uuid
the\_geom

## Uploaded

uuid, string, layer UUID

name, string, nice name

description, string, description of the uploaded shape
the\_geom

## (Optional, collates info from other tables and some calculation results) Stats

s\_uuid, string, layer or object that these stats belong to

extent, string, layer or object extents

area, double, layer or object area

type, byte, shape file = 0 grid file = 1 object = 2

other1, string, field for misc info, e.g. grid resolution, number of shapes

## Distributions

uuid, string, distribution UUID

min\_depth, double, m

max\_depth, double, m

path, string, path to the grid file

+other existing fields in distributions file.

- An occurrence will exist in the single index and recorded with this UUID.  Process for queries is: distribution query to single index, single index to THIS service for intersecting distribution UUIDs, single index to return occurrences with these distribution UUIDs.

# Adding a new Layer

**Step 1:** Create an entry in the layers table for the new layer

**Step 2:** Import the shape file into a table with the spid value (using shp2pgsql)

**Step 3:** Insert records into the objects table.  Substitute fields table values for the <…>’s.

```
INSERT INTO objects (pid, id, name, "desc", fid, the_geom)  SELECT nextval('objects_id_seq'::regclass), <sid>, MAX(<name>), MAX(<description>), '<sfid>',ST_UNION(the_geom)  FROM "<spid>" GROUP BY <sid>;
```

**Step 4:** Populate the obj\_names table (this table makes use of obj\_names database function for searching in the ‘name’ column).

```
INSERT INTO obj_names (name) SELECT lower(objects.name) FROM objects LEFT OUTER JOIN obj_names ON lower(objects.name)=obj_names.name WHERE obj_names.name IS NULL GROUP BY lower(objects.name);
```

**Step 5:** Update the objects table to reference obj\_names.

```
UPDATE objects SET name_id=obj_names.id FROM obj_names WHERE name_id IS NULL AND lower(objects.name)=obj_names.name;
```

**Step 6:** Update the srid to EPSG:4326 (WGS84).

```
UPDATE objects SET the_geom = ST_SETSRID(the_geom,4326) WHERE ST_SRID(the_geom) = -1;
```

```
UPDATE objects SET the_geom= ST_TRANSFORM(the_geom,4326) WHERE ST_SRID(the_geom) <> 4326;
```