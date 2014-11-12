**NOTE:** The load process for checklists is essentially a more ad-hoc version of the [expert distributions load process](wiki/ExpertDistributionsLoadProcess). Ensure that you are familiar with that process before reading further.

# Data Format
The data for checklists will typically be received as:

  1. A CSV file, like the CSV file for [expert distributions](wiki/ExpertDistributionsLoadProcess), however it may only be a skeleton. Some manual work may be needed to get it into the exact same format as that described in the description of the expert distributions load process
  1. A shape file may be received for each checklist. Alternatively, a reference to an object from the spatial portal's gazetter search may be provided.

# Load Process
Given the nature of the data received for checklists, this will always be a pretty ad-hoc process

  1. The data from the CSV file can be imported into the distributiondata table using the ExpertDistributionsDataImportTool, however 'c' should be used instead of 'e' for the type field. **TODO** - update and rename this tool to handle checklists too.
  1. If shape files were supplied, they should be loaded into the distributionshapes table using shp2pgsql in a similar way to that used in the [expert distributions load process](wiki/ExpertDistributionsLoadProcess). If references to objects from the gazetter search were provided, locate the appropriate rows in the objects table and copy the the\_geom column values to the distributionshapes table. The names field in the distributionshapes should describe the area covered.
  1. Once the distributionshapes table has been updated, the new rows in the distributiondata table for the checklists need to have their geom\_idx field set. Update this field to reference the appropriate record in the distrubtionshapes table. As there can be a many-to-one mapping from checklist entries in distributiondata to entries in distributionshapes, this process will probably have to be done manually.