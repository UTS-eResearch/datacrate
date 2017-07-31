# DataCrate Specification version pre 0.1

This is a draft work in progress, any and all details of this spec could change,
including its very existence.

The samples and examples here have not been
verified.

While this file is still on the develop branch on
Github it will change frequently.



## Introduction

This document specifies a method of packaging research data based on [BagIt]
with additional metadata in a human-and-machine-readable format based on
[schema.org] linked-data.

A DataCrate is a dataset a set of files contained in a single directory:

*  Which is a [BagIt] bag conforming to the [DataCrate BagIt profile],   

*  With a CATALOG.html tag file in the root bag directory which describes the
   files and directories in the BagIt payload (/data) directory. The
   CATALOG.html has metadata about the dataset as a whole, and may have
   information about individual files and directories, such as their title,
   license, authorship or other provenance information. Metadata is expressed in
   RDFa format.

*  If there is enough metadata, and the dataset has a DOI, a DataCite metadata
   file and a human-readable citation in CATALOG.html (TODO: need to work on the
   markup for this).

This specification is a practical guide for software authors to create tools for
generating and consuming research packages. The format is not optimized for
hand-authoring, it is optimized for maximum convenience for data consumers via
the use of HTML, for users to read, and JSON-LD for programmers to write code
to process DataCrates.

The DataCrate specification aims to ensure that packages are as self documenting
as possible. The CATALOG.html files are in [RDFa] format with (mostly)
schema.org based metadata and thus are readable by both humans and machines.
Anyone with a Data Crate zip file can unzip it, notice that there is a
CATALOG.html file in the root directory, open that in a web browser, and find
out about the data; where it came from, who to contact about it, if it has a
homepage and so on. This is an improvement over simple zipping or bagging of
files or using XML-based metadata formats.

Because DataCrates could be created at many different stages in the research
data lifecycle there is a very minimal mandated minimum set of metadata, the
only compulsory metadata is:
*  a creation date
*  a contact person (which could be done by role) and a
*  description of the data.

Future versions of this specification will allow for profiles to be specified
that are appropriate to a range of services such as repositories, registries.
and research software applications.

There are a number of existing metadata and packaging standards in this area but
no single standard joining packaging with linked-data-ready metadata that covers
the range of common concerns for research data in an academic context, where it
is important to be able to associate data with funded research projects and
publications, and desirable to be able to describe the *contents* of a data
package, not just the data in aggregate.

## Examples

There are examples of DataCrates in the [samples] directory.

## Defintions

*Data entity*: A dataset, directory or file.

*Metadata entity*: A Person, Organization, Project, item of equipment, license
or any other *thing* that forms part of the metadata for a DataCrate.

*DataCrate Microdocument*: a fragment of [RDFa] used to describe a *data entity*
*or a metadata entity* such as a person, organization, project or grant.

*DataCrate Context*: The JSON-LD context which is used for this standard,
available [here](./context.json).

*DataCrate-Framed JSON-LD*: a JSON-LD document containing DataCrate metadata,
which has been framed using [JSON-LD Framing 1.1] and [The DataCrate JSON-LD frame].

*DataCrate JSON-LD Context*: A JSON-LD context that provides human-readable
labels for datacrate metadata. These labels are used in CATALOG.json which
contains *DataCrate-Framed JSON-LD*.

## Conventions

Different forms of RDF terms and URIs are used in different places.

Throughout this specification, terms will be referred to using [Qnames], eg the
*property schema:name* or the *[schema:name] property*. The prefix of each Qname
is defined in the [DataCrate JSON-LD Context].

In the CATALOG.json document, terms are used using their DataCrate JSON-LD names as
defined in the [DataCrate context].

In this document RDFa types and properties in CATALOG.html are be expressed as
full URIs. That is, we use ```typeof='http://schema.org/Dataset'``` rather than
```typeof='schema:Dataset'``` or ```typeof='Dataset'``` to enable snippets of DataCrate
markup to be copied an pasted without losing context.

## Core metadata standard for DataCrate: Schema.org

Schema.org is the base metadata standard for DataCrate with a few additions to
supplement there are gaps in schema.org's ability to represent needed metadata.
Future versions of this spec may be based on other metadata standards.

Additional metadata can be drawn from any RDF vocabulary to allow open-ended
extensibility.

## Summary of Coverage

This specification has guidelines for ways to represent:

* Contact information for a data set, with heuristics for creating BagIt
  metadata from this information.

* Descriptive information for a dataset and the files within it such as a
  text description, spatial and temporal coverage.

* Associated Publications.

* Funding relationships.

* Provenance information of various kinds; who (people and organizations) and
  what (instruments and computer programs) created or contributed to the data
  set and individual files within it.

* If there is enough metadata, to a DataCite citation for the crate.


## Compromises / limitations of schema.org

One crucial *metadata entity* is a research project. Schema.org has no class for
a project, although it does have a [schema:funder] property. DataCrate uses an
entity with at least two types, [schema:organization] AND [vivo:Project],  to
represent a research project, which MAY have a [schema:funder] property or
properties.

Likewise, schema.org does not have a way to represent research equipment so
DataCrate uses the [vivo:Equipment] class.

Schema.org has no class for a file format, so we have used this W3C class:
https://www.w3.org/ns/formats/vocab-data/Format.

A [schema:Dataset] has no contactPoint property, unlike DCAT. DataCrate uses
[schema:accountablePerson] for a contact point, similar to the concept
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

```
BagIt-Profile-Identifier: https://raw.githubusercontent.com/UTS-eResearch/datacrate/develop/spec/0.1/profile-datacrate-v0.1.json
DataCrate-Specification-Identifier: https://github.com/UTS-eResearch/datacrate/blob/develop/spec/0.1/data_crate_specification_v0.1.md
```

## CATALOG.html and CATALOG.json

CATALOG.html MUST be an RDFa document. CATALOG.json MUST be a *DataCrate-framed
JSON-LD* document which has the same RDF content as CATALOG.html via the
following process.

* Parse CATALOG.html using an RDFa parser.
* Use the [DataCrate JSON-LD frame] to organize the document as a tree with the
  DataSet at the root.

```
{
  "@context": {
    "Publisher": "schema:publisher",
    "DatePublished": "schema:datePublished",
    "HasPart": "schema:hasPart",
    "ID": "schema:identifier",
    "Identifier": "schema:identifier",
    "Description": "schema:description",
    "License": "schema:license",
    "Title": "schema:name",
    "Name": "schema:name",
    "Creator": "schema:creator",
    "Contributor": "schema:contributor",
    "Related": "schema:relatedLink",
    "Translator": "schema:translator",
    "Funder": "schema:Funder",
    "Person": "schema:Person",
    "Contact": "schema:accountablePerson",
    "Email": "schema:email",
    "Phone": "schema:telephone",
    "Dataset": "schema:Dataset",
    "fileFormat": "schema:fileFormat",
    "encodingFormat": "schema:encodingFormat",
    "TemporalCoverage": "schema:TemporalCoverage",
    "SpatialCoverage": "schema:spatialCoverage",
    "ContentLocation": "schema:contentLocation",
    "Keywords": "schema:keywords",
    "Subject": "schema:subject",
    "GivenName": "schema:givenName",
    "FamilyName": "schema:familyName",
    "HasFile": "pcdm:hasFile",
    "fileOf": "pcdm:fileOf",
    "MemberOf": "pcdm:memberOf",
    "HasMember": "pcdm:hasMember",
    "Object": "pcdm:Object",
    "Collection": "pcdm:Collection",
    "Place": "schema:Place",
    "Organization": "schema:Organization",
    "Affiliation": "schema:affiliation",
    "GeoShape": "schema:GeoShape",
    "GeoCoordinates": "schema:GeoCoordinates",
    "geo": "schema:geo",
    "Latitude": "schema:latitude",
    "Longitude": "schema:longitude",
    "Box": "schema:Box",
    "CreativeWork": "schema:CreativeWork",
    "MediaObject": "schema:MediaObject",
    "Project": "vivo:Project",
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
    "pcdm": "http://pcdm.org/models#",
    "rdf": "http://www.w3.org/1999/02/22-rdf-syntax-ns#",
    "rdfa": "http://www.w3.org/ns/rdfa#",
    "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
    "schema": "http://schema.org/",
    "vivo": "http://vivoweb.org/ontology/core#"
  },
  "@type": "Dataset",
  "@explicit": true,
  "ContentLocation": {
    "@type": "Place",
    "@embed": "@always"
  },
  "Contact": {
    "@type": "Person",
    "@embed": "@always"
  },
  "Contributor": {
    "@type": "Project",
    "@embed": "@always"
  },
  "Description": {
    "@id": "CATALOG.html",
    "@embed": "@always"
  },
  "Related": {
    "@type": "ScholarlyArticle",
    "@embed": "@always"
  },
  "HasPart": {
    "@embed": "@last"
  }
}
```

## About CATALOG.html

CATALOG.html MUST contain an RDFa element which wraps metadata about the whole
data set creating a DataCrate microdocument. The div element is used in this
example, but any HTML5 element MAY be used as long as it is expressed in
well-formed HTML. This applies to all the examples below.

```
    <div typeof='http://schema.org/Dataset'
          about='data'>
       <span property='http://schema.org/description'>
           This dataset is doesn't really exist
       </span>
       ...    
    </div>
```

This corresponds to to the following *DataCrate-framed JSON-LD*. The [DataCrate
JSON-LD Context] MUST be included inline as below (ordering is not meaningful)
and MAY contain additional items.

From now on the @context will be omitted from examples.

```
{
  "@context":
  {
      "Publisher": "schema:publisher",
      "DatePublished": "schema:datePublished",
      "HasPart": "schema:hasPart",
      "ID": "schema:identifier",
      "Identifier": "schema:identifier",
      "Description":  "schema:description",
      "License": "schema:license",
      "Title": "schema:name",
      "Name": "schema:name",
      "Creator": "schema:creator",
      "Contributor": "schema:contributor",
      "Related": "schema:relatedLink",
      "Translator": "schema:translator",
      "Funder": "schema:funder",
      "Person": "schema:Person",
      "Contact": "schema:accountablePerson",
      "Email": "schema:email",
      "Phone": "schema:telephone",
      "Dataset": "schema:Dataset",
      "fileFormat": "schema:fileFormat",
      "encodingFormat": "schema:encodingFormat",
      "TemporalCoverage": "schema:TemporalCoverage",
      "SpatialCoverage": "schema:spatialCoverage",
      "ContentLocation": "schema:contentLocation",
      "Keywords": "schema:keywords",
      "Subject": "schema:about",
      "GivenName":  "schema:givenName",
      "FamilyName": "schema:familyName",
      "HasFile": "pcdm:hasFile",
      "fileOf": "pcdm:fileOf",
      "MemberOf": "pcdm:memberOf",
      "HasMember": "pcdm:hasMember",
      "Object": "pcdm:Object",
      "Collection": "pcdm:Collection",
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
      "Project": "vivo:Project",
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
      "pcdm": "http://pcdm.org/models#",
      "rdf": "http://www.w3.org/1999/02/22-rdf-syntax-ns#",
      "rdfa": "http://www.w3.org/ns/rdfa#",
      "rdfs": "http://www.w3.org/2000/01/rdf-schema#",
      "schema": "http://schema.org/",
      "vivo": "http://vivoweb.org/ontology/core#"

  }
,
  "@graph": [
    {
      "@id": "data",
      "@type": "Dataset",

      "Description": "This dataset is doesn't really exist"
    }
  ]
}

```


DataCrate uses schema.org to indicate the structure of a schema:Dataset via a
structural convention that each *data entity* (Dataset, directory or file) and
each  *Metadata entity* (Person, Organization, item of equipment etc) is
represented by a *DataCrate Microdocument*. The root of the data structure MUST
be a microdocument (eg an HTML ```div``` element) with type [schema:Dataset].
Other *data entities* and *metadata entities* MUST be nested inside the
DataCrate Dataset. All nested *DataCrate microdocuments* have at least one type:
[schema:CreativeWork].  (examples follow below).

If there are files in the root of the payload (/data) directory each file MAY be
described by a [schema:CreativeWork] *DataCrate microdocument* related to the
schema:Dataset with a property [schema:hasPart].

NOTE: As per the BagIt standard, every file in the payload directory MUST be in
the BagIt Manifest.  Describing every file in the payload directory is possible
with DataCrate, but where there are too many files to describe it is not
mandatory.  Future versions of this specification will allow for profiles -
using which it will be possible to specify constraints such as "every file must
have a schema:description".

A convenient way to represent a *DataCrate microdocument* for a *data entity*
in HTML is as a table row. In the context of the markup above this adds a part
to the schema:Dataset. For example, in the context of the above [schema:Dataset]
a table of files MAY begin with:

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
    property='http:schema.org/hasPart'>
```

The first cell could be:

```
<td>
  <div property='http://schema.org/hasPart' typeof='http://schema.org/hasPart' href='./wcr03_victoria_arch_3cm_shape.ply'>
   <a  href='./data/wcr03_victoria_arch_3cm_shape.ply'>wcr03_victoria_arch_3cm_shape.ply</a>
   <details> ... </details>
  </div>
</td>
```

Within the div element describing the file above, technical metadata about the file
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

The most important information to include is a [schema:encodingFormat] property,
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
```
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
```

The ID data/datawcc08_archencentre2_traj.txt is a path URI, which references a file
relative to CATALOG.html in the root directory.


An example of a file description follows:

```
<tr href='./wcr03_victoria_arch_3cm_shape.ply'  typeof='http://schmea.org/MediaObject' property='pdcm:hasMember'><td><span property='http://schema.org/hasPart' typeof='http://schema.org/hasPart' href='./wcr03_victoria_arch_3cm_shape.ply'>
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

...

</tr>


```

The corresponding DataCrate JSON-LD is:
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


  "Description": "Victoria Arch point file data, sampled to 3cm resolution.",
  "License": "CSIRO: available for research and academic purposes."
},

```

### Describing a directory but not files

To describe a directory without listing all the files in it, DataCrate SHOULD
have a description of the directory. This DataCrate Microdocument, describes the
directory 'data/lots_of_little_files'.

```
<div>
  <h2 >Directory: lots_of_little_files</h2>
  <div>Collection metadata:
    <div typeof='http://schema.org/CreativeWork' about='data/lots_of_little_files'>
      <table><span rev='schema:hasPart' href='data/.'></span><tr>
        <th>Title</th>
        <td>
            <div  property='http://schema.org/name'>Too many files</div>
        </td>
       </tr>
      <tr>
      <th>Description</th>
      <td>
         <div  property='http://schema.org/description'>
           This directory contains many small files, that we’re not going to
           describe in detail.
         </div>
       </td>
       </tr>
     </table>
</div>
</div>
</div>

```


## How to represent various kinds of metadata

### Contacts, Creators and contributors: People

A core principle of Linked data is to use URIs as to identify things such as
people.  The following is the minimum recommended way of representing a
[schema:creator] in a data crate. This property MAY be applied in the context of
a [schema:Dataset] or to a file (schema:MediaObject).

```
<a href='http://orcid.org/0000-0002-8367-6908'    
    property='http://schema.org/creator'>
      J. Xuan
</a>
```

This uses the preferred type ID for researchers, an ORCID, to unambiguously
identify an author. However, it does not provide very much information about the
author in the DataCrate so CATALOG.html SHOULD have a DataCrate Microdocument
for each person who contributed to the data or publications mentioned in the
crate, and for the the organizations concerned. Creators SHOULD be represented
by DataCrate Microdocuments with two types: [schema:CreativeWork] and
[schema:Person].


### Organizations

An organization SHOULD be represented by a DataCrate microdocument with two types:
[schema:CreativeWork] and [schema:Organization]. For example:

```
<div typeof='http://schema.org/Dataset' about='data'>
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

This *DataCrate microdocument* about a publication references people, described
in an example above with the [schema:creator] property.

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
  <td>
  <div  property='http://schema.org/identifier'>
   <a href='http://dx.doi.org/10.1109/TCYB.2014.2386282'>http://dx.doi.org/10.1109/TCYB.2014.2386282</a></div>
   </td>
    <td>
       <div  property='http://schema.org/name'>Topic Model for Graph Mining</div>
    </td>
    <td>
       <div href='http://orcid.org/0000-0002-8367-6908'
             property='http://schema.org/creator'>
          <a href='#http://orcid.org/0000-0002-8367-6908'>J. Xuan</a>
       </div>

       <div href='http://orcid.org/0000-0003-0690-4732'
            property='http://schema.org/creator'>
           <a href='#http://orcid.org/0000-0003-0690-4732'>J. Lu</a>
       </div>

       <div href='http://orcid.org/0000-0003-3960-0583'
            property='http://schema.org/creator'>
         <a href='#http://orcid.org/0000-0003-3960-0583'>G. Zhang</a>
        </div>

      <div href='https://orcid.org/0000-0002-6953-3986'
           property='http://schema.org/creator'>
         <a href='#https://orcid.org/0000-0002-6953-3986'>X. Luo</a>
      </div>
</td>
    <td><div>2168-2267</div></td>
    <td><div>IEEE Transactions on Cybernetics</div></td>
    <td><div></div></td>
    <td><div>2015</div></td>
    <td><div>ScholarlyArticle</div></td>

</tr>

</table>
```

#### Relating a publication to a dataset

A reference a publication related to a DataSet, or a directory or file MUST use
the property [schema:relatedItem], pointing to an entity with a type
"ScholarlyArticle". Building on the examples of Organizations, People and
Publications above, relating the publication
http://dx.doi.org/10.1109/TCYB.2014.2386282 to could look like:

```
<div typeof='http://schema.org/Dataset' about='./data'>
...
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

Putting together the examples for People, Organizations and Publications we get
the following *DataCrate-framed JSON-LD*.

```
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

To associate a research project (with or without funding) to a [schema:Dataset],
use the Contributor property to reference an item of type [vivo:Project].

```
<div typeof='http://schema.org/Dataset' about='./data'>
...
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
    typeof='schema:CreativeWork' property='schema:hasPart'>    
    <td>
      <div  property='http://schema.org/identifier'>
        <a href='https://github.com/UTS-eResearch/datacrate'>
          https://github.com/UTS-eResearch/datacrate
        </a>
      </div>
   </td>
    <td>
      <div  property='http://schema.org/name'>DataCrate</div>
    </td>
    <td>
      <div  property='http://schema.org/description'>The provisioner project is a</div>
    </td>
    <td>
      <div  property='http://schema.org/Funder'>UTS</div>
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
Use the [schema:Publisher] property, referencing a schema:Organization.

### Date of publication
Use the [schema:datePublished] property with a date in ISO 8601 date format.

### Licensing

To attach a license to a *data entity* the entity SHOULD have a
[schema:license] property with a value of a DataCrate Microdocument that
describes a license. If this is not possible a URL MAY be used as the value.

### Equipment

To specify which equipment was used to create or contribute to a *data entity*,
the *DataCrate microdocument* for the entity SHOULD have a [schema:contributor]
property, with a value of a microdocument of type [vivo:Equipment].  This example
shows how to associate equipment with an ID of
"https://confluence.csiro.au/display/ASL/Hovermap" with a file
"data/wcc02_arch_traj2.ply":

```
<div typeof='http://schema.org/Dataset' about='./data'>
...
<tr href='data/wcc02_arch_traj2.ply'
    id='data/wcc02_arch_traj2.ply'  
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


This results in the following DataCrate-Framed JSON-LD:

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

To associate a *data entity* with a metadata entity representing a
*geographical location or region* the DataCrate microdocument describing the
entity SHOUD have a property of [schema:contentLocation] with a value of
[schema:Place].

This example shows how to define a place, using a [geonames] ID:

```
<div typeof='http://schema.org/Dataset' about='./data'>
...
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
    typeof='http://schema.org:CreativeWork http://schema.org/Place'
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

The DataCrate microdocument for a Place may use any of the resources available
in http://schema.org/geo to describe places. Future profiles of DataCrate may
mandate the use of a subset of these.

### Time

TODO: Add a section on locating data in time.


### Subjects & Keywords

Subject properties (equivalent to a Dublin Core Subject) on DataCrate
Microdocuments  MUST use the [schema:about] property.

Keyword properties MUST use [schema:Keyword].


## Minimum metadata summary

A DataCrate MUST have a root [schema:Dataset] with ID of "data" with:
 *  a [schema:description] property, which describes the whole dataset contained
    in the data (BagIt payload) directory.

 *  A creation date in ISO data format for the dataset using
    [schema:dataModified], equivalent semantically to bagging-date in BagIt.

 *  A contact person (or role) expressed via a property schema:accountablePerson
    on the [schema:Dataset] of with a value of type [schema:Person], with  at least
    one of the following properties.
    *  [schema:email] (text)
    *  [schema:phone] (text)
    *  [schema:affiliation] - with a text value or a schema:organization.

### BagIt metadata

BagIt metadata should be included in bag-info.txt where it is available as
follows in the Dataset object at the root of '@graph' in *DataCrate-framed JSON-LD*.

TODO: add to samples

```
Source-Organization:   ['Publisher']['Name']
Organization-Address:  ['Contact']['Publisher']['Name']
Contact-Name: ['Contact']['Name']
Contact-Phone: ['Contact']['Phone']
Contact-Email:  ['Contact']['Email']
External-Description: ['Description']
Bagging-Date:  To be entered by the software tool used to create the DataCrate
External-Identifier:  ['ID']
Bag-Size:  To be entered by the software tool used to create the DataCrate
Payload-Oxum:  TTo be entered by the software tool used to create the DataCrate

Bag-Group-Identifier: N/A
Bag-Count:  N/A
Internal-Sender-Identifier: N/A
Internal-Sender-Description: N/A
```

### Recommended IDs

Users of DataCrate SHOULD use the following IDs where possible:
*  For a DataCrate, a [schema:identifier] property which SHOULD be a DOI URL.
*  For People participating the research projects: ORCID identifiers.
*  For organizations including funders, pending a global identifier scheme for
   use the homepage URL for research organizations.
*  For items of type schema:Place: a geonames URI.
*  For file formats, use Pronom URLs, for example http://www.nationalarchives.gov.uk/PRONOM/fmt/831.

In the absence of the above, DataCrates SHOULD contain stable persistent URIs to
identify all entities wherever possible.


## Datacite citations

If there is sufficient metadata in a DataCrate, it should contain a DataCite
citation DataCite.xml in the root of the bag directory compliant with the [DataCite Schema
v4.0]:

To generate DataCite.xml a DataCrate MUST have the following at the  schema:Dataset
level:
*  A [schema:identifier] for the DataCrate which is a DOI URL.
*  At least one [schema:Creator] with a [schema:givenName] and [schema:familyName].
*  At least one [schema:Name] (which maps to a DataCite title).
*  A least one [schema:publisher] property which SHOULD be an organization but may be a String
   value.

The mandatory DataCite resource type MUST be set to "DataCrate-v0.1".

## Extending DataCrate

To extend DataCrate implementers SHOULD try to use schema-valid schema.org
properties and classes and MAY use terms form other widely-used and supported
vocabularies and ontologies when this is not possible.

[BagIt]: https://en.wikipedia.org/wiki/BagIt
[samples]: ./samples
[DataCite Schema v4.0]: https://schema.datacite.org/meta/kernel-4.0/metadata.xsd
[BagIt profile]: https://github.com/ruebot/bagit-profiles
[Qnames]: https://en.wikipedia.org/wiki/QName
[schema:keyword]: https://schema.org/keyword
[schema:about]: https://schema.org/about
[schema:creator]: https://schema.org/creator
[schema:Person]: https://schema.org/Person
[schema:identifier]: https://schema.org/identifier
[schema:relatedItem]: https://schema.org/relatedItem
[schema:Organization]: https://schema.org/Organization
[schema:Dataset]: https://schema.org/Dataset
[schema:MediaObject]: https://schema.org/MediaObject
[schema:CreativeWork]: https://schema.org/CreativeWork
[schema:hasPart]: https://schema.org/hasPart
[schema:funder]: https://schema.org/funder
[schema:encodingFormat]: https://schema.org/encodingFormat
[schema:contentSize]: https://schema.org/schema:encodingFormat
[DataCrate BagIt Profile]: https://raw.githubusercontent.com/UTS-eResearch/datacrate/develop/spec/0.1/profile-datacrate-v0.1.json
[DataCrate JSON-LD Context]: ./context.json
[JSON-LD Framing 1.1]: https://json-ld.org/spec/latest/json-ld-framing/
[The DataCrate JSON-LD frame]: ./frame.json
[geonames]: http://www.geonames.org
[RDFa]: https://en.wikipedia.org/wiki/RDFa
[schema.org]: http://schema.org
