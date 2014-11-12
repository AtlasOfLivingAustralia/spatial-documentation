# Introduction

This is intended to document the linkages between Spatial Portal and BIE/BioCache.

# Linking - BIE/BioCache -> Spatial Portal

There is a requirement to provide a link into the spatial portal to view the occurrence data associated with a particular taxon or other entity.

  * Subspecies/Species/Genus/Family pages - include occurrence records for the viewed taxon

> |  http://spatial.ala.org.au/webportal?species_lsid=urn:lsid:biodiversity.org.au:apni.taxon:29586 |
|:------------------------------------------------------------------------------------------------|

  * Collection pages  - include occurrence records for the viewed collection

> | http://spatial.ala.org.au/webportal?institution_code=WAV&collection_code=MAM |
|:-----------------------------------------------------------------------------|
> | http://spatial.ala.org.au/webportal?collectionUid=co12                       |

  * Institution pages  - include occurrence records for the viewed institution

> | http://spatial.ala.org.au/webportal?institution_=WAV |
|:-----------------------------------------------------|
> | http://spatial.ala.org.au/webportal?institutionUid=in123 |

  * Data Provider pages  - include occurrence records for the viewed data provider

> |  http://spatial.ala.org.au/webportal?dataProviderUid=dp123 |
|:-----------------------------------------------------------|

  * Dataset pages - include occurrence records for the viewed dataset

> |  http://spatial.ala.org.au/webportal?dataResourceUid=dr123 |
|:-----------------------------------------------------------|


# Linking - Spatial Portal ->  BIE/BioCache

The occurrence export comes with IDs for each occurrence record. These IDs can be used to deeplink to the full detailed HTML page for this occurrence.
> |   http://biocache.ala.org.au/occurrences/occurrence-id |
|:-------------------------------------------------------|
> |  e.g. http://biocache.ala.org.au/occurrences/34598513  |

# WMS

There is a requirement to provide a WMS service to allow the inclusion of a map on the pages with the BIE/BioCache.
The intention is that these maps are simple, non-pannable and give a overview of the distribution of records for the entity. Below the map (which could be a simple PNG), there will be a link to view the data in more detail within the spatial portal. For performance reasons it will not be practical to display >10000 points on a single map. It is therefore suggested we adopt an approach of display the density of records once a sensible threshold is reach.

  * Subspecies/Species/Genus/Family pages - include occurrence records for the viewed taxon

> |  http://spatial.ala.org.au/wms?family_lsid=urn:lsid:biodiversity.org.au:apni.taxon:397778 |
|:------------------------------------------------------------------------------------------|
> |  http://spatial.ala.org.au/wms?genus_lsid=urn:lsid:biodiversity.org.au:apni.taxon:301430  |
> |  http://spatial.ala.org.au/wms?species_lsid=urn:lsid:biodiversity.org.au:apni.taxon:295863 |
> |  http://spatial.ala.org.au/wms?subspecies_lsid=urn:lsid:biodiversity.org.au:apni.taxon:301430 |

  * Collection pages  - include occurrence records for the viewed collection

> |  http://spatial.ala.org.au/wms?collectionUid=co12 |
|:--------------------------------------------------|

  * Institution pages  - include occurrence records for the viewed institution

> | http://spatial.ala.org.au/wms?institutionUid=in123 |
|:---------------------------------------------------|

  * Data Provider pages  - include occurrence records for the viewed data provider

> |  http://spatial.ala.org.au/wms?dataProviderUid=dp123 |
|:-----------------------------------------------------|

  * Dataset pages - include occurrence records for the viewed dataset

> |  http://spatial.ala.org.au/wms?dataResourceUid=dp123 |
|:-----------------------------------------------------|

This can be done using indexes generated on top of the export file.  These indexes are already in place for the family, genus, species.


# Other maps within BIE/BioCache

The BIE/BioCache, Citizen science and Collectory contains some smaller maps for specific bits of functionality.
For these maps we need to specify a few things so that these maps can maintain the same look and feel as the mapping within the spatial portal, This includes:

  * A default base layer to use
  * Common styling of mapping widgets (same styling for navigation controls where navigation is required)
