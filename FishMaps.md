#What functions are required in conjunction with non-point data? The CMAR sharks/rays/fish data presents a useful test case.

# Data

We need to support a full range of spatial data types within the ALA-

  1. Point occurrences.
  1. Grid/raster of species occurrences, species estimates and predictions and environmental data layers.
  1. Polygonal distributions and contextual layers.

These data should be stored in a spatially aware systems and applications and structured to efficiently deliver data suitable for the widest range of applications. The nature of the storage, structuring and indexing depends on Best Current Practice tools and standards and the nature of the applications.

# Basic Functions for Species Data

What basic functions are needed for the search and display species data?

  1. Tabulate or map results of species search, with a differentiation of data types
  1. Filtering by species attributes
  1. Tabulate species within an 'area'

# Interfaces to Spatial Data

There is a need for simple public interfaces to data and services and ones that will efficiently support the research community. These can be delivered by three interfaces-

  * The SP provides a Map tab for displaying all spatial data types. More complex functions are exposed through the Analysis tab. The SP is a one stop shop that needs to integrate, manage and display all the spatial data.

  * Simplified spatial functions associated with specific datasets and applications are also required: Mapping widgets? Such a (re-usable) widget should link to the spatial data using the current tools, but provide a (simplified) map interface that only exposes required functions. This widget should be easily re-skinned and redeployed.

  * Spatial web services should support ALA and external requirements.