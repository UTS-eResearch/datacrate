# DataCrate Specification version 1.0.0 rc1

This is a release candidate. The final version 1.0 release is planned for Q1 2019.

As this spec is still under development, v1.0 will be updated and committed to
github here: https://github.com/UTS-eResearch/datacrate/tree/master/spec/1.0 .
We are not using branches, just the master.
The version number above will be incremented as major changes occur to v1.0.

Please give feedback via github issues or a pull request.

## Introduction & definition of a *DataCrate*

The key words MUST, MUST NOT, REQUIRED, SHALL, SHALL NOT, SHOULD, SHOULD NOT,
RECOMMENDED, MAY, and OPTIONAL in this document are to be interpreted as
described in [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).

This document specifies a method, known as *DataCrate*, of organizing file-based
data and digital repository data with associated metadata in both human and
machine readable formats, based on the [schema.org] [linked data] vocabulary, for
core metadata and [PCDM] for representing repository collections.

The aim is to provide researchers with a way of distributing self-describing
packages of research data with standards-based metadata at the package and the
file level, with the ability to include additional domain-specific linked data
using standard linked-data practices.

A *DataCrate* is a single [Dataset]: a set of files contained in a single
directory. There are two ways of organizing a *DataCrate*:

1. For working data or data that does not need to be distributed with checksums,
   a *Working DataCrate* is a plain-old directory containing payload data files,
   with two metadata files at the root; one for humans (`CATALOG.html`) and one
   for machines (`CATALOG.json`) and a `/CATALOG_files` directory for
   additional HTML content.

2. For distribution, or archiving, where integrity is important, a *Bagged
   DataCrate* is a [BagIt] bag conforming to the [DataCrate BagIt profile] with
   the payload files in the `/data` directory. A *Bagged DataCrate* has a clear
   separation between metadata and payload, meaning that metadata can be
   updated without affecting the integrity fo the data and can be
   integrity-checked using the checksums in the [BagIt] manifest.

If a *Bagged DataCrate* has sufficient metadata then it can be distributed as a
*Citable DataCrate*, with a [DataCite] citation. See below.

About the contents of a DataCrate:

*  A *DataCrate* has a `CATALOG.json` containing JSON-LD in [Flattened Document Form]
   for ease of processing. This describes the DataCrate and its contents, using
   Schema.org for interoperable metadata and discovery, with any other metadata
   that is considered useful for potential users. In both kinds of *DataCrate*
   this file is in the root directory.

*  Both kinds of *DataCrate* have a human-readable `CATALOG.html` file which
   describes the files and directories in a dataset. The `CATALOG.html` has
   metadata about the [Dataset] as a whole, and MAY link to further pages with
   information about individual files and directories, such as their title,
   license, authorship or other provenance information and pages about context
   such as people, organizations and equipment. In both kinds of *DataCrate*
   `CATALOG.html` is in the root of the *DataCrate*.


*  If there is enough metadata, and the dataset has a DOI, a *Bagged DataCrate*
   may be distributed as a *Citable DataCrate* which has a DataCite metadata
   file in `/metadata/datacite.xml` and a human-readable citation
   in `CATALOG.html`. Working DataCrates do not have a `datacite.xml` file.

This specification is a practical guide for software authors to create tools for
generating and consuming research data packages. The JSON-LD format is not
optimized for hand-authoring, it is optimized for maximum convenience for
programmers.

The DataCrate specification aims to ensure that packages are as self documenting
as possible:

-  Anyone with a *DataCrate* zip file can unzip it, notice that there is a
   `CATALOG.html` file in the base directory, open that in a web browser, and
   find out about the data; where it came from, who to contact about it, if it
   has a homepage and so on. This is intended to be more user friendly than
   simple zipping or bagging of files or using machine readable XML, JSON or
   bespoke metadata formats.

-  Repository managers can put *DataCrates* on the web with the `CATALOG.html`
   file giving a detailed 'peek' inside the dataset's contents and context. 

-  In a *Bagged DataCrate* the metadata files `CATALOG.html` and `CATALOG.json`
   are not in the BagIt payload directory (`/data`), and hence can be changed
   (for example to update to a new version of DataCrate) without changing the
   data payload and its checksums.

In an academic context, it is important to be able to associate data with funded
research projects and publications, and desirable to be able to describe the
*contents* of a data package, not just the data in aggregate, so DataCrate
allows for file-level descriptions; to describe file format and extent,
licensing, copyright ownership and authorship and other metadata and contextual
information at a granular level.

Because DataCrates could be created at many different stages in the research
data lifecycle there is a very minimal mandated minimum set of metadata. There
are no metadata requirements at all for a *Working DataCreate*.

For a *Bagged DataCrate* the mandatory metadata is:
* a creation date,
* contact details and
* a human-readable description of the data.

The requirements for a *Citable DataCrate*, which is a subtype of *Bagged
DataCrate* with a [DataCite] citation, are described below.

Future versions of this specification may allow for profiles to be specified
that are appropriate to a range of services such as repositories, registries.
and research software applications.


### Examples

The following examples are hosted on the web, as static mini-sites with a download link:

* [Sample](https://data.research.uts.edu.au/examples/v1.0/sample/): A slightly silly, simple sample datacrate.

* [Glop Pot](https://data.research.uts.edu.au/examples/v1.0/Glop_Pot/): Cave exploration data.

* [Facilitated family case conferencing]( https://data.research.uts.edu.au/examples/v1.0/luckett/): Some small data sets from a clinical trial, this example has very detailed affiliation data for the researchers.

* [GTM](https://data.research.uts.edu.au/examples/v1.0/GTM/): Some MATLAB code that supports a publication.

* [Farms to Freeways](https://data.research.uts.edu.au/examples/v1.0/farms_to_freeways/):
  A dataset exported from an Omeka Classic repository [using some Python scripts](https://github.com/UTS-eResearch/omeka-datacrate-tools).

* [Victoria Arch](https://data.research.uts.edu.au/examples/v1.0/Victoria_Arch_pub/): More cave
  data collected by a drone with a 3d lidar scanner, with video. This
   example contains examples of data-provenance,
   [via the use of schema.org `CreateAction` properties that show that files result from observation-actions on the `object` of study (the cave).](https://data.research.uts.edu.au/examples/v1.0/Victoria_Arch_pub/CATALOG_files/pairtree_root/Da/ta/Ca/pt/ur/e_/wc/r0/3/index.html)

* [Dataset for IDRC Project: Exploring the opportunities and challenges of implementing open research strategies within development institutions. International Development Research Center](https://data.research.uts.edu.au/examples/v1.0/Data_Package-IDRC_Opportunities_and_Challenges_Open_Research_Strategies/): This dataset was the subject of [a presentation](http://ptsefton.com/2017/10/19/datacrate.htm) at eResearch Australasia 2017. The published version used DataCrate v0.1 the link here is to an updated version of the dataset.

* [Curation](https://data.research.uts.edu.au/examples/v1.0/curation/): A simple dataset to demonstrate how [CreateAction]s can be used to provide a change-history of the DataCrate itself.


### Additional Definitions

*Data Entity*: A [Dataset], directory or file, which is represented by a [JSON Object].

*Context Entity*: A [Person], [Organization] (including research projects), item of equipment ([IndividualProduct]), [license] or any other *thing* or *event* that forms part of the metadata for a DataCrate,
or supporting information, represented by a [JSON Object].

*[DataCrate JSON-LD Context]*: A [JSON-LD] context that provides human-readable
labels for DataCrate metadata. These labels are used in `CATALOG.json` which
contains *DataCrate-flattened JSON-LD*.

*DataCrate-flattened JSON-LD*: A JSON-LD document, using the *DataCrate JSON-LD
Context* containing DataCrate metadata, which has been flattened according to
the rules in [JSON-LD 1.1].

*Root Dataset*: A *Data Entity* which is a [Dataset] representing the entire
contents of the DataCrate. Datasets may be nested but the *Root Dataset* can be
located by looking for an Dataset with [path] of "./", which means the DataCrate
is a *Working DataCrate*, and if that is not found, a path of "data/" which
means the DataCrate is a *Bagged DataCrate*.

*DataCrate website*: A set of linked pages which describe the DataCrate, its
*Data Entities* and *Context Entities*, one entity per page with a home-page at
`CATALOG.html` that describes the *Root Dataset* and sub-pages residing in
`CATALOG_files`.


## Conventions

Throughout this specification, RDF terms are be referred to using the keys
defined in [DataCrate JSON-LD Context]. Following [schema.org] practice property
names start with lowercase letters and class names with uppercase.

In `CATALOG.json` RDF terms use their DataCrate JSON-LD names
as defined in the [DataCrate JSON-LD context].

In examples, paths for a *Working DataCrate* are used. That is, the [path] of a
file is relative to a `CATALOG.json` document in the root of the DataCrate
without a [BagIt] `/data` directory.

## Core metadata standard for DataCrate: Schema.org

[Schema.org] is the base metadata standard for DataCrate.
Schema.org was chosen because it is widely used on the World Wide Web and
supported by search engines, on the assumption that discovery is likely to be
maximized if commercial search engines index the content. NOTE: As far as we
know there is no alternative existing well-maintained linked-data schema for
research data with the coverage needed for this project - i.e. a single standard
for expressing all the examples presented in this spec.

Future versions of this specification may be based on other metadata standards;
as far as possible this will be done by swapping in a new *DataCrate Context*,
leaving the keys as described here the same.

Additional metadata MAY be drawn from any RDF vocabulary to allow open-ended
extensibility.

Generally, the standard keys for [Schema.org] should be used, however there are a
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


## Structure of a DataCrate 


The BagIt file-system structure for a *Bagged DataCrate* is as follows.

```

           <base directory>/
           |   bagit.txt
           |   bag-info.txt (with BagIt-Profile-Identifier: matching this specification)
           |   manifest-<algorithm>.txt
           |   CATALOG.html
           |   CATALOG.json
           \--- CATALOG_files # Directory for additional HTML files
                | - pairtree_root
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
   |   CATALOG.html
   |   CATALOG.json
   \ --- CATALOG # Directory for additional HTML files
   |   [payload files]
```

[Part 2 of the BagIt profile specification](https://github.com/bagit-profiles/bagit-profiles/blob/master/README.md) says:
> Bags complying to a BagIt profile MUST contain the tag BagIt-Profile-Identifier
> in their bag-info.txt, which value is the URI of the JSON file containing the
> profile it conform to. This tag MAY be repeated if the bag conforms to multiple
> profiles. The URI that identifies the profile SHOULD be versioned, e.g.
> https://example.com/bagitprofiles/profile-bar-v0.1.json.  Resolving the URI with
> this specification.

The bag-info.txt for version 1.0 of a *Bagged DataCrate* MUST contain the following
metadata:

```
BagIt-Profile-Identifier: https://raw.githubusercontent.com/UTS-eResearch/datacrate/develop/spec/1.0/profile-datacrate-v1.0.json
DataCrate-Specification-Identifier: https://github.com/UTS-eResearch/datacrate/blob/develop/spec/1.0/data_crate_specification_v1.0.md
```

### `CATALOG.html` and `CATALOG.json`

-  `CATALOG.json` MUST be a *DataCrate-flattened JSON-LD* document.

-  `CATALOG.html` MUST:
    - Be an HTML 5 document containing a human readable summary
      of the [Dataset] DataCrate contents of `CATALOG.json`.
    - Describe in natural language only the DataCrate *Data Entity*.
    - Link to additional HTML files, in a subdirectory: `/CATALOG_files`. `CATALOG.html`.
    - Contain the *Flattened DataCrate JSON-LD* in a `script` element in the `head` element of the HTML.

Example of how to embed *Flattened DataCrate JSON-LD* in `CATALOG.html`.

```
<script type="application/ld+json">
  {
  "@context": { ...}
  "@graph": [ ...]
</script>
```

#### About `CATALOG.json`

The following *DataCrate-flattened JSON-LD* represents a minimal description of
a *Working DataCrate* dataset. The [DataCrate JSON-LD Context] MUST be included inline as below.

Any schema.org context or other linked data MAY be included.

Any unused keys SHOULD be omitted. 

Schema.org elements SHOULD be used as defined in the standard
Schema.org context. However, this standard does use variant names for some
elements.

* [File] is mapped to [schema:MediaObject] which was chosen as a compromise as
  it has many of the properties that are needed to describe a generic file.
  Future version of schema.org or a research data extension may define [File].

* [Journal] is mapped to [Periodical].

* [path] is mapped to [schema:contentURL]. This property is used on some
  classes which do not strictly (yet) allow it.

(From now on the `@context` will be omitted from examples.)

The `@context` MUST be provided as a local context in `CATALOG.json`. Terms
which are not used SHOULD be omitted. The CATALOG MUST map from terms directly
to URIs to make it easier for programmers to deal with DataCrates.

For example:

```
    {
  "@context": {http://schema.org/URL",
    "description": "http://schema.org/description",
    "identifier": "http://schema.org/identifier",
    "manufacturer": "http://schema.org/manufacturer",
    "model": "http://schema.org/model",

```

The `@context` MUST NOT use [CURIE]s or other less direct methods. This
requirement will be relaxed if and when tool support for JSON-LD improves.

These are not acceptable:

```
{ 
"@context": {"@id": "http://schema.org/URL",
"description": "schema:description",
```

## *Working DataCrates*

For DataCrates which are used in-situ and have not been distributed as a BagIt bag, 
the minimal markup is as follows.

```
{"@context":
    {
      "description": "http://schema.org/description",
      "Dataset": "http://schema.org/Dataset"
    }
},
  "@graph": [
    {
      "@id": "/",
      "path": "/",
      "@type": "Dataset",
      "Description": "This data set doesn't really exist"
    }
  ]
}
```

DataCrates which are packaged for distribution SHOULD:

* Have a DOI as an identifier. 
* Use the DOI URL as the `@id`.
* Include the DOI without a URL as an [identifier].
* Link to a [DataDownload] using the [distribution] property.

```
{
    "@id": "https://doi.org/10.4225/59/59672c09f4a4b",
    "@type": "Dataset",
    "citation": {
        "@id": "https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0181020"
    },
    "path": "data/",
    "datePublished": "2017",
    "description": "Palliative care planning for nursing home residents with advanced dementia is often suboptimal. This study compared effects of facilitated case conferencing (FCC) with usual care (UC) on end-of-life care",
     
    "identifier": [
        "https://doi.org/10.4225/59/59672c09f4a4b",
        "doi.org/10.4225/59/59672c09f4a4b"
    ],
    "name": "Data files associated with the manuscript:Effects of facilitated family case conferencing for advanced dementia: A cluster randomised clinical trial",
    "distribution": [
        {
            "@id": "https://data.research.uts.edu.au/examples/v1.0/timluckett.zip"
        }
    ]
}

{
    "@id": "https://data.research.uts.edu.au/examples/v1.0/timluckett.zip",
    "contentUrl": "https://data.research.uts.edu.au/examples/v1.0/timluckett.zip",
    "@type": "DataDownload",
    "encodingFormat": "zip"
}
```

If there are files in the payload directory (which is either `/data` for a
*Bagged DataCrate* or `/` for a *Working DataCrate*) each file MAY be
described by a *MediaObject* related to the schema:Dataset with a property
*HasPart*.

[See a DataCrate with a DOI identifier.](https://data.research.uts.edu.au/examples/v1.0/Data_Package-IDRC_Opportunities_and_Challenges_Open_Research_Strategies/)

For a published DataCrate the `@id` property should be a DOI URL if possible. 

```
{
  "@type": "Dataset",
  "name": "NCDC Storm Events Database",
  "description": "Storm Data is provided by the National Weather Service (NWS) and contain statistics on...",
  "url": "https://catalog.data.gov/dataset/ncdc-storm-events-database",
  "sameAs":"https://gis.ncdc.noaa.gov/geoportal/catalog/search/resource/details.page?id=gov.noaa.ncdc:C00510",
}
```

NOTE: As per the BagIt standard, every file in the payload directory of a
*Bagged DataCrate* MUST be in the BagIt Manifest. Describing every file in the
payload directory is possible with DataCrate, but is not mandatory. For
use-cases where it is important to describe all files, future versions of this
specification MAY allow for profiles, using which it will be possible to specify
constraints such as "every file must have a [description]".

To describe a file we will use this example. Take a file with this path, which
is in the root directory fo the *Working DataCrate* dataset:

    ./cp7glop.ai

For a *Bagged DataCrate* the path would be `data/cp7glop.ai` - the relative
path from `/CATALOG.json` to the file in the payload directory.

An example `DataCrate-flattened JSON-LD` for the above would be as follows:

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
        "fileFormat": "https://www.nationalarchives.gov.uk/PRONOM/fmt/19"
      }
      ]
```

### Thumbnails

A [File] or any other item MAY have a [thumbnail] property which references another file.

For example, the below [RepositoryObject] is related to four files which are all
versions of the same image (via [hasFile]) one of which is a thumbnail. The
thumbnail MUST be included in the DataCrate.

If [thumbnail]s are incidental to the data set, they need not be referenced by [hasPart] or [hasFile] relationships but must be in the BagIt manifest if in a *Bagged DataCrate*.

```
{
  "@id": "https://omeka.uws.edu.au/farmstofreeways/api/items/383",
  "@type": [
    "RepositoryObject",
    "Image"
  ],
  "identifier": [
    "ftf_photo_stapleton1"
  ],
  "interviewee": [
    {
      "@id": "https://omeka.uws.edu.au/farmstofreeways/api/items/595",
    }
  ],
  "description": [
    "Photo of Eugenie Stapleton inside her home"
  ],
  "license": [
    "Content in the Western Sydney Women's Oral History Project: From farms to freeways collection is licensed under a Creative Commons CC BY 3.0 AU licence (https://creativecommons.org/licenses/by/3.0/au/)."
  ],
  "publisher": [
    "University of Western Sydney"
  ],
  "hasFile": [
    {
      "@id": "files/383/original_c0f1189ec13ca936e8f556161663d4ba.jpg"
    },
    {
      "@id": "files/383/fullsize_c0f1189ec13ca936e8f556161663d4ba.jpg"
    },
    {
      "@id": "files/383/thumbnail_c0f1189ec13ca936e8f556161663d4ba.jpg"
    },
    {
      "@id": "files/383/square_thumbnail_c0f1189ec13ca936e8f556161663d4ba.jpg"
    }
  ],
  "thumbnail": [
    {
      "@id": "files/383/thumbnail_c0f1189ec13ca936e8f556161663d4ba.jpg"
    }
  ],
  "name": [
    "Photo of Eugenie Stapleton 1"
  ],
  "relatedLink": [
    "<a href=\"https://omeka.uws.edu.au/farmstofreeways/items/show/512\">Audio recording of interview with Eugenie Stapleton</a><br /><a href=\"https://omeka.uws.edu.au/farmstofreeways/items/show/454\">Transcript of interview with Eugenie Stapleton</a> <br /><a href=\"https://omeka.uws.edu.au/farmstofreeways/items/show/384\">Photo of Eugenie Stapleton 2</a><br /><a href=\"https://omeka.uws.edu.au/farmstofreeways/items/show/464\">Letter from Eugenie Stapleton</a>"
  ],
  "copyrightHolder": [
    {
      "@id": "https://westernsydney.edu.au",
    }
  ],
  "copyright": [
    "Copyright University of Western Sydney 2015"
  ]
},
{
  "@type": "File",
  "path": [
    "data/files/384/original_2ebbe681aa6ec138776343974ce8a3dd.jpg"
  ],
  "@id": "files/384/original_2ebbe681aa6ec138776343974ce8a3dd.jpg"
},
{
  "@type": "File",
  "path": [
    "data/files/384/fullsize_2ebbe681aa6ec138776343974ce8a3dd.jpg"
  ],
  "@id": "files/384/fullsize_2ebbe681aa6ec138776343974ce8a3dd.jpg"
},
{
  "@type": "File",
  "path": [
    "data/files/384/thumbnail_2ebbe681aa6ec138776343974ce8a3dd.jpg"
  ],
  "@id": "files/384/thumbnail_2ebbe681aa6ec138776343974ce8a3dd.jpg"
},
{
  "@type": "File",
  "path": [
    "data/files/384/square_thumbnail_2ebbe681aa6ec138776343974ce8a3dd.jpg"
  ],
  "@id": "files/384/square_thumbnail_2ebbe681aa6ec138776343974ce8a3dd.jpg"
},

 
```

[See an example of a thumbnail.](https://data.research.uts.edu.au/examples/v1.0/sample/CATALOG_files/pairtree_root/pi/cs/=2/01/7-/06/-1/1%5E/20/12/,5/6,/14/,j/pg/index.html)

### Describing a directory but not files

To describe a directory without listing all the files in it, a DataCrate SHOULD
have a description of the directory using an entity of ``@type`` [Dataset]. This
DataCrate JSON-LD, describes the directory `lots_of_little_files` in an *Working
DataCrate*. To make it clear that the files are not listed in `CATALOG.json`
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
[See an example of an HTML page describing a directory of small files.](https://data.research.uts.edu.au/examples/v1.0/sample/CATALOG_files/pairtree_root/lo/ts/_o/f_/li/tt/le/_f/il/es/index.html)

## How to represent various kinds of metadata

### People

A core principle of Linked data is to use URIs as to identify things such as
people. The following is the minimum recommended way of representing a
[creator] in a DataCrate. This property MAY be applied in the context of
a directory ([Dataset]) or to a [File].
```

{
      "@type": "Dataset",
      "@id": "some_id",
      "creator": {"@id": "https://orcid.org/0000-0002-8367-6908"}

}

```

This uses an ORCID, to unambiguously identify an author and could be used as is.
However, it does not provide very much information about the author and it is
not possible to use this information to reliably construct a [DataCite] citation.
The `DataCrate-flattened JSON-LD` @graph SHOULD contain additional
information about *Metadata Entities* for the use of both humans (once it has
been rendered as `CATALOG.html`) and machines.

```

{
    "@id": "https://orcid.org/0000-0002-8367-6908",
    "@type": "Person",
    "affiliation": "University of Technology Sydney",
    "name": "J. Xuan"
  }
```

Note the string-value of the organizational affiliation. This SHOULD be improved
by also providing a *Context Entity* for the organization.



### Organizations as values

An [Organization] SHOULD be the value for the [publisher] property
of a [Dataset] or [ScholarlyArticle] or [affiliation]
property of a [Person].

```
{
      "@type": "Dataset",
      "@id": "some_id",
      "publisher": {"@id": "https://uts.edu.au"}
}

{
  "@id": "https://uts.edu.au",
  "@type": "Organization",
  "name": "University of Technology Sydney"
}
```


An [Organization] SHOULD also be used for a [Person]'s affiliation. 
```
{
      "@type": "Dataset",
      "@id": "some_id",
      "publisher": {"@id": "https://uts.edu.au"}
},
{
  "@id": "https://uts.edu.au",
  "@type": "Organization",
  "name": "University of Technology Sydney"
},
{
       "@id": "https://orcid.org/0000-0002-3545-944X",
       "@type": "Person",
       "affiliation": {"@id": "https://uts.edu.au"},
       "email": "peter.sefton@uts.edu.au",
       "name": "Peter Sefton"
     }
```

Sometimes researchers want to affiliate with a sub-part of an institution, such as
institute, faculty department, or research group.

Here's a (real) example of a researcher who has four institutional affiliations
for a published article and data set:

```
{
  "@id": "https://doi.org/10.4225/59/59672c09f4a4b",
  "@type": "Dataset",
  "path": "./",
  "creator": [
    {
      "@id": "https://orcid.org/0000-0002-6756-6119"
    },
    ...

}

{
  "@id": "https://orcid.org/0000-0002-6756-6119",
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
    "@id": "https://uts.edu.au"
  },
  "name": "Faculty of Health, University of Technology Sydney"
},

{
  "@id": "https://uts.edu.au",
  "@type": "Organization",
  "name": "University of Technology Sydney"
},

```

Thus *DataCrate Flattened JSON-LD* MAY express chained affiliations with more
precision than by using string-literals as values for [affiliation].

[See an example of a Person with four affiliations.](https://data.research.uts.edu.au/examples/v1.0/luckett/CATALOG_files/pairtree_root/ht/tp/+=/=o/rc/id/,o/rg/=0/00/0-/00/02/-6/75/6-/61/19/index.html)


### Contact point

A DataCrate SHOULD have contact information, using a [contactPoint] property
with a value of [ContactPoint]. Note that [Dataset] does not (yet) have a
contactPoint property, so strictly this would have to be on a Person or
Organization which is related to the Dataset via a [creator] or [contributor]
property. If there is a ContactPoint it MUST have `contactType` of
`customer service` and MUST have a url which is an http URI. The ID of the
contact point MAY be an email address.

NOTE: This usage follows that of Google Dataset search. 

NOTE: The Google structured Data tool gives an error if mailto: URLs are used in
the "url" property.

```
{
      "@id": "https://orcid.org/0000-0001-6121-5409",
      "@type": "Person",
      "affiliation": {
        "@id": "1"
      },
      "contactPoint": {
        "@id": "tim.luckett@uts.edu.au"
      },
      "familyName": "Luckett",
      "givenName": "Tim",
      "identifier": "https://orcid.org/0000-0001-6121-5409",
      "name": "Tim Luckett"
    },


  {
      "@id": "tim.luckett@uts.edu.au",
      "@type": "ContactPoint",
      "contactType": "customer service",
      "email": "tim.luckett@uts.edu.au",
      "identifier": "tim.luckett@uts.edu.au",
      "url": "https://orcid.org/0000-0001-6121-5409"
    },

```

This is not ideal, as there is no direct semantic relationship between the
contactPoint property and the Dataset, so the same [contactPoint] should be
added to the *Root Dataset*, in anticipation of this being added to Schema.org,
even though this is not strictly supported at the moment.

```
{
  "@id": "https://doi.org/10.4225/59/59672c09f4a4b",
  "@type": "Dataset",
  "path": "./",
  "contactPoint": {
        "@id": "tim.luckett@uts.edu.au"
      },
}
```

[See an example of an item of `@type` `ContactPoint`, which is linked to a `Dataset` and to an `Organization` via a `contactPoint` property.](https://data.research.uts.edu.au/examples/v1.0/luckett/CATALOG_files/pairtree_root/ht/tp/+=/=o/rc/id/,o/rg/=0/00/0-/00/01/-6/12/1-/54/09/index.html)

### Publications

To associate a publication with a dataset the *DataCreate Flattened JSON-LD* MUST include a URL
which SHOULD be a DOI URL, as the ID of a publication using the [citation] or
property.

For example:
```
"citation": {"@id": "https://doi.org/10.1109/TCYB.2014.2386282"}
```

The publication SHOULD be described in the *DataCrate Flattened JSON-LD*.

```
{
  "@id": "https://doi.org/10.1109/TCYB.2014.2386282",
  "@type": "ScholarlyArticle",
  "creator": [
    {
      "@id": "https://orcid.org/0000-0002-8367-6908"
    },
    {
      "@id": "https://orcid.org/0000-0003-0690-4732"
    },
    {
      "@id": "https://orcid.org/0000-0003-3960-0583"
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

[See an example of a journal article.](https://data.research.uts.edu.au/examples/v1.0/GTM/CATALOG_files/pairtree_root/ht/tp/+=/=d/x,/do/i,/or/g=/10/,1/10/9=/TC/YB/,2/01/4,/23/86/28/2/index.html)

### Publisher

The root [Dataset] in *Bagged DataCrate* SHOULD have a [publisher] property. This
should be a an [Organization] though it MAY be a string-literal or a URI. 

```
{
  "@id": "https://doi.org/10.5281/zenodo.1009240",
  "@type": "Dataset",
   <...>
  "name": "Sample dataset for DataCrate v0.2",
  "publisher": {
    "@id": "https://uts.edu.au"
  },
  "temporalCoverage": "2017"
},

{
  "@id": "https://uts.edu.au",
  "@type": "Organization",
  "identifier": "https://uts.edu.au",
  "name": "University of Technology Sydney"
},
```
[See an example of an Organization which is linked to the publisher property of a Dataset.](https://data.research.uts.edu.au/examples/v1.0/sample/CATALOG_files/pairtree_root/ht/tp/+=/=u/ts/,e/du/,a/u/index.html)


### Funding and grants

To associate a research project (with or without funding) with a [Dataset], the
*DataCrate-flattened JSON-D* SHOULD contain an entity for the project using type
"Organization", referenced by a [funder] property. The project organization
SHOULD in turn reference any external [funder], either by using its URL as an
`@id` or via a *Context Entity* describing the funder. 

NOTE: To make it very clear where funding is coming from, the *Root DataSet*
SHOULD also reference funders directly, as well as via a chain of references.
 
```
{
  "@id": "https://doi.org/10.5281/zenodo.1009240",
  "@type": "Dataset",
  "funder": {
    "@id": "https://github.com/UTS-eResearch/datacrate"
  },
  ...
},

{
  "@id": "https://eresearch.uts.edu.au/projects/provisioner",
  "@type": "Organization",
  "description": "The University of Technology Sydney Provisioner project is ...",
  "funder": [
    {
      "@id": "https://uts.edu.au"
    },
    {
      "@id": "https://ands.org.au"
    }
  ],
  "identifier": "https://eresearch.uts.edu.au/projects/provisioner",
  "name": "Provisioner"
},

{
  "@id": "https://uts.edu.au",
  "@type": "Organizsation",
  "identifier": "https://uts.edu.au",
  "name": "University of Technology Sydney"
},

{
  "@id": "https://ands.org.au",
  "@type": "Organization",
  "description": "The core purpose of the Australian National Data Service (ANDS) is ...",
  "identifier": "https://ands.org.au",
  "name": "Australian National Data Service"
},

```
[See an example of a Dataset which has three listed funders](https://data.research.uts.edu.au/examples/v1.0/sample/CATALOG.html) in a chained relationship, [for example this Organization is linked as a funder and in turn links to another funder.](https://data.research.uts.edu.au/examples/v1.0/sample/CATALOG_files/pairtree_root/ht/tp/s+/==/gi/th/ub/,c/om/=U/TS/-e/Re/se/ar/ch/=p/ro/je/ct/s=/da/ta/cr/at/e/index.html)

### Date of publication

The [datePublished] property MUST be a date in ISO 8601 date format and SHOULD
be specified to least the precision of a day.

```
{
  "@id": "https://doi.org/10.5281/zenodo.1009240",
  "@type": "Dataset",
  "datePublished": "2017-06-29",
},
```

### Licensing and copyright

If a *Data Entity* has a license the entity SHOULD have a [license] property
with a value of [CreativeWork] that describes the license. The ID of the license
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
    "@id": "https://orcid.org/0000-0002-0068-716X"
  },
  "description": "Abstract for the Pilot Project initial findings",
  "encodingFormat": "Hypertext Markup Language",
  "fileFormat": "https://www.nationalarchives.gov.uk/PRONOM/fmt/96",
  "license": {
    "@id": "https://creativecommons.org/licenses/by/4.0/"
  },
  "sameAs": "https://www.scidatacon.org/2016/sessions/56/paper/265/"
},

{
  "@id": "https://orcid.org/0000-0002-0068-716X",
  "@type": "Person",
  "identifier": "https://orcid.org/0000-0002-0068-716X",
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

[See this example of a an audio recording with a `copyrightHolder` property (which is different from the `creator` property) and which is linked to a license.](https://data.research.uts.edu.au/examples/v1.0/Data_Package-IDRC_Opportunities_and_Challenges_Open_Research_Strategies/CATALOG_files/pairtree_root/Po/li/cy/%5E2/0a/nd/%5E2/0I/mp/le/me/nt/at/io/n%5E/20/Re/vi/ew/%5E2/0I/nt/er/vi/ew/s=/In/te/rv/ie/w_/Au/di/o=/In/te/rv/ie/w-/25/_0/9_/20/15/-1/3_/43/-J/ua/n_/Bi/ca/rr/eg/ui/,f/la/c/index.html)


### Equipment

To specify which equipment was used to create or update a *Data Entity*, the
*DataCrate* JSON-LD should have a *Context Entity* for each item of equipment
which SHOULD be of `@type` [IndividualProduct]. The entity SHOULD have a serial
number, manufacturer that identifies it as completely as possible. In this case
the equipment is a bespoke machine. The equipment SHOULD be described on a web
page, and the address of the description SHOULD be used as its `@id`.

```
{
  "@id": "https://confluence.csiro.au/display/ASL/Hovermap",
  "@type": "IndividualProduct",
  "description": "The CSIRO bentwing is an unmanned aerial vehicle (UAV, commonly known as a drone) with a LIDAR mounted underneath to capture 3D information on the surroundings.",
  "identifier": "https://confluence.csiro.au/display/ASL/Hovermap",
  "name": "bentwing"
}
```

To allow for flexibility in modeling provenance, and following best practice
used by archivists DataCrate uses subtypes of the [Action], [CreateAction] and [UpdateAction]
class to model the contributions of *Context Entities* of type [Person] or [Organization].

In this example the CreateAction has a human [agent], the object is a Place (a
cave) and the Hovermap drone is the [instrument] used in the file creation
event.

```
{
      "@id": "DataCapture_wcc02",
      "@type": "CreateAction",
      "agent": {
        "@id": "http://orcid.org/0000-0002-1672-552X"
      },
      "identifier": "DataCapture_wcc02",
      "instrument": {
        "@id": "https://confluence.csiro.au/display/ASL/Hovermap"
      },
      "object": {
        "@id": "victoria_arch"
      },
      "result": [
        {
          "@id": "wcc02_arch.laz"
        },
        {
          "@id": "wcc02_arch_traj.txt"
        }
      ]
    },

{
      "@id": "victoria_arch",
      "@type": "Place",
      "address": "Wombeyan Caves, NSW 2580",
      "description": "This is the GDA94 datum, which is for most situations is close to WGS84",
      "geo": {
        "@id": "08563107-deb2-4d3b-a25f-83a09b5b61d4"
      },
      "identifier": "victoria_arch",
      "name": "Victoria Arch"
    },
```

[See an example of a data-capture CreateAction with an `Object` and two files as `result`s.](https://data.research.uts.edu.au/examples/v1.0/Victoria_Arch_pub/CATALOG_files/pairtree_root/Da/ta/Ca/pt/ur/e_/wc/c0/2/index.html)

### Software 

To specify which software was used to create or update a file the software application SHOULD be represented with an entity of type [SoftwareApplication], with a [version] property. For example:

```
{
      "@id": "https://www.imagemagick.org/",
      "@type": [
        "SoftwareApplicatio n",
        "SoftwareApplication"
      ],
      "URL": "https://www.imagemagick.org/",
      "identifier": "https://www.imagemagick.org/",
      "name": "ImageMagick",
      "version": " ImageMagick 7.0.8-2 Q16 x86_64 2018-06-19"
    },


```

The software SHOULD be associated with the [File] it created using a
CreateAction with the [File] referenced by a [result] property. Any input files
SHOULD be referenced by the [object] property.

In this example, an image with the `@id` of `pics/2017-06-11 12.56.14.jpg` was
transformed into an new image `pics/sepia_fence.jpg` using the `ImageMagick`
software application. Actions MAY have human-readable names, which could be
machine generated for use at scale.

```
{
      "@id": "Photo_Capture_1",
      "@type": "CreateAction",
      "agent": {
        "@id": "https://orcid.org/0000-0002-3545-944X"
      },
      "description": "Photo snapped on a photo walk on a misty day",
      "identifier": "Photo1",
      "endTime": "2017:06:11T12:56:14+10:00",
      "instrument": [
        {
          "@id": "EPL1"
        },
        {
          "@id": "Panny20mm"
        }
      ],
      "result": {
        "@id": "pics/2017-06-11 12.56.14.jpg"
      }
    },
    {
      "@id": "SepiaConversion_1",
      "@type": "CreateAction",
      "name": "Convert dog image to sepia",
      "description": "convert -sepia-tone 80% test_data/sample/pics/2017-06-11\\ 12.56.14.jpg test_data/sample/pics/sepia_fence.jpg",
      "identifier": "SepiaConversion",
      "endTime": "2018:09:19T17:01:07+10:00",
      "instrument": {
        "@id": "https://www.imagemagick.org/"
      },
      "object": {
        "@id": "pics/2017-06-11 12.56.14.jpg"
      },
      "result": {
        "@id": "pics/sepia_fence.jpg"
      }
    },

```

[See an example](https://data.research.uts.edu.au/examples/v1.0/sample/CATALOG_files/pairtree_root/Ph/ot/o1/index.html) of a [CreateAction], with a result which is a [File] and an [object], which is a place.

### Provenance

To record an action which changes the DataSet's metadata, or changes its state
in a publication or other workflow, a [CreateAction] or [UpdateAction] SHOULD be
associated with a *Data Entity*.

A curation Action MUST have at least one [object] which associates it with either the DataSet or one of its components.

An Action which creates new *Data entities* - for example, the creation of a new metadata file - SHOULD have these as [result]s. 

An Action SHOULD have a [name] and MAY have a [description].

An Action SHOULD have an [endTime], which MUST be in ISO 8601 date format and
SHOULD be specified to at least the precision of a day. An Action MAY have a [startTime]
meeting the same specifications.

An Action SHOULD have a human [agent] who was responsible for authorizing the
action, and MAY have an [instrument] which associates the action with a
particular piece of software (for example, the content management system or data
catalogue through which an update was approved) which SHOULD be of `@type` SoftwareApplication.

An Action's status MAY be recorded in an [actionStatus] property. The status must be one of the values enumerated by [ActionStatusType]: ActiveActionStatus, CompletedActionStatus, FailedActionStatus or PotentialActionStatus.

An Action which has failed MAY record any error information in an [error] property.

[UpdateAction] SHOULD only be used for actions which affect the DataSet as a whole, such as movement through a workflow.

To record curation actions which modify a [File] within a DataSet - for example,
by correcting or enhancing metadata - the old version of the [File] SHOULD be
retained, and a [CreateAction] added which has the original version as its [object]
and the new version as its [result].

```
{
    "@id": "history-01",
    "@type": "CreateAction",
    "object": { "@id": "https://doi.org/10.5281/zenodo.1009240" }
    "name": "DataCrate created",
    "endTime": "2018-08-31",
    "agent": { "@id": "https://orcid.org/0000-0001-5152-5307" },
    "instrument": { "@id": "https://stash.research.uts.edu.au" },
    "actionStatus": "CompletedActionStatus"
},

{
    "@id": "history-02",
    "@type": "UpdateAction",
    "object": { "@id": "https://doi.org/10.5281/zenodo.1009240" }
    "name": "DataCrate published",
    "endTime": "2018-09-10",
    "agent": { "@id": "https://orcid.org/0000-0001-5152-5307" },
    "instrument": { "@id": "https://stash.research.uts.edu.au" },
    "actionStatus": "CompletedActionStatus"
},

{ 
    "@id": "history-03",
    "@type": "CreateAction",
    "object": { "@id": "./metadata.xml.v0.1" },
    "result": { "@id": "./metadata.xml" }
    "name": "metadata update",
    "endTime": "2018-09-12",
    "agent": { "@id": "https://orcid.org/0000-0001-5152-5307" },
    "instrument": { "@id": "https://stash.research.uts.edu.au" },
    "actionStatus": "CompletedActionStatus"
},

{
    "@id": "history-04",
    "@type": "UpdateAction",
    "object": { "@id": "https://doi.org/10.5281/zenodo.1009240" }
    "name": "DataCrate published",
    "endTime": "2018-09-13",
    "agent": { "@id": "https://orcid.org/0000-0001-5152-5307" },
    "instrument": { "@id": "https://stash.research.uts.edu.au" },
    "actionStatus": "FailedActionStatus",
    "error": "Record is already published"
},


{
    "@id": "https://stash.research.uts.edu.au",
    "@type": "IndividualProduct",
    "name": "Stash",
    "description": "UTS Research Data Catalogue",
    "identifier": "https://stash.research.uts.edu.au"
}
```


### Extra metadata such as Exif

Schema.org has an generic extension mechanism for encoding adding arbitrary
properties and values which are not available as Schema.org properties. An
example of of this is the Schema.org [recommended way (see example 2)](http://schema.org/ImageObject)
of including [Exif] technical image metadata.

To include EXIF, or other data which can be encoded as property/value pairs, add
an array of references to *Anonymous Entities* which encode each property. This
example shows one property of several hundred.

```
{
      "@id": "pics/2017-06-11 12.56.14.jpg",
      "@type": "ImageObject",
      "contentSize": "5114778",
      "path": "data/pics/2017-06-11 12.56.14.jpg",
      "creator": {
        "@id": "https://orcid.org/0000-0002-3545-944X"
      },
      "description": "Depicts a fence at a disused motor racing venue with the front part of a slightly out of focus black dog in the foreground.",
      "encodingFormat": "Exchangeable Image File Format (Compressed)",
      "exifData": [
        {
          "@id": "_:b0"
        },
        ...
        {
          "@id": "_:b312"
        },
      ]

{
      "@id": "_:b312",
      "@type": "PropertyValue",
      "name": "InternalSerialNumber",
      "value": "4102011002108002               "
    },

```

[See an example of Exif data displayed in HTML.](https://data.research.uts.edu.au/examples/v1.0/sample/CATALOG_files/pairtree_root/pi/cs/=2/01/7-/06/-1/1%5E/20/12/,5/6,/14/,j/pg/index.html)

### Places

To associate a *Data Entity* with a *Context Entity* representing a *geographical
location or region* the entity SHOULD have
a property of [contentLocation] with a value of type [Place].

This example shows how to define a place, using a [geonames] ID:
```
{
  "@id": "https://www.geonames.org/8152662/catalina-park.html",
  "@type": "Place",
  "description": "Catalina Park is a disused motor racing venue, located at Katoomba ...",
  "geo": {
    "@id": "b4168a98-8534-4c6d-a568-64a55157b656"
  },
  "identifier": "https://www.geonames.org/8152662/catalina-park.html",
  "name": "Catalina Park"
},
```
The place has a [geo] property, referencing an *Context Entity* of `@type` [GeoCoordinates]:

```
{
  "@id": "b4168a98-8534-4c6d-a568-64a55157b656",
  "@type": "GeoCoordinates",
  "latitude": "-33.7152",
  "longitude": "150.30119",
  "name": "Latitude: -33.7152 Longitude: 150.30119"
},
```

The [GeoCoordinates] item SHOULD have a human readable [name], which is used in the generating the `CATALOG.html` file.

And the place is referenced from the [contentLocation] property of the dataset.
```
{
  "@id": "https://doi.org/10.5281/zenodo.1009240",
  "@type": "Dataset",
  "outputOf": "DataCrate",
  "contact": {
    "@id": "https://orcid.org/0000-0002-3545-944X"
  },
  "contentLocation": {
    "@id": "https://www.geonames.org/8152662/catalina-park.html"
  },
```

[Place] MAY use any of the [resources available in Schema.org](http://schema.org/geo) to
describe places. Future profiles of DataCrate may mandate the use of a subset of
these. Any directory or file or *Context Entity* may be geo-located. For example this file:

```
{
  "@id": "pics/19093074_10155469333581584_5707039334816454031_o.jpg",
  "@type": "File",
  "contentLocation": {
    "@id": "https://www.geonames.org/8152662/catalina-park.html"
  },
  "contentSize": "132765",
  "path": "pics/19093074_10155469333581584_5707039334816454031_o.jpg",
  "creator": {
    "@id": "https://orcid.org/0000-0002-3545-944X"
  },
```

[See an example of a place in HTML.](https://data.research.uts.edu.au/examples/v1.0/sample/CATALOG_files/pairtree_root/ht/tp/+=/=w/ww/,g/eo/na/me/s,/or/g=/81/52/66/2=/ca/ta/li/na/-p/ar/k,/ht/ml/index.html)

### Time

To describe the time period which a DataCrate Data Entity is *about*, use
[temporalCoverage].


### Subjects & keywords

Subject properties (equivalent to a Dublin Core Subject) on DataCrate
MUST use the [about] property.

Keyword properties MUST use [keyword].

### Digital Library and Repository content

To describe an export from a Digital Library or repository system, DataCrate uses the
Portland Common Data Model ([PCDM]). A *Context Entity* from a repository, representing an
abstract entity such as a person, or a work, or a place SHOULD have a```@type```
of [RepositoryObject], in addition to any other types.
Objects MAY be grouped together in [RepositoryCollection]s
with [hasMember] pointing to the the [RepositoryObject]. The keys
RepositoryObject and RepositoryCollection were chosen to avoid collision between
the terms Collection and Object with other vocabularies.

NOTE: [PCDM] specifies that Files should have only technical metadata, not
descriptive metadata, which is *not* a restriction in DataCrate. If the
DataCrate is to be imported into a strict [PCDM] repository, modeling of
object/file relationships will be necessary.

For example, this data is exported from an [Omeka](https://omeka.org) repository:

```
{
   "@id": "https://omeka.uws.edu.au/farmstofreeways/api/collections/6",
   "@type": [
      "RepositoryCollection"
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
   "hasMember": [
      {
         "@id": "https://omeka.uws.edu.au/farmstofreeways/api/items/166"
      },
      {
         "@id": "https://omeka.uws.edu.au/farmstofreeways/api/items/167"
      },
      {
         "@id": "https://omeka.uws.edu.au/farmstofreeways/api/items/168"
      },
      {
         "@id": "https://omeka.uws.edu.au/farmstofreeways/api/items/169"
      }
   ]
},
{
   "@id": "https://omeka.uws.edu.au/farmstofreeways/api/items/166",
   "@type": [
      "RepositoryObject",
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
   "hasFile": [
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
[See an example of a repository exported to a DataCrate.](https://data.research.uts.edu.au/examples/v1.0/farms_to_freeways/)

## Minimum metadata summary for *Bagged DataCrates*

A *Bagged DataCrate* MUST have a *Root [Dataset]* with:
 *  a [description] property, which describes the whole dataset contained
    in the data (BagIt payload) directory.

 *  A creation date in ISO data format for the dataset using
    [dateModified] which is the last date that the *payload*, *not* the
    metadata was changed.

 *  A [contactPoint] property referencing a [ContactPoint] *Context Entity*.


### BagIt metadata

BagIt metadata SHOULD be included in bag-info.txt where it is available as
follows in the Dataset object at the root of '@graph' in *DataCrate-flattened JSON-LD*.

The following metadata SHOULD be extracted from the *DataCrate-flattened JSON* in
the base directory of the bag. The expressions below show how to look-up BagIt metadata. For example, "['contactPoint'] -> ['email']" means find a [contactPoint] property, resolve that to a *Context Entity* and get its [email] property.

```
Source-Organization: ['publisher'] -> ['name']
Contact-Name: ['contactPoint'] -> ['name']
Contact-Phone: ['contactPoint'] -> ['phone']
Contact-Email: ['contactPoint'] -> ['email']
External-Description: ['description']
Bagging-Date: To be entered by the software tool used to create the DataCrate
External-Identifier: ['@id'] (if the ID is an http or https URL)


Bag-Group-Identifier: N/A
Bag-Count: N/A
Internal-Sender-Identifier: N/A
Internal-Sender-Description: N/A
```

For example:

```
BagIt-Profile-Identifier: https://raw.githubusercontent.com/UTS-eResearch/datacrate/master/spec/1.0/profile-datacrate-v1.0.json
DataCrate-Specification-Identifier: https://github.com/UTS-eResearch/datacrate/blob/master/spec/1.0/data_crate_specification_v1.0.md
Contact-Email: peter.sefton@uts.edu.au
Contact-Name: Contact Peter Sefton
Description: This is a simple dataset for demonstration purposes it contains just one image and a directory full of useless text files.
Bagging-Date: 2018-09-23T23:48:07.867Z

```


### Recommended IDs

*DataCrate-flattened JSON-LD* documents SHOULD use the following IDs where possible:
*  For a DataCrate, an `@id` which SHOULD be a DOI URL.
*  For People participating in the research process: [ORCID] identifiers.
*  For [Organization]s including [funder]s, pending a global identifier scheme,
   use the homepage URL of the organization.
*  For items of type schema:Place: a geonames URL.
*  For file formats; [Pronom] URLs, for example https://www.nationalarchives.gov.uk/PRONOM/fmt/831.

In the absence of the above, DataCrates SHOULD contain stable persistent URIs to
identify all entities wherever possible.

## Datacite citations

In a *Bagged DataCrate*, if there is sufficient metadata in `CATALOG.json`, it
SHOULD contain a DataCite citation, datacite.xml in the `/metadata` relative to
the root of the bag, compliant with the [DataCite Schema v4.0].

To generate DataCite.xml a DataCrate MUST have the following at the
schema:Dataset level:

*  An `@id` for the DataCrate that is a DOI URL.

*  At least one [creator] with either a [name] or a
   [givenName] *and* [familyName].

*  At least one [name] (which maps to a DataCite title).

*  A least one [publisher] property which SHOULD be an organization but
   may be a String value.

The mandatory DataCite resource type MUST be set to "DataCrate-v0.2".

[See an example data citation.](https://data.research.uts.edu.au/examples/v1.0/farms_to_freeways/metadata/datacite.xml)

## The *DataCrate Website*

The *DataCrate CATALOG.html* is an entry point to the *DataCrate Website*, a
collection of HTML pages which describe each *Data Entity* and each *Contextual
Entity*. The CATALOG.html file MUST describe the *Root Dataset* and link to
other pages, one per entity, which are stored in /CATALOG_files, and organized
according to the [Pairtree specification]. [Pairtree] was chosen as it specifies
how to generate a path from an identifier, and divides up the file-space
efficiently, which is an advantage in very large DataCrates, where there may be
thousands of pages in the *DataCrate Website*. The path to additional HTML file
is: `/CATALOG_files/` followed by the `@id` of the file converted to a Pairtree
path followed by `index.html`.

The *DataCrate website* MUST contain the same information as the JSON-LD
`CATALOG.json`, organized as described below, with the exception that files that
do not have a description, creator or other metadata that cannot be derived
automatically MAY not be listed.

*DataCrate CATALOG.html* must be a static pre-compiled HTML file that will display
without scripting. It MAY contain extra features enabled by Javascript.


The `CATALOG.html` and `index.html` files file SHOULD:

*  Show the contents of each data entity in a human readable form, showing each
   *Data Entity* and each *Context Entity* in HTML.
*  When a *Data Entities* and *Metadata Entities* is referenced by its ID:
    * If it has a [name] property, provide a link to its HTML version.
    * If it does not have a name (eg a [GeoCoordinates] location), show it
       embedded in the HTML for the entity.
*  For keys that resolve in the `DataCrate JSON-LD Context` to a URI, indicate
   this (the simplest way is to link the key to its definition.
*  For external URI values, provide a link.

An example implementation can be found in the [CalcyteJS] tool.

To create the web-content in a DataCrate:
* Traverse the JSON-LD `@graph` by iterating over every entity in the *DataCrate-flattened JSON-LD*:
  * Compile an index of items by ID (the `ID-index` for the purposes of this explanation).


* Make a second pass of the JSON-LD to add reciprocal properties linking back from a referenced entity to the entity that references it.
  * For the following structural properties, use the inverse property: `hasFile -> fileOf  hasPart -> isPartOf hasMember -> memberOf`.
  * For other properties use JSON-LD's `@reverse` feature. For example: `{"@reverse": { creator: [ { '@id': '1' } ] }}`

* Starting with the *Root Dataset*, call a function Entity-to-HTML to generate `CATALOG.html` which should contain:
   * A prominent citation in DataCite text format if there is sufficient metadata to generate one.
   * For *Hosted DataCrates* a prominent link to a downloadable version of the dataset (in addition to the fact that there will be a [distribution] property).

* Function: Entity-to-HTML: A function to generate a web page summarizing 
   * The properties of the entity. For example by using a two-column table showing the property in the left column and its values in the right column.
      * Large lists of properties should be made usable for example by generating nested HTML `summary` elements to paginate them.
      * For properties that link to another entity, call Entity-to-HTML
   * For properties that reference an external URI, show the URI as a link. 
   * For each referenced entity which is found in the `ID-index` call
     Entity-to-HTML (which will save the page) and link to it by calculating its path from its ID (see below).
   * Save the web page on a path calculated as follows: If it is the *Root Dataset* page, use `CATALOG.html`, or for all other pages calculate a pairtree path using the item `@id` and add that to `CATALOG_html`. For example: the path.
     `https://orcid.org/0000-0002-3545-944X`
     becomes:
     `CATALOG_files/pairtree_root/ht/tp/+=/=o/rc/id/,o/rg/=0/00/0-/00/02/-3/54/5-/94/4X/index.html`


## Extending DataCrate

To extend DataCrate implementers SHOULD try to use valid schema.org
properties and classes and MAY use terms form other
vocabularies and ontologies when this is not possible.

The terms (properties and classes) used SHOULD be added as keys to the
`@context` in the *DataCrate-flattened JSON-LD*. URIs in the `@context` SHOULD
resolve to a useful human readable page. Where this is not possible - for
example if the URI resolves to an RDF ontology file a human-readable URI SHOULD
be provided using SameAs.

For example. This URI from the [BIBO] ontology resolves to an ontology file,
which is not useful for humans, but [this page](http://neologism.ecs.soton.ac.uk/bibo.html#interviewee)
is human-readable:

```
{
  @context: {
    "interviewee": "http://purl.org/ontology/bibo/interviewee"
  }
  @graph: [
 {
      "@id": "http://purl.org/ontology/bibo/interviewee",
      "sameAs": "http://neologism.ecs.soton.ac.uk/bibo.html#interviewee",
      "@type": "URL"
}
  ]
}

```

When generating the *DataCrate Website* from *DataCrate-flattened JSON-LD*, the code MUST use a SameAs URI as a
target for an explanatory link for the term instead of the URI supplied in the @context.


[See an example of the key "interviewee" resolving to a human-readable page.](https://data.research.uts.edu.au/examples/v1.0/Data_Package-IDRC_Opportunities_and_Challenges_Open_Research_Strategies/CATALOG_files/pairtree_root/Po/li/cy/%5E2/0a/nd/%5E2/0I/mp/le/me/nt/at/io/n%5E/20/Re/vi/ew/%5E2/0I/nt/er/vi/ew/s=/In/te/rv/ie/w_/Au/di/o=/In/te/rv/ie/w-/25/_0/9_/20/15/-1/4_/02/-S/im/on/_H/od/so/n,/fl/ac/index.html)
and
[download the *DataCrate-flattened JSON-LD*](https://data.research.uts.edu.au/examples/v1.0/Data_Package-IDRC_Opportunities_and_Challenges_Open_Research_Strategies/CATALOG.json)

Where there is no RDF ontology available, then implementors SHOULD attempt to
provide context by creating stable web-accessible URIs to document properties
and classes, for example, by linking to page describing an XML element or
attribute from an XML schema, pending the publication of a formal ontology.


[JSON-LD]:(https://json-ld.org/spec/latest/json-ld/)
[JSON-LD 1.1]:(https://json-ld.org/spec/latest/json-ld/)
[BagIt]: https://en.wikipedia.org/wiki/BagIt
[samples]: ./samples
[DataCite Schema v4.0]: https://schema.datacite.org/meta/kernel-4.0/metadata.xsd
[BagIt profile]: https://github.com/ruebot/bagit-profiles
[CURIE]: https://www.w3.org/TR/curie/
[keyword]: http://schema.org/keywords
[about]: http://schema.org/about
[name]: http://schema.org/name
[creator]: http://schema.org/creator
[Person]: http://schema.org/Person
[schema:Collection]: http://schema.org/Collection
[identifier]: http://schema.org/identifier
[funder]: http://schema.org/funder
[relatedItem]: http://schema.org/relatedItem
[Organization]: http://schema.org/Organization
[Dataset]: http://schema.org/Dataset
[File]: http://schema.org/MediaObject
[path]: http://schema.org/contentUrl
[schema:contentUrl]: http://schema.org/contentUrl
[schema:MediaObject]: http://schema.org/MediaObject
[ScholarlyArticle]: http://schema.org/ScholarlyArticle
[CreativeWork]: http://schema.org/CreativeWork
[SoftwareApplication]: http://schema.org/SoftwareApplication
[hasPart]: http://schema.org/hasPart
[memberOf]: http://schema.org/memberOf
[funder]: http://schema.org/funder
[encodingFormat]: http://schema.org/encodingFormat
[accountablePerson]: http://schema.org/accountablePerson
[datePublished]: http://schema.org/datePublished
[license]: http://schema.org/license
[contact]: http://schema.org/accountablePerson
[contributor]: http://schema.org/contributor
[contentLocation]: http://schema.org/contentLocation
[copyrightHolder]: http://schema.org/copyrightHolder
[Place]: http://schema.org/Place
[description]: http://schema.org/description
[geo]: http://schema.org/geo
[agent]: http://schema.org/agent
[instrument]: http://schema.org/instrument
[sameAs]: http://schema.org/sameAs
[GeoCoordinates]: http://schema.org/GeoCoordinates
[email]: http://schema.org/email
[phone]: http://schema.org/phone
[affiliation]: http://schema.org/affiliation
[givenName]: htts://schema.org/givenName
[familyName]: http://schema.org/familyName
[publisher]: http://schema.org/publisher
[translator]: http://schema.org/translator
[thumbnail]: http://schema.org/thumbnail
[translationOf]: http://schema.org/translationOf
[Product]: http://schema.org/Product
[contactPoint]: http://schema.org/contactPoint
[contactType]: http://schema.org/contactType
[ContactPoint]: http://schema.org/ContactPoint
[CreateAction]: http://schema.org/CreateAction
[result]: http://schema.org/result
[event]: http://schema.org/event
[object]: http://schema.org/object
[error]: http://schema.org/error
[UpdateAction]: http://schema.org/UpdateAction
[Action]: http://schema.org/Action
[IndividualProduct]: http://schema.org/IndividualProduct
[distribution]: http://schema.org/distribution
[DataDownload]: http://schema.org/DataDownload
[Exif]: https://en.wikipedia.org/wiki/Exif
[DataCrate BagIt Profile]: https://raw.githubusercontent.com/UTS-eResearch/datacrate/master/spec/1.0/profile-datacrate-v1.0.json
[DataCrate JSON-LD Context]: ./context.json
[JSON-LD Framing 1.1]: https://json-ld.org/spec/latest/json-ld-framing/
[DataCrate JSON-LD frame]: ./frame.json
[geonames]: https://www.geonames.org
[RDFa]: https://en.wikipedia.org/wiki/RDFa
[schema.org]: http://schema.org
[DCAT]: https://www.w3.org/TR/vocab-dcat/
[DataCite]: https://www.datacite.org/
[SPAR]: https://www.sparontologies.net/
[FRAPO]: https://www.sparontologies.net/ontologies/frapo
[PCDM]: https://github.com/duraspace/pcdm/wiki
[pcdm:Collection]: https://pcdm.org/2016/04/18/models#Collection
[hasFile]: https://pcdm.org/2016/04/18/models#hasFile
[RepositoryCollection]: https://pcdm.org/2016/04/18/models#Collection
[RepositoryObject]: https://pcdm.org/2016/04/18/models#Object
[isOutputOf]: https://sparontologies.github.io/frapo/current/frapo.html#d4e526
[ResearchObject]: https://www.researchobject.org/
[Flattened Document Form]: https://json-ld.org/spec/latest/json-ld/#flattened-document-form
[Pairtree]: https://confluence.ucop.edu/display/Curation/PairTree
[Pairtree specification]: https://confluence.ucop.edu/display/Curation/PairTree?preview=/14254128/16973838/PairtreeSpec.pdf
[linked data]: https://en.wikipedia.org/wiki/Linked_data
[ORCID]: https://orcid.org
[JSON Object]: https://w3c.github.io/json-ld-syntax/#terminology
[BIBO]: http://purl.org/ontology/bibo/interviewee
