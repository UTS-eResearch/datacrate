# DataCrate Specification version pre 0.1

This is a work in progress, any and all details of this spec could change,
including its very existence.

## Quick introduction

This specification is designed to be a practical guide for software authors to
create tools for generating and consuming research data packages with metadata
in both human and machine readable formats linked data principles (RDFa and
JSON-LD). The format is not optimised for hand-authoring, it is optimised for
maximum convenience for data consumers via hte use of HTML for readers and
JSON-LD for programmers to consume.

A DataCrate:

*  Is s BagIt bag conforming to the Data Crate BagIt profile (TODO),   

*  With a CATALOG.html tag file in the bag directory in RDFa format, which
   describes the files and directories in the BagIt payload (/data) directory,,
   with information about individual files and directories, such as their title,
   license, authorship or other provenance information.
   
The aim is that if someone unpacks a DataCrate from a zip file, or comes across
one on the web, that the package is as self documenting as possible. The
CATALOG.html files are in [RDFa] format and thus are readable by
both humans and machines. Anyone with a Data Crate zip file can unzip it,
notice that there is a CATALOG.html file in the root directory, open that in a
web browser, and find out about the data; where it came from, who to contact
about it, if it has a homepage and so on. 

Because DataCrates could be created at many different stages in the research
data lifecycle there is no mandated minimum set of metadata. Instead this
specification will attempt to allow for validation against a set of profiles
that are appropriate to a range of services. 
There are a number of existing metadata standards in this area but no single
linked-data ready metadata standard that covers the range of common concerns.
This specification does reference as many standards as it can with a view to
making crosswalks easy to build. 

## Core metadata standard for Data Crates: Schema.org

The linked-data metadata standard with the best coverage for generic
descriptions of data, including temporal and spatial coverage is schema.org. We
have had to supplement schema.org in only a few areas: we have
chosen a few terms from other vocaublaries where there are gaps in schema.org.

## Compromises / limitations of schema.org

Schema.org has no class for a project, which makes it difficult to describe
research data provenance. The work-around is to use a schema:Organisation to
represent a funded (or indeed unfunded) research project, which in can have a
schema:funder  properties with an additional type from the Vivo ontology.

Schema.org also has no class for a file format, so we have used this W3C class:
https://www.w3.org/ns/formats/vocab-data/Format.

A schema:Dataset has no contactPoint property (DCAT does) but we have used
schema:accountablePerson to mean something like "Corresponding Author".



This specification has guidelines for ways to represent:

* Contact information for a data set, with heuristics for creating BagIt
  metadata from this information.

* Descriptive information for a dataset and the files within it such as a
  text description, spatial and temporal coverage.

* DataCite metadata: the metadata used by the DataCite specification.

* Associated Publications.

* Funding relationships.

* Provenance information of various kinds; who (people and organisations) and
  what (instruments and computer programs) created or contributed to the data
  set and individual files within it. 

The README file contains:

* Information about the Data Crate file format and what version of this
  specification the Data Crate conforms to.

*  Basic *contact* information for the crate.

* If there is enough metadata, to a DataCite citation for the crate. (eventually, TODO)

* A link to more information about the folders and files in the Data Crate
  payload, in the data/CATALOG.html file.

## Introduction

The Bagit file-system structure is as follows.

```

           <base directory>/
           |   bagit.txt
           |   bag-info.txt
           |   manifest-<algorithm>.txt
           |   CATALOG.html
           |   CATALOG.json 
           |   
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

```
BagIt-Profile-Identifier Some-bagit-profile-id

```
TODO: provide an example of a CATALOG.html

```
A minimal CATALOG.html file
```

### CATALOG.json and CATALOG.html

The CATALOG.html file contains an RDFa element which wraps metadata about the
whole data set. The div element is used an example here, but you can use any
element you wish as long as it well-formed HTML.

    <div typeof='http://schema.org/Dataset'
          about='#dataset-example'> 
    ...  </div>

Which corresponds to to the following JSON-LD, with the DataCrate context.

From now on the @context will be ommitted from examples.

```
{
  "@context": {
    "ID": "schema:identifier",
    "Identifier": "schema:identifier",
    "Description": "schema:description",
    "License": "schema:license",
    "Title": "schema:name",
    "Name": "schema:name",
    "Creator": "schema:creator",
    "Contributor": "schema:contributor",
    "Funder": "schema:funder",
    "Person": "schema:person",
    "ContactPerson": "schema:accountablePerson",
    "Email": "schema:email",
    "Phone": "schema:telephone",
    "Dataset": "schema:Dataset",
    "fileFormat": "schema:fileFormat",
    "TemporalCoverage": "schema:TemporalCoverage",
    "SpatialCoverage": "schema:spatialCoverage",
    "ContentLocation": "schema:contentLocation",
    "Keywords": "schema:keywords",
    "Subject": "schema:subject",
    "GivenName": "schema:givenName",
    "FamilyName": "schema:familyName",
    "HasFile": "pcdm:hasFile",
    "MemberOf": "pcdm:memberOf",
    "HasMember": "pcdm:hasMember",
    "Object": "pcdm:object",
    "Place": "schema:Place",
    "GeoShape": "schema:GeoShape",
    "GeoCoordinates": "schema:GeoCoordinates",
    "geo": "schema:geo",
    "Latitude": "schema:latitude",
    "Longitude": "schema:longitude",
    "Box": "schema:Box",
    "CreativeWork": "schema:CreativeWork",
    "Project": "vivo:Project",
    "Format": "formats:Format",
    "formats": "http://www.w3.org/ns/formats/",
    "cc": "http://creativecommons.org/ns#",
    "dct": "http://purl.org/dc/terms/",
    "foaf": "http://xmlns.com/foaf/0.1/",
    "pcdm": "http://pcdm.org/models#",
    "rdf": "http://www.w3.org/1999/02/22-rdf-syntax-ns#",
    "rdfa": "http://www.w3.org/ns/rdfa#",
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "schema": "http://schema.org/",
    "vivo": "http://vivoweb.org/ontology/core#"
  },
  "@graph": [
    {
      "@id": "#dataset-example",
      "@type": [
        "Dataset",
      ],
      ...
    }
}

```

DataCrate uses schema.org to indicate the structure of a dataset via a
structural convention that each file, and each other entity (Person,
Organization) is represented by a micro-document of type schema:CreativeWork -
this is largely because schema.org (VERSION TODO) doesn't natively support
nested datasets.

If there are files in the root of the payload (/data) directory then each file
must be attached to a schema:CreativeWork via hasPart on the

For example, in the context of the above schema:Dataset a table of files might
begin with:



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

A convenient way to represent a member file/CreativeWork in HTML is as a table row. In
the context of the markup above this adds a member to the schema:Dataset

```
<tr href='#FILE_./wcc08_archencentre2_traj.txt'  typeof='http://schema.org/Creativework' property='pdcm:hasMember'>
```

Each piece of metadata can then be a cell, the second cell in a row could be:

```
<td>
  <div  property='http://schema.org/description'>Victoria Arch, bentwing trajectory data</div>
</td>
```

The corresponding JSON-LD for the above would be:

"@graph": [
  {
    "@id": "#259be016-4d5a-46ae-8f04-3ac46ccd24df",
    "@type": [
      "Dataset",
      "Collection"
    ],
    "HasMember": null,
    "ContactPerson": {
      "@id": "#Mike%20Lake"
   
    "pdcm:hasMember": [
      {
        "@id": "#FILE_./wcc08_archencentre2_traj.txt",
        "@type": "Object",
        "Description": "Victoria Arch centre, bentwing trajectory data",
      },

The ID #FILE_./wcc08_archencentre2_traj.txt could be any arbitrary URI, and
should not be interpreted as path. The ID "./wcr03_victoria_arch_3cm_shape.ply" in
the following example *is* a path URI, which references a file relative to the
payload (/data) directory where the CATALOG.html file resides.


```
<td>
  <div property='http://pcdm.org/models#hasFile' typeof='http://pcdm.org/models#File' href='./wcr03_victoria_arch_3cm_shape.ply'>
   <a  href='./wcr03_victoria_arch_3cm_shape.ply'>wcr03_victoria_arch_3cm_shape.ply</a>
  </div>
</td>
```

Within the div describing the file above, technical metadata about the file
should be included.

```
<details>
  <table>
    <tr>
        <th>File format</th>
        <td property='http://schema.org/encodingFormat'  >Polygon File Format</td>
    </tr>
    <tr>
        <th>Format version</th>
        <td property='http://schema.org/encodingFormat' href='http://www.nationalarchives.gov.uk/PRONOM/fmt/831' typeof='http://www.w3.org/ns/formats/Format'><a href='http://www.nationalarchives.gov.uk/PRONOM/fmt/831'>PLY</a></td>
    </tr>
    <tr>
        <th>Size</th>
        <td property='http://schema.org/contentSize'>591006805</td>
    </tr>
  </table>
</details>
```

The most important information to include is a schema:encodingFormat property,
with a reference to a description of the format. URLs that reference
Pronom-defined formats are preferred, but for data which is in a local or ad-hoc
format the format can be defined locally.  

```

```

In RDFa in the CATALOG.html the description of a complete PCDM Object, which
describes a file looks like this:

```
<tr href='#FILE_./wcr03_victoria_arch_3cm_shape.ply'  typeof='http://pcdm.org/models#Object' property='pdcm:hasMember'><td><span property='http://pcdm.org/models#hasFile' typeof='http://pcdm.org/models#File' href='./wcr03_victoria_arch_3cm_shape.ply'>
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

The corresponding programmer-friendly JSON-LD is:
```
{
  "@id": "#FILE_./wcr03_victoria_arch_3cm_shape.ply",
  "@type": "Object",
  "HasFile": {
    "@id": "wcr03_victoria_arch_3cm_shape.ply",
    "@type": "pcdm:File",
    "schema:contentSize": "591006805",
    "schema:encodingFormat": [
      "Polygon File Format",
      {
        "@id": "http://www.nationalarchives.gov.uk/PRONOM/fmt/831"
      }
    ],
    "fileFormat": "None"
  },
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

## Notes on representing linked data

### People

A core principle of Linked data is to use URIs as names for things. Lets take
the example of representing the creator of a data set:

```
<span property='http://schema.org/creator'>Robert Zlot</span>
```

In the above example the creator property has a potentially ambiguous string value
only. Using linked data, in Schema.org we could use an orcid identifier>. The
following is the minimum recommended way of representing a creator in a data
crate.

```
<span property='http://schema.org/creator' about='http://orcid.org/0000-0002-1672-552X'>Robert Zlot</span> 
```

However, in a data crate there may be multiple files, with multiple creators in
many-to-many relationships, so it is recommended that creators be represented as
entities in their own right, so that author details are kept in one place, and
the data crate can can contain more detailed information.







### Instruments


### Organisations


### Places


### Metadata conventions

Data Crate metadata is expressed in schema.org.

Data Crate metadata can be drawn from any RDF vocabulary to allow open-ended
extensibility, but defines some core properties that should be used for
interoperability 

A minimal DataCrate CATALOG.html file would need to have only the following elements.

*  A container that has two types: schema:Dataset and pcdm:Collection.
*  One or more PCDM Objects referencing one or more PCDM files.




TODO: We will publish a table mapping Datacrate metadata in schema.org to the
following standards:

1.  [DCAT] - Covers high level dissemination metadata
2.  [DataCite] - Data publication metadata
3.  [RIFCS] (TODO)
4.  [PCDM]
5.  Frictionless data

TODO: It would also be possible to align datacrate with Research Object Bundles.
