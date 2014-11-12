# Fields Table Entires

All layers that are ingested must have at least one associated entry in the "fields" database table. For environmental layers, the fields table entry simply provides additional information about the layer that is used within the spatial portal.

For contextual layers, a fields table entry provides information that is used when objects are created for the contexutal layer. See [Object Creation](wiki/ContextualLoadShape#Objection_creation).

The fields table has the following columns:

  * **name** - Name of the layer. Should match the displayname from the layers table
  * **id** - Id of the layer. Should start with 'cl' for contextual layers and 'el' for environmental layers
  * **desc** - Description of the layer
  * **type** - Type of the layer. In general, this will be 'c' for contextual layers and 'e' for environmental layers. However for contextual layers ingested using the [GridClassBuilder](wiki/ContextualLoadGridClassBuilder) there will be two fields table entries, the first with type 'a' for the classes, and the second with type 'b' for the individual objects
  * **spid** - The id for the layer as used in the layers table
  * **sid** - The the postgis column to use for the id when generating objects. See [Object Creation](wiki/ContextualLoadShape#Object_creation). Only applicable for contextual layers
  * **sname** - The the postgis column to use for the name when generating objects. See [Object Creation](wiki/ContextualLoadShape#Object_creation). Only applicable for contextual layers
  * **sdesc** -The the postgis column to use for the description when generating objects. See [Object Creation](wiki/ContextualLoadShape#Object_creation). Only applicable for contextual layers
  * **indb** - true if the layer/field entry should be included for sampling in the biocache
  * **enabled** - True if the field should be enabled
  * **last\_update** - The date on which the entry was last updated
  * **namesearch** - True if objects generated from this fields table entry should appear in the gazetter name search. Only applicable for contextual layers
  * **defaultlayer** - True if this is default fields entry for the associate layer. For a layer with multiple fields table entries, this value should be true for one entry, and false for all the rest. From an email from Adam Collins 24/1/2012: "defaultlayer was carried over from the old Gazetteer.  Not in use right now, though it should be.  When there are multiple layersdb.fields records for a layersdb.layers.name or layersdb.layers.id used in an /intersect/` * ` service then defaultlayer will indicate which of the layersdb.fields entries to use."
  * **intersect** - True if tabulation should be done for this layer/fields entry. Only applicable for contextual layers.
  * **layerbranch** - Used somewhere in the UI. Should be false for all but one entry in the fields table.
  * **analysis** - True if this layer should have analysis run on it
  * **addtomap** - True if this layer should be available for display in the spatial portal. If false, it will not be visible, but will still be accessible via the web services.

## Editing fields table entries
The tools in the layer-ingestion project will create a single entry in the fields table for a layer by default (or two for a contextual layer ingested using the [GridClassBuilder](wiki/ContextualLoadGridClassBuilder)).

You may wish to edit the generated fields table entries, or create additional ones (see below).

The analysis column value and for contextual layers the intersect column value are what you would typically want to edit.

## Creating fields table entries
When loading a contextual layer, you may want to create additional fields table entries. You may want to do this if you have a dataset that can divided up into several different ways. For example the dataset could be divided up by state, region and subregion, and you want objects available for all of these.

Things to remember when creating additional fields table entries for a contexutal layer:
  * The spid must be the same for all entries
  * The id for the entries should have the same prefix (this is not stictly necessary but good convention). E.g. cl666a, cl666b, cl666c.
  * defaultlayer should be true for only 1 entry.
  * the sid/sname/sdesc combination needs to be different for each entry

After creating additional fields table entries, it is necessary to re-run the ContextualObjectCreator tool. See [Creating a contextual layer from an ESRI shapefile](wiki/ContextualLoadShape).

**NOTE** - It is not possible to create additional fields table entries for layers ingested using the [GridClassBuilder](wiki/ContextualLoadGridClassBuilder).