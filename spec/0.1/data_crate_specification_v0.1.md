# DataCrate Specification version pre 0.1

This is a work in progress, any and all details of this spec could change,
including its very existence.

## Introduction

This document specifies a method of packaging research data based on [BagIt]
with additional metadata in a human-and-machine-readable format based on
[schema.org] linked-data.

A DataCrate:

*  Is a BagIt bag conforming to the DataCrate [BagIt profile] [https://raw.githubusercontent.com/UTS-eResearch/datacrate/develop/spec/0.1/profile-datacrate-v0.1.json],   

*  With a CATALOG.html tag file in the bag directory which describes the files
   and directories in the BagIt payload (/data) directory. The CATALOG.html file
   has  with information about individual files and directories, such as their
   title, license, authorship or other provenance information, in RDFa format.

*  If there is enough metadata, and the dataset has a DOI, a DataCite metadata
   file and a human-readable citation in CATALOG.html (TODO: need to work on the
   markup for this).

This specification is a practical guide for software authors to create tools for
generating and consuming research packages. The format is not optimised for
hand-authoring, it is optimised for maximum convenience for data consumers via
the use of HTML, for users to read, and JSON-LD for programmers to write code
toprocess DataCrates.

If someone unpacks a DataCrate from a zip or tar file, or comes
across one on the web, that the package is as self documenting as possible. The
CATALOG.html files are in [RDFa] format with (mostly) schema.org based metadata
and thus are readable by both humans and machines. Anyone with a Data Crate zip
file can unzip it, notice that there is a CATALOG.html file in the root
directory, open that in a web browser, and find out about the data; where it
came from, who to contact about it, if it has a homepage and so on.

Because DataCrates could be created at many different stages in the research
data lifecycle there is a very minimal mandated minimum set of metadata, the
only compulsory metadata is a creation date, a contact person (or role) and a
description of the data. Future versions of this specification will allow for
profiles to be specified that are appropriate to a range of services.

There are a number of existing metadata and packaging standards in this area but
no single spec joining packaging with linked-data-ready metadata standard that
covers the range of common concerns for research data, particularly in an
academic context where it is important to be able to associate data with funded
research projects and publications.

## Defintions

*DataCrate Microdocument* refers to a fragment of [RDFa] used to describe an
entity such as a person, organization, project or grant.

## Conventions

Different forms of RDF terms and URIS are used in different places.

Throughout this specification, terms will be referred to using the *property
schema:name* or the *schema:name property*.

In the CATALOG.json document, terms are used using their JSON-LD names as
defined in telephone context.



## Core metadata standard for DataCrate: Schema.org

Schema.org is the base metadata standard for DataCrate with a few additions
where there are gaps its ability to represent needed metadata.  Future versions
of this spec may be based on other metadata standards.

Additional metadata can be drawn from any RDF vocabulary to allow open-ended
extensibility.

## Coverage

This specification has guidelines for ways to represent:

* Contact information for a data set, with heuristics for creating BagIt
  metadata from this information.

* Descriptive information for a dataset and the files within it such as a
  text description, spatial and temporal coverage.

* Associated Publications.

* Funding relationships.

* Provenance information of various kinds; who (people and organisations) and
  what (instruments and computer programs) created or contributed to the data
  set and individual files within it.

* If there is enough metadata, to a DataCite citation for the crate.


## Compromises / limitations of schema.org

One useful item of metadata for research data is associating it with a research
project, but Schema.org has no class for a project, although it does have a
schema:Funder property. DataCrate uses a an entity of type of both
schema:Organisation AND vivo:Project  to represent a research project, which MAY
have a schema:funder property or properties.

Likewise, schema.org does not have a good way of representing research equipment
so  DataCrate uses the vivo:Equipment class.

Schema.org has no class for a file format, so we have used this W3C class:
https://www.w3.org/ns/formats/vocab-data/Format.

A schema:Dataset has no contactPoint property, unlike DCAT. DataCrate uses
schema:accountablePerson for a contact point, similar to the concept
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
           |   CATALOG.html
           |   CATALOG.json
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
>

The bag-info.txt for version 0.1 of a DataCrate MUST contain the following
metadata:

``` BagIt-Profile-Identifier:
https://raw.githubusercontent.com/UTS-eResearch/datacrate/develop/spec/0.1/profile-datacrate-v0.1.json
DataCrate-Specification-Identifier:
https://github.com/UTS-eResearch/datacrate/blob/develop/spec/0.1/data_crate_specification_v0.1.md
```

## CATALOG.html and CATALOG.json

CATALOG.html must be an RDFa document. CATALOG.json MUST be a JSON document
which has the same information (TODO - I don't know how to express JSON
equivalence) as CATALOG.html via the following process.

* Parse CATALOG.html using an RDFa parser.
* Use the following JSON-LD frame to organise the document.

```
{
  "@explicit": true,
  "Related": {
    "@type": "ScholarlyArticle",
    "@embed": "@always"
  },
  "@context": {
    "License": "schema:license",
    "fileOf": "pcdm:fileOf",
    "Subject": "schema:subject",
    "Related": "schema:relatedLink",
    "DatePublished": "schema:datePublished",
    "dct": "http://purl.org/dc/terms/",
    "Person": "schema:Person",
    "Creator": "schema:creator",
    "Interviewee": "bibo:interviewee",
    "MediaObject": "schema:MediaObject",
    "Funder": "schema:Funder",
    "Title": "schema:name",
    "Equipment": "vivo:Equipment",
    "rdf": "http://www.w3.org/1999/02/22-rdf-syntax-ns#",
    "Box": "schema:Box",
    "formats": "http://www.w3.org/ns/formats/",
    "ScholarlyArticle": "schema:ScholarlyArticle",
    "SoftwareApplication": "schema:SoftwareApplication",
    "Object": "pcdm:Object",
    "Email": "schema:email",
    "Longitude": "schema:longitude",
    "GeoCoordinates": "schema:GeoCoordinates",
    "cc": "http://creativecommons.org/ns#",
    "MemberOf": "pcdm:memberOf",
    "Contributor": "schema:contributor",
    "ContentLocation": "schema:contentLocation",
    "Identifier": "schema:identifier",
    "encodingFormat": "schema:encodingFormat",
    "geo": "schema:geo",
    "pcdm": "http://pcdm.org/models#",
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "foaf": "http://xmlns.com/foaf/0.1/",
    "SpatialCoverage": "schema:spatialCoverage",
    "Translator": "schema:translator",
    "TemporalCoverage": "schema:TemporalCoverage",
    "Affiliation": "schema:affiliation",
    "Description": "schema:description",
    "Dataset": "schema:Dataset",
    "fileFormat": "schema:fileFormat",
    "Latitude": "schema:latitude",
    "FamilyName": "schema:familyName",
    "Phone": "schema:telephone",
    "Publisher": "schema:publisher",
    "Organization": "schema:Organization",
    "Project": "vivo:Project",
    "GeoShape": "schema:GeoShape",
    "Format": "formats:Format",
    "GivenName": "schema:givenName",
    "Place": "schema:Place",
    "Contact": "schema:accountablePerson",
    "Name": "schema:name",
    "schema": "http://schema.org/",
    "HasPart": "schema:hasPart",
    "CreativeWork": "schema:CreativeWork",
    "bibo": "http://purl.org/ontology/bibo/",
    "HasFile": "pcdm:hasFile",
    "Collection": "pcdm:Collection",
    "vivo": "http://vivoweb.org/ontology/core#",
    "ID": "schema:identifier",
    "HasMember": "pcdm:hasMember",
    "Keywords": "schema:keywords",
    "rdfa": "http://www.w3.org/ns/rdfa#"
  },
  "Contact": {
    "@type": "Person",
    "@embed": "@always"
  },
  "ContentLocation": {
    "@type": "Place",
    "@embed": "@always"
  },
  "HasPart": {
    "@embed": "@last"
  },
  "Contributor": {
    "@type": "Project",
    "@embed": "@always"
  },
  "@type": "Dataset",
  "Description": {
    "@embed": "@always",
    "@id": "CATALOG.html"
  }
}
```

## About CATALOG.html

CATALOG.html MUST contain an RDFa element which wraps metadata about the whole
data set (schema:Dataset). The div element is used in this example, but any
HTML5 element is allowed as long as it is expressed in well-formed HTML, this
applies to all the examples below.

```
    <div typeof='http://schema.org/Dataset'
          about='data'>
       <span property='http://schema.org/description'>
           This dataset is doesn't really exist
       </span>
       ...    
    </div>
```

RDFa types and properties in CATALOG.html MUST be expressed as full URIs. That
is, use typeof='http://schema.org/Dataset' rather than typeof='schema:Dataset'
or typeof='Dataset' to enable snippets of DataCrate markup to be copied an
pasted without losing context.

This corresponds to to the following JSON-LD, with the DataCrate context. The
context MUST be included as below (ordering is not meaningful) and MAY contain
additional items.

From now on the @context will be omitted from examples.

```
{
  "@context":
  {
    # TODO paste in latest one

  },
  "@graph": [
    {
      "@id": "data",
      "@type": "Dataset",

      "Description": "This dataset is doesn't really exist"
    }
  ]
}

```


DataCrate uses schema.org to indicate the structure of a dataset via a
structural convention that each file, and each other entity (Person,
Organization, item of equipment etc) is represented by a microdocument of type
schema:CreativeWork (examples follow below).

Note that the version of schema.org current at the time of
writing (version 3.2) doesn't natively support nested datasets or have a
mechanism to bundle data about files and associated entities.

If there are files in the root of the payload (/data) directory the file MAY be
described by a schema:CreativeWork micro-document related to the data.

NOTE: Describing every file in the payload directory is possible with DataCrate,
but where there are too many files to describe it is not mandatory, although
files must still be listed in the BagIt manifest. Future version of this
specification will allow for profiles - using which it will be possible to
specify constraints such as "every file must have a schema:description".

A convenient way to represent a member file/CreativeWork in HTML is as a table
row. In the context of the markup above this adds a member to the
schema:Dataset. For example, in the context of the above schema:Dataset a table
of files MAY begin with:

```
<table>
<tr>
    <th>Filename</th>
    <th>Description</th>
    <th>Format</th>
    <th>Equipment</th>
    <th>Creator</th>
    <th>Contributor</th>
    <th>License</th>
</tr>
```

An entry describing a file as a table row would begin:

```
<tr href='./data/wcc08_archencentre2_traj.txt'  
    typeof='http://schema.org/Creativework'
    property='pdcm:hasMember'>
```

The first cell.
```
<td>
  <div property='http://pcdm.org/models#hasFile' typeof='http://pcdm.org/models#File' href='./wcr03_victoria_arch_3cm_shape.ply'>
   <a  href='./data/wcr03_victoria_arch_3cm_shape.ply'>wcr03_victoria_arch_3cm_shape.ply</a>
   <details> ... </details>
  </div>
</td>
```

Within the div describing the file above, technical metadata about the file
MAY be included, for example:

```
<details>
  <table>
    <tr>
        <th>File format</th>
        <td property='http://schema.org/encodingFormat'>Polygon File Format</td>
    </tr>
    <tr>
        <th>Format version</th>
        <td property='http://schema.org/encodingFormat'
             href='http://www.nationalarchives.gov.uk/PRONOM/fmt/831' typeof='http://www.w3.org/ns/formats/Format'>
           <a href='http://www.nationalarchives.gov.uk/PRONOM/fmt/831'>PLY</a>
        </td>
    </tr>
    <tr>
        <th>Size</th>
        <td property='http://schema.org/contentSize'>591006805</td>
    </tr>
  </table>
</details>
```

The most important information to include is a schema:encodingFormat property,
with a reference to a description of the format. URLs SHOULD reference
Pronom-defined formats if possible, but for data which is in a local or ad-hoc
format the format SHOULD be defined locally, or another informative URI SHOULD
be used.


The second cell in a row:

```
<td>
  <div  property='http://schema.org/description'>
       Victoria Arch, bentwing trajectory data
  </div>
</td>
```

The corresponding JSON-LD for the above would be:

"@graph": [
  {
    "@id": "data",
    "@type": [
      "Dataset"
    ],
    "hasPart": [
      {
        "@id": "data/wcc08_archencentre2_traj.txt",
        "@type": "CreativeWork",
        "Description": "Victoria Arch centre, bentwing trajectory data",
      },

The ID data/datawcc08_archencentre2_traj.txt is a path URI, which references a file
relative to CATALOG.html in the root directory.


An example of a file description follows:

```
<tr href='./wcr03_victoria_arch_3cm_shape.ply'  typeof='http://pcdm.org/models#Object' property='pdcm:hasMember'><td><span property='http://pcdm.org/models#hasFile' typeof='http://pcdm.org/models#File' href='./wcr03_victoria_arch_3cm_shape.ply'>
 <a  href='./wcr03_victoria_arch_3cm_shape.ply'>wcr03_victoria_arch_3cm_shape.ply</a>
<details><table><tr>
    <th>File format</th>
    <td property='schema:encodingFormat'>Polygon File Format</td>
</tr>
<tr>
    <th>Format version</th>
    <td property='schema:encodingFormat' href='http://www.nationalarchives.gov.uk/PRONOM/fmt/831' typeof='http://www.w3.org/ns/formats/Format'><a href='http://www.nationalarchives.gov.uk/PRONOM/fmt/831'>PLY</a></td>
</tr>
<tr>
    <th>Mime</th>
    <td property='schema:fileFormat'>None</td>
</tr>
<tr>
    <th>Size</th>
    <td property='schema:contentSize'>591006805</td>
</tr>
</table></details>
</span>
</td>
<td><div  property='http://schema.org/description'>Victoria Arch point file data, sampled to 3cm resolution.</div>
</td>
<td></td>
<td><a href='#bentwing'>bentwing</a></td>
<td><div href='#Robert%20Zlot' property='http://schema.org/creator'><a href='#Robert%20Zlot'>Robert Zlot</a></div>
</td>
<td><div href='#Lukas%20Kaul' property='http://schema.org/contributor'><a href='#Lukas%20Kaul'>Lukas Kaul</a></div>
</td>
<td><div  property='http://schema.org/license'>CSIRO: available for research and academic purposes.</div>
</td>

</tr>


```

The corresponding JSON-LD is:
```
{
  "@id": "data/wcr03_victoria_arch_3cm_shape.ply",
  "@type": ["CreativeWork", "MediaObject"]
    "schema:contentSize": "591006805",
    "schema:encodingFormat": [
      "Polygon File Format",
      {
        "@id": "http://www.nationalarchives.gov.uk/PRONOM/fmt/831"
      }
    ],
    "fileFormat": "None"

  "Contributor": {
    "@id": "#Lukas%20Kaul"
  },
  "Creator": {
    "@id": "#Robert%20Zlot"
  },
  "Description": "Victoria Arch point file data, sampled to 3cm resolution.",
  "License": "CSIRO: available for research and academic purposes."
},

```

### Describing a directory but not files

To describe a directory, but not the files in it use markup like this:



## Minimum metadata summary

A DataCrate MUST have a root schema:Dataset with ID of "./data" or "data" with:
 *  a schema:description property describing the whole dataset.

 *  A creation date in ISO data format for the dataset using
    schema:dataModified, equivalent semantically to bagging-date in BagIt.

 *  A contact person (or role) expressed via a property schema:accountablePerson
    on the schema:Dataset of with a value of type schema:Person, with  at least
    one of the following properties:
    *  schema:email (text)
    *  schema:phone (text)
    *  schema:affiliation - with a text value or a schema:Organisation.

## How to represent various kinds of metadata

### Contacts, Creators and contributors: People

A core principle of Linked data is to use URIs as to identify things such as
people  Using linked data, in Schema.org we could use an ORCiD
identifier.  The following is the minimum recommended way of representing a
schema:creator in a data crate. This property MAY be applied in the context of a schema:Dataset or to a file (schema:MediaObject).

```
<a href='http://orcid.org/0000-0002-8367-6908'    
    property='http://schema.org/creator'>
      J. Xuan
</a>
```

This uses the prefered type ID for researchers, an ORCiD to unambiguously
identify an author. However, it does not provide very much information about the
author in the DataCrate so it is recommended to have an entry for each person
who contributed to the data or publications mentioned in the crate, and for the
the organisations concerned. Creators SHOULD represented as entities in their
own right, so that author details are kept in one place.


### Organizations

Organisations SHOULD be represented as entities. For example:

```
<div typeof='http://schema.org/Dataset' about='./data'>
...
<h2>Organizations</h2>
<table>
<tr>
    <th>ID</th>
    <th>Name</th>
    <th>City</th>
    <th>TYPE:</th>
</tr>

<tr href='http://uts.edu.au'
    id='http://uts.edu.au'  
    typeof='schema:CreativeWork http://schema.org/Organization'
    property='schema:hasPart'>    
    <td><div  property='http://schema.org/identifier'><a href='http://uts.edu.au'>http://uts.edu.au</a></div>
</td>
    <td><div  property='http://schema.org/name'>University of Technology Sydney</div>
</td>
    <td><div>Sydney</div></td>
    <td><div>Organization</div></td>

</tr>

<tr href='http://shu.edu.cn/' id='http://shu.edu.cn/'  typeof='schema:CreativeWork http://schema.org/Organization' property='schema:hasPart'>    <td><div  property='http://schema.org/identifier'><a href='http://shu.edu.cn/'>http://shu.edu.cn/</a></div>
</td>
    <td><div  property='http://schema.org/name'>Shanghai University</div>
</td>
    <td><div>Shanghai</div></td>
    <td><div>Organization</div></td>

</tr>

</table>
```

### Publications

The following is an example of marking-up a publication, using a microdocument
with type schema:ScholarlyArticle. In this example the ID of the document is a
DOI URL (http://dx.doi.org/10.1109/TCYB.2014.2386282). IDs for publications
SHOULD be DOI URLs where possible.

This publication microdocument references people, described in an example above
with the schema:creator property.

```
<div typeof='http://schema.org/Dataset' about='./data'>
...
<h2>Publications</h2>
<table>
<tr>
    <th>ID</th>
    <th>Title</th>
    <th>Creator</th>
    <th>ISSN</th>
    <th>Journal name</th>
    <th>Issue</th>
    <th>Year</th>
    <th>TYPE:</th>
</tr>

<tr href='http://dx.doi.org/10.1109/TCYB.2014.2386282'
    id='http://dx.doi.org/10.1109/TCYB.2014.2386282'  
    typeof='schema:CreativeWork http://schema.org/ScholarlyArticle'
    property='schema:hasPart'>    
  <td><div  property='http://schema.org/identifier'><a href='http://dx.doi.org/10.1109/TCYB.2014.2386282'>http://dx.doi.org/10.1109/TCYB.2014.2386282</a></div>
</td>
    <td><div  property='http://schema.org/name'>Topic Model for Graph Mining</div>
</td>
    <td><div href='http://orcid.org/0000-0002-8367-6908' property='http://schema.org/creator'><a href='#http://orcid.org/0000-0002-8367-6908'>J. Xuan</a></div>
<br/>
<div href='http://orcid.org/0000-0003-0690-4732' property='http://schema.org/creator'><a href='#http://orcid.org/0000-0003-0690-4732'>J. Lu</a></div>
<br/>
<div href='http://orcid.org/0000-0003-3960-0583' property='http://schema.org/creator'><a href='#http://orcid.org/0000-0003-3960-0583'>G. Zhang</a></div>
<br/>
<div href='https://orcid.org/0000-0002-6953-3986' property='http://schema.org/creator'><a href='#https://orcid.org/0000-0002-6953-3986'>X. Luo</a></div>
</td>
    <td><div>2168-2267</div></td>
    <td><div>IEEE Transactions on Cybernetics</div></td>
    <td><div></div></td>
    <td><div>2015</div></td>
    <td><div>ScholarlyArticle</div></td>

</tr>

</table>
```

#### Relating a publication to a file

A reference a publication related to a DataSet, or a directory or file MUST use
the property schema:relatedItem, pointing to an entity with a type
"ScholarlyArticle". Building on the examples of Organizations, People and
Publications above, relating the publication
http://dx.doi.org/10.1109/TCYB.2014.2386282 to a dataset looks like:

```
<div typeof='http://schema.org/Dataset' about='./data'>
<tr>
  <th>Related</th>
  <td><div href='http://dx.doi.org/10.1109/TCYB.2014.2386282'
           property='http://schema.org/relatedLink'>
           <a href='#http://dx.doi.org/10.1109/TCYB.2014.2386282'>
              Topic Model for Graph Mining
           </a>
      </div>
  </td>
</tr>
```

Putting together the examples for People, Organizations and Publications the
RDFa examples translate to the following JSON-LD, which has an entity of type
schema:Dataset, with a relatedLink to an entity of type ScholarlyArticle with
four creators, each with an affiliation.  The JSON-LD MUST be framed using the
DataCrate JSON-LD Frame so that it is laid-out in a tree structure like this:

"@graph": [
  {
    "@type": "Dataset",
    "@id": "./",
    "Related": {
      "@id": "http://dx.doi.org/10.1109/TCYB.2014.2386282",
      "Name": "Topic Model for Graph Mining",
      "@type": [
        "ScholarlyArticle",
        "CreativeWork"
      ],
      "Creator": [
        {
          "@id": "http://orcid.org/0000-0003-3960-0583",
          "Name": "G. Zhang",
          "@type": [
            "Person",
            "CreativeWork"
          ],
          "ID": "http://orcid.org/0000-0003-3960-0583",
          "Affiliation": {
            "@id": "http://uts.edu.au",
            "Name": "University of Technology Sydney",
            "@type": [
              "Organization",
              "CreativeWork"
            ],
            "ID": "http://uts.edu.au"
          }
        },
        {
          "@id": "http://orcid.org/0000-0002-8367-6908",
          "Name": "J. Xuan",
          "@type": [
            "Person",
            "CreativeWork"
          ],
          "ID": "http://orcid.org/0000-0002-8367-6908",
          "Affiliation": {
            "@id": "http://uts.edu.au",
            "Name": "University of Technology Sydney",
            "@type": [
              "Organization",
              "CreativeWork"
            ],
            "ID": "http://uts.edu.au"
          }
        },
        {
          "@id": "http://orcid.org/0000-0003-0690-4732",
          "Name": "J. Lu",
          "@type": [
            "CreativeWork",
            "Person"
          ],
          "ID": "http://orcid.org/0000-0003-0690-4732",
          "Affiliation": {
            "@id": "http://uts.edu.au",
            "Name": "University of Technology Sydney",
            "@type": [
              "Organization",
              "CreativeWork"
            ],
            "ID": "http://uts.edu.au"
          }
        },
        {
          "@id": "https://orcid.org/0000-0002-6953-3986",
          "Name": "X. Luo",
          "@type": [
            "Person",
            "CreativeWork"
          ],
          "ID": "https://orcid.org/0000-0002-6953-3986",
          "Affiliation": {
            "@id": "http://shu.edu.cn/",
            "Name": "Shanghai University",
            "@type": [
              "Organization",
              "CreativeWork"
            ],
            "ID": "http://shu.edu.cn/"
          }
        }
      ],
      "ID": "http://dx.doi.org/10.1109/TCYB.2014.2386282"
    }
```


### Funding and grants

To associate a research project (with or without funding) to a schema:Dataset,
use the Contributor property to reference an item of type vivo:Project.

```
<tr>
  <th>Contributor</th>
  <td><div href='https://github.com/UTS-eResearch/datacrate'
           property='http://schema.org/contributor'>
           <a href='#https://github.com/UTS-eResearch/datacrate'>
           DataCrate</a></div>
</td>
</tr>


<h2>Projects</h2>
<table>
<tr>
    <th>ID</th>
    <th>Name</th>
    <th>Description</th>
    <th>Funder</th>
</tr>

<tr href='https://github.com/UTS-eResearch/datacrate'
    id='https://github.com/UTS-eResearch/datacrate'  
    typeof='schema:CreativeWork' property='schema:hasPart'>    <td><div  property='http://schema.org/identifier'><a href='https://github.com/UTS-eResearch/datacrate'>https://github.com/UTS-eResearch/datacrate</a></div>
</td>
    <td><div  property='http://schema.org/name'>DataCrate</div>
</td>
    <td><div  property='http://schema.org/description'>The provisioner project is a</div>
</td>
    <td><div  property='http://schema.org/Funder'>UTS</div>
</td>

</tr>

</table>

```

Which yields this DataCrate-framed JSON-LD:

```
"@graph": [
  { "@id": "data",
    "Contributor": {
      "Name": "DataCrate",
      "Funder": "UTS",
      "Description": "... this is an example.",
      "@type": [
        "CreativeWork",
        "Project"
      ],
      "ID": "https://github.com/UTS-eResearch/datacrate",
      "@id": "https://github.com/UTS-eResearch/datacrate"
    },
```

### Publisher
Use the schema:Publisher property, referencing a schema:Organization.

### Date of publication
Use the schema:datePublised property with a date in ISO 8601 date format.

### Equipment

To associate a piece of equipment with a file, directory or dataset, use a
```schema:contributor property```, referencing a microdocument of type ```vivo:Equipment```.
This example shows how to associate equipment with ID of
https://confluence.csiro.au/display/ASL/Hovermap with a file

```
<tr href='./data/wcc02_arch_traj2.ply'
    id='./data/wcc02_arch_traj2.ply'  
    typeof='schema:CreativeWork schema:MediaObject'
    property='schema:hasPart'>    
...
<td>
<div href='https://confluence.csiro.au/display/ASL/Hovermap' property='http://schema.org/contributor'><a href='#https://confluence.csiro.au/display/ASL/Hovermap'>bentwing</a></div>
</tr>

...

<h2>Equipment</h2>
<table>
<tr>
    <th>ID</th>
    <th>Name</th>
    <th>Description</th>
    <th>TYPE:</th>
</tr>

<tr href='https://confluence.csiro.au/display/ASL/Hovermap'
    id='https://confluence.csiro.au/display/ASL/Hovermap'  
    typeof='schema:CreativeWork http://vivoweb.org/ontology/core#Equipment'
    property='schema:hasPart'>    
    <td>
      <div  property='http://schema.org/identifier'>
       <a href='https://confluence.csiro.au/display/ASL/Hovermap'>
         https://confluence.csiro.au/display/ASL/Hovermap
       </a>
      </div>
</td>
    <td><div  property='http://schema.org/name'>bentwing</div>
</td>
    <td>
      <divproperty='http://schema.org/description'>The CSIRO bentwing ...</div>
</td>
    <td><div>Equipment</div></td>
</tr>
</table>



```

This results in the following DataCrate Framed JSON-LD:
```
{
  "@id": "wcc02_arch_traj2.ply",
  "Contributor": [
    {
      "@id": "https://confluence.csiro.au/display/ASL/Hovermap"
    }
}
...

{
  "@id": "https://confluence.csiro.au/display/ASL/Hovermap",
  "ID": "https://confluence.csiro.au/display/ASL/Hovermap",
  "@type": [
    "CreativeWork",
    "Equipment"
  ],
  "Name": "bentwing",
  "Description": "The CSIRO bentwing is ..."
}

```




### Places

To associate a Dataset, or a file or directory with a geographical location or
region, use a property of schema:contentLocation referring to a schema:Place.

For example, define a place, using a geonames ID.
```

<h2>Places</h2>
<table>
<tr>
    <th>ID</th>
    <th>URL</th>
    <th>Name</th>
    <th>Description</th>
    <th>TYPE:</th>
    <th>Address</th>
    <th>geo&gt;GeoCoordinates</th>
</tr>

<tr href='http://www.geonames.org/8152662/catalina-park.html'
    id='http://www.geonames.org/8152662/catalina-park.html'  
    typeof='schema:CreativeWork http://schema.org/Place'
    property='schema:hasPart'>    
    <td>
      <div  property='http://schema.org/identifier'>
      <a href='http://www.geonames.org/8152662/catalina-park.html'>
        http://www.geonames.org/8152662/catalina-park.html
      </a>
      </div>
   </td>
    <td>
      <div>
        <a href='https://en.wikipedia.org/wiki/Catalina_Park'>
          https://en.wikipedia.org/wiki/Catalina_Park
        </a>
      </div>
   </td>
    <td><div  property='http://schema.org/name'>Catalina Park</div>
</td>
    <td>
      <div  property='http://schema.org/description'>Catalina Park is ...</div>
    </td>
    <td><div>Place</div></td>
    <td><div>Katoomba, NSW</div></td>
    <td><div  property='http://schema.org/geo'
    typeof='http://schema.org/GeoCoordinates' ><div>Latitude: <span property='http://schema.org/latitude'>-33.7152</span></div><div>Longitude: <span property='http://schema.org/longitude'>150.30119</span></div></div>
</td>

</tr>

</table>

<div>
  <div typeof='http://schema.org/Dataset' about='data/.'>
<table>
...
<tr>
  <th>ContentLocation</th>
  <td><div href='http://www.geonames.org/8152662/catalina-park.html' property='http://schema.org/contentLocation'><a href='#http://www.geonames.org/8152662/catalina-park.html'>Catalina Park</a></div>
</td>
</tr>

```

Use any of the resources available in http://schema.org/geo to describe
places. Future profiles of DataCrate may mandate the use of a subset of these.

### Time

TODO: Add a section on locating data in time.

# Summary of metadata requirements

# Recommended IDs

Users of DataCrate SHOULD use the following IDs where possible:
*  For a DataCrate, a schema:identifier which SHOULD be a DOI URL.
*  For People participating the research projects: ORCiD identifiers.
*  For organizations including funders, pending a global identifier scheme for
   use the homepage URL for research organizations.
*  For items of type schema:Place: a geonames URI.

In the absence of the above, DataCrates SHOULD contain stable persistent URIs to
identify all entities wherever possible, such


# Datacite citations

If there is sufficient metadata in a DataCrate, it should contain a DataCite
citation DataCite.xml, in the bag directory compliant with the [DataCite Schema
v4.0]:

The minimum metadata required at the DataSet level is:
*  A schema:ID for the dataset which is a DOI URL.
*  At least one schema:Creator with a schema:firstName and schema:lastName name.
*  At least one schema:Name (which maps to a DataCite title).
*  A least one Publisher which SHOULD be an organization but may be a String value.

The mandatory resource type MUST be set to "DataCrate-v0.1".

TODO: Example


# Extending DataCrate

To extend DataCrate implementers SHOULD try to use schema-valid schema.org
properties and classes where possible.

[DataCite Schema v4.0]: https://schema.datacite.org/meta/kernel-4.0/metadata.xsd
[BagIt profile]: https://github.com/ruebot/bagit-profiles
