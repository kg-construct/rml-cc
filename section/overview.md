## Overview and Example {#overview}

This section gives a brief overview of the RML mapping language. 
It also provides simple examples of the generation of RDF collections and containers from documents and relational data.

Herebelow we present the three main constructs for generating collections and containers. Other predicates, and their use in examples, will be explained further down this document.

An [`rml:GatherMap`](#rml-gathermap) is a term map that generates a collection (`rdf:List`) or container (`rdf:Bag`, `rdf:Seq`, `rdf:Alt`). 
A gather map has a list of term maps that inform the RML processor which RDF terms have to be generated as members of the list or container. 
The [`rml:gather`](#rml-gather) predicate is used to link an instance of [`rml:GatherMap`](#rml-gathermap) with a list of term maps. The generation of a collection or container depends on the `rml:gatherAs` predicate, which may take on any of the following values: `rdf:List`, `rdf:Bag`, `rdf:Seq`,  and `rdf:Alt`.

<figure>
  <img src="./resources/images/overview.svg" alt="Graphical overview of RML's vocabulary to generate RDF collections and containers."/>
  <figcaption>Graphical overview of RML's vocabulary to generate RDF collections and containers.</figcaption>
</figure>


### Running example {#runningexample}

In this section, the data source consists of a JSON file, `data.json`, containing the following JSON array:

```json
[ 
  { "id": "a",  "values": [ "1" , "2" , "3" ] },
  { "id": "b",  "values": [ "4" , "5" , "6" ] },
  { "id": "c",  "values": [ "7" , "8" , "9" ] } 
]
```

The associated RML mapping starts as follows:

```turtle
@prefix rml: <http://semweb.mmlab.be/ns/rml#>.
@prefix rr:  <http://www.w3.org/ns/r2rml#>.
@prefix ql:  <http://semweb.mmlab.be/ns/ql#>.
@prefix ex:  <http://example.com/ns>.
@base        <http://example.com/ns>.

<#TM> a rr:TriplesMap;
  rml:logicalSource [
    rml:source "data.json" ;
    rml:referenceFormulation ql:JSONPath ;
    rml:iterator "$.*" ;
  ];

  rr:subjectMap [
    rr:template "{id}" ;
  ] ;
.
```

### A simple example {#simpleexample}

Given the JSON document and the RML mapping completed with the following predicate object map:

```turtle
  rr:predicateObjectMap [
    rr:predicate ex:with ;
    rr:objectMap [
        rml:gather ( [ rml:reference "values.*" ; ] ) ;
        rml:gatherAs rdf:List ;
    ] ;
  ] ;
```

The iterator entails one iteration for each document whithin the array.
Each iteration yields an instance of collection/container whose head node is a blank node.

We expect the following output:

```turtle
  <a> ex:with ("1" "2" "3") .
  <b> ex:with ("4" "5" "6") .
  <c> ex:with ("7" "8" "9") .
```


### Identifying collections and containers

If we now provide a template in the object map for identifying a collection/container, then each iteration yields an instance of collection/container whose head node is identified as instructed:

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

We expect the following output:

```turtle
  <a> ex:with <list/a> .
  <list/a> rdf:first "1" ; rdf:rest ("2" "3") .
  <b> ex:with <list/b> .
  <list/b> rdf:first "4" ; rdf:rest ("5" "6") .
  <c> ex:with <list/c> .
  <list/c> rdf:first "7" ; rdf:rest ("8" "9") .
```



