## Overview {#overview}

*This section is non-normative.*

The RDF Mapping Language (RML) [[RML]] is a language for expressing mappings between heterogeneous data and RDF. In RML, rules can be expressed to iterate over a data source and refer to specific data within an iteration. Using these iterators and references, RML rules define how to express data in the data source in RDF. RML is based on and extends R2RML [[R2RML]]. R2RML is defined to express customized mappings only from relational databases to RDF datasets.

This document describes RML-CC:
an extension of RML that enables the generation of RDF collections and containers with RML.


### Conformance {#conformance}
As well as sections marked as non-normative, all authoring guidelines, diagrams, examples, and notes in this specification are non-normative. Everything else in this specification is normative.

The key words MUST and SHOULD in this document are to be interpreted as 
described in [BCP 14](https://www.rfc-editor.org/info/bcp14) [[RFC2119]] [[RFC8174]] when, 
and only when, they appear in all capitals, as shown here.


### Document conventions {#conventions}
We assume readers have basic familiarity with RDF and RML.

In this document, examples assume
the following namespace prefix bindings unless otherwise stated:

| Prefix | Namespace                         |
| ------ | --------------------------------- |
| `rml:` | http://w3id.org/rml/              |
| `xsd:` | http://www.w3.org/2001/XMLSchema# |
| `ex:`  | http://example.org/               |
| `:`    | http://example.org/               |

The examples are contained in color-coded boxes. We use the Turtle syntax [[Turtle]] to write RDF.

<pre class="ex-input">
# This box contains an example input
</pre>

<pre class="ex-mapping">
# This box contains an example mapping
</pre>

<pre class="ex-output">
# This box contains the example output
</pre>
