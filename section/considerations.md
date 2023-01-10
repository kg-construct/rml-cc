## Considerations {#considerations}

### The use of `rr:column`

In RML, `rr:column` is considered a subproperty of `rml:reference`. One can still avail of `rr:column` when creating mappings for relational databases. The use of `rr:column` is not discouraged, but one is encouraged to favor `rml:reference`. 


### Using a `rml:GatherMap` in various types of term map

Although most examples demonstrate the use of a gather map in the context of an object map, a gather map is a regular term map.
As such, it can be used in other types of term maps such as a subject or predicate map.

Term maps generate single RDF terms to be used as the terms of RDF triples.
If such a term map generates a collection or container by means of a gather map, the term retained to form an RDF triple is the head node of the collection or container.
In the case of an RDF list, this is the node that is the subject of the first `rdf:first` predicate.

The [examples section](#gatherinsubject) demonstrates how a gather map can be used within a subject map.

### Generating lists and containers based on multiple iterations

In most of our examples, we have covered the cases where we either did not provide an `rr:template`, `rr:constant` or `rml:reference` directive for the collection or container to be generated. Each collection or container is identified by the iteration. When a gather map generates more than one list or container, then a new blank node identifier is generated for each list or container.

In this section, we will exemplify what must happen when an `rr:template`, `rr:constant` or `rml:reference` is provided to a gather map that has multi-valied term maps in its `rml:gather`.

We use the document JSON below. Notice that there are two JSON object with the value `"a"` for `"id"`.

```json
[ 
  { "id": "a",  "values": [ "1" , "2" , "3" ] },
  { "id": "b",  "values": [ "4" , "5" , "6" ] },
  { "id": "a",  "values": [ "7" , "8" , "9" ] } 
]
```

The following gather map without directives for a blank node identifier or IRI must generate three lists, two related to `<a>` and one to `<b>`.

Predicate object map:

```turtle
  rr:predicateObjectMap [
    rr:predicate ex:with ;
    rr:objectMap [
        rml:gather ( [ rml:reference "values.*" ; ] ) ;
        rml:gatherAs rdf:List ;
    ] ;
  ] ;
```

Expected output:

```turtle
  <a> ex:with ("1" "2" "3"), ("7" "8" "9")  .
  <b> ex:with ("4" "5" "6") .
```

When an `rr:template`, `rr:constant` or `rml:reference` is provided, then the processor concatenates (i.e., append) the new collection or container to the previous one. For example, if we add a `rr:template` directive to the predicate object map:

```turtle
  rr:predicateObjectMap [
    rr:predicate ex:with ;
    rr:objectMap [
        rr:template "list/{id}" ;
        rml:gather ( [ rml:reference "values.*" ; ] ) ;
        rml:gatherAs rdf:List ;
    ] ;
  ] ;
```

Then the processor must generate following output:

```turtle
  <a> ex:with <list/a> .
  <list/a> rdf:first "1" ; rdf:rest ("2" "3" "7" "8" "9") .
  <b> ex:with <list/b> .
  <list/b> rdf:first "4" ; rdf:rest ("5" "6") .
```

It is assumed that a processor will respect the order of the iterations.

### Multiple iterations and multi-valued term maps

When dealing with multi-valued term maps in a gather map, the default strategy is `rml:append`. Given the following document an mapping:

```json
[ 
  { "id": "a",  "values1": [ "1" ], "values2": [ "a" , "b" ] },
  { "id": "b",  "values1": [ "3" , "4" ], "values2": [ "c" , "d" ] },
  { "id": "a",  "values1": [ "5" , "6" ], "values2": [ "e" ] } 
]
```

```turtle
  rr:predicateObjectMap [
    rr:predicate ex:with ;
    rr:objectMap [
        rr:template "list/{id}" ;
        rml:gather ( [ rml:reference "values1.*" ; ] [ rml:reference "values2.*" ; ] ) ;
        rml:gatherAs rdf:List ;
    ] ;
  ] ;
```

The expected output is:

```turtle
  <a> ex:with <list/a> .
  <list/a> rdf:first "1" ; rdf:rest ("a" "b" "5" "6" "e") .
  <b> ex:with <list/b> .
  <list/b> rdf:first "3" ; rdf:rest ("4" "c" "d") .
```

When changing the default strategy to `rml:cartesianProduct`, however, each iteration will yield multiple lists. As they are all identified with the same template, all those lists are combined into one list as described in the previous section. In other words, the following mapping will yield the following result:

```turtle
  rr:predicateObjectMap [
    rr:predicate ex:with ;
    rr:objectMap [
        rr:template "list/{id}" ;
        rml:gather ( [ rml:reference "values1.*" ; ] [ rml:reference "values2.*" ; ] ) ;
        rml:gatherAs rdf:List ;
        rml:strategy rml:cartesianProduct ;
    ] ;
  ] ;
```

```turtle
  <a> ex:with <list/a> .
  <list/a> rdf:first "1" ; rdf:rest ("a" "1" "b" "5" "e" "6" "e") .
  <b> ex:with <list/b> .
  <list/b> rdf:first "3" ; rdf:rest ("c" "3" "d" "4" "c" "4" "d") .
```

### Well-formedness of collections and containers

There is an important difference between valid RDF and well-formed containers and collections. The following RDF is valid, though the collection is ill-formed as the first cons-pair has two `rdf:rest` properties.

```
ex:illformed rdf:first 1 ; rdf:rest (2, 3), (4, 5) .
```

An RML collection and container validator (RMLCCV) is a system that checks for the well-formedness of collections and containers. The RMLCCV MUST report on any ill-formed collections and containers that are raised in the RDF generation process. An RML processor may include an RMLCCV, but this is not required.