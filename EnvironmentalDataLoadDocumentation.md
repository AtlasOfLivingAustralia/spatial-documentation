# Environmental Data Load (EDL) Documentation

  * a series of "edl" scripts are used to load and manage the environmental data library, these are located in the edl-scripts folder in svn

  * ensure that gdal tools are installed

  * need to modify edlconfig.py (a skeleton is provided - edlconfig-base.py).

```
gdalapps = "/Library/Frameworks/GDAL.framework/Versions/1.7/Programs/"
dataset = "/Users/jac24n/Documents/layer_data/geotiffs/dynamic_land_data_set_diva/Scene01"
geoserver_userpass = "admin:*****"
geoserver_url = "http://spatial-dev.ala.org.au:8082"
t_srs = "epsg:4326"
s_srs = "epsg:4326"
source = "ALA-SPATIAL"
```

  * convert tiff files to bil using **tif2bil.py** (bil is a diva compatible GIS image format, ascii grids have been found to be problematic)

  * import bil files into a diva grid file (either using wine or a vm)
    * **Data->Import to Gridfile->single file**
    * then check BIL
    * this creates a corresponding **grd** and **gri** file, these are used to seed the inter layer dissimilarity matrix, used in maxent and generate the cut point csv

  * **ingest.py** is used to load tiff files up into geoserver

  * check that the geoserver layers are present using web UI

  * register layers with our layers table

  * **index.py** creates the necessary insert statements (tailor these to suit the environment your are targeting)

  * add these to the layers table update scripts in svn

  * a cutpoint csv file is generated from the uploaded grd and gri files (by Adam) - this uses an equal area algorithm

  * each layer must also have an entry in the **units.csv** file - this is used to ensure that the generated styles have the correct units embedded.

  * this is converted into a geoserver style (sld) file using the **style.py** script - this iterates through the CSV file and populates the template.sld template with the correct values

  * the resulting files are stored in the **SLDs** sub folder where they can be modified by hand and placed under version control. Ask Angus if any of these have been modified by hand

  * the **load-styles.py** script uses the REST interface to load up the generated SLD files.