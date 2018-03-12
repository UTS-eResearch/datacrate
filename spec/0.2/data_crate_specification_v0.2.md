# DataCrate Specification version 0.2

This is a draft work in progress, any and all details of this spec could change,
including its very existence.

Please give feeback via github issues or a pull request.

The samples and examples here have not been exhaustively verified, and there may
be some inconsistencies between this spec and the associated files, such as the
[DataCrate JSON-LD Context]. Where there *is* a discrepancy, the file is the
authority rather than the spec.

## Changes since version 0.1

The following changes have been made since version 0.1:

-  Added a new kind of DataDrate, a *Working DataCrate* which is not bagged, for
   use on working data.

-  ```CATALOG.html``` is now ```index.html``` - it remains in the package root

-  RDFa metadata is no longer required in ```index.html```

-  Metadata files - ```datacite.xml``` and ```CATALOG.json``` are now in a
   metadata Directory

- The spec now references the [SPAR] ontologies for terms which are not available
  in [schema.org].


## Introduction

This document specifies a method of organising file-based data  with associated
metadata, known as *DataCrate* in both human and machine readable formats, based
on [schema.org] linked-data supplemented with terms from the [SPAR] ontologies
where schema.org does not have coverage. The motivation for this work comes from
the research domain.

A *DataCrate* is a dataset a set of files contained in a single directory. There
are two ways of organizing a *DataCrate*.

1. For working data or data that does not need to be distributed with checksums,
   *Working DataCrate* is a plain-old directory containing payload data files,
   with two metadata files   at the root.

2. For distribution, or archiving where integrity is important, a *Bagged
   DataCrate* is a [BagIt] bag conforming to the [DataCrate BagIt profile] with
   the payload files in the ```/data``` directory. A *Bagged DataCrate* has a
   clear separation between metadata and payload, and can be integrity-checked
   using the checksums in the [BagIt] manifest.

About the contents of a datacrate:
*  A *DataCrate* has a ```index.html``` tag file describes the files and
   directories in the BagIt payload (/data) directory.  The ```index.html``` has
   metadata about the dataset as a whole, and MAY have information about
   individual files and directories, such as their title, license, authorship or
   other provenance information. In both kinds of datacrate ```index.html``` is
   in the root of the datacrate.

*  A DataCrate has a ```CATALOG.json``` with a JSON-LD version
   of ```index.html```, the metadata in this file is organized in a particular way
   via a JSON-LD frame, for easy processing. In a *Working DataCrate* this is
   stored in the root directory, in a *Bagged DataCrate* it is
   in ```/metadata``` - to align with the TODO RDA spec name here.

*  If there is enough metadata, and the dataset has a DOI, a DataCrate has a
   DataCite metadata file and a human-readable citation in ```index.html```.

This specification is a practical guide for software authors to create tools for
generating and consuming research data packages. The format is not optimized for
hand-authoring, it is optimized for maximum convenience for data consumers with
the use of HTML for users to read and  JSON-LD for programmers and search-engines.

The DataCrate specification aims to ensure that packages are as self documenting
as possible.  Anyone with a Data Crate zip file can unzip it, notice that there
is an ```index.html``` file in the base directory, open that in a web browser,
and find out about the data; where it came from, who to contact about it, if it
has a homepage and so on, which is more usable than simple zipping or bagging of
files or using XML-based metadata formats. DataCrates can also be hosted on
web-servers no effort as the index.html file describes the contents.

In an academic context, it is important to be able to associate data with funded
research projects and publications, and desirable to be able to describe the
*contents* of a data package, not just the data in aggregate, so DataCrate
allows for file-level descriptions; to describe file format and extent,
licensing, copyright ownership and authorship and other metadata at a granular
level.

Because DataCrates could be created at many different stages in the research
data lifecycle there is a very minimal mandated minimum set of metadata. There
are no metadata requirements at all for a *Working DataCreate*. For a *Bagged
DataCrate* the mandatory metadata is:
*  A creation date,
*  Contact details and
*  A human-readable description of the data.

Future versions of this specification may allow for profiles to be specified
that are appropriate to a range of services such as repositories, registries.
and research software applications.


## Examples

There are examples of DataCrates in the [samples] directory.

## Definitions

*Data Entity*: A dataset, directory or file.

*MetaData Entity*: A [Person], [Organization], [Project], item of [Equipment], [license]
or any other *thing* that forms part of the metadata for a DataCrate.

*[DataCrate JSON-LD Context]*: A [JSON-LD] context that provides human-readable
labels for datacrate metadata. These labels are used in ```CATALOG.json``` which
contains *DataCrate-flattened JSON-LD*.

*DataCrate-flattened JSON-LD*: a JSON-LD document, using the *DataCrate JSON-LD
Context* containing DataCrate metadata, which has been flattened according to
the rules in  [JSON-LD 1.1].

## Conventions

Throughout this specification, RDF terms are be referred to using the names
defined in  [DataCrate JSON-LD Context]. Following [schema.org] practice property
names start with lowercase letters and class names with uppercase.

In ```CATALOG.json``` RDF terms use their DataCrate JSON-LD names
as defined in the [DataCrate JSON-LD context].

In examples paths for a *Working DataCrate* are used. That is, the [path] of a
file is relative to a ```CATALOG.json``` document in the root of the DataCrate
rather than in /metadata, when it would be located in a *Bagged DataCrate*. The
difference is illustrated by example below.

## Core metadata standard for DataCrate: Schema.org

Schema.org is the base metadata standard for DataCrate with a few additions to
supplement gaps in schema.org's ability to represent needed metadata. Schema.org
was chosen because it is widely used on the World Wide Web, and supported by
search engines, on the basis that this will improve the chances of commercial
search engines indexing the content using the semantics provided by DataCrate,
in the absence of an existing linked-data schema for research data with the
coverage needed for this project.

Future versions of this specification may be based on other metadata standards.

Additional metadata MAY be drawn from any RDF vocabulary to allow open-ended
extensibility.

## Summary of Coverage

This specification has guidelines for ways to represent:

* Contact information for a data set, with heuristics for creating BagIt
  metadata from this information for *Bagged DataCrates*.

* Descriptive information for a dataset and the files within it such as an
  abstract, spatial and temporal coverage.

* Associated Publications.

* Funding relationships.

* Provenance information of various kinds; who (people and organizations) and
  what (instruments and computer programs) created or contributed to the data
  set and individual files within it.

* If there is enough metadata, a DataCite citation for
  the crate.

## Note on compromises / limitations of schema.org

One crucial *MetaData Entity* is a research project. Schema.org has no class for
a project, although it does have a [funder] property. DataCrate
represents a research project using [frapo:Project] (from the [SPAR] ontologies
[FRAPO] ontology), which in turn MAY have a [funder] property.


Likewise, schema.org does not have a way to represent research equipment so
DataCrate uses the [frapo:Equipment] class.

A [Dataset] has no contactPoint property, unlike [DCAT], so DataCrate uses
[accountablePerson] for a contact point, similar to the concept
"Corresponding Author" in academic publishing.


# Structure / DataCrate by example

This section describes the DataCrate specification starting from the BagIt
structure on which it is built, with examples. It contains some normative
statements (what a DataCrate SHOULD, MUST and MAY have).

The Bagit file-system structure is as follows.

```

           <base directory>/
           |   bagit.txt
           |   bag-info.txt (with BagIt-Profile-Identifier: matching this specification)
           |   manifest-<algorithm>.txt
           |   ```index.html```
           \--- metadata
               |   ```CATALOG.json```
               |   [optional DataCite.xml]
               |   [optional additional tag files]
           \--- data/
                 |   [payload files]
           \--- [optional tag directories]/
                 |   [optional tag files]

```

[Part 2 of the BagIt profile specification]() says:
> Bags complying to a BagIt profile MUST contain the tag BagIt-Profile-Identifier
> in their bag-info.txt, which value is the URI of the JSON file containing the
> profile it conform to. This tag MAY be repeated if the bag conforms to multiple
> profiles. The URI that identifies the profile SHOULD be versioned, e.g.
> http://example.com/bagitprofiles/profile-bar-v0.1.json.  Resolving the URI with
> this specification.


The bag-info.txt for version 0.1 of a DataCrate MUST contain the following
metadata:

```
BagIt-Profile-Identifier: https://raw.githubusercontent.com/UTS-eResearch/datacrate/develop/spec/0.1/profile-datacrate-v0.1.json
DataCrate-Specification-Identifier: https://github.com/UTS-eResearch/datacrate/blob/develop/spec/0.1/data_crate_specification_v0.1.md
```

## ```index.html``` and ```CATALOG.json```

-  ```index.html``` MUST be an HTML 5 document.

-  ```CATALOG.json``` MUST be a *DataCrate-flattened JSON-LD* document.


## About ```CATALOG.json```

The following *DataCrate-flattened JSON-LD* represents a minimal description of
a *Working DataCrate* dataset.  The [DataCrate JSON-LD Context] MUST be included inline as below.
Ordering is meaningful as it is used to sort the order in which metadata is
displayed.


(From now on the @context will be omitted from examples.)

```
{
  "@context": #TODO add latest one
  {
      "ContentSize": "schema:contentSize",
      "Copyright": "schema:copyrightHolder",
      "Publisher": "schema:publisher",
      "DatePublished": "schema:datePublished",
      "HasPart": "schema:hasPart",
      "ID": "schema:identifier",
      "Description":  "schema:description",
      "License": "schema:license",
      "Title": "schema:name",
      "Name": "schema:name",
      "Creator": "schema:creator",
      "Contributor": "schema:contributor",
      "Related": "schema:relatedLink",
      "SameAs":  "schema:sameAs",
      "BasedOn": "schema:isBasedOn",
      "Translator": "schema:translator",
      "TranslationOf": "schema:translationOf",
      "Funder": "schema:funder",
      "Person": "schema:Person",
      "Contact": "schema:accountablePerson",
      "Email": "schema:email",
      "Phone": "schema:telephone",
      "Dataset": "schema:Dataset",
      "fileFormat": "schema:fileFormat",
      "encodingFormat": "schema:encodingFormat",
      "TemporalCoverage": "schema:temporalCoverage",
      "SpatialCoverage": "schema:spatialCoverage",
      "ContentLocation": "schema:contentLocation",
      "Keywords": "schema:keywords",
      "Subject": "schema:subject",
      "GivenName":  "schema:givenName",
      "FamilyName": "schema:familyName",
      "Place": "schema:Place",
      "Organization": "schema:Organization",
      "Affiliation": "schema:affiliation",
      "Funder": "schema:Funder",
      "GeoShape": "schema:GeoShape",
      "GeoCoordinates": "schema:GeoCoordinates",
      "geo": "schema:geo",
      "Latitude": "schema:latitude",
      "Longitude": "schema:longitude",
      "Box": "schema:Box",
      "CreativeWork": "schema:CreativeWork",
      "MediaObject": "schema:MediaObject",
      "Project": "frapo:Project",
      "Equipment": "vivo:Equipment",
      "ScholarlyArticle": "schema:ScholarlyArticle",
      "SoftwareApplication": "schema:SoftwareApplication",
      "Format": "formats:Format",
      "formats": "http://www.w3.org/ns/formats/",
      "Interviewee": "bibo:interviewee",
      "bibo": "http://purl.org/ontology/bibo/",
      "cc": "http://creativecommons.org/ns#",
      "dct": "http://purl.org/dc/terms/",
      "foaf": "http://xmlns.com/foaf/0.1/",
      "rdf": "http://www.w3.org/1999/02/22-rdf-syntax-ns#",
      "rdfa": "http://www.w3.org/ns/rdfa#",
      "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
      "schema": "http://schema.org/",
      "vivo": "http://vivoweb.org/ontology/core#"

  },
  "@graph": [
    {
      "@id": "data",
      "@type": "Dataset",
      "Description": "This dataset doesn't really exist"
    }
  ]
}

```


If there are files in the payload (/data) directory each file MAY be
described by a *Dataset* related to the
schema:Dataset with a property *HasPart*.

NOTE: As per the BagIt standard, every file in the payload directory MUST be in
the BagIt Manifest.  Describing every file in the payload directory is possible
with DataCrate, but is not mandatory.  For use-cases where it is important to
describe all files, future versions of this specification MAY allow for
profiles, using which it will be possible to specify constraints such as "every
file must have a [description]".

To describe a file we will use this example. Take a file with this path, which is in the root directory fo the *Working DataCrate* dataset:

    ./cp7glop.ai

For a *Bagged DataCrate* the path would be ```../data/cp7glop.ai``` - the relative path from ```metadata/CATALOG.json``` to the file in the payload directory.

An example ```DataCrate-flattened JSON-LD``` for the above would be as follows:

```
"@graph": [
  {
    "@id": "data",
    "@type": [
      "Dataset"
    ],
    "hasPart": [
      {
        "@id": "./cp7glop.ai"
      }],
   }
      {
        "@id": "./cp7glop.ai",
        "@type": "MediaObject",
        "ContentSize": "383766",
        "Description": "Illustrator file for Glop Pot",
        "EncodingFormat": "Acrobat PDF 1.5 - Portable Document Format",
        "fileFormat": "http://www.nationalarchives.gov.uk/PRONOM/fmt/19"
      }
      ]
```




### Describing a directory but not files

To describe a directory without listing all the files in it, DataCrate SHOULD
have a description of the directory. This DataCrate JSON-LD, describes the
directory 'lots_of_little_files' in an *Working DataCrate*.

```
{
      "@id": "lots_of_little_files",
      "path": "./lots_of_little_files",
      "@type": "Dataset",
      "Description": "This directory contains many small files, that we’re not going to describe in detail.",
      "Name": "Too many files",

}
```

## How to represent various kinds of metadata

### Contacts, creators and contributors: people

A core principle of Linked data is to use URIs as to identify things such as
people.  The following is the minimum recommended way of representing a
[creator] in a DataCrate. This property MAY be applied in the context of
a directory ([Dataset]) or to a file ([MediaObject]).

{
      "@type": "Dataset",
      "@id": "some_id",
      "Creator": {"@id": "http://orcid.org/0000-0002-8367-6908"}

}

This uses an ORCID, to unambiguously identify an author and could be used as is. However, it does not
provide very much information about the author. The ```DataCrate-flattened JSON-LD``` @graph SHOULD contain self-contained information about *Metadata Entities*.

{
    "@id": "http://orcid.org/0000-0002-8367-6908",
    "@type": "Person",
    "Affiliation": "University of Technology Sydney",
    "Name": "J. Xuan"
  }

Note the string-value of the organizational affiliation. This SHOULD be improved by
also providing a *Metadata Entity* for the organization.

### Organizations as values

An [organization] SHOULD be the value for the [publisher] property
of a [Dataset] or [ScholarlyArticle] or [affiliation]
property of a [Person].

```
{
      "@type": "Dataset",
      "@id": "some_id",   
      "Publisher": {"@id": "http://uts.edu.au"}
}

{
  "@id": "http://uts.edu.au",
  "@type": "Organization",
  "Name": "University of Technology Sydney"
}
```


A [organization] SHOULD also be used for [Person]'s affiliation. For example
the contact person:

```
{
      "@type": "Dataset",
      "@id": "some_id",   
      "Publisher": {"@id": "http://uts.edu.au"}
      "Contact": , {"@id": "http://orcid.org/0000-0002-3545-944X"}
},
{
  "@id": "http://uts.edu.au",
  "@type": "Organization",
  "Name": "University of Technology Sydney"
},
{
       "@id": "http://orcid.org/0000-0002-3545-944X",
       "@type": "Person",
       "Affiliation": {"@id": "http://uts.edu.au"},
       "Email": "peter.sefton@uts.edu.au",
       "Name": "Peter Sefton"
     }
```


### Publications

To associate a publication with a dataset the JSON-LD MUST include a URL
(preferably a DOI) as the ID of a publication.

For example:
```
"Related": {"@id": "http://dx.doi.org/10.1109/TCYB.2014.2386282"}
```

The publication SHOULD be described in the *DATACRATE*.

```    
{
  "@id": "http://dx.doi.org/10.1109/TCYB.2014.2386282",
  "@type": "ScholarlyArticle",
  "creator": [
    {
      "@id": "http://orcid.org/0000-0002-8367-6908"
    },
    {
      "@id": "http://orcid.org/0000-0003-0690-4732"
    },
    {
      "@id": "http://orcid.org/0000-0003-3960-0583"
    },
    {
      "@id": "https://orcid.org/0000-0002-6953-3986"
    }
  ],
  "identifier": "http://dx.doi.org/10.1109/TCYB.2014.2386282",
  "issn": "2168-2267",
  "name": "Topic Model for Graph Mining",
  "journal": "IEEE Transactions on Cybernetics"
}
```

### Publisher

The root [Dataset] in  *Bagged DataCrate* must have a [publisher] property. This should be a an  [Organization].

```
{
  "@id": "http://dx.doi.org/10.5281/zenodo.1009240",
  "@type": "Dataset",
   <...>
  "name": "Sample dataset for DataCrate v0.2",
  "publisher": {
    "@id": "http://uts.edu.au"
  },
  "temporalCoverage": "2017"
},

{
  "@id": "http://uts.edu.au",
  "@type": "Organisation",
  "identifier": "http://uts.edu.au",
  "name": "University of Technology Sydney"
},
```

### Funding and grants

To associate a research project (with or without funding) to a [Dataset], use
the [outputOf] property referencing an instance of [frapo:Project]. This example
shows a [Dataset] which has an [outputOf] property referencing a project which in turn
has the the same [Organisation] referenced by [funder] and [publisher]
properties.

```
{
  "@id": "http://dx.doi.org/10.5281/zenodo.1009240",
  "@type": "Dataset",
  "outputOf": {
    "@id": "https://github.com/UTS-eResearch/datacrate"
  },
  ...
},

{
  "@id": "http://eresearch.uts.edu.au/projects/provisioner",
  "@type": "Project ",
  "description": "The University of Technology Sydney Provisioner project is ...",
  "funder": [
    {
      "@id": "http://uts.edu.au"
    },
    {
      "@id": "http://ands.org.au"
    }
  ],
  "identifier": "http://eresearch.uts.edu.au/projects/provisioner",
  "name": "Provisioner"
},

{
  "@id": "http://uts.edu.au",
  "@type": "Organisation",
  "identifier": "http://uts.edu.au",
  "name": "University of Technology Sydney"
},

{
  "@id": "http://ands.org.au",
  "@type": "Organization",
  "description": "The core purpose of the Australian National Data Service (ANDS) is ...",
  "identifier": "http://ands.org.au",
  "name": "Australian National Data Service"
},

```


### Date of publication
Use the [datePublished] property with a date in ISO 8601 date format.
{
  "@id": "http://dx.doi.org/10.5281/zenodo.1009240",
  "@type": "Dataset",
  "datePublished": "2017-06-29",
},

### Licensing and copyright

If a *Data Entity* has a license the entity SHOULD have a [license] property
with a value of CreativeWork that describes the license.  The ID of the license
should be its URL (eg a Creative Commons License URL) and a summary of the
license included in the DataCrate. If this is not possible a URL MAY be used as
the value.

To note the copyright holder of a Data Item, use the [copyrightHolder]

The [copyrightHolder] property references A
type [creativeWork], which describes the license.

This Data Item has a copyright holder which is different from its creator. There
is a reference to an [Organization] describing the copyright holder.

TODO: Example from Cameron's dataset


### Equipment

To specify which equipment was used to create or contribute to a *Data Entity*,
it SHOULD have a TODO [contributor] property, with a value of a microdocument of
type [Equipment].  This example shows how to associate equipment  with a file
"data/wcc02_arch_traj2.ply":

TODO: Example from victoria arch.


### Places

To associate a *Data Entity* with a MetaData Entity representing a *geographical
location or region*  the entity SHOULD have
a property of [contentLocation] with a value of type [Place].

This example shows how to define a place, using a [geonames] ID:
```
{
  "@id": "http://www.geonames.org/8152662/catalina-park.html",
  "@type": "Place",
  "description": "Catalina Park is a disused motor racing venue, located at Katoomba ...",
  "geo": {
    "@id": "b4168a98-8534-4c6d-a568-64a55157b656"
  },
  "identifier": "http://www.geonames.org/8152662/catalina-park.html",
  "name": "Catalina Park"
},
```
The place has a [geo] property, referencing a [GeoCoordinates] item:

```
{
  "@id": "b4168a98-8534-4c6d-a568-64a55157b656",
  "@type": "GeoCoordinates",
  "latitude": "-33.7152",
  "longitude": "150.30119"
},
```

And the place is referenced from the [contentLocation] property of the dataset.
```
{
  "@id": "http://dx.doi.org/10.5281/zenodo.1009240",
  "@type": "Dataset",
  "outputOf": "DataCrate",
  "contact": {
    "@id": "http://orcid.org/0000-0002-3545-944X"
  },
  "contentLocation": {
    "@id": "http://www.geonames.org/8152662/catalina-park.html"
  },
```


[Place] MAY use any of the resources available in http://schema.org/geo to
describe places. Future profiles of DataCrate may mandate the use of a subset of
these. Any directory or file or *Metadata Entity* may be geo-located. For example, this file:

```
{
  "@id": "pics/19093074_10155469333581584_5707039334816454031_o.jpg",
  "@type": "File",
  "contentLocation": {
    "@id": "http://www.geonames.org/8152662/catalina-park.html"
  },
  "contentSize": "132765",
  "path": "pics/19093074_10155469333581584_5707039334816454031_o.jpg",
  "creator": {
    "@id": "http://orcid.org/0000-0002-3545-944X"
  },
  ```


### Time

To describe the time period which a DataCrate Data Entity is *about*, use [temporalCoverage].


### Subjects & keywords

Subject properties (equivalent to a Dublin Core Subject) on DataCrate
MicroDocuments  MUST use the [about] property.

Keyword properties MUST use [Keyword].

## Minimum metadata summary for *Bagged DataCrates*

A *Bagged DataCrate* MUST have a root [Dataset] with:
 *  a [description] property, which describes the whole dataset contained
    in the data (BagIt payload) directory.

 *  A creation date in ISO data format for the dataset using
    [dataModified] which is the last date that the *payload*, *not* the
    metadata was changed.

 *  A contact person (or role) expressed via a property schema:accountablePerson
    on the [Dataset] of with a value of type [Person], with  at
    least one of the following properties.
    *  [email] (text)
    *  [phone] (text)
    *  [affiliation] - with a text value or reference to an [Organization].

### BagIt metadata

BagIt metadata SHOULD be included in bag-info.txt where it is available as
follows in the Dataset object at the root of '@graph' in *DataCrate-flattened JSON-LD*.

The following metadata SHOULD be extracted from the *DataCrate-flattened JSON* in
the base directory of the bag.

```
Source-Organization:   ['Publisher']['Name']
Organization-Address:  ['Contact']['Publisher']['Name']
Contact-Name: ['Contact']['Name']
Contact-Phone: ['Contact']['Phone']
Contact-Email:  ['Contact']['Email']
External-Description: ['Description']
Bagging-Date:  To be entered by the software tool used to create the DataCrate
External-Identifier:  ['ID'] (if the ID is an http or https URL)
Bag-Size:  To be entered by the software tool used to create the DataCrate
Payload-Oxum:  TTo be entered by the software tool used to create the DataCrate

Bag-Group-Identifier: N/A
Bag-Count:  N/A
Internal-Sender-Identifier: N/A
Internal-Sender-Description: N/A
```

### Recommended IDs

Users of DataCrate SHOULD use the following IDs where possible:
*  For a DataCrate, a [identifier] property which SHOULD be a DOI URL.
*  For People participating the research projects: ORCID identifiers.
*  For organizations including funders, pending a global identifier scheme for
   use the homepage URL for research organizations.
*  For items of type schema:Place: a geonames URL.
*  For file formats; Pronom URLs, for example http://www.nationalarchives.gov.uk/PRONOM/fmt/831.

In the absence of the above, DataCrates SHOULD contain stable persistent URIs to
identify all entities wherever possible.

## index.html

*DataCrate index.html*  MUST contain the same information as CATALOG.json,
organized as described below, with the exception that files that do not have a
description, creator or other metadata that cannot be derived automatically MAY
not be listed.

*DataCrate index.html* must be a static pre-compiled HTML file that will display
without scripting. It MAY contain extra features enabled by Javascript.

(TODO: will publish a sample implementation)

## To generate an index HTML

The following is pseudo code for how to generate the file:

TODO: not sure if this is a good approach - working on some javascript code in
calcyteJS as an example.

```
Set the page <title> to "DataCrate: $name-of-crate"

function show_dataset_metadata():

  Generate a table with an id <table id='dataset[@id]'>
  Add table to a Table of contents
      For each property of the Dataset:
         Generate a row <tr> with:
             <th>$property-name</th>
             if $property-value is a scalar:
                <td>$property-value<td>
             else:
                 link to property (eg a creator)

    Show a table of files that are in an hasPart relationship to the dataset with all the metadata.

   For each child Dataset (eg a directory) call show_data_set_metadata()

   For each @type (other than Dataset and mediaObject):
      Create a table listing all the metadata for each type.



```


## Datacite citations

In a *Bagged DataCrate*, if there is sufficient metadata in ```CATALOG.json```, it SHOULD contain a DataCite
citation, datacite.xml in the ```/metadata``` relative to the root of the bag, compliant with the
[DataCite Schema v4.0].

To generate DataCite.xml a DataCrate MUST have the following at the
schema:Dataset level:

*  A [identifier] for the DataCrate that is a DOI URL.

*  At least one [creator] with either a [name] or a
   [givenName] *and* [familyName].

*  At least one [name] (which maps to a DataCite title).

*  A least one [publisher] property which SHOULD be an organization but
   may be a String value.

The mandatory DataCite resource type MUST be set to "DataCrate-v0.1".

## Extending DataCrate

To extend DataCrate implementers SHOULD try to use valid schema.org
properties and classes and MAY use terms form other widely-used and supported
vocabularies and ontologies when this is not possible.

[BagIt]: https://en.wikipedia.org/wiki/BagIt
[samples]: ./samples
[DataCite Schema v4.0]: https://schema.datacite.org/meta/kernel-4.0/metadata.xsd
[BagIt profile]: https://github.com/ruebot/bagit-profiles
[CURIE]: https://www.w3.org/TR/curie/
[keyword]: https://schema.org/keyword
[about]: https://schema.org/about
[name]: https://schema.org/name
[creator]: https://schema.org/creator
[Person]: https://schema.org/Person
[identifier]: https://schema.org/identifier
[relatedItem]: https://schema.org/relatedItem
[Organization]: https://schema.org/Organization
[Dataset]: https://schema.org/Dataset
[MediaObject]: https://schema.org/MediaObject
[CreativeWork]: https://schema.org/CreativeWork
[hasPart]: https://schema.org/hasPart
[funder]: https://schema.org/funder
[encodingFormat]: https://schema.org/encodingFormat
[accountablePerson]: https://schema.org/accountablePerson
[datePublished]: https://schema.org/datePublished
[license]: https://schema.org/license
[accountablePerson]: https://schema.org/accountablePerson
[contributor]: https://schema.org/contributor
[contentLocation]: https://schema.org/contentLocation
[copyrightHolder]: https://schema.org/copyrightHolder
[Place]: https://schema.org/Place
[description]: https://schema.org/description
[email]: https://schema.org/email
[phone]: https://schema.org/phone
[affiliation]: https://schema.org/affiliation
[givenName]: htts://schema.org/givenName
[familyName]: https://schema.org/familyName
[publisher]: https://schema.org/publisher
[translator]: https://schema.org/translator
[translationOf]: https://schema.org/translationOf

[DataCrate BagIt Profile]: https://raw.githubusercontent.com/UTS-eResearch/datacrate/develop/spec/0.1/profile-datacrate-v0.1.json
[DataCrate JSON-LD Context]: ./context.json
[JSON-LD Framing 1.1]: https://json-ld.org/spec/latest/json-ld-framing/
[DataCrate JSON-LD frame]: ./frame.json
[geonames]: http://www.geonames.org
[RDFa]: https://en.wikipedia.org/wiki/RDFa
[schema.org]: http://schema.org
[frapo:Project]: http://vivoweb.org/ontology/core#Project
[vivo:Equipment]: http://vivoweb.org/ontology/core#Equipment
[DCAT]: https://www.w3.org/TR/vocab-dcat/
