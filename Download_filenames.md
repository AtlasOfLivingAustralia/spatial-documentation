#Some ideas on standardizing filenames on downloads from the Spatial Portal

# Introduction

Currently, we have a mix of filenames associated with downloads from the Spatial Portal. Ideally, we would like a consistent naming strategy not only in the SP, but across the ALA.

# Details

Current downloads
  1. Species list: "Species\_list\_20110707\_1310018189872.CSV"
  1. Species sample:
  1. Scatterplot data+image:
  1. Classify: "My\_classification\_1.zip"
  1. Predict: "My\_prediction\_1.zip"
  1. Map

Suggestions
  1. Rename-able files are fine for now with default as above
  1. Filenames from black-box packages such as MaxEnt and likely GDM to use the standard export filenames
  1. Additional filenames to use the STEM of the tool/process. For example, "Classification_" and "Prediction_"
  1. When adding date\_time, stick with "yearmonthday\_hourminute" format
  1. Use underscore rather than spaces
  1. All zip files must have a readme.txt file that lists the filenames, a tab and then the description of the file content
  1. Prediction (and GDM) should include the citation file as would be generated if downloading the same species occurrences