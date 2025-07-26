# The Protected Species Data Interchange Standard

<!-- markdownlint-disable no-duplicate-heading-->
<!-- markdownlint-disable no-inline-html-->

## Author

David Steckler  
Mysticetus  
August 2025

## Abstract

The Protected Species Data Interchange Standard is a geospatial data interchange format that extends GeoJSON [[RFC7946](<https://datatracker.ietf.org/doc/html/rfc7946>)] to support sharing and dissemination of data specific to operations (including but not limited to legal compliance and mitigation), and research around protected animal species. Typical data includes information about whales, dolphins, pinnipeds, sea turtles, polar bears, fisheries, avian, and terrestrial animals. Meta-data formats are  also specified, including information about observational effort, mitigation actions, behavioral observations, vehicle tracks, and a the types/methods of animal detections, and observation technology used.

## Status of This Memo

Dave's writing it. When he has a first draft, he will reach out to others in the industry. When we think we're ready, we'll submit to IETC for review.

## Copyright Notice

   Copyright (c) 2025 IETF Trust and the persons identified as the
   document authors.  All rights reserved.

   This document is subject to BCP 78 and the IETF Trust's Legal
   Provisions Relating to IETF Documents
   (<http://trustee.ietf.org/license-info>) in effect on the date of
   publication of this document.  Please review these documents
   carefully, as they describe your rights and restrictions with respect
   to this document.  Code Components extracted from this document must
   include Simplified BSD License text as described in Section 4.e of
   the Trust Legal Provisions and are provided without warranty as
   described in the Simplified BSD License.

## Table of Contents

TBD

## Introduction

Protected Species Data Interchange Standard is an extension of GeoJSON [[RFC7946](<https://datatracker.ietf.org/doc/html/rfc7946>)] that standardizes the transfer and dissemination of information about protected species animals (marine, terrestrial, and aerial).

This standard only extends GeoJSON and specifies additional required and optional JSON schema elements, notably as part of the GeoJSON  *properties* dictionary. This standard does not modify any part of the GeoJSON or underlying JSON [[RFC8259](<https://datatracker.ietf.org/doc/html/rfc8259>)] standards. GeoJSON and JSON standards **MUST** be adhered to.

The Protected Species Data Interchange Standard  adds a number of required and optional fields and sub-fields to the *properties* key of the standard GeoJSON object to  represent a wide variety of data types and values across protected species sciences, industries, and regulatory frameworks. This standard is intended to facilitate data sharing and dissemination of protected species data to a wide variety of collaborators and audiences.

This standard is specifically **NOT** intended to inform or specify standards for data collection, archival, aggregation/combination, QA, or analysis techniques.

GeoJSON feature objects augmented by this standard include, but are not limited to, *Point*, *MultiPoint*, *LineString*, and *Polygon*.

As the fundamental augmentation, a *record_id* field is a required key on all PS GeoJSON objects. The *record_id* key/value pair **MUST** be present in the GeoJSON *properties* dictionary, and the value **MUST** be a valid UUID [[RFC9562](<https://www.ietf.org/rfc/rfc9562.pdf>)]. Beyond *record_id*, other required and optional fields vary depending on the data type to be sent/received.

### Requirements Language

   The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
   "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and
   "OPTIONAL" in this document are to be interpreted as described in
   [[RFC2119](<https://datatracker.ietf.org/doc/html/rfc2119>)].

### Conventions Used in This Document

   The ordering of the members of any JSON object defined in this
   document MUST be considered irrelevant, as specified by [[RFC7159](<https://datatracker.ietf.org/doc/html/rfc7159>)].

   Some examples use the combination of a JavaScript single-line comment
   (//) followed by an ellipsis (...) as placeholder notation for
   content deemed irrelevant by the authors.  These placeholders must of
   course be deleted or otherwise replaced, before attempting to
   validate the corresponding JSON code example.

   Whitespace is used in the examples inside this document to help
   illustrate the data structures, but it is not required.  Unquoted
   whitespace is not significant in JSON.

### Universally Unique Identifier Usage

This standard uses Universally Unique Identifiers in a required field (*record_id*) and other optional fields that reference other objects. The UUID standard is described in [[RFC9562](<https://www.ietf.org/rfc/rfc9562.pdf>)]. As of this writing, there is no specified valid use of *Nil* UUID (00000000-0000-0000-0000-000000000000) or *Max* UUID (FFFFFFFF-FFFF-FFFF-FFFF-FFFFFFFFFFFF), but it is intended that external systems such as search/query engines **SHOULD** allow these for query purposes.

### Units

Latitides and longitudes **MUST** be in the WGS84 Geodetic System, as published and maintained by the United States National Geospatial-Intelligence Agency [[WGS84](<https://earth-info.nga.mil/index.php?dir=wgs84&action=wgs84>)].

Distances, including altitudes and depths, **MUST** be in meters.

Bearings **MUST** be a floating point value in the range 0-360 (0 inclusive, 360 exclusive), and **MUST** be True bearings relative to the geographic north pole.

Timestamps **MUST** be a string value in ISO 8601 [[ISO8601](<https://www.iso.org/iso-8601-date-and-time-format.html>)] combined format, with the T separator between date and time. For example, "*20250801T12:34:56.34773Z*".

### Simple Example

The following is a simple example of Protected Species Data Interchange of a whale sighting made by a visual observer (aka Marine Mammal Observer or Protected Species Observer). Other types of animal detections (e.g. acoustic detections, automated detections from IR cameras, animals detected from drones or satellites, photo identification of an individual animal, etc.) as well as associated metadata (e.g. vehicle GPS tracks, effort data, project information, etc.), use different sets of required and optional keys, as described in subsequent sections.

```json
{
    // Underlying GeoJSON object
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-160.863597, 71.555680]
    },

    // Required and optional properties to specify items unique to protected species interactions
    "properties": {
        "record_id" : "C6986655-D0B8-4B02-93BA-B79F5E03A253", // required
        "species_latin" : "Balaena mysticetus", // required
        "species_english" : "Bowhead whale", // optional
        "species_narwc" : "BOWH", // unspecified (neither required nor optional) additional key/values are allowed
        "timestamp_utc" : "20250801T12:34:56.34773Z", // required
        "timestamp_local" : "20250801T08:34:56.34773-0400", // optional
        "source" : // required
        {
            "name" : "Mysticetus", // required
            "reliability" : "Expert" // optional
        },
        "detection_metrics" : // required
        {
            "detection_id" : "Vis38095", // required
            "detection_method" : "Unaided Eye", // required
            "distance_method" : "Reticle Binocular", // required
            "optics_type" : "Nikon 7x50", // required for optical devices, optional otherwise
            "reticle" : 1.5, // required for reticle-based devices, optional otherwise
            "bearing_from_station" : 270.0, // required
            "distance_from_station" : 1237.4, // required
            "effective_eye_height" : 12.2 // required for optical devices, optional otherwise
        },
        "observer_metrics" : // required
        {
            "name" : "David Steckler", // required
            "eye_height" : 1.8, // required for devices (e.g. 7x50 reticle binos) that use total eye height, otherwise optional
        },
        "station_metrics" : // required
        {
            "name" : "Left Bridge Wing", // required
            "vehicle" : "SS Minnow", // optional
            "x" : 23.2, // optional. X-axis (generally relative to a bow-stern line of a vehicleoat) offset in meters from GPS antenna
            "y" : 12.1, // optional. Y-axis (generally relative to port-starboard line of a vehicle) offset in meters from GPS antenna
            "z" : 12.232 // required for stations that use devices (e.g. 7x50 reticle binos) that use total eye height, otherwise optional. Generally deck height above water - may be MSL or ASL for terrestrial observations.
        }
        "behavior_metrics" : // optional, if included, is an array of behaviors and unix timestamps
        [ 
            {
                "behavior" : "Blow", // required
                "behavior_timestamp_utc" : "20250801T12:34:56.34773Z" // required
            },
            {
                "behavior" : "Fluke Up",
                "behavior_timestamp_utc" : "20250801T12:35:21.812093Z"
            },
        ],
        "general_metrics" : // optional
        {
            "count" : 3, // optional
            "calves" : -1, // optional (-1 indicates explicit N/A)
            "notes" : "Animals were spotted of the starboard bow during a rain squall. Took photos for later photo id efforts."
        },

        // Property keys that do not conflict with documented required or optional keys (for example, "some_other_notes") are /
        // Senders MAY include any additional properties (and any number of sub-properties, arrays, etc.) in such additional 
        // properties. Readers MAY use these values, and readers MUST ignore these values if they
        // are not used (i.e. no errors should be triggered as long as the JSON and GeoJSON standards are adhered to).
        "some_other_notes" : 
        {
            "salish_sea_status" : "The sea was angry that day, my friends!"
        }
    }
}
```

## Non-Goals

This standard **ONLY** specifies the interchange format of protected species data between entities. This standard specifically does **NOT** attempt to address any of the following topics:

### Protected Species Data Collection Protocols / Schemas / Technologies

There are presently thousands of divergent schemas and protocols used around the world for protected species data collection. Standards for protected species data collection are severely lacking. However, this standard makes no attempt to remedy this situation.

### Protected Species Data Archival Schemas / Technologies

There are presently many thousands of incompatible protected species databases on the internet, on personal computers, and on millions of hard drives. Standards for protected species data storage and archival are severely lacking. However, this standard makes no attempt to remedy this situation.

### Protected Species Data QA/QC Processes

There are presently many thousands of divergent QA/QC modalities for ensuring protected species data quality. Standards for protected species QA/QC processes are severely lacking. However, the this standard makes no attempt to remedy this situation.

### Protected Species Data Aggregation / Combination Processes

There are presently any number of incompatible processes used to merge protected species data across multiple projects. Standards for protected species data aggregation (including especially data denormalization) are severely lacking. However, this standard makes no attempt to remedy this situation.

### Protected Species Name Encoding Conventions

There are currently hundreds of divergent encoding conventions in use for species names when stored in data records. Many of these highly cryptic conventions have history from a time when every byte mattered, and taxonomic standard Latin names are frequently ignored. While this standard **DOES** require a Latin species name property for animal records, this standard does **NOT** attempt to remedy the incompatible common name encodings of protected species around the planet.

## Record Types

Below are listed the standardized record types. Some records, especially *Localized Animal(s) Detection Record* have extensive optional sub-trees of information about related tasks (for example, *mitigation_metrics*) that may or may not be appropriate for a specific record.

Senders **MAY** include any additional properties at their discretion as long as the additional property names do not conflict with any specified required or optional property names. Receivers **MUST** successfully ignore such additional properties (no errors) they do not use.

### Localized Animal(s) Dectection Record

Localized animal detections are implemented as a GeoJSON *Point* object. For example:

```json
{
    // Underlying GeoJSON object
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-160.863597, 71.555680]
    },

    // Required and optional properties to specify items unique to protected species interactions
    "properties": { 
        // ... 
    },
    // ...
}
```

#### Required Properties

| Name | Type | Description | Examples |
|------|------|-------------|---------|
| record_id | string | A unique identifier for this record. | "C6986655-D0B8-4B02-93BA-B79F5E03A253" |
| species_latin | string | Latin name of the detected animal(s). If not known to species level, can be "(genus) sp.". Unknown values are represented via the English word "Unidentified", which may have a qualifier | "Balaena mysticetus"<br>"Balaena sp."<br>"Unidentified"  |
| timestamp_utc | string | The UTC timestamp of this detection event. | "20250801T12:34:56.34773Z" |
| source | sub-tree | Information about the source of this detection. | "source" : {...} |
| source.name | string | Name of the source of this data. | "source" : { ..., "name" : "Mysticetus" ... } |
| TODO - MORE TO ADD | | | |

#### Optional properties

TODO

#### Example

TODO

### Non-Localized (Presence) Animal(s) Detection Record

TBD - Mostly thinking non-localized PAM detections here. Bearing lines + ambiguity and probability ellipses go here.

#### Required Properties

TODO

#### Optional properties

TODO

#### Example

TODO

### Animal Behavioral Record

#### Required Properties

TODO

#### Optional properties

TODO

#### Example

TODO

### Animal Individual ID Record

TBD - ***Will Need Ted Cheeseman's (and others) help***

#### Required Properties

TODO

#### Optional properties

TODO

#### Example

TODO

### Effort Record

#### Required Properties

TODO

#### Optional properties

TODO

#### Example

TODO

### Vehicle Track Record

#### Required Properties

TODO

#### Optional properties

TODO

#### Example

TODO