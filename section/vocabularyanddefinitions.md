## Vocabulary and definitions {#vocabulary}

This section introduces the classes, properties, and constants of the RML Containers and Collections specification.

### Classes

#### `rml:GatherMap`
Gather maps are term maps that use `rml:gather` and `rml:gatherAs` to generate collections and containers from a list of term maps. If a gather map has no template or term type directives, it is assumed to be generating blank nodes for each iteration.

* A `rml:GatherMap` MUST have exactly one `rml:gather` property.
* A `rml:GatherMap` MUST have exactly one `rml:gatherAs` property.

### Properties

#### `rml:gather`

The `rml:gather` informs the RML processor where the terms of a collection and container come from. This property relates gather maps with a non-empty list of term maps. That list may contain gather maps, which means one will generate nested containers and collections. One is not limited to nesting only containers or collections, both containers and collections can be mixed.

* The domain of `rml:gather` is `rml:GatherMap`.
* The range of `rml:gather` is a non-empty list of `rr:TermMap` instances. This list may include instances of `rml:GatherMap`. We thus support the nesting of collections and containers.

#### `rml:strategy`
This informs the processor how to create collections and containers when faced with multi-valued term maps. Within this specification, we defined both `rml:Append` and `rml:CartesianProduct`. A gather map does not need to specify a strategy. The default strategy is `rml:Append`.

This property allows one to design and implement their own strategies for gathering terms into collections and containers.

* The domain of `rml:strategy` is `rml:GatherMap`.
* The range of `rml:strategy` is an IRI.

#### `rml:gatherAs`

The property `rml:gatherAs` relates a gather map with the desired result: a type of container or collections.

* The domain of `rml:gatherAs` is `rml:GatherMap`.
* The range of `rml:gatherAs` is one of the following: `rdf:Seq`, `rdf:Bag`, `rdf:Alt`, `rdf:List`.

#### `rml:allowEmptyListAndContainer`
The range of `rml:allowEmptyListAndContainer` is a `xsd:boolean` and is by default false. This predicate is to be used alongside `rml:gather` and `rml:gatherAs`. When true and an `rml:gather` does not yield any element, then the gather map will generate `rdf:nil` for an RDF collection, or a resource with no members for an RDF container.

* The domain of `rml:allowEmptyListAndContainer` is `rml:GatherMap`.
* The range of `rml:allowEmptyListAndContainer` is `xsd:boolean`.

### Constants

#### `rml:Append`
#### `rml:CartesianProduct`