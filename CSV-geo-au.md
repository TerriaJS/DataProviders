---
layout: default
title: CSV-geo-au
permalink: /CSV-geo-au/
---
##  CSV-geo-au 1.1


**CSV-geo-au** is a convention for publishing **point** or **region-mapped** geospatial data in CSV format to data.gov.au and other open data portals. Datasets in this format are supported by [TerriaJS](https://github.com/terriajs/terriajs) (and hence the [National Map](http://nationalmap.gov.au/)) and will be useful for other purposes too. A `State` column in a CSV file tagged ` CSV-geo-au` can unambiguously be understood to refer to an Australian state, for example.

Datasets with line feature or explicit polygons (instead of references to standard polygon boundaries) are not covered by this standard, and should be provided as GeoJSON.

In this document:

* **Recommended**: The best field name for maximum reusability. High priority for support in TerriaJS (the software that runs the National Map). Sometimes several options are recommended, depending on your need for precision.
* **Accepted**: A field name which is reasonably reusable. Generally supported by TerriaJS.
* **Discouraged**: A field name which is ambiguous or not intuitive to a wide audience, but may be commonly used due to existing software. Possibly supported by TerriaJS but may be discontinued.

It is generally acceptable to include "discouraged" fields if there is also recommended or accepted fields as the recommended or accepted fields will be used.

**Document Status: initial use. This document will evolve, but it is unlikely that field names currently recommended will become deprecated.**

## Principles of design

In designing this specification, we have tried to balance these goals:

* Maximising the chance that existing CSV files may accidentally conform, correctly.
* Allowing motivated dataset publishers to be very precise about the exact boundaries their data relates to.
* Making column names guessable without consulting the specification.
* Matching what is currently supported in TerriaJS
* Encouraging the production of datasets which are easy to use by consumers who are unaware of this specification.
* Aligning with attribute names already used by authorities such as the ASGS

## General recommendations

Geospatial fields SHOULD be provided after all other fields.

The CSV format MUST:

* Consist only of one header row followed by data rows (no other metadata within the file)
* Use `,` as field delimiter
* Use `\r\n` (Windows) or `\n` (Linux, OSX) as end of line character
* Use double quotes around any value containing a comma, and double-double quotes to represent double quotes: `"like ""this"""`

It SHOULD be encoded in UTF-8. Headers are not considered to be case-sensitive.

Consider publishing the data as a [Tabular Data Package](http://dataprotocols.org/tabular-data-package/) by following those recommendations and including a `datapackage.json`. (Data Package use in Australia is experimental, for now.)

### Tags
In data.gov.au, datasets that conform to this standard SHOULD be tagged "` CSV-geo-au`". The dataset MUST NOT contain CSV files that do not conform (but may contain other non-CSV files) as these may be ambiguous and lead to confusing display in the National Map and other services.

###Quick summary
Tables should look like one of these:

    ID,Population,LGA_code,State
    1,100600,24600,VIC

or


    ID,Population,Postcode,State
    1,28000,3000,VIC

or

    ID,Name,Lat,Lon
    1,Bacchus Marsh Airport,-37.7313,144.4212

EITHER a latitude/longitude pair, OR one or more of the following region fields should be provided.


## Point data

To encode individual points with a latitude and longitude, two fields are required. Each MUST be a number in decimal degrees. Numbers SHOULD NOT be enclosed in double quotes.

##### Recommended field names
* `Lat`, `Lon` [the only format currently supported by TerriaJS]

##### Accepted field names
* `Latitude`, `Longitude`; 
* `Lat`, `Lng` [TerriaJS support imminent]

##### Discouraged
* `x`, `y`; 
* `WKT` (single column with data in `POINT(-37.8 144.9)` format); 
* `easting`, `northing`; 
* combined format: `(-37.8, 144.9)`; 
* GeoJSON

Locations SHOULD be given in the GDA94 datum ([EPSG:4283](http://spatialreference.org/ref/epsg/4283/)), but WGS84 is acceptable ([EPSG:4326](http://spatialreference.org/ref/epsg/4326/)). The difference is [generally less than one metre](http://www.geoproject.com.au/gda.faq.html). The datum chosen SHOULD be indicated in the metadata for the dataset. (There is currently no standard for this.)

## LGAs, postcodes and other regions 


### State

Authority  | Region name | Name or code | Year | Field name
---|---|---|---|---
ABS| State (ABS approximation) |2-3 letter code (VIC,NT); or name (New South Wales)   |Unspecified|`state`
ABS| State (ABS approximation) |2-3 letter code, or name  |2014|`state_2011`
ABS| State (ABS approximation) |1 digit code (3=Queensland) |Unspecified|`ste_code` (recommended),`ste`
ABS| State (ABS approximation) |1 digit code  |2014|`ste_code_2014`

The contents MUST be the two or three-letter form of the state or territory ("VIC", "NT"). It is not considered to be case-sensitive.

### Australian Statistical Geography Standard (ABS statistical regions)

These statistical area types [defined by the ABS](http://www.abs.gov.au/websitedbs/d33  10114.nsf/home/australian+statistical+geography+standard+%28asgs%29) are all currently supported by TerriaJS unless stated otherwise. In each case, the more precise `...2011` version SHOULD be used if you know which version of the ABS boundaries is appropriate. (TerriaJS does not currently support different versions.)

These field names generally match those used by the ABS. Synonyms are provided for consistency, simplicity and to match common practice.

#### Statistical area 1 (SA1)

*Note: Not currently supported by TerriaJS for performance reasons*

Name or code | Year | Field name | Synonym
---|---|---|---
11-digit code  |Unspecified|`sa1_maincode`| `sa1`,`sa1_code`
11-digit code  |2011|`sa1_maincode_2011`
7-digit code  |Unspecified|`sa1_7digitcode`
7-digit code  |2011|`sa1_7digitcode_2011`

#### Statistical area 2 (SA2)

Name or code | Year | Field name | Synonym
---|---|---|---
9-digit code *(1 digit state + 2 digit SA4 + 2 digit SA3 + 4 digits)*  |Unspecified|`sa2_code`| `sa2`
9-digit code                                                           |2011       |`sa2_code_2011`
5-digit code *(1 digit state + 4 digits)*                              |Unspecified|`sa2_5digitcode`
5-digit code                                                           |2011       |`sa2_5digitcode_2011`
Name (eg "O'Connor (WA)")                                              |Unspecified|`sa2_name`
Name                                                                   |2011       |`sa2_name_2011`

#### Statistical area 3 (SA3)

Name or code | Year | Field name | Synonym
---|---|---|---
5-digit code *(1 digit state + 2 digit SA4 + 2 digits)*  |Unspecified |`sa3_code`|`sa3`
5-digit code  |2011        |`sa3_code_2011`
Name (eg "North Sydney - Mosman")         |Unspecified |`sa3_name`
Name          |2011        |`sa3_name_2011`

#### Statistical area 4 (SA4)

Name or code | Year | Field name| Synonym
---|---|---|---
3 digit code *(1-digit state code + 2)*  |Unspecified |`sa4_code`|`sa4`
3 digit code                             |2011        |`sa4_code_2011`
Name *(eg "Melbourne - Inner South")*    |Unspecified |`sa4_name`
Name                                     |2011        |`sa4_name_2011`

#### [Greater Capital City Statistical Areas](http://www.abs.gov.au/ausstats/abs@.nsf/0/89DE0953464EE050CA257801000C651B?opendocument) (GCCSA)

Name or code | Year | Field name| Synonym
---|---|---|---
5-character alphanumeric code *(1-digit state code + 4)*  |Unspecified |`gccsa_code`|`gccsa`
5-character alphanumeric code *(eg 1GSYD)*                |2011        |`gccsa_code_2011`
Name *(eg "Greater Sydney")*                              |Unspecified |`gccsa_name`
Name                                                      |2011        |`gccsa_name_2011`

#### [Signifcant Urban Areas](http://www.abs.gov.au/ausstats/abs@.nsf/Latestproducts/7D88D2916BF4BBE3CA257A980013999D?opendocument) (SUA)

Name or code | Year | Field name| Synonym
---|---|---|---
4-digit code *(non-hierarchical)*       |Unspecified |`sua_code`|`sua`
4-digit code *(eg 5009)*                |2011        |`sua_code_2011`
Name *(eg "Warragul - Drouin")*         |Unspecified |`sua_name`
Name                                    |2011        |`sua_name_2011`

### ABS structures in the ASGS that are not supported by TerriaJS
Structure | Name or code | With year | Without year | Without year (Synonym)
---|---|---|---|---|---
[Mesh block](http://www.abs.gov.au/ausstats/abs@.nsf/0/A53A152BBF2992EBCA257801000C64BE?opendocument) | 11 digit code | `mb_code_2011` | `mb_code`| `mb`
[Section of state](http://www.abs.gov.au/ausstats/abs@.nsf/Latestproducts/3353FBEF62DC7586CA257A9800139A0B?opendocument) | 2 digit code | `sos_code_2011` | `sos_code` | `sos`
[Section of state range](http://www.abs.gov.au/ausstats/abs@.nsf/Latestproducts/939141C8833A30C6CA257A9800139A20?opendocument) | 3-digit code | `sosr_code_2011` | `sosr_code` | `sosr`
[Urban Centres and Localities](http://www.abs.gov.au/ausstats/abs@.nsf/Latestproducts/05773C1D8C9F2022CA257A98001399F7?opendocument) | 6-digit code | `ucl_code_2011` | `ucl_code` | `ucl`
[Indigenous Regions](http://www.abs.gov.au/ausstats/abs@.nsf/Latestproducts/05773C1D8C9F2022CA257A98001399F7?opendocumenthttp://www.abs.gov.au/ausstats/abs@.nsf/0/E9E5A81D2A2FCE9ACA2579100014B0E4?opendocument) | 3-digit code | `ireg_code_2011` | `ireg_code` | `ireg`
[Indigenous Locations](http://www.abs.gov.au/ausstats/abs@.nsf/0/540CFB81D35853FECA2579100014B035?opendocument) | 8-digit code | `iloc_code_2011` | `iloc_code` | `iloc`
[Indigenous Areas](http://www.abs.gov.au/ausstats/abs@.nsf/0/540CFB81D35853FECA2579100014B035?opendocument) | 6-digit code | `iare_code_2011` | `iare_code` | `iare`
[Remoteness Areas](http://www.abs.gov.au/AUSSTATS/abs@.nsf/DirClassManualsbyTopic/9A784FB979765947CA25738C0012C5BA?OpenDocument) | 2-digit code | `ra_code_2011` | `ra_code` | `ra`

### Non-ABS structures in ASGS
#### Postcode

A four digit Australian postcode.

##### Recommended field names 

Authority  | Region name | Name or code | Year | Field name
---|---|---|---|---
PSMA| Postcode |4 digit code  |Unspecified|`postcode`
PSMA| Postcode|4 digit code  |2015|`postcode_2015`
ABS| Postal area (ABS approximation) |4 digit code  |Unspecified|`poa`
ABS| Postal area (ABS approximation) |4 digit code  |2011|`poa_2011`

Note: PSMA's boundaries are not open data, and TerriaJS hence uses the ABS Postal areas to display postcodes. They are [not quite the same](http://www.abs.gov.au/websitedbs/censushome.nsf/home/factsheetspoa?opendocument&navpos=450).) 

For greater precision, additional fields `Suburb` and `State` MAY be provided. For example: `Postcode` 3068, `Suburb` Clifton Hill, `State` VIC.


#### Local Government Area

Name or code | Year | Field name | Synonym
---|---|---|---
[5 digit code](http://www.abs.gov.au/AUSSTATS/abs@.nsf/DetailsPage/1270.0.55.003July%202011) *(eg "31000")* |Unspecified|`lga_code` (recommended),`lga`
5 digit code  |2014|`lga_code_2014`
Name *(eg "Brisbane")* |Unspecified|`lga_name`|`adm2` (see note)
Name  |2014|`lga_name_2014`

Complete lists of 5 codes are available [here](http://www.abs.gov.au/AUSSTATS/abs@.nsf/DetailsPage/1270.0.55.003July%202011).

#### Using name fields

The `lga_name` field SHOULD be used only a human-readable addition to `lga_code`. It is NOT recommended as the primary lookup (and is not currently supported as such by TerriaJS). 

The `adm2` field (not currently supported by TerriaJS) must contain the short form of the LGA name, with no "City of", "Council" etc. For example: "Melbourne", "Greater Geelong". It SHOULD be capitalised like this. 

A separate `State` column (and/or `lga_code` column) MUST be provided, as LGA names are not unique across states.

#### Commonwealth Electoral Division

ABS approximations of electoral districts. [More information](http://www.abs.gov.au/ausstats/abs@.nsf/0/9C8331F55896F9C5CA2578D40012CF99?opendocument)

Name or code | Year | Field name| Synonym
---|---|---|---
3 digit code *(1-digit state code + 2, e.g. "402")*  |Unspecified|`ced_code`|`ced`
3 digit code  |2011                               |`ced_code_2011`
Name *(eg "Barker")*    |Unspecified|`ced_name`
Name  |2011                                       |`ced_name_2011`


#### State Electoral Division

ABS approximations of electoral districts. [More information](http://www.abs.gov.au/ausstats/abs@.nsf/Latestproducts/94496C7EA68A1522CA2578D40012CFB8)

Name or code | Year | Field name| Synonym
---|---|---|---
5 digit code *(1-digit state code + 4, e.g. "20106")*  |Unspecified|`sed_code`|`sed`
5 digit code  |2011                               |`sed_code_2011`
Name *(eg "Albert Park (Southern Metropolitan)")*    |Unspecified|`sed_name`
Name  |2011                                       |`sed_name_2011`

#### State Suburbs

ABS approximations of suburbs. [More information](http://www.abs.gov.au/AUSSTATS/abs@.nsf/Previousproducts/2C6132C0B332C336CA2578D40012CF76)

Name or code | Year | Field name| Synonym
---|---|---|---
5 digit code *(1-digit state code + 4, e.g. "10002")*  |Unspecified|`sesc_code`|`ssc`
5 digit code                                           |2011       |`ssc_code_2011`
Name *(eg "Abbotsford (NSW)")*                         |Unspecified|`ssc_name`
Name                                                   |2011       |`ssc_name_2011`

The field name `suburb` is currently treated as a synonym for `ssc` but may change.

### Non-ABS structures in the ASGS that are not supported by TerriaJS
Structure | Name or code | With year | Without year | Without year (Synonym)
---|---|---|---|---|---
[Australian Drainage Divisions](http://www.abs.gov.au/ausstats/abs@.nsf/0/EC3F333AD6AC80B3CA2578D40012CFDA?opendocument) | 3 character code: `D__` | `add_code_2011` | `add_code`
[Natural Resource Management Regions](http://www.abs.gov.au/ausstats/abs@.nsf/0/EA3811589050F62ACA2578D40012CFFE?opendocument) | 3-digit code | `nrmr_code_2011` | `nrmr_code` | `nrmr`
[Tourism Regions](http://www.abs.gov.au/ausstats/abs@.nsf/0/FA98FE427F88B97FCA2578D40012D01C?opendocument) | 5 character code: `_R___` | `tr_code_2011` | `tr_code` | `tr`

### Other boundaries

* `CNT2` (`iso2` acceptable): "Two letter country codes" ([ISO 3166-1 Alpha 2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2))
* `CNT3` (`iso3` acceptable): "Three letter country codes" ([ISO 3166-1 Alpha 3](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3))

### Other boundaries not currently supported by TerriaJS
These are included here to support standardisation and future support by TerriaJS.

* `SOS`, `SOS_2011`, `SOS_2006`: Section of State (ABS) (http://www.abs.gov.au/ausstats/abs@.nsf/Lookup/2901.0Chapter23102011)
* `SOSR`, `SOSR_2011`, `SOSR_2006`: Section of State Range (ABS)
* `SLA`, `SLA_2011`, `SLA_2006` : Statistical Local Area (ABS)
* `PHN`, `PHN_2015`, `PHN_2014`: Primary Health Network (Department of Health)