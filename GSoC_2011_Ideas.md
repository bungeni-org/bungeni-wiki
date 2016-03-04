**This page is a work in progess**

# GSoC 2011 Project Ideas



## XML Node level diff and merge

**Technical Knowledge** : Java XML processing, good knowledge of XML concepts and technologies.

A critical requirement when dealing with legislative documents is managing different versions and consolidating changes into a master document. The project has developed  a legislative XML document standard called [Akoma Ntoso](http://www.akomantoso.org) (also see http://akomantoso.googlecode.com ).

A typical use case is a legislative bill --  this is drafted by a member of parliament and then changes are submitted on this document by different members. These submitted changes are triaged and discussed and go  through a voting process before selected changes are approved as amendments.
These approved amendments are "merged" (consolidated) back into the original bill document.

In Bungeni the bill document will be an XML document (either ODF or Akoma Ntoso). The changes are submitted on copies of this document -- and finally, approved amendments from different users are merged into the master.

A XML aware diff-merge module is required for Bungeni.
One of the projects identified as a feasible candidate is the JNDiff project http://jndiff.sourceforge.net/. This needs to be taken from a proof of concept application to something practically usable with Akoma Ntoso documents.

Also see :
  * [XML diff survey](http://www.scribd.com/doc/14482474/XML-diff-survey)
  * http://twprojects.cs.unibo.it:8080/taf-en/
  * [Detecting changes on semi structured data](http://goo.gl/OzNXb)



## Online petitioning system

**Technical Knowledge** : Python and WSGI

The Bungeni legislative information system provides a workflow driven environment for managing typical parliamentary processes like : questions,motions and bills involving  different kinds of actors like : the member of parliament, the clerk and the speaker.

An "online petitioning system" is intended to make the general public also an involved actor in the processes of the system.

Using this public facing module - the general public can propose and submit petitions on issues of public / private importance. The petition submission to parliament will involve a workflow where the petition will be channeled for approval via a specific committee or office in the parliament - to be directed appropriately to other bodies (e.g. a member or a committee ) within the parliament to address the content of the petition. To this effect the "Petition" content type must be developed in Bungeni with the appropriate workflows using the Bungeni workflow engine.

Also see :
  * http://petitions.number10.gov.uk/

## Social Media Integration

**Technical Knowledge** : Python and WSGI

The Bungeni legislative information system publishes different kinds of parliamentary content like Questions, Bills and Motions. Items like these, once published, go through various public phases which may be of interest to the public. For e.g. in the case of a Bill, it goes through different readings in the parliamentary chambers, and stages like "publishing to the gazette".

A "social media" integration module will allow Bungeni content to be selectively published onto different social media outlets. For example - a parliament may have a page on facebook or a twitter feed. The "social media" integration module will push Bungeni content onto such outlets from within the Bungeni content publishing interface.

## Federated URI Rewriters Based on Ontologies (FURBO)


URL shortening services (http://en.wikipedia.org/wiki/URL_shortening) and Apache's mod\_rewrite (http://httpd.apache.org/docs/2.2/mod/mod_rewrite.html) are different solutions for the same need: accessing a web resource by also using a URI that is, for a number of reasons, different from its physical URL. Despite their differences, both approaches enrich the namespace of the World Wide Web, and add a whole layer of names that are meant to be useful to the final user rather than representing any physical characterization of data on the servers.

But while url shorteners are end-user tools and act on individual instances of web documents, mod\_rewrite is a tool for administrators and provide rules applying to whole sets of web documents. Both rely on monolithic architectures with a tight integration between the rewriting engine and the rewriting data.

The idea behind FURBO is to create a federated architecture of rule-based rewriting engines sharing and reciprocally updating the rewriting rules using a peer-to-peer network. Each participating engine accesses the rewriting rules created by any other federated engine, and has tools and procedures to verify the existence, availability, authoritativeness and maliciousness of any other engine, creating a persistent network of reciprocal reputation assessments that should prevent the typical misuses and exploits that URL shorteners fall prey of.

Rewriting may happen on the individual instance, or, through rules, on any number of URIs that share common features, creating customized views on, basically, the whole Web. Rewriting rules can be private or shared, and redirect transparently, non-trasparently, or simply return the requested resource. Partial or incomplete requests, rather than returning errors, may return a list of matching candidates among which the user can select the required destination.

Finally, rewriting rules can be associated to metadata that reflect either folksonomic tags over the requested resource, or a full ontological conceptualization based on the IFLA FRBR document model (http://www.ifla.org/publications/functional-requirements-for-bibliographic-records). Thus it is possible to handle URIs that may map to multiple versions and multiple data formats for the same document, and have a number of policies (including defaults, contextualized rules, user agent's configuration and end-user interaction) that are used to identify the specific instance of the document the URIs currently refers to.

We will employ a use-case based on legislative documents expressed in any number of formats, including plain text, MS Word, Open Office, PDF, (x)HTML, XML (in a specific data format for legislative documents called Akoma Ntoso) and print. Most frequently, legislative documents contain references that need to resolve to different versions of the same destination, according to often sophisticated legal reasoning. For instance, most references from a piece of legislative document to another need to resolve to the version of the destination that was in force when the event being evaluated took place, regardless of when the original document was promulgated and regardless of when the reference was traversed. For this reason, an engine for mapping abstract references to actual documents needs to be put in place, that uses URI rewriting techniques strongly associated to a relevant conceptual model (i.e., IFLA FRBR) for dynamic decisions of the correct resource being linked. Such engine needs to work in a federated context in order to allow individual countries and legal publishers to provide navigation support for their citizens and customers regardless of which country or which legal publisher is concretely hosting the document.

The project will therefore deal with URI/IRI and HTTP technicalities (in particular status codes 300, 302, 303, and 307), peer-to-peer data sharing, distributed trust and reputation approaches, the IFLA FRBR document model, and the XML vocabulary for legislative documents called Akoma Ntoso (www.akomantoso.org).