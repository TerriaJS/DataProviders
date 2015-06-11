---
layout: page
title: Supported data sources
permalink: /datasources/
---
Terria can display data from a wide range of data sources. The key principles are:

- data is loaded directly from your data source into the user's browser
- metadata comes from your data source, but can be overriden in a [data source configuration file](../datasourceconfig.md).
- the better the metadata at your service end, the easier it is to display in Terria

There are three main types:

- "catalog item" services: 1 URL points to one dataset, such as through WMS or WFS
- "catalog group" services: 1 URL points to a collection of datasets, such as through WMS `getCapabilities` or CKAN
- "file" services: 1 URL points to a file which is completely downloaded into the browser by HTTP and then displayed


## Files
### CSV
Three kinds of geospatial CSVs are supported:

- points (latitude, longitude)
- regions such as postcodes or state electoral divisions
- points with time

Read the [Aus-Geo-CSV specification](Aus-Geo-CSV) for full details.

### GeoJSON
...

### KML
...

### Other OGR files
Any file format supported by the OGR suite (GDAL) is indirectly supported by first uploading it through an online conversion process. This is built into TerriaJS. There are several limitations:

- the user must choose to allow this to happen
- large files take a long time and may fail
- the overall user experience is slower and less engaging than for natively supported file types

## Catalog group types
### CKAN (group)

CKAN is a data catalogue used by the Federal Government and several State Governments. Depending on its configuration, spatial files may be hosted directly (such as .geojson files), or it may contain links to WMS services.

CKAN does not have a simple filter for geospatial datasets, so the CKAN group type uses the CKAN API and several heuristics to attempt to locate them.

You can make this easier by:

- tagging all spatial CSV's `Aus-Geo-CSV`
- providing GeoJSON versions of all spatial files




### WMS

#### Performance
To get quick performance in Terria, your server [must be configured with cached map tiles](http://docs.geoserver.org/latest/en/user/geowebcache/using.html). Ensure the cache is populated for every layer.

Map tiles should be served in 50ms or less, with 400ms considered the absolute maximum for acceptable user experience. To measure the speed, use the Dataset Testing tool:

1. Add `#tools=1` to the end of your Terria URL (for instance, `nationalmap.research.nicta.com/#tools=1`)
2. Open the group you wish to test
3. Click `Tools` in the menu at the top right
4. Choose settings: `All Opened` tests every opened group, `All Enabled` tests only individual datasets that are selected. You can set a specific tile zoom range to test as well.
5. Click `Request Tiles`.

For each dataset, you will see whether all tiles were successfully retrieved, and the average and maximum response time.



#### Single layer
#### getCapabilities


### WFS `getCapabilities`
### ArcGIS

