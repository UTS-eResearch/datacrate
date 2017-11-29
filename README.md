# Research Data Crate


## About this project

This project is to develop a specification for a standardised way of
packaging file-based *research data* for re-use and distribution. (Archiving and
preservation are not *immediate* concerns but we want to work with those
communities of practice ASAP).

The current draft version is [0.1](./spec/0.1/data_crate_specification_v0.1.md)

Peter Sefton and Peter Bugeia developed a way of bundling data from the [HIEv]
repository for deposit in the university Research Data Repository and for re-use
by researchers. This work was [presented at eResearch Australasia in
2013](http://ptsefton.com/2013/11/01/1944.htm). The idea was picked up on the
Cr8it project with which we were both involved, but without the benefit of a
specification to align the two pieces of work. Two other projects, [Collections]
and [Calcyte] have also embraced the idea.

It's time for a specification which we can all agree on for the structure of
Data Crates, and which metadata standards to use and get on with creating tools
that help researchers.


## Requirements

It says above that this project is a specification for a standardised way of
packaging file-based research data for re-use and distribution. So, what do we
mean by data packaging?

In this context, packaging data means:

*  Being able to distribute a data set via a single file (using Zip, TAR or a
   disc image format as appropriate) and/or via a URL with integrity checks

*  Host a data set on a web server with appropriate access controls in a way that
  allows people to inspect the data set via an HTML page containing summary metadata, at the same
  time making the metadata available as part of the semantic web for use by machines to build indexes or otherwise consume metadata.
 

The goals are:

-  To maximise the utility of the data for researchers (including researchers’ ‘future
selves’). Given that a researcher has found a DataCrate package they should be able to tell what it *is*, *how the data may be used* and what all the files contain.

*  To enable discovery of the data by exposing metadata as widely as possible
(respecting access rights)

*  To enable automated ingest into repositories or catalogues such as ReDBOX.

NOTE: The package format does not need to be able to be created by people, that
will be done by software tools such as Cr8it and Calcyte, this means some
complexity is allowable, but complexity of implementation will add cost so there
needs to be a cost-benefit analysis. Cost of consumption of data also costs, so
we will try to minimise that in the design.


## FAQ

### Why are you inventing a new format?

We're not really. Data Crate is based on the Bagit packaging spec, with
additional human and machine readable metadata in RDFa.

### Why Schema.org? Surely there are suitable general purpose metadata formats for Research Data

Well, actually no there aren't any RDF-ready well-supported extensible schemas
that cover all of the kids of general purpose metadata you might want to add to
a research data package before sending it off into the wold.

#### RDF-based formats

[DCAT] is close to what we wanted for this project but does not have coverage
for all the fields we wanted. DCAT also uses a terms from a wide range of
vocabularies, making it more complicated to use than Schema.org.

It is RDF based, though. There is an effort to
extend DCAT but it will take some time to complete.

[Research Object Bundles] are very close in spirit to Data Crates, and there is
even an attempt to map them on to BagIt. However, RO metadata is very focussed
on highly nuanced distinctions between different kinds of creators of research
data, without addressing a lot of the who-what-where type metadata.

"The [VIVO] ontology represents researchers in the context of their experience, outputs, interests, accomplishments, and associated institutions." - it is a designed for discovering information about researchers. It does not cover all the metadata one might want about research outputs, but we have drawn on it for 

#### Non-RDF-based formats

[Datacite] has a lot of metadata that is useful for Data Crates, because it is but it is
oriented towards describing things at the dataset level, not the file level, and thus only useful for some metadata. It is also focussed on published data which has a DOI (although you can enter a null DOI). There was an RDF version of Datacite but it is out of date.

[RIF-CS] is a standard created by the Australian National Data Service based on
ISO 2146 for data dissemination which has good general purpose coverage but it's
an XML based format that can't be used in a linked-data context directly.

[Frictionless data]  packages are attractive in that they are simple, JSON based metadata but they don't 
interoperate with the semantic web, as there is no JSON-LD version and they do not reference mainstream approaches
to metadata such as Dublin Core except in passing.


### RDFa, really? Isn't that a bit out there?

Remember, Schema.org, which is a standard sponsored by major web companies has
as RDFa as its default encoding. The commercial web has been using this approach
for years.

https://www.w3.org/TR/vocab-dcat/#Property:dataset_contactPoint

[VIVO]: https://bioportal.bioontology.org/ontologies/VIVO
[DCAT]: https://www.w3.org/TR/vocab-dcat/
[BagIt]: https://tools.ietf.org/html/draft-kunze-bagit-14
[Datacite]: https://schema.datacite.org/
[Calcyte]: https://codeine.research.uts.edu.au/eresearch/calcyte/tree/038302bea5719f500be2836d975894c2e69e931a
