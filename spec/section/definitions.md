## Definitions {#definitions}

### Iterations {#iterations}

In the course of this document, term **"iteration"** is used to refer to the iterations that stem from the logical source when processing the input documents.
An iteration results from the input data (documents, records returned by a query to a database etc.) on which the logical source may apply an optional `rml:iterator`.

In the [running example](#runningexample), the data source consists of a single document, the iterator then extracts each of the three sub-documents within the array, thus the logical source yields three iterations.


### Multi-valued term map {#multivaluedtermmap}

A **multi-valued term map** is a term map that, during a single [iteration](#iterations), may yield multiple RDF terms or multiple collections or containers in the case of a gather map.


### Named collection or container {#named}

A **named collection or container** is a collection or container whose head node is assigned either an IRI or a blank node identifier.


### Well-formed vs. ill-formed collections and containers {#wellformedness}

There is an important difference between valid RDF and well-formed containers and collections. The following RDF is valid, though the collection is ill-formed since the first cons-pair has two `rdf:first` and two `rdf:rest` properties:

<pre class="ex-output">
ex:illformedList 
  rdf:first 1 ; rdf:rest (2, 3) ;
  rdf:first 4 ; rdf:rest (5, 6) .
</pre>

Similarly, an ill-formed container would have multiple times the same `rdf:_n` property, e.g.:

<pre class="ex-output">
ex:illformedContainer rdf:_1 1 ; rdf:_2 2 ; rdf:_3 3 ; rdf:_1 4 .
</pre>

An RML collection and container validator (RMLCCV) is a system that checks for the well-formedness of collections and containers. The RMLCCV MUST report on any ill-formed collections and containers that are raised in the RDF generation process. An RML processor may include an RMLCCV, but this is not required.
