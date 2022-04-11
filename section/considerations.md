## Considerations {#considerations}

### Identifying collections and containers

An `rr:template` can be provided for the creation of blank node IDs (`rr:BlankNode`) or IRIs (`rr:IRI`). In some cases, there are challenges when identifiers are generated. The challenge generating identifiers for collections and containers: one needs to be careful when a gather map uses multi-valued references in the template or generates multiple collections and containers:

* In the former, the gather map must return “deep” copies of collections. This is to avoid only generating a new IRI or blank node identifier for the first cons-pair and hence having multiple lists sharing the same sublist.
* In the latter, we may end up with multiple lists and containers sharing the same blank node identifier or IRI, which may produce valid RDF, but nonsensical containers and collections.