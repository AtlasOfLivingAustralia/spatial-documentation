#Map printing.

# Introduction

We need a design for handling the printing of maps from the spatial portal. What libraries exist? How useful are they? What are the basic requirements?

# Details

  * Requirement for a Print Preview of the map window
  * Need to include legend of displayed layer but the portal can display multiple layers concurrently so how do we handle legends? Do we assume any layers selected in the Layer Selector will be displayed as is with legend. We could have big legends - and these must eat map real estate
  * Need north arrow
  * Need scale (optional?)
  * Need the ability of adding a latitude-longitude grid
  * Need the ability of adding a title