# Layer metadata entry

The tools provided in the [layer-ingestion](wiki/LayerIngestionProcess#Layer_ingestion_tools) project generate skeleton metadata records for newly loaded layers. These records need to be fleshed out.
This can be done using the layer table edit tool: http://spatial-dev.ala.org.au/ws/admin/layers.

Check all existing values. The following values will need to be added:

  * Source - organisation from which the data was sourced
  * Notes - notes on the layer
  * metadata path - a link to the official metadata record for the spatial data. May be an external URL, or may be a reference to a record in the local Geonetwork instance. See [Geonetwork and ANZMET Lite](wiki/GeonetworkAndAnzmet). Use paths relative to _COMMON\_GEONETWORK\_URL_.
  * Classification 1 and Classification 2 text (check for similar layers using http://spatial-dev.ala.org.au/layers)
  * mddatest - Date on which the metadata entry for the layer was created
  * citation\_date - date on which the source organisation created/last updated the dataset
  * resparty\_role - function performed by the party responsible for the data. Will be one of:
    * resourceProvider - party that supplies the resource
    * custodian - party that accepts accountability and responsibility for the data and ensures appropriate care and maintenance of the resource
    * owner - party that owns the resource
    * user - party who uses the resource
    * distributor - party who distributes the resource
    * originator - party who created the resource
    * pointOfContact - party who can be contacted for acquiring knowledge about or acquisition of the resource
    * principalInvestigator - key party responsible for gathering information and conducting research
    * processor - party who has processed the data in a manner such that the resource has been modified


  * licence level/licence link/licence notes - licencing information for the layer. Value for licence level will be one of:
    * 0: no agreement;
    * 1: permission required for any derived works;
    * 2: derived works allowed under conditions specified by licensor;
    * 3: cc or equivalent licence

  * Domain (terrestrial, marine or both - separated by a comma)
  * Keywords as used to aid searching/auto-complete

Note that the update of the information from the layers table may take up to 1 hour to become visible in the spatial portal as an automatic process runs regularly to check for updates.