# Geonetwork

Geonetwork is a web application used to store metadata records for datasets that do not have official metadata records elsewhere.

Geonetwork is installed on both the development and production servers at /usr/local/geonetwork. It can be started and stopped using the start-geonetwork.sh and stop-geonetwork.sh scripts in the bin directory.

The development instance of geonetwork can be accessed at http://spatial-dev.ala.org.au/geonetwork. The production instance can be accessed at http://spatial.ala.org.au/geonetwork.

## Loading a metadata record

Metadata records are loaded into geonetwork via xml files created using ANZMet lite (see below). Follow the following process to load records into geonetwork. This process must be carried out on both the development and production servers.

  1. Copy the metadata record xml files to a directory on the server (development or production)
  1. Browse to the appropriate instance of geonetwork (see the urls listed above)
  1. Log in as admin
  1. Select "administration" from the top menu bar
  1. In the popup window, select "Batch Import"
  1. In the "directory" textbox, enter the absolute path to the directory where the metadata xml files were copied to. Select an appropriate category for the xml records.
  1. Hit the upload button.

## Linking to a metadata record

The "metadatapath" value for a layer in the layers table in the database can point to a geonetwork metadata record. To reference a geonetwork metadata record you will need its uuid. The uuid can be obtained from the xml file for the metadata record. Open it up and locate the "file identifier" towards the top, it will look something like this:

```
  <gmd:fileIdentifier>
    <gco:CharacterString>D070922C-6805-4C2A-9705-34B026249DF5</gco:CharacterString>
  </gmd:fileIdentifier>
```

The uuid is D070922C-6805-4C2A-9705-34B026249DF5. To link to the metadata record from the layers table, set the metadatapath to the following:

```
<COMMON_GEONETWORK_URL>/srv/en/metadata.show?uuid=D070922C-6805-4C2A-9705-34B026249DF5
```

` <COMMON_GEONETWORK_URL> ` is a special string that will insert the appropriate path to the appropriate geonetwork instance, depending on whether the layers service is running on the development or production server. Using this special string prevents you from having to modify database entries when migrating them from development to production.

# ANZMet Lite

ANZMet lite is an application that can be used to create metadata records from importation into geonetwork. A custom version has been development to support some extra metadata fields required by the ala. A windows installer for this custom version is located at http://spatial.ala.org.au/files/ANZMetLiteSetup.msi.

The application has a wizard format and is fairly self-explanatory.