External config can be specified with a simple properties file at ` /data/regions/config/regions-config.properties `. The following configuration values can be overridden:
| **key** | **default** | **use**|
|:--------|:------------|:-------|
|bie.baseURL|http://bie.ala.org.au|the base url for the bie app|
|biocache.baseURL|http://biocache.ala.org.au|the base url for the biocache app|
|spatial.layers.service.url|spatial.ala.org.au/ws|the base url for the spatial services|
|actions.baseURL|http://spatial.ala.org.au/actions|the base url for the common ui components|
|ala.baseURL|http://www.ala.org.au|the base url for the main Atlas webapp|
|headerAndFooter.baseURL|http://www2.ala.org.au/commonui|the base url for the common ui components|

Be careful of the trailing slashes in urls. These are not consistent. Always replace like with like.

An example config file is:
```
biocache.baseURL=http://biocache.ala.org.au
```