# Research Data Crate


## About this project

This project is to develop a specification for a standardised way of
packaging file-based *research data* for re-use and distribution. (Archiving and
preservation are not *immediate* concerns but we want to work with those
communities of practice ASAP).

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

* Host a data set on a web server with appropriate access controls in a way that
  allows people to inspect the data set by reading summary metadata, at the same
  time making the metadata available as part of the semantic web
 

Other goals are:

-  Embedding human and machine readable metadata in the package: To maximise the
utility of the data for other researchers (including researchers’ ‘future
selves’)

*  To enable discovery of the data by exposing metadata as widely as possible
(respecting access rights)

*  To enable automated ingest into repositories or catalogues such as ReDBOX.

NOTE: The package format does not need to be able to be created by people, that
will be done by software tools such as Cr8it and Calcyte, this means some
complexity is allowable, but complexity of implementation will add cost so there
needs to be a cost-benefit analysis. Cost of consumption of data also costs, so
we will try to minimise that in the design.

## Principles

TODO: Copy principles from existing design docs

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

[VIVO]: TODO

#### Non-RDF-based formats

[Datacite] has a lot of metadata that is useful for Data Crates, but it is
oriented towards publications, and thus only usful for some metadata and for
data sets that are actually published formally.

[RIF-CS] is a standard created by the Australian National Data Service based on
ISO 2146 for data dissemination which has good general purpose coverage but it's
an XML based format that can't be used in a linked-data context directly.

[Frictionless data] packages have a 

### Why not use Frictionless data packages

Frictionless data packages are attractive in that they are simple, JSON based metadata but:
* Don't interoperate with the semantic web, as there is no JSON-LD version.

* Don't have a 

### RDFa, really? Isn't that a bit out there?

Remember, Schema.org, which is a standard sponsored by major web companies has
as RDFa as its default encoding. The commercial web has been using this approach
for years.

https://www.w3.org/TR/vocab-dcat/#Property:dataset_contactPoint


[DCAT]: https://www.w3.org/TR/vocab-dcat/
[BagIt]: https://tools.ietf.org/html/draft-kunze-bagit-14
[Datacite]: https://schema.datacite.org/
[Calcyte]: https://codeine.research.uts.edu.au/eresearch/calcyte/tree/038302bea5719f500be2836d975894c2e69e931a
