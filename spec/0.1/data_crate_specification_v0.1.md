# DataCrate Specification version 0.1

This is a draft work in progress, any and all details of this spec could change,
including its very existence.

The samples and examples here have not been exhaustively verified, so there may
be some inconsistencies between this spec and the associated files, such as the
[DataCrate JSON-LD Context]. Where there *is* a discrepancy, the file is the
authority rather than the spec.


## Introduction

This document specifies a method of packaging research data based on [BagIt]
with additional metadata in a human-and-machine-readable format based on
[schema.org] linked-data.

A DataCrate is a dataset a set of files contained in a single directory.

*  A DataCrate is a [BagIt] bag conforming to the [DataCrate BagIt profile],   

*  A DataCrate has a CATALOG.html tag file in the root directory which
   describes the files and directories in the BagIt payload (/data) directory.
   The CATALOG.html has metadata about the dataset as a whole, and MAY have
   information about individual files and directories, such as their title,
   license, authorship or other provenance information. Metadata is expressed in
   RDFa format.

*  A DataCrate has a CATALOG.json file in the root bag directory with a JSON-LD
   version of the RDFa Metadata in CATALOG.HTML, the metadata in this file is
   organized in a particular way via a JSON-LD frame, for easy processing. The
   metadata is redundant with the metadata in CATALOG.html.

*  If there is enough metadata, and the dataset has a DOI, a DataCrate has a
   DataCite metadata file and a human-readable citation in CATALOG.html.

This specification is a practical guide for software authors to create tools for
generating and consuming research data packages. The format is not optimized for
hand-authoring, it is optimized for maximum convenience for data consumers with
the use of HTML for users to read, for search engines with RDFa metadata
embedded in the HTML and JSON-LD for programmers.

The DataCrate specification aims to ensure that packages are as self documenting
as possible. The CATALOG.html files are in [RDFa] format with (mostly)
schema.org based metadata and thus are readable by both humans and machines.
Anyone with a Data Crate zip file can unzip it, notice that there is a
CATALOG.html file in the root directory, open that in a web browser, and find
out about the data; where it came from, who to contact about it, if it has a
homepage and so on, which is more usable than simple zipping or bagging of
files or using XML-based metadata formats.

In an academic context, it is important to be able to associate data with funded
research projects and publications, and desirable to be able to describe the
*contents* of a data package, not just the data in aggregate, so DataCrate
allows for file-level descriptions; to describe licensing, copyright
ownership and authorship and other metadata at a granular level.

Because DataCrates could be created at many different stages in the research
data lifecycle there is a very minimal mandated minimum set of metadata. The
only mandatory metadata is:
*  A creation date,
*  A contact person (which could be done by role) and
*  A human-readable description of the data.

Future versions of this specification may allow for profiles to be specified
that are appropriate to a range of services such as repositories, registries.
and research software applications.


## Examples

There are examples of DataCrates in the [samples] directory. 

## Defintions

*Data Entity*: A dataset, directory or file.

*MetaData Entity*: A Person, Organization, Project, item of equipment, license
or any other *thing* that forms part of the metadata for a DataCrate.

*DataCrate Microdocument*: a fragment of [RDFa] used to describe a *Data Entity*
*or a MetaData Entity* such as a person, organization, project or grant.

*DataCrate RDFa Document*: a complete description of a dataset in HTML + RDFa
format, which is to be saved in the bag root directory as CATALOG.html.

*DataCrate-Framed JSON-LD*: a JSON-LD document containing DataCrate metadata,
which has been framed using [JSON-LD Framing 1.1] and the [DataCrate JSON-LD frame].

*DataCrate JSON-LD Context*: A JSON-LD context that provides human-readable
labels for datacrate metadata. These labels are used in CATALOG.json which
contains *DataCrate-Framed JSON-LD*.


## Conventions

Throughout this specification, RDF terms are be referred to using [CURIE]s eg the
*property schema:name* or the *[schema:name] property*. The prefix of each CURIE
is defined in the [DataCrate JSON-LD Context].

In the CATALOG.json document, RDF terms use their DataCrate JSON-LD names
as defined in the [DataCrate JSON-LD context].

In a DataCrate RDFa Document RDFa types and properties in CATALOG.html are be
expressed as full URIs. That is, we use ```typeof='http://schema.org/Dataset'```
rather than ```typeof='schema:Dataset'``` or ```typeof='Dataset'``` to enable
snippets of DataCrate markup to be copied an pasted without losing context.

## Core metadata standard for DataCrate: Schema.org

Schema.org is the base metadata standard for DataCrate with a few additions to
supplement gaps in schema.org's ability to represent needed metadata.
Future versions of this spec may be based on other metadata standards.

Additional metadata may be drawn from any RDF vocabulary to allow open-ended
extensibility.

## Summary of Coverage

This specification has guidelines for ways to represent:

* Contact information for a data set, with heuristics for creating BagIt
  metadata from this information.

* Descriptive information for a dataset and the files within it such as an
  abstract, spatial and temporal coverage.

* Associated Publications.

* Funding relationships.

* Provenance information of various kinds; who (people and organizations) and
  what (instruments and computer programs) created or contributed to the data
  set and individual files within it.

* If there is enough metadata, a DataCite citation for the crate.

## Compromises / limitations of schema.org

One crucial *MetaData Entity* is a research project. Schema.org has no class for
a project, although it does have a [schema:funder] property. DataCrate
represents a research project using a *DataCrate Microdocument* as a
[schema:Organization] using an entity with at least two types,
[schema:Organization] AND [vivo:Project],  to represent a research project,
which MAY have a [schema:funder] property or properties.

Likewise, schema.org does not have a way to represent research equipment so
DataCrate uses the [vivo:Equipment] class.

Schema.org has no class for a file format, so we have used this W3C class:
https://www.w3.org/ns/formats/vocab-data/Format.

A [schema:Dataset] has no contactPoint property, unlike [DCAT]. DataCrate uses
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
* Use the [DataCrate JSON-LD frame] to organize CATALOG.json document as a tree with the
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

CATALOG.html MUST contain a DataCrate Microdocument which wraps metadata about
the whole data set. The div element is used in this example, but any HTML5
element MAY be used as long as it is expressed in well-formed HTML. This applies
to all the examples below.

```
    <div typeof='http://schema.org/Dataset'
          about='data'>
       <span property='http://schema.org/description'>
           This dataset is ... . It is useful for ...
       </span>
       ...    
    </div>
```

The above example corresponds to to the following *DataCrate-framed JSON-LD*.
The [DataCrate JSON-LD Context] MUST be included inline as below (ordering is
not meaningful) and MAY contain additional items.

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
structural convention that each *Data Entity* (Dataset, directory or file) and
each  *MetaData Entity* (Person, Organization, item of equipment etc) is
represented by a *DataCrate Microdocument*. The root of the data structure MUST
be a microdocument (eg an HTML ```div``` element) with type [schema:Dataset].
Other *Data Entities* and *MetaData Entities* MUST be nested inside the
DataCrate Dataset. All nested *DataCrate Microdocuments* have at least one type:
[schema:CreativeWork].

If there are files in the root of the payload (/data) directory each file MAY be
described by a *DataCrate microdocument* related to the
schema:Dataset with a property [schema:hasPart].

NOTE: As per the BagIt standard, every file in the payload directory MUST be in
the BagIt Manifest.  Describing every file in the payload directory is possible
with DataCrate, but is not mandatory.  For use-cases where it is important to
describe all files, future versions of this specification MAY allow for profiles
- using which it will be possible to specify constraints such as "every file
must have a [schema:description]".

A table row is a convenient way to represent a *DataCrate microdocument* for a
*Data Entity* in HTML. In the context of the markup above this adds a part to
the schema:Dataset. For example a table of files MAY begin with:

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

An entry describing a file as a table row would then begin:

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

Within the div element describing the file above, technical metadata about the
file MAY be included, for example:

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
[schema:creator] in a DataCrate. This property MAY be applied in the context of
a [schema:Dataset] or to a file (schema:MediaObject).

```
<a href='http://orcid.org/0000-0002-8367-6908'    
    property='http://schema.org/creator'>
      J. Xuan
</a>
```

This uses an ORCID, to unambiguously identify an author. However, it does not
provide very much information about the author in the DataCrate. CATALOG.html
SHOULD have a *DataCrate Microdocument* for each person who contributed to the
data or publications mentioned in the crate, and for the the organizations
concerned. Creators SHOULD be represented by DataCrate Microdocuments with two
types: [schema:CreativeWork] and [schema:Person].


### Organizations

An organization SHOULD be represented by a *DataCrate Microdocument* with two types:
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

The following is an example of marking-up a publication, using a DataCrate
Microdocument with type [schema:ScholarlyArticle]. In this example the ID of the
document is a DOI URL (http://dx.doi.org/10.1109/TCYB.2014.2386282). IDs for
publications SHOULD be DOI URLs where possible.

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

A reference a *Data Entity* or *Metadata Entity* or  MUST use the property
[schema:relatedItem], pointing to a *DataCrate Microdocument*. Building on the
examples of Organizations, People and Publications above, referencing the
publication http://dx.doi.org/10.1109/TCYB.2014.2386282 could look like:

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
use the [schema:contributor] property to reference a *DataCrate Microdocument*
with type [vivo:Project].

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

Which yields this *DataCrate-framed JSON-LD*:

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
Use the [schema:publisher] property, referencing a [schema:Organization].

### Date of publication
Use the [schema:datePublished] property with a date in ISO 8601 date format.

### Licensing and Copyright

If a *Data Entity* has a license the entity SHOULD have a [schema:license]
property with a value of a DataCrate Microdocument that describes the license,
the ID of the license should be its URL (eg a Creative Commons License URL). If
this is not possible a URL MAY be used as the value.

To note the copyright holder of a Data Item, use the [schema:copyrightHolder]

For example this table row describes a file:
```
<tr href="./Data%20Management%20Planning/Final_Self_Audit/Templates_for_self_audit/AAA_Template_ECV_self_audit.docx" id="Data%20Management%20Planning/Final_Self_Audit/Templates_for_self_audit/AAA_Template_ECV_self_audit.docx" typeof="CreativeWork schema:MediaObject" property="schema:hasPart">    <td> <a href="Data%20Management%20Planning/Final_Self_Audit/Templates_for_self_audit/AAA_Template_ECV_self_audit.docx">AAA_Template_ECV_self_audit.docx</a>
<details><table><tbody><tr>
    <th>File format</th>
    <td property="http://schema.org/encodingFormat">Microsoft Office Open XML</td>
</tr>
<tr>
    <th>Format version</th>
    <td property="http://schema.org/encodingFormat"><a href="http://www.nationalarchives.gov.uk/PRONOM/fmt/189">Microsoft Office Open XML</a><span property="http://schema.org/fileFormat" href="http://www.nationalarchives.gov.uk/PRONOM/fmt/189"></span></td>
</tr>
<tr>
    <th>Mime</th>
    <td property="http://schema.org/fileFormat">None</td>
</tr>
<tr>
    <th>Size</th>
    <td property="http://schema.org/contentSize">75046</td>
</tr>
</tbody></table></details>
</td>
    <td><div property="http://schema.org/description">Empty template for data audit for ECV project</div>
</td>
    <td><div href="http://orcid.org/0000-0002-0068-716X" property="http://schema.org/creator"><a href="#http://orcid.org/0000-0002-0068-716X">Cameron Neylon</a></div>
</td>
    <td><div href="https://creativecommons.org/licenses/by/4.0/" property="http://schema.org/license"><a href="#https://creativecommons.org/licenses/by/4.0/">CC BY 4.0</a></div>
</td>
    <td><div href="#IDRC" property="http://schema.org/copyrightHolder"><a href="#IDRC">International Development Research Center</a></div>
</td>

</tr>
```

The [schema:copyrightHolder] property references a DataCrate Microdocument of
type [schema:creativeWork], which describes the license.

```
<tr href="https://creativecommons.org/licenses/by/4.0/" id="https://creativecommons.org/licenses/by/4.0/" typeof="http://schema.org/CreativeWork" property="schema:hasPart">    <td><div property="http://schema.org/name">CC BY 4.0</div>
</td>
    <td><div property="http://schema.org/description">Attribution 4.0 International (CC BY 4.0)

You are free to:

Share — copy and redistribute the material in any medium or format
Adapt — remix, transform, and build upon the material
for any purpose, even commercially.
 This license is acceptable for Free Cultural Works.
The licensor cannot revoke these freedoms as long as you follow the license terms.
Under the following terms:

Attribution — You must give appropriate credit, provide a link to the license, and indicate if changes were made. You may do so in any reasonable manner, but not in any way that suggests the licensor endorses you or your use.
No additional restrictions — You may not apply legal terms or technological measures that legally restrict others from doing anything the license permits.</div>
</td>
    <td><div property="http://schema.org/identifier"><a href="https://creativecommons.org/licenses/by/4.0/">https://creativecommons.org/licenses/by/4.0/</a></div>
</td>

</tr>
```

This Data Item has a copyright holder which is different from its creator. There is a reference to a DataCrate
Microdocument of type [schema:Organization] describing the copyright holder.

```
<tr href="#IDRC" id="IDRC" typeof="http://schema.org/CreativeWork http://schema.org/Organization" property="schema:hasPart">    <td><div property="http://schema.org/identifier">IDRC</div>
</td>
    <td><div property="http://schema.org/name">International Development Research Center</div>
</td>
    <td><div property="http://schema.org/description">Canadian Frown Corporation and funder of development research</div>
</td>
    <td><div>150 Kent Street, Ottawa, Ontario, Canada K1P 0B2</div></td>
    <td><div></div></td>
    <td><div>Organization</div></td>

</tr>

```

### Equipment

To specify which equipment was used to create or contribute to a *Data Entity*,
the *DataCrate Microdocument* for the entity SHOULD have a [schema:contributor]
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

To associate a *Data Entity* with a MetaData Entity representing a *geographical
location or region* the DataCrate Microdocument describing the entity SHOUD have
a property of [schema:contentLocation] with a value of a DataCrate Microdocument
of type  [schema:Place].

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

To describe the time period which a DataCrate Data Entity is *about*, use [schema:temporalCoverage].


### Subjects & Keywords

Subject properties (equivalent to a Dublin Core Subject) on DataCrate
MicroDocuments  MUST use the [schema:about] property.

Keyword properties MUST use [schema:Keyword].


## Minimum metadata summary

A DataCrate MUST have a root [schema:Dataset] with ID of "data" with:
 *  a [schema:description] property, which describes the whole dataset contained
    in the data (BagIt payload) directory.

 *  A creation date in ISO data format for the dataset using
    [schema:dataModified], equivalent semantically to bagging-date in BagIt.

 *  A contact person (or role) expressed via a property schema:accountablePerson
    on the [schema:Dataset] of with a value of type [schema:Person], with  at
    least one of the following properties.
    *  [schema:email] (text)
    *  [schema:phone] (text)
    *  [schema:affiliation] - with a text value or reference to a DataCrate
       Microdocumtn of type [schema:Organization].

### BagIt metadata

BagIt metadata SHOULD be included in bag-info.txt where it is available as
follows in the Dataset object at the root of '@graph' in *DataCrate-framed JSON-LD*.

The following metadata SHOULD be extracted from the *DataCrate-framed JSON* at
the root of the dataset.

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
*  For a DataCrate, a [schema:identifier] property which SHOULD be a DOI URL.
*  For People participating the research projects: ORCID identifiers.
*  For organizations including funders, pending a global identifier scheme for
   use the homepage URL for research organizations.
*  For items of type schema:Place: a geonames URI.
*  For file formats, use Pronom URLs, for example http://www.nationalarchives.gov.uk/PRONOM/fmt/831.

In the absence of the above, DataCrates SHOULD contain stable persistent URIs to
identify all entities wherever possible.


## Datacite citations

If there is sufficient metadata in a DataCrate, it SHOULD contain a DataCite
citation, datacite.xml in the root of the bag directory compliant with the [DataCite Schema
v4.0].

To generate DataCite.xml a DataCrate MUST have the following at the  schema:Dataset
level:
*  A [schema:identifier] for the DataCrate which is a DOI URL.
*  At least one [schema:creator] with either a [schema:name] or a [schema:givenName] and [schema:familyName].
*  At least one [schema:name] (which maps to a DataCite title).
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
[CURIE]: https://www.w3.org/TR/curie/
[schema:keyword]: https://schema.org/keyword
[schema:about]: https://schema.org/about
[schema:name]: https://schema.org/name
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
[schema:accountablePerson]: https://schema.org/accountablePerson
[schema:datePublished]: https://schema.org/datePublished
[schema:license]: https://schema.org/license
[schema:accountablePerson]: https://schema.org/accountablePerson
[schema:contributor]: https://schema.org/contributor
[schema:contentLocation]: https://schema.org/contentLocation
[schema:copyrightHolder]: https://schema.org/copyrightHolder
[schema:Place]: https://schema.org/Place
[schema:description]: https://schema.org/description
[schema:email]: https://schema.org/email
[schema:phone]: https://schema.org/phone
[schema:affiliation]: https://schema.org/affiliation
[schema:givenName]: htts://schema.org/givenName
[schema:familyName]: https://schema.org/familyName
[schema:publisher]: https://schema.org/publisher
[schema:translator]: https://schema.org/translator
[schema:translationOf]: https://schema.org/translationOf

[DataCrate BagIt Profile]: https://raw.githubusercontent.com/UTS-eResearch/datacrate/develop/spec/0.1/profile-datacrate-v0.1.json
[DataCrate JSON-LD Context]: ./context.json
[JSON-LD Framing 1.1]: https://json-ld.org/spec/latest/json-ld-framing/
[DataCrate JSON-LD frame]: ./frame.json
[geonames]: http://www.geonames.org
[RDFa]: https://en.wikipedia.org/wiki/RDFa
[schema.org]: http://schema.org
[vivo:Project]: http://vivoweb.org/ontology/core#Project
[vivo:Equipment]: http://vivoweb.org/ontology/core#Equipment
