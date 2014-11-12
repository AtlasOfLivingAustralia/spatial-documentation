# Introduction

Web application providing services for contextual, environmental, distribution and checklist layers.


# Dependencies
  * [layers-store](wiki/SystemDocumentationLayersStore)
  * [local files](wiki/SystemDocumentationLocalFiles)


# Configuration
  * layers-store
    * WEB-INF/classes/layer.properties, update for the environment.
    * WEB-INF/spring/data-config.xml, update for Postgres.
  * WEB-INF/classes/user.properties, update for the environment.
    * set layers\_service\_url to the deployed URL.


# Deployment
Deploy as WAR.

# Reloading
The layers service can be reloaded (i.e. data pulled from the layers db will be reloaded) by calling /intersect/reloadconfig e.g. http://spatial.ala.org.au/layers-service/intersect/reloadconfig)