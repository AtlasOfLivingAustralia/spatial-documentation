# What are the different types of species data that we will have to handle at some point?

# Introduction

From the perspective of the Spatial Portal, as of Janyary 14, 2011, there is one data type: occurrence records that have a given latitude and longitute point. This will change with the addition of fish maps from CSIRO CMAR. What structures are anticipated and how to handle them?

# Details

The following is a list of the current and anticipated species data structures:
  * Occurrence data: points
  * Maps showing extent of species distributions
    * Expert opinion (manual): grids or polygons of presence (1) of a species. This may evolve into the next category
    * Output distrbution models from MaxEnt or other modelling techniques: grid of species probabilities 0-1 where "1" implies certain presence.
  * Species check lists: each list is related to a poygon or polygon set. Checklists occur for NRM regions, reserves and parks, islands and could equally be linked to systematic survey sites. All species in a list resolve spatially to the same area. Recent conversations with Charlie Veron suggests that this form will best capture the majority of his knowledge of the distribition of corals (internationally) - in this case 141 ecoregions.
  * Systematic survey data. This type of data may include all of the above, but would be expected to resolove spatially to either point occurrence or  Checklist form. Each survey set must correspond to a single survey method applied to a specific area (single polygon in most cases?) within a limited timeframe. The survey data (TERN-based) will be accompanied by a suite of variables (e.g., environmental data, survey method, date/time, images, video, survey staff etc.

# Implications

  1. A species search may identify one or more different forms, all of which could be displayed as separate Active Layers.
  1. Do we need to identify different forms in the search? Probably not if all forms available are mapped and the user has a choice of deselection or deletion.
  1. None of the forms conforms to a gazetteer entry (unique name and location).

# Associated Data

An email from Piers about field capture of some TERN data raises the question about what should be 'stored' in the ALA vs. linked to by the ALA. In the example, associated data in addition to species includes

  1. Soil data including bulk density
  1. Substrate type
  1. Vegetation cover
  1. Vegetation class
  1. Vegetation height
  1. Leaf area index

On this topic (species types), my feeling is that this may correspond to a species list for a designated area (defined by GPS coordinates) with links to associated data for the area (stored by TERN)?