# DataCrate Specification version 0.2.1

This is a draft work in progress, any and all details of this spec could change,
including its very existence.

As this spec is still under development, v0.2 will be updated and committed to
github here: https://github.com/UTS-eResearch/datacrate/tree/master/spec/0.2.
The version number above will be incremented if there are breaking changes from
0.2.1, edits for clarity and more detail will not be considered a new version.

Please give feeback via github issues or a pull request.

The samples and [examples](#Examples) here have not been exhaustively verified, and there may
be some inconsistencies between this spec and the associated files, such as the
[DataCrate JSON-LD Context]. Where there *is* a discrepancy, the file is the
authority rather than the spec (this doc).

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


## Introduction & definition of a *DataCrate*

This document specifies a method of organising file-based data, known as
*DataCrate*,  with associated metadata, in both human and machine readable
formats, based on the [schema.org] linked-data vocabularly, supplemented with
terms from the [SPAR] ontologies and [PCDM] where schema.org does not have
coverage.

The aim is to provide researchers with a way of distributing self-describing
packages of research data with standards based metadata at the package and the
file level.

A *DataCrate* is a dataset a set of files contained in a single directory. There
are two ways of organizing a *DataCrate*.

1. For working data or data that does not need to be distributed with checksums,
   a *Working DataCrate* is a plain-old directory containing payload data files,
   with two metadata files at the root; one for humans (```index.html```) and
   one for machines (```CATALOG.json```).

2. For distribution, or archiving; where integrity is important, a *Bagged
   DataCrate* is a [BagIt] bag conforming to the [DataCrate BagIt profile] with
   the payload files in the ```/data``` directory. A *Bagged DataCrate* has a
   clear separation between metadata and payload, and can be integrity-checked
   using the checksums in the [BagIt] manifest.

If a *Bagged DataCrate* has sufficient metadata then it can be distributed as a
*Citable DataCrate*, with a [DataCite] citation. See below.

About the contents of a datacrate:

*  Both kinds of *DataCrate* have a human-readable ```index.html``` file which
   describes the files and directories in dataset.  The ```index.html``` has
   metadata about the [Dataset] as a whole, and MAY have information about
   individual files and directories, such as their title, license, authorship or
   other provenance information. In both kinds of *DataCrate* ```index.html```
   is in the root of the *DataCrate*.

*  A *DataCrate* has a ```CATALOG.json``` with a JSON-LD version of
   ```index.html```, the metadata in this file is [Flattened Document Form] for
   ease of processing. In a *Working DataCrate*. In both kinds of *DataCrate*
   this file is the root.

*  If there is enough metadata, and the dataset has a DOI, a *Bagged DataCrate*
   may be  distributed as a *Citable DataCrate* which has a DataCite metadata
   file in ```/metadata/datacite.xml``` and a human-readable citation
   in ```index.html```. Working datacrates do not have a ```datacite.xml``` file.

This specification is a practical guide for software authors to create tools for
generating and consuming research data packages. The JSON-LD format is not
optimized for hand-authoring, it is optimized for maximum convenience for data
consumers with the use of HTML for users to read and  JSON-LD for programmers
and search-engines.

The DataCrate specification aims to ensure that packages are as self documenting
as possible:

-  Anyone with a Data Crate zip file can unzip it, notice that there is an
   ```index.html``` file in the base directory, open that in a web browser, and
   find out about the data; where it came from, who to contact about it, if it
   has a homepage and so on, which is more usable than simple zipping or bagging
   of files or using XML-based metadata formats. DataCrates can also be hosted
   on web-servers with minimal effort as the index.html file describes the
   contents.

-  Repository managers can put *DataCrates* on the web with the ```index.html``
   file giving a detailed peek inside the dataset. (This spec does not deal with
   showing previews or thumbnails of content but a future version will).

In an academic context, it is important to be able to associate data with funded
research projects and publications, and desirable to be able to describe the
*contents* of a data package, not just the data in aggregate, so DataCrate
allows for file-level descriptions; to describe file format and extent,
licensing, copyright ownership and authorship and other metadata at a granular
level.

Because DataCrates could be created at many different stages in the research
data lifecycle there is a very minimal mandated minimum set of metadata. There
are no metadata requirements at all for a *Working DataCreate*.

For a *Bagged DataCrate* the mandatory metadata is:
*  A creation date,
*  Contact details and
*  A human-readable description of the data.

The requirements for a *Citable DataCrate*, which is a subtype of *Bagged
DataCrate* with a [DataCite] citation, are described below.

Future versions of this specification may allow for profiles to be specified
that are appropriate to a range of services such as repositories, registries.
and research software applications.


## Examples

There are examples of DataCrates in the [samples] directory.

Examples hosted on the web, as static mini-repositories with a download link:

*  [Sample](https://data.research.uts.edu.au/examples/v0.2/sample/). A slightly silly simple sample datacrate.

*  [Glop Pot](https://data.research.uts.edu.au/examples/v0.2/Glop_Pot/). Cave exploration data.

*  [GTM](https://data.research.uts.edu.au/examples/v0.2/GTM/). Some MATLAB code that supports a publication.

*  [Farms to
   Freeways](https://data.research.uts.edu.au/examples/v0.2/farms_to_freeways/).
   A dataset exported from an Omeka Classic repository [using some Python scripts](https://github.com/UTS-eResearch/omeka-datacrate-tools).

*  [Victoria Arch](https://data.research.uts.edu.au/public/Victoria_Arch/) more cave
   data collected by a drone with a 3d lidar scanner, with video.

*  [Dataset for IDRC Project: Exploring the opportunities and challenges of implementing open research strategies within development institutions. International Development Research Center](https://data.research.uts.edu.au/examples/v0.2/Data_Package-IDRC_Opportunities_and_Challenges_Open_Research_Strategies/): This dataset was the subject of [a presentation](http://ptsefton.com/2017/10/19/datacrate.htm) at eResearch Australasia 2017. The published version used DataCrate v0.1 the link here is to an updated version of the dataset.


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

Throughout this specification, RDF terms are be referred to using the keys
defined in  [DataCrate JSON-LD Context]. Following [schema.org] practice property
names start with lowercase letters and class names with uppercase.

In ```CATALOG.json``` RDF terms use their DataCrate JSON-LD names
as defined in the [DataCrate JSON-LD context].

In examples, paths for a *Working DataCrate* are used. That is, the [path] of a
file is relative to a ```CATALOG.json``` document in the root of the DataCrate
without a [BagIt] ```/data``` directory.

## Core metadata standard for DataCrate: Schema.org

[Schema.org] is the base metadata standard for DataCrate with a few additions to
supplement gaps in schema.org's ability to represent dataset metadata.
Schema.org was chosen because it is widely used on the World Wide Web and
supported by search engines, on the assumption that discovery is likely to be
maximised if commercial  search engines index the content.  NOTE: As far as we
know there are no alternative existing well-maintained linked-data schema for
research data with the coverage needed for this project - ie a single standard
for expressing all the examples presented in this spec.

Future versions of this specification may be based on other metadata standards,
as far as possible this will be done by swapping in a new *DataCrate Context*,
leaving the keys as described here the same.

Additional metadata MAY be drawn from any RDF vocabulary to allow open-ended
extensibility.

Generally, the standard keys for Schema.org should be used, however there are a
few keys which are defined differently than the standard, to allow for new terms
to be defined in [schema.org] or for them to be mapped to other vocabularies.

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

* If there is enough metadata, a DataCite citation for the crate so it can be
  made into a *Bagged DataCrate* which is also a *Citable DataCrate*.

## Note on compromises / limitations of schema.org

One crucial *MetaData Entity* is a research project. Schema.org has no class for
a project, although it does have a [funder] property. DataCrate
maps the key [Project] to [frapo:Project] (from the [SPAR] ontologies
[FRAPO] ontology), which in turn MAY have a [funder] property.

Likewise, schema.org does not have a way to represent research equipment so
DataCrate uses the [frapo:Equipment] class.

A [Dataset] has no contactPoint property, unlike [DCAT], so The *DataCrate
JSON-LD Context* maps the key [contact] to schema:accountablePerson; the concept
is similar to "Corresponding Author" in academic publishing.


# Structure / DataCrate by example

This section describes the DataCrate specification starting from the BagIt
structure on which it is built, with examples. It contains some normative
statements (what a DataCrate SHOULD, MUST and MAY have).

The Bagit file-system structure for a *Bagged DataCrate* is as follows.

```

           <base directory>/
           |   bagit.txt
           |   bag-info.txt (with BagIt-Profile-Identifier: matching this specification)
           |   manifest-<algorithm>.txt
           |   index.html
           |   CATALOG.json # TODO work out how to link between these
           \--- metadata
               |   [optional datacite.xml] # Aligned with RDA working group
               |                           # draft packaging spec
               |   [optional additional tag files]
           \--- data/
                 |   [payload files]
           \--- [optional tag directories]/
                 |   [optional tag files]

```

A *Working DataCrate* has this structure:

```
   <base directory>/
   |   index.html
   |   CATALOG.json
   |   [payload files]
```

[Part 2 of the BagIt profile specification]() says:
> Bags complying to a BagIt profile MUST contain the tag BagIt-Profile-Identifier
> in their bag-info.txt, which value is the URI of the JSON file containing the
> profile it conform to. This tag MAY be repeated if the bag conforms to multiple
> profiles. The URI that identifies the profile SHOULD be versioned, e.g.
> http://example.com/bagitprofiles/profile-bar-v0.1.json.  Resolving the URI with
> this specification.


The bag-info.txt for version 0.2 of a *Bagged DataCrate* MUST contain the following
metadata:

```
BagIt-Profile-Identifier: https://raw.githubusercontent.com/UTS-eResearch/datacrate/develop/spec/0.2/profile-datacrate-v0.2.json
DataCrate-Specification-Identifier: https://github.com/UTS-eResearch/datacrate/blob/develop/spec/0.2/data_crate_specification_v0.2.md
```

## ```index.html``` and ```CATALOG.json```

-  ```CATALOG.json``` MUST be a *DataCrate-flattened JSON-LD* document.

-  ```index.html``` MUST be an HTML 5 document containing a human readable summary of the contents of ```CATALOG.json```.


## About ```CATALOG.json```

The following *DataCrate-flattened JSON-LD* represents a minimal description of
a *Working DataCrate* dataset.  The [DataCrate JSON-LD Context] MUST be included inline as below.

Any schema.org context MAY be included and unused parts of the schema MAY be omitted. Schema.org elements SHOULD be used as defined in the standard Schema.org context. However, this standard does use variant names for two elements.

*  [File] is mapped to [schema:MediaObject] which was chosen as a compromise as
   it has many of the properties that are needed to describe a generic file. Future version of schema.org or a research data extension may define [File].

*  [path] is mapped to [schema:contentURL]. (TODO maybe change this?)

(From now on the ```@context``` will be omitted from examples.)

The ```@context``` MUST be provided in ```CATALOG.json```. Keys which are not
used MAY be ommitted.

```
{"@context" :
    {
    "name": "schema:name",
    "givenName":  "schema:givenName",
    "familyName": "schema:familyName",
    "description":  "schema:description",
    "File": "schema:MediaObject",
    "path": "schema:contentUrl",
    "contentSize": "schema:contentSize",
    "copyright": "schema:copyrightHolder",
    "publisher": "schema:publisher",
    "journal": "schema:periodical",
    "issn": "schema:issn",
    "datePublished": "schema:datePublished",
    "identifier": "schema:identifier",
    "license": "schema:license",
    "creator": "schema:creator",
    "contributor": "schema:contributor",
    "related": "schema:relatedLink",
    "sameAs":  "schema:sameAs",
    "basedOn": "schema:isBasedOn",
    "translator": "schema:translator",
    "translationOf": "schema:translationOf",
    "funder": "schema:funder",
    "Person": "schema:Person",
    "contact": "schema:accountablePerson",
    "email": "schema:email",
    "phone": "schema:telephone",
    "Dataset": "schema:Dataset",
    "fileFormat": "schema:fileFormat",
    "encodingFormat": "schema:encodingFormat",
    "temporalCoverage": "schema:temporalCoverage",
    "spatialCoverage": "schema:spatialCoverage",
    "contentLocation": "schema:contentLocation",
    "keywords": "schema:keywords",
    "subject": "schema:subject",
    "givenName":  "schema:givenName",
    "familyName": "schema:familyName",
    "Place": "schema:Place",
    "Organization": "schema:Organization",
    "memberOf": "schema:memberOf",
    "member": "schema:hasMember",
    "Project": "frapo:Project",
    "affiliation": "schema:affiliation",
    "Funder": "schema:Funder",
    "GeoShape": "schema:GeoShape",
    "GeoCoordinates": "schema:GeoCoordinates",
    "geo": "schema:geo",
    "latitude": "schema:latitude",
    "longitude": "schema:longitude",
    "Box": "schema:Box",
    "CreativeWork": "schema:CreativeWork",
    "MediaObject": "schema:MediaObject",
    "Project": "frapo:Project",
    "isOutputOf": "frap:isOutputOf",
    "Equipment": "frapo:Equipment",
    "ScholarlyArticle": "schema:ScholarlyArticle",
    "SoftwareApplication": "schema:SoftwareApplication",

    "Project": "frapo:Project",
    "isOutputOf": "frapo:isOutputOf",
    "Equipment": "frapo:Equipment",
    "interviewee": "bibo:interviewee",
    "interviewer": "bibo:interviewer",
    "hasFile": "pcdm:hasFile",
    "hasMember": "pcdm:hasMember",
    "RepositoryCollection": "pcdm:Collection",
    "RepositoryObject": "pcdm:object",
    "pcdm": "http://pcdm.org/models#",
    "bibo": "http://purl.org/ontology/bibo/",
    "cc": "http://creativecommons.org/ns#",
    "dct": "http://purl.org/dc/terms/",
    "foaf": "http://xmlns.com/foaf/0.1/",
    "rdf": "http://www.w3.org/1999/02/22-rdf-syntax-ns#",
    "rdfa": "http://www.w3.org/ns/rdfa#",
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "schema": "http://schema.org/",
    "frapo": "http://purl.org/cerif/frapo/"
  }
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


If there are files in the payload directory (which is either ```/data``` for a
*Bagged DataCrate* or ```/``` for a *Working DataCrate*) each file MAY be
described by a *Dataset* related to the schema:Dataset with a property
*HasPart*.

NOTE: As per the BagIt standard, every file in the payload directory of a
*Bagged DataCrate*  MUST be in the BagIt Manifest.  Describing every file in the
payload directory is possible with DataCrate, but is not mandatory.  For
use-cases where it is important to describe all files, future versions of this
specification MAY allow for profiles, using which it will be possible to specify
constraints such as "every file must have a [description]".

To describe a file we will use this example. Take a file with this path, which
is in the root directory fo the *Working DataCrate* dataset:

    ./cp7glop.ai

For a *Bagged DataCrate* the path would be ```data/cp7glop.ai``` - the relative
path from ```/CATALOG.json``` to the file in the payload directory.

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
        "contentSize": "383766",
        "description": "Illustrator file for Glop Pot",
        "encodingFormat": "Acrobat PDF 1.5 - Portable Document Format",
        "fileFormat": "http://www.nationalarchives.gov.uk/PRONOM/fmt/19"
      }
      ]
```



### Describing a directory but not files

To describe a directory without listing all the files in it, DataCrate SHOULD
have a description of the directory using an entity of ``@type`` [Dataset]. This
DataCrate JSON-LD, describes the directory 'lots_of_little_files' in an *Working
DataCrate*. To make it clear that the files are not listed in ```CATALOG.json```
this example shows and empty list for [hasPart].

```
{
      "@id": "lots_of_little_files",
      "path": "./lots_of_little_files",
      "@type": "Dataset",
      "hasPart": [],
      "description": "This directory contains many small files, that we’re not going to describe in detail.",
      "name": "Too many files",

}
```

## How to represent various kinds of metadata

### Contacts, creators and contributors: people

A core principle of Linked data is to use URIs as to identify things such as
people.  The following is the minimum recommended way of representing a
[creator] in a DataCrate. This property MAY be applied in the context of
a directory ([Dataset]) or to a [File].
```

{
      "@type": "Dataset",
      "@id": "some_id",
      "Creator": {"@id": "http://orcid.org/0000-0002-8367-6908"}

}

```

This uses an ORCID, to unambiguously identify an author and could be used as is.
However, it does not provide very much information about the author and it is
not possible to use this informtion to reliably construct a [DataCite] citation.
The ```DataCrate-flattened JSON-LD``` @graph SHOULD contain additional
information about *Metadata Entities* for the use of both humans (once it has
been rendered as ```index.html```) and machines.

```

{
    "@id": "http://orcid.org/0000-0002-8367-6908",
    "@type": "Person",
    "affiliation": "University of Technology Sydney",
    "name": "J. Xuan"
  }
```

Note the string-value of the organizational affiliation. This SHOULD be improved
by also providing a *Metadata Entity* for the organization.

### Organizations as values

An [organization] SHOULD be the value for the [publisher] property
of a [Dataset] or [ScholarlyArticle] or [affiliation]
property of a [Person].

```
{
      "@type": "Dataset",
      "@id": "some_id",
      "publisher": {"@id": "http://uts.edu.au"}
}

{
  "@id": "http://uts.edu.au",
  "@type": "Organization",
  "name": "University of Technology Sydney"
}
```


A [organization] SHOULD also be used for [Person]'s affiliation. For example
the contact person:

```
{
      "@type": "Dataset",
      "@id": "some_id",
      "publisher": {"@id": "http://uts.edu.au"}
      "contact": , {"@id": "http://orcid.org/0000-0002-3545-944X"}
},
{
  "@id": "http://uts.edu.au",
  "@type": "Organization",
  "name": "University of Technology Sydney"
},
{
       "@id": "http://orcid.org/0000-0002-3545-944X",
       "@type": "Person",
       "affiliation": {"@id": "http://uts.edu.au"},
       "email": "peter.sefton@uts.edu.au",
       "name": "Peter Sefton"
     }
```

Sometimes researchers want to affiliate with a sub-part of an instition, such as
institute, faculty department, or research group.

Here's a (real) example of a researcher who has four institutional affiliations
for a published article and data set:

```
{
  "@id": "http://doi.org/10.4225/59/59672c09f4a4b",
  "@type": "Dataset",
  "contact": {
    "@id": "http://orcid.org/0000-0001-6121-5409"
  },
  "path": "./",
  "creator": [
    {
      "@id": "http://orcid.org/0000-0002-6756-6119"
    },
    ...

}

{
  "@id": "http://orcid.org/0000-0002-6756-6119",
  "@type": "Person",
  "affiliation": [
    {
      "@id": "1"
    },
    {
      "@id": "2"
    },
    {
      "@id": "3"
    },
    {
      "@id": "4"
    }
  ],
  "name": "Meera Agar"
},

```

The first affiliation is a Faculty of a university. The Faculty is associated
with the university via [memberOf].

```
{
  "@id": "1",
  "@type": "Organization",
  "memberOf": {
    "@id": "http://uts.edu.au"
  },
  "name": "Faculty of Health, University of Technology Sydney"
},

{
  "@id": "http://uts.edu.au",
  "@type": "Organization",
  "name": "University of Technology Sydney"
},

```

Thus *DataCrate Flattened JSON-LD* MAY express chained affiliations with more
precision than by using string-literals as values for [affiliation].


### Publications

To associate a publication with a dataset the *DataCreate Flattened JSON-LD* MUST include a URL
(preferably a DOI) as the ID of a publication using the [related] or
[isOutputOf] property.

For example:
```
"related": {"@id": "https://doi.org/10.1109/TCYB.2014.2386282"}
```

The publication SHOULD be described in the *DataCrate Flattened JSON-LD*.

```
{
  "@id": "https://doi.org/10.1109/TCYB.2014.2386282",
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
  "identifier": "https://doi.org/10.1109/TCYB.2014.2386282",
  "issn": "2168-2267",
  "name": "Topic Model for Graph Mining",
  "journal": "IEEE Transactions on Cybernetics",
  "datePublished": "2015"
}
```

### Publisher

The root [Dataset] in  *Bagged DataCrate* must have a [publisher] property. This
should be a an [Organization] though it MAY be a string-literal or a URI.

```
{
  "@id": "https://doi.org/10.5281/zenodo.1009240",
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
the ([FRAPO]) [isOutputOf] property referencing an instance of [Project]. This example
shows a [Dataset] which has an [isOutputOf] property referencing a project which in turn
has the the same [Organisation] referenced by [funder] and [publisher]
properties. The [isOutputOf] property MAY also be used to associate a dataset with a publication.

```
{
  "@id": "https://doi.org/10.5281/zenodo.1009240",
  "@type": "Dataset",
  "isOutputOf": {
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

Use the [datePublished] property with a date in ISO 8601 date format to at least the precision of a day.

```
{
  "@id": "https://doi.org/10.5281/zenodo.1009240",
  "@type": "Dataset",
  "datePublished": "2017-06-29",
},
```

### Licensing and copyright

If a *Data Entity* has a license the entity SHOULD have a [license] property
with a value of [CreativeWork] that describes the license.  The ID of the license
should be its URL (eg a Creative Commons License URL) and a summary of the
license included in the DataCrate. If this is not possible a URL MAY be used as
the value.


This Data Item has a copyright holder which is different from its creator. There
is a reference to an [Organization] describing the copyright holder and a
[sameAs] relation to a web page.

```
{
  "@id": "SciDataCon Presentations/AAA_Pilot_Project_Abstract.html",
  "@type": "File",
  "contentSize": "17085",
  "path": "SciDataCon Presentations/AAA_Pilot_Project_Abstract.html",
  "copyrightHolder": {
    "@id": "IDRC"
  },
  "creator": {
    "@id": "http://orcid.org/0000-0002-0068-716X"
  },
  "description": "Abstract for the Pilot Project initial findings",
  "encodingFormat": "Hypertext Markup Language",
  "fileFormat": "http://www.nationalarchives.gov.uk/PRONOM/fmt/96",
  "license": {
    "@id": "https://creativecommons.org/licenses/by/4.0/"
  },
  "sameAs": "http://www.scidatacon.org/2016/sessions/56/paper/265/"
},

{
  "@id": "http://orcid.org/0000-0002-0068-716X",
  "@type": "Person",
  "identifier": "http://orcid.org/0000-0002-0068-716X",
  "name": "Cameron Neylon"
},

{
  "@id": "IDRC",
  "@type": "Organization",
  "description": "Canadian Frown Corporation and funder of development research",
  "identifier": "IDRC",
  "name": "International Development Research Center"
},

```


### Equipment

To specify which equipment or software was used to create or contribute to a
*Data Entity*, it SHOULD have a [contributor] property, with a value of type
[Equipment] or [SoftwareApplication].

NOTE: Capturig provenance about what equipment and software was used to create
files  is an area which needs to be expanded in future versions of this
specification. Other work in this area includes [ResearchObject] and the [Software Sustainability Institute's work on provenance](https://www.software.ac.uk/who-do-we-work/provenance-tool-suite)

This example shows how to associate equipment with a file.

```

{
  "@id": "https://confluence.csiro.au/display/ASL/Hovermap",
  "@type": "Equipment",
  "description": "The CSIRO bentwing is an unmanned aerial vehicles (UAV, commonly known as a drone) with a LIDAR mounted underneath to capture 3D information on the surroundings.",
  "identifier": "https://confluence.csiro.au/display/ASL/Hovermap",
  "name": "bentwing"
},

{
  "@id": "wcc04_archentrance_traj2.ply",
  "@type": "File",
  "contentSize": "547332",
  "path": "wcc04_archentrance_traj2.ply",
  "contributor": [

    {
      "@id": "https://confluence.csiro.au/display/ASL/Hovermap"
    }
  ],
  "creator": {
    "@id": "http://orcid.org/0000-0002-1672-552X"
  },
  "description": "Victoria Arch entrance, bentwing trjectory data",
  "encodingFormat": "Polygon File Format",
  "fileFormat": "http://www.nationalarchives.gov.uk/PRONOM/fmt/831",
},
```


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
  "@id": "https://doi.org/10.5281/zenodo.1009240",
  "@type": "Dataset",
  "outputOf": "DataCrate",
  "contact": {
    "@id": "http://orcid.org/0000-0002-3545-944X"
  },
  "contentLocation": {
    "@id": "http://www.geonames.org/8152662/catalina-park.html"
  },
```

[Place] MAY use any of the [resources available in Schema.org](http://schema.org/geo) to
describe places. Future profiles of DataCrate may mandate the use of a subset of
these. Any directory or file or *Metadata Entity* may be geo-located. For example this file:

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

To describe the time period which a DataCrate Data Entity is *about*, use
[temporalCoverage].


### Subjects & keywords

Subject properties (equivalent to a Dublin Core Subject) on DataCrate
MUST use the [about] property.

Keyword properties MUST use [keyword].

### Digital Library and Repository content

To describe an export from a Digital Library or repository system, use the
Portland Common Data Model ([PCDM]); a record from the library representing an
abstract entity such as a person, or a work, or a place should have a```@type```
of [RepositoryObject], in addition to any other types. Objects MAY be grouped
together in [RepostioryCollection]s with [hasMember] pointing to the the
[RepositoryObject]. The keys RepositoryObject and RepositoryCollection were
chosen to avoid collision with other similarly named properties.

NOTE: PDCM specifies that Files should have only technical metadata, not
descriptive metadata, which is *not* a restriction in DataCrate. If the
DataCrate is to be imported into a strict PCDM repository, modelling of
object/file relationships will have to be worked out.

For example, this data is exported from an [Omeka](http://omeka.org) repository:

```
{
   "@id": "http://omeka.uws.edu.au/farmstofreeways/api/collections/6",
   "@type": [
      "pcdm:Collection"
   ],
   "title": [
      "Project Materials"
   ],
   "description": [
      "Materials associated with the project, including fliers seeking participants, lists of sources and question outline.   "
   ],
   "publisher": [
      "University of Western Sydney"
   ],
   "rights": [
      "Copyright University of Western Sydney 2015"
   ],
   "pcdm:hasMember": [
      {
         "@id": "http://omeka.uws.edu.au/farmstofreeways/api/items/166"
      },
      {
         "@id": "http://omeka.uws.edu.au/farmstofreeways/api/items/167"
      },
      {
         "@id": "http://omeka.uws.edu.au/farmstofreeways/api/items/168"
      },
      {
         "@id": "http://omeka.uws.edu.au/farmstofreeways/api/items/169"
      }
   ]
},
{
   "@id": "http://omeka.uws.edu.au/farmstofreeways/api/items/166",
   "@type": [
      "pcdm:Object",
      "Text"
   ],
   "title": [
      "Western Sydney Women's Oral History Project: Flier (illustrated)"
   ],
   "description": [
      "Flier (illustrated) seeking participants for the project."
   ],
   "publisher": [
      "University of Western Sydney"
   ],
   "rights": [
      "Copyright University of Western Sydney 2015"
   ],
   "text": [
      "<div style=\"text-align:center;\">DID YOU LIVE IN PENRITH OR BLACKTOWN IN THE 1950\u2019S?<br /><br /></div>\n<div style=\"text-align:center;\">IF SO, YOU MAY BE INTERESTED<br />TO SHARE YOUR MEMORIES OF THOSE<br />TIMES!<br /><br /></div>\n<div>The Women's Research Centre at the University of Western Sydney Nepean. is currently conducting interviews for an oral history project:<br /><br /></div>\n<div style=\"text-align:center;\">\"FROM FARMS TO FREEWAYS - WOMEN'S MEMORIES<br />OF WESTERN SYDNEY\"<br /><br /></div>\n<div>If you have lived in either of these areas from the late 1940s or early 1950s through to the 1960s and would like to share your memories of this time of rapid change. please see Robyn Arrowsmith during recess.<br /><br /></div>\n<div>More information is also available from Dr. Deborah Chambers: on (02) 678 7375.</div>"
   ],
   "originalFormat": [
      "Paper"
   ],
   "identifier": [
      "FTF_flier_illust"
   ],
   "rightsHolder": [
      "Western Sydney University"
   ],
   "license": [
      "Content in the Western Sydney Women's Oral History Project: From farms to freeways collection is licensed under a Creative Commons CC BY 3.0 AU licence (https://creativecommons.org/licenses/by/3.0/au/)."
   ],
   "pcdm:hasFile": [
      {
         "@id": "./content/166/original_eece70f73bf8979c0bcfb97065948531.pdf"
      },
     ...
   ]
},
{
   "@type": "File",
   "path": "./content/166/original_eece70f73bf8979c0bcfb97065948531.pdf",
   "@id": "./content/166/original_eece70f73bf8979c0bcfb97065948531.pdf"
},
```





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
Source-Organization:   ['publisher']['name']
Organization-Address:  [contact]['publisher']['name']
Contact-Name: [contact]['name']
Contact-Phone: [contact]['phone']
Contact-Email:  [contact]['email']
External-Description: ['description']
Bagging-Date:  To be entered by the software tool used to create the DataCrate
External-Identifier:  ['@id'] (if the ID is an http or https URL)
Bag-Size:  To be entered by the software tool used to create the DataCrate
Payload-Oxum:  To be entered by the software tool used to create the DataCrate

Bag-Group-Identifier: N/A
Bag-Count:  N/A
Internal-Sender-Identifier: N/A
Internal-Sender-Description: N/A
```

### Recommended IDs

Users of DataCrate SHOULD use the following IDs where possible:
*  For a DataCrate, an ```@id``` which SHOULD be a DOI URL.
*  For People participating the research projects: ORCID identifiers.
*  For [Organization]s including [funder]s, pending a global identifier scheme for
   use the homepage URL of the organization.
*  For items of type schema:Place: a geonames URL.
*  For file formats; [Pronom] URLs, for example http://www.nationalarchives.gov.uk/PRONOM/fmt/831.

In the absence of the above, DataCrates SHOULD contain stable persistent URIs to
identify all entities wherever possible.

## index.html

*DataCrate index.html*  MUST contain the same information as ```CATALOG.json```,
organized as described below, with the exception that files that do not have a
description, creator or other metadata that cannot be derived automatically MAY
not be listed.

*DataCrate index.html* must be a static pre-compiled HTML file that will display
without scripting. It MAY contain extra features enabled by Javascript.

(TODO: will publish a sample implementation)

## To generate an index HTML

The following is pseudo code for how to generate the file:

TODO: not sure if this is a good approach - working on some javascript code in
[calcyteJS]() as an example.

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

*  An ```@id``` for the DataCrate that is a DOI URL.

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


[JSON-LD]:(https://json-ld.org/spec/latest/json-ld/)
[JSON-LD 1.1]:(https://json-ld.org/spec/latest/json-ld/)
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
[schema:Collection]: http://schema.org/Collection
[identifier]: https://schema.org/identifier
[funder]: https://schema.org/funder
[relatedItem]: https://schema.org/relatedItem
[Organization]: https://schema.org/Organization
[Dataset]: https://schema.org/Dataset
[File]: https://schema.org/MediaObject
[path]: https://schema.org/path
[schema:contentUrl]: https://schema.org/contentUrl
[schema:MediaObject]: https://schema.org/MediaObject
[ScholarlyArticle]: https://schema.org/ScholarlyArticle
[CreativeWork]: https://schema.org/CreativeWork
[SoftwareApplication]: https://schema.org/SoftwareApplication
[hasPart]: https://schema.org/hasPart
[memberOf]: https://schema.org/memberOf
[funder]: https://schema.org/funder
[encodingFormat]: https://schema.org/encodingFormat
[accountablePerson]: https://schema.org/accountablePerson
[datePublished]: https://schema.org/datePublished
[license]: https://schema.org/license
[contact]: https://schema.org/accountablePerson
[contributor]: https://schema.org/contributor
[contentLocation]: https://schema.org/contentLocation
[copyrightHolder]: https://schema.org/copyrightHolder
[Place]: https://schema.org/Place
[description]: https://schema.org/description
[geo]: https://schema.org/geo
[GeoCoordinates]: https://schema.org/GeoCoordinates
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
[DCAT]: https://www.w3.org/TR/vocab-dcat/
[DataCite]: https://data.research.uts.edu.au/public/Victoria_Arch/
[SPAR]: http://www.sparontologies.net/
[FRAPO]: http://www.sparontologies.net/ontologies/frapo
[PCDM]:
[pcdm:Collection]
[Project]: https://sparontologies.github.io/frapo/current/frapo.html#d4e2428
[frapo:Project]: https://sparontologies.github.io/frapo/current/frapo.html#d4e2428
[isOutputOf]: https://sparontologies.github.io/frapo/current/frapo.html#d4e526
[Equipment]: https://sparontologies.github.io/frapo/current/frapo.html#d4e2428

[Flattened Document Form]: https://json-ld.org/spec/latest/json-ld/#flattened-document-form
