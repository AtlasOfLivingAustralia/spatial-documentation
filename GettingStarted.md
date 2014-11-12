# Quick Guide
  1. install base software
  1. download sources
  1. configure apps
  1. run apps in development
  1. build war files

# Software versions
  * Java 6 or higher
  * Geoserver 2.1.3 or higher

# Details
## Install base software
  * Install Java 6 or higher
  * Install Tomcat 6 or higher
  * Install Geoserver 2.1.3 or higher
  * Install Postgresql 8.4 or higher with Postgis 1.4 or higher.

## Download sources
The project sources are available at http://code.google.com/p/alageospatialportal/source/checkout. They are managed by Subversion source control. You can use a Subversion client to create a working copy of the code or simply download the sources.

The project files for the Netbeans IDE are included. If using a different IDE you will need to create your own project.

## Configure the apps
A set of generic options available across all the apps that might be of interest.
  * Spatial Portal UI (webportal) configuration path: Add the
```
-DWEBPORTAL_CONFIG_DIR=/etc/ala/webportal_config/dev/ 
```
to your Java options

  * Specify a set of memory for the applications to run. A recommended amount would be about 10GB

  * Authentication details for the ALA Spatial Portal is available at http://code.google.com/p/ala-bie/wiki/Authentication Currently Authentication is mainly required by the actions module and the webportal module utilises the web version of the Authentication service.

  * To manually install any required external jar files not available via maven, please follow these [instructions](http://maven.apache.org/guides/mini/guide-3rd-party-jars-local.html)

  * Install Geoserver and the REST module based on your environment. Please provide the username, password and base REST url for layers-service and alaspatial modules. The webportal module will require the base Geoserver WMS url. Some [geoserver configuration](wiki/SystemDocumentationGeoserver) is also required.

  * Setup the [local directories](wiki/SystemDocumentationLocalFiles).  For testing you can unpack [ala.tar.gz](http://alageospatialportal.googlecode.com/svn/trunk/database/ala.tar.gz) into /data/.

  * Setup a postgis database [layersdb](wiki/SystemDocumentationLayersDB).  A little test data can be loaded with [testdata.sql.gz](http://alageospatialportal.googlecode.com/svn/trunk/database/testdata.sql.gz).

### Spatial Portal

Check out the module [webportal](http://code.google.com/p/alageospatialportal/source/browse/#svn%2Ftrunk%2Fwebportal) from the trunk. The project also needs some extra jar files that may not be available as part of the Maven repository. These are available in the [requiredlibs](http://code.google.com/p/alageospatialportal/source/browse/#svn%2Fdeprecated%2Frequiredlibs) of the depracated branch and [zk-rangeslider](http://code.google.com/p/alageospatialportal/source/browse/#svn%2Ftrunk%2Fzk-rangeslider) off the trunk.
The important configuration is mostly in [webportal\_config.xml](http://code.google.com/p/alageospatialportal/source/browse/trunk/webportal_config/dev/webportal_config.xml). This can be placed anywhere on your system as the path to this should be passed onto your Tomcat instance via the -DWEBPORTAL\_CONFIG\_DIR option as mentioned in the previous section. The configuration you are likely to want to change includes:
  * "tns:proxyAllowedHosts": a list of allowed external domains that you may want to download some data from
  * Printing path to point to your local domain/hosts ( this is where files are temporarily created when a user choses to Export the map. Typically, http://spatial.ala.org.au/print with the print folder residing in the webportal webapp. E.g:

```

<CATALINA_HOME>/webapps/webportal/print/

```

  * Geoserver/BIE/Biocache/Layers if you have your own setup
  * Various layer options under "tns:supplementary" node

You can leave the above as default if you want to hit the ALA system for information and just install the webportal on your system. The authentication for the Spatial Portal UI looks for a specific cookie as installed by the [ALA Authentication system](https://auth.ala.org.au/).

The [webportal\_config](http://code.google.com/p/alageospatialportal/source/browse/trunk/webportal_config) app in the trunk is a great place to start the configuration settings.

### Layers store
Check out the module [layers-store](http://code.google.com/p/alageospatialportal/source/browse/#svn%2Ftrunk%2Flayers-store) from the trunk.
The important configuration is mostly in [layer.properties](http://code.google.com/p/alageospatialportal/source/browse/trunk/layers-store/src/main/resources/layer.properties) in the resources directory. The configuration you are likely to want to change includes:
  * all the directory paths to fit your environment
  * grid layer resolution based on your data

Another important configuration file that you will most likely edit is the [data-config.xml](http://code.google.com/p/alageospatialportal/source/browse/trunk/layers-store/src/main/resources/spring/data-config.xml) available in the resources/spring directory. This is a Spring configuration file that has details of the database connection.

You will need to reuse both of your updated layer.properties and data-config.xml files in Layers service and Spatial Analysis.

### Layers service
Check out the module [layers-service](http://code.google.com/p/alageospatialportal/source/browse/#svn%2Ftrunk%2Flayers-service) from the trunk.
The important configuration is mostly in [data\_config.xml](http://code.google.com/p/alageospatialportal/source/browse/trunk/layers-service/src/main/webapp/WEB-INF/spring/data-config.xml) in the spring directory, also [user.properties](http://alageospatialportal.googlecode.com/svn/trunk/layers-service/src/main/resources/user.properties). Reuse layer.properties and data-config.xml from Layers store.  This is a Spring configuration file that has details of the database connection.

### Spatial Analysis
Check out the module [alaspatial](http://code.google.com/p/alageospatialportal/source/browse/#svn%2Ftrunk%2Falaspatial) from the trunk.
The important configuration is mostly in [alaspatial.properties](http://code.google.com/p/alageospatialportal/source/browse/trunk/alaspatial/src/main/resources/alaspatial.properties) in the resources directory. The configuration you are likely to want to change includes:
  * working and output directories along with the urls that reflect these as necessary
  * layers resolution based on your data and the path
  * ALOC (Classification), Maxent (Prediction), GDM, GDAL paths
  * Geoserver details

The Analysis services utilises the Layers-Store library. To override the configuration for this library in your own system, you may modify the layer.properties file as you see fit. This file is also available in the resources directory. See the section on ALA Spatial Layers Store for more information about this configuration.

Another important configuration file that you will most likely edit is the [data-config.xml](http://code.google.com/p/alageospatialportal/source/browse/trunk/alaspatial/src/main/resources/spring/data-config.xml) available in the resources/spring directory. Reuse layer.properties and data-config.xml from Layers store.  This is a Spring configuration file that has details of the database connection.



### Actions
Check out the module [actions](http://code.google.com/p/alageospatialportal/source/browse/#svn%2Ftrunk%2Factions) from the trunk.
The important configuration is mostly in [data\_config.xml](http://code.google.com/p/alageospatialportal/source/browse/trunk/actions/src/main/webapp/WEB-INF/spring/data-config.xml) in the spring directory. This is a Spring configuration file that has details of the database connection.

Another important file would be the [web.xml](http://code.google.com/p/alageospatialportal/source/browse/trunk/actions/src/main/webapp/WEB-INF/web.xml) available under the WEB-INF directory. The configuration you are likely to want to change includes:
  * ALA Authentication configuration details
  * host details


## Run the apps
Once the app is configured for your environment, you need to start them up in the following sequence under tomcat:
  1. geoserver
  1. layers-service
  1. alaspatial
  1. actions
  1. webportal


## Build the apps
Each app builds into a single war file. All projects use Maven. You will typically want to create a run configuration for the build process in your IDE. If you are not using an IDE or prefer to have more control on the build, please use the following Maven statement to do a clean build

```
mvn clean install
```

You may also want to have a different set of settings for both your development and production environment. See ProductionEnvironment for instructions for deploying the war file.