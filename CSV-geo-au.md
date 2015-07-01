---
layout: default
title: csv-geo-au
permalink: /csv-geo-au/
---
##  csv-geo-au 1.1


**csv-geo-au** is a specification for publishing **point** or **region-mapped** Australian geospatial data in CSV format to data.gov.au and other open data portals. Datasets in this format are supported by [TerriaJS](https://github.com/terriajs/terriajs) (and hence the [National Map](http://nationalmap.gov.au/)) and are intended to be as reusable as possible. A `State` column in a CSV file tagged ` csv-geo-au` can unambiguously be understood to refer to an Australian state, for example.

Datasets with line feature or explicit polygons (instead of references to standard polygon boundaries) are not covered by this standard, and should be provided as GeoJSON.

**Document Status: initial use. This document will evolve, but it is unlikely that field names currently recommended will become deprecated.**

### Terminology

* **Recommended**: The best field name for maximum reusability. High priority for support in TerriaJS (the software that runs the National Map). Sometimes several options are recommended, depending on your need for precision.
* **Accepted**: A field name which is reasonably reusable. Generally supported by TerriaJS.
* **Discouraged**: A field name which is ambiguous or not intuitive to a wide audience, but may be commonly used due to existing software. Possibly supported by TerriaJS but may be discontinued.

It is generally acceptable to include "discouraged" fields if there is also recommended or accepted fields as the recommended or accepted fields will be used.


### Goals

In designing this specification, we have tried to balance these goals:

* Maximising the chance that existing CSV files may accidentally conform, correctly.
* Allowing motivated dataset publishers to be very precise about the exact boundaries their data relates to.
* Making column names guessable without consulting the specification.
* Encouraging the production of datasets which are easy to use by consumers who are unaware of this specification.
* Aligning with attribute names already used by authorities such as the ASGS

### General recommendations

The CSV format MUST:

* Consist only of one header row followed by data rows (no other metadata within the file)
* Use `,` as field delimiter
* Use `\r\n` (Windows) or `\n` (Linux, OSX) as end of line character
* Use double quotes around any value containing a comma, and double-double quotes to represent double quotes: `"like ""this"""`

It SHOULD be encoded in UTF-8. Headers are not considered to be case-sensitive.

### Use in data.gov.au
In data.gov.au and other CKAN-based portals, resources (individual files) that conform to this standard SHOULD be given a resource type of `csv-geo-au`. This is required for National Map to locate and display them. Resources tagged as `csv-geo-au` can also be previewed on data.gov.au like other CSV files.

###Quick summary
Tables should look like one of these:

    ID,Population,LGA_code_2015,State
    1,100600,24600,VIC

or


    ID,Population,Postcode,State
    1,28000,3000,VIC

or

    ID,Name,Lat,Lon
    1,Bacchus Marsh Airport,-37.7313,144.4212

EITHER a latitude/longitude pair, OR one or more region fields should be provided.

## Point data (latitude & longitude)

To encode individual points with a latitude and longitude, two fields are required. Each MUST be a number in decimal degrees. Numbers SHOULD NOT be enclosed in double quotes.

##### Recommended field names
* `Lat`, `Lon` *[the only format currently supported by TerriaJS]*

##### Accepted field names
* `Latitude`, `Longitude`; *[not currently supported by TerriaJS]*
* `Lat`, `Lng`  *[not currently supported by TerriaJS]*

##### Discouraged
* `x`, `y`; 
* `WKT` (single column with data in `POINT(-37.8 144.9)` format); 
* `easting`, `northing`; 
* combined format: `(-37.8, 144.9)`; 
* GeoJSON

Locations SHOULD be given in the GDA94 datum ([EPSG:4283](http://spatialreference.org/ref/epsg/4283/)), but WGS84 is acceptable ([EPSG:4326](http://spatialreference.org/ref/epsg/4326/)). The difference is [generally less than one metre](http://www.geoproject.com.au/gda.faq.html). The datum chosen SHOULD be indicated in the metadata for the dataset. (There is currently no standard for this.)

## LGAs, postcodes and other regions 


### Australian Statistical Geography Standard (ABS statistical regions)

For each boundary type, there are usually three field names that can be used for matching on codes:

- "**Field with year**" (eg `sa4_code_2011`). This is the most precise, and recommended, particularly for boundaries which change frequently. Certain boundaries move significantly every year (eg LGA), and some are completely renumbered in each reissue (eg, Tourism Regions). (TerriaJS does not currently support different versions.)
- "**Field without year**" (eg `sa4_code`). This is acceptable when the year is not known.

These field names generally match those used by the ABS. In addition, we define:

- "**Synonym**" (eg `sa4`). This unofficial  shorthand is useful for matching spreadsheets in this form, but it is not recommended due to ambiguity: does the field contain codes or names? (TerriaJS always assumes codes.)

In addition, we define field names for matching on names (eg `sa4_name_2011`). 

Please note that ***there is currently no support for matching any regions by name***, although this support is under development.


### [State/Territory](http://www.abs.gov.au/ausstats/abs@.nsf/Latestproducts/07361C81F0DA2584CA257801000C6500?opendocument) (STE)

Name or code | Field with year | Field without year | Synonyms
---|---|---|---|---
Full name (New South Wales)   |`ste_name_2011`| `ste_name`|`state`
1 digit code (3=Queensland) |`ste_code_2011`  |`ste_code` |`ste`

*Note: TerriaJS may in the future support state abbreviations (TAS etc)*

#### [Statistical area 1](http://www.abs.gov.au/ausstats/abs@.nsf/Latestproducts/7CAFD05E79EB6F81CA257801000C64CD?opendocument) (SA1)

*Note: Not currently supported by TerriaJS for performance reasons. The use of "maincode" here follows the ABS' convention.*

Name or code | Field with year | Field without year | Synonym
---|---|---|---
11-digit code  |`sa1_maincode_2011`|`sa1_maincode`| `sa1`,`sa1_code`
7-digit code  |`sa1_7digitcode_2011`|`sa1_7digitcode`

#### [Statistical area 2](http://www.abs.gov.au/ausstats/abs@.nsf/Latestproducts/88F6A0EDEB8879C0CA257801000C64D9?opendocument) (SA2)

Name or code | Field with year | Field without year | Synonym
---|---|---|---
9-digit code<br/> *(1 digit state + 2 digit SA4 + 2 digit SA3 + 4)*  |`sa2_code_2011`|`sa2_code`| `sa2`
5-digit code<br/> *(1 digit state + 4)*                              |`sa2_5digitcode_2011`|`sa2_5digitcode`
Name (eg "O'Connor (WA)")                                              |`sa2_name_2011`|`sa2_name`

#### [Statistical area 3](http://www.abs.gov.au/ausstats/abs@.nsf/Latestproducts/E7369D1FCE596315CA257801000C64E5?opendocument) (SA3)

Name or code | Field with year | Field without year | Synonym
---|---|---|---
5-digit code *(1 digit state + 2 digit SA4 + 2 digits)*  |`sa3_code_2011` |`sa3_code`|`sa3`
Name *(eg "North Sydney - Mosman")*          |`sa3_name_2011` |`sa3_name`

#### [Statistical area 4](http://www.abs.gov.au/ausstats/abs@.nsf/Latestproducts/B01A5912123E8D2BCA257801000C64F2?opendocument) (SA4)

Name or code | Field with year | Field without year| Synonym
---|---|---|---
3 digit code *(1-digit state code + 2)*  |`sa4_code_2011` |`sa4_code`|`sa4`
Name *(eg "Melbourne - Inner South")*    |`sa4_name_2011`|`sa4_name`

#### [Greater Capital City Statistical Areas](http://www.abs.gov.au/ausstats/abs@.nsf/0/89DE0953464EE050CA257801000C651B?opendocument) (GCCSA)

Name or code | Field with year | Field without year| Synonym
---|---|---|---
5-character alphanumeric code <br/>*(1-digit state code + 4, eg 1GSYD)*  |`gccsa_code_2011` |`gccsa_code`|`gccsa`
Name *(eg "Greater Sydney")*                              |`gccsa_name_2011`|`gccsa_name`

#### [Signifcant Urban Areas](http://www.abs.gov.au/ausstats/abs@.nsf/Latestproducts/7D88D2916BF4BBE3CA257A980013999D?opendocument) (SUA)

Name or code |  Field with year | Field without year| Synonym
---|---|---|---
4-digit code *(non-hierarchical, eg 5009)*       |`sua_code_2011` |`sua_code`|`sua`
Name *(eg "Warragul - Drouin")*         |`sua_name_2011`| `sua_name`

### ABS structures in the ASGS that are not currently supported by TerriaJS

*Note: As of June 2015, no decisions have been made about future support of these structures.*

Structure | Name or code | With year | Without year | Synonym
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
#### Postcode / [postal area](http://www.abs.gov.au/AUSSTATS/abs@.nsf/Previousproducts/736A18F4CBF81595CA2578D40012CF51?opendocument)

A four digit Australian postcode.

##### Recommended field names 

Authority  | Region name | Name or code | Field with year | Field without year
---|---|---|---|---
PSMA| Postcode |4 digit code  |`postcode_2015`|`postcode`
ABS| Postal area (ABS approximation) |4 digit code  |`poa_2011`|`poa`

*Note: PSMA's boundaries are not open data, and TerriaJS hence uses the ABS Postal areas to display postcodes. They are [not quite the same](http://www.abs.gov.au/websitedbs/censushome.nsf/home/factsheetspoa?opendocument&navpos=450).)* 

For greater precision, additional fields `Suburb` and `State` MAY be provided. For example: `Postcode` 3068, `Suburb` Clifton Hill, `State` VIC.


#### [Local Government Area](http://www.abs.gov.au/AUSSTATS/abs@.nsf/Previousproducts/77214EF6765D0541CA2578D40012CF2E?opendocument)

Name or code | Field with year | Field without year | Synonym
---|---|---|---
[5 digit code](http://www.abs.gov.au/AUSSTATS/abs@.nsf/DetailsPage/1270.0.55.003July%202011) *(eg "31000")* |`lga_code_2014`|`lga_code`|`lga`
Name *(eg "Brisbane")* |`lga_name_2014`|`lga_name`|`adm2` (see note)

Complete lists of 5 codes are available [here](http://www.abs.gov.au/AUSSTATS/abs@.nsf/DetailsPage/1270.0.55.003July%202011).

#### Using name fields

The `lga_name` field SHOULD be used only a human-readable addition to `lga_code`. It is NOT recommended as the primary lookup (and is not currently supported as such by TerriaJS). 

The `adm2` field (not currently supported by TerriaJS) must contain the short form of the LGA name, with no "City of", "Council" etc. For example: "Melbourne", "Greater Geelong". It SHOULD be capitalised like this. 

A separate `State` column (and/or `lga_code` column) MUST be provided, as LGA names are not unique across states.

#### [Commonwealth Electoral Division](http://www.abs.gov.au/ausstats/abs@.nsf/0/9C8331F55896F9C5CA2578D40012CF99?opendocument)

ABS approximations of electoral districts.

Name or code | Field with year | Field without year | Synonym
---|---|---|---
3 digit code *(1-digit state code + 2, e.g. "402")*  |`ced_code_2011`|`ced_code`|`ced`
Name *(eg "Barker")*    |`ced_code_2011`|`ced_name`


#### [State Electoral Division](http://www.abs.gov.au/AUSSTATS/abs@.nsf/Previousproducts/94496C7EA68A1522CA2578D40012CFB8?opendocument)

ABS approximations of electoral districts.

Name or code | Field with year | Field without year | Synonym
---|---|---|---
5 digit code *(1-digit state code + 4, e.g. "20106")*  |`sed_code_2011`|`sed_code`|`sed`
Name *(eg "Albert Park (Southern Metropolitan)")*    |`sed_name_2011`|`sed_name`

#### [State Suburbs](http://www.abs.gov.au/AUSSTATS/abs@.nsf/Previousproducts/2C6132C0B332C336CA2578D40012CF76)

ABS approximations of suburbs.

Name or code | Field with year| Field without year | Synonym
---|---|---|---
5 digit code *(1-digit state code + 4, e.g. "10002")*  |`ssc_code_2011`|`ssc_code`|`ssc`
Name *(eg "Abbotsford (NSW)")*                         |`ssc_name_2011`|`ssc_name`

The field name `suburb` is currently treated as a synonym for `ssc` but may change.

### Non-ABS structures in the ASGS that are not supported by TerriaJS
Structure | Name or code | With year | Without year | Without year (Synonym)
---|---|---|---|---|---
[Australian Drainage Divisions](http://www.abs.gov.au/ausstats/abs@.nsf/0/EC3F333AD6AC80B3CA2578D40012CFDA?opendocument) | 3 character code: `D__` | `add_code_2011` | `add_code`
[Natural Resource Management Regions](http://www.abs.gov.au/ausstats/abs@.nsf/0/EA3811589050F62ACA2578D40012CFFE?opendocument) | 3-digit code | `nrmr_code_2011` | `nrmr_code` | `nrmr`
[Tourism Regions](http://www.abs.gov.au/ausstats/abs@.nsf/0/FA98FE427F88B97FCA2578D40012D01C?opendocument) | 5 character code: `_R___` | `tr_code_2011` | `tr_code` | `tr`

### Country boundaries

Name or code | Field | Synonyms
---|---|---|---|---
Two letter country code ([ISO 3166-1 Alpha 2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2))<br/>*(eg AU)* | `cnt2` | `iso2`
Three letter country code ([ISO 3166-1 Alpha 3](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3))<br/>*(eg AUS)* | `cnt3` | `iso3`

### Other boundaries not currently supported by TerriaJS
These are included here to support standardisation and future support by TerriaJS. As of June 2015, no decisions have been made about future support.

#### [Primary Health Network](http://www.health.gov.au/internet/main/publishing.nsf/Content/phn-boundaries) (Department of Health)

Name or code | Field with year | Field without year | Synonym
---|---|---|---
6 character `PHN___` code *(eg PHN101)* | `phn_code_2015` | `phn_code` | `phn`
Name *(eg "Central and Eastern Sydney")* | `phn_name_2015` | `phn_name`

#### [Statistical Local Area](http://www.abs.gov.au/ausstats/abs@.nsf/Lookup/2901.0Chapter23002011) 

An obsolete ABS structure.

Name or code | Field with year | Field without year | Synonym
---|---|---|---
Code | `sla_code_2006` | `sla_code` | `sla`
Name | `sla_name_2006` | `sla_name` 

