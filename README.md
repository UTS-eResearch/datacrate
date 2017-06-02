# Research Data Crate


## About this project

This github project is to develop a specification for a standardised way of
packaging file-based research data for re-use and distribution. (Archiving and
preservation are not *immediate* concerns but we want to work with those
communities of practice ASAP)

This packaging work started as part of the HIEv project for Western Sydney
University with work done by Peter Sefton and Peter Bugeia to specify a way of
bundling data from the HIEv repository for deposit in the university Research
Data Repository and for re-use by researchers, which was [presented at eResearch
Australasia in 2013](http://ptsefton.com/2013/11/01/1944.htm). This idea was
picked up by the Cr8it project with which we were involved, but without the
benefit of a specification to align the two pieces of work. Two other projects
have also embraced the idea. It's time for a specification which we can all
agree on for the structure of Data Crates and which metadata standards to use.

# The Data Crate spec will

* Have both human and machine readable metadata at a package (data
  set/collection) level as well as at a file level
* Allow for and encourage inclusion of contextual metadata such as descriptions
  of organisations, facilities, experiments and people linked to files with
  meaningful relationships (eg to say a file was created by a particular
  machine, as part of a particular experiment, at an organisation).
*  Be a bagit profile (TODO: Explain! What's bagit?)
*  With a README.html tag file at the root with bagit-style metadata about the
   distribution (contact details etc) with a link to
*  a CATALOG.html file inside the payload (data) dir with detailed information
   about the files in the package, and a redundant CATALOG.json in JSON-LD
   format
*  And JSON schema (we hope) for the JSON-LD (not sure how realistic this is)

The Data Crate spec will use:
*  A core set of of metadata fields which is the union of relevant metadata items from: [DCAT], [Bagit], [Datacite] (via the datacite ontology?) [Frictionless data] and [Research Object Bundles] (using fields very loosely here) TODO: link these;
*   A hierarchy of the above formats in choosing metadata items (with a published crosswalk matrix to allow casting Data Crates into different metadata formats or packaging formats)

Data Crate will:
*  Be extensible easily as it is linked-data ready.
*  Have some heuristics for filling in metadata - eg if bagit:Contact-Email is not populated, use the Email address of a the first contributor.

