## Examples {#examples}

In the following section, we present additional examples and describe the expected output. For each example, we provide the document that correspond with the current iteration. For example, if we were to generate RDF from the following JSON document and RML mapping:

```
{ 
 "items": [ 
    { "id": "a",  "values": [ "1" , "2" , "3" ] },
    { "id": "b",  "values": [ "4" , "5" , "6" ] },
    { "id": "c",  "values": [ "7" , "8" , "9" ] } 
  ]
}
```

```
@prefix rml: <http://semweb.mmlab.be/ns/rml#>.
@prefix rr: <http://www.w3.org/ns/r2rml#>.
@prefix ql: <http://semweb.mmlab.be/ns/ql#>.
@prefix ex: <http://example.com/ns>.
@base <http://example.com/ns>.

<#TM> a rr:TriplesMap;
  rml:logicalSource [
    rml:source "data.json" ;
    rml:referenceFormulation ql:JSONPath ;
    rml:iterator "$.items[*]" ;
  ];

  rr:subjectMap [
    rr:template "{id}" ;
  ] ;
.
```

We will illustrate the result of a gather map with respect to one given iteration (unless specified otherwise).

### A simple example {#simpleexample}

Given the JSON document and the RML mapping completed with the following predicate object map:

```
  rr:predicateObjectMap [
    rr:predicate ex:with ;
    rr:objectMap [
        rml:gather ( [ rml:reference "values.*" ; ] ) ;
        rml:gatherAs rdf:List ;
    ] ;
  ] ;
```

We expect the following output:

```
  <a> ex:with ("1" "2" "3") .
  <b> ex:with ("4" "5" "6") .
  <c> ex:with ("7" "8" "9") .
```

This example illustrates that, if template, column, or reference is provided for identifying a container or list, then each iteration yield a new instance of a container or list even if the contents of two iterations are the same.

### Identifying lists and containers

Given the JSON document and the RML mapping completed with the following predicate object map:

```
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

```
  <a> ex:with <list/a> .
  <list/a> rdf:first "1" ; rdf:rest ("2" "3") .
  <b> ex:with <list/b> .
  <list/b> rdf:first "4" ; rdf:rest ("5" "6") .
  <c> ex:with <list/c> .
  <list/c> rdf:first "7" ; rdf:rest ("8" "9") .
```

### Dealing with empty collections and containers

By default, `rml:allowEmptyListAndContainer` is false. Processing the following JSON document with the predicate object map provided in [our simple example](#simpleexample) would not result in a list for `<d>`.

```
{ 
 "items": [ 
    { "id": "a",  "values": [ "1" , "2" , "3" ] },
    { "id": "b",  "values": [ "4" , "5" , "6" ] },
    { "id": "c",  "values": [ "7" , "8" , "9" ] },
    { "id": "d",  "values": [] } 
  ]
}
```

However, when one overrides the value for this property and set it to true, then the following prodicate object map:

```
  rr:predicateObjectMap [
    rr:predicate ex:with ;
    rr:objectMap [
        rml:allowEmptyListAndContainer true ;
        rml:gather ( [ rml:reference "values.*" ; ] ) ;
        rml:gatherAs rdf:List ;
    ] ;
  ] ;
```

will generate:

```
  <a> ex:with ("1" "2" "3") .
  <b> ex:with ("4" "5" "6") .
  <c> ex:with ("7" "8" "9") .
  <d> ex:with () .
```

There is one special case when dealing with empty *collections*. As `rdf:nil` is serverved for the empty list, an RML processor MUST replace each IRI or blank node that is an empty list with `rdf:nil`. In other words, when the following predicate object map is used:

```
  rr:predicateObjectMap [
    rr:predicate ex:with ;
    rr:objectMap [
        rr:template "list/{id}" ;
        rml:allowEmptyListAndContainer true ;
        rml:gather ( [ rml:reference "values.*" ; ] ) ;
        rml:gatherAs rdf:List ;
    ] ;
  ] ;
```

then we expect the following output:

```
  <a> ex:with <list/a> .
  <list/a> rdf:first "1" ; rdf:rest ("2" "3") .
  <b> ex:with <list/b> .
  <list/b> rdf:first "4" ; rdf:rest ("5" "6") .
  <c> ex:with <list/c> .
  <list/c> rdf:first "7" ; rdf:rest ("8" "9") .
  <d> ex:with () . 
```