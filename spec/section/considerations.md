## Considerations {#considerations}

### The use of `rr:column`

In RML, `rr:column` is considered a subproperty of `rml:reference`. One can still avail of `rr:column` when creating mappings for relational databases. The use of `rr:column` is valid but one is encouraged to favor `rml:reference`. 


### Using a `rml:GatherMap` in various types of term map

Although most examples demonstrate the use of a gather map in the context of an object map, a gather map is a regular term map.
As such, it can be used in other types of term maps such as a subject or predicate map.

Term maps generate RDF terms (IRI, blank node, literal) to be used as the terms of RDF triples.
If such a term map generates a collection or container by means of a gather map, the term retained to form an RDF triple is the head node of the collection or container.
In the case of an RDF list, this is the node that is the subject of the first `rdf:first` predicate.

The [examples section](#gatherinsubject) demonstrates how a gather map can be used within a subject map.


### Named collection or container: assigning an IRI or blank node identifier to a collection and container

If a gather map does not contain any `rr:template`, `rr:constant` or `rml:reference` property, then the head node of each generated collection or container is a new blank node.

Conversely, if a gather map contains either a `rr:template`, `rr:constant` or `rml:reference` property, then the gather map yields [**named collections or containers**](#named) whose head node is identified as instructed by the `rr:template`, `rr:constant` or `rml:reference` property.


The following mapping:

<pre class="ex-mapping">
  rr:predicateObjectMap [
    rr:predicate ex:with ;
    rr:objectMap [
        rr:template "seq/{id}" ;
        rml:gather ( [ rml:reference "values.*" ; ] ) ;
        rml:gatherAs rdf:Seq ;
    ] ;
  ] ;
</pre>

will yield the following output:

<pre class="ex-output">
  :a ex:with :seq/a . :seq/a rdf:_1 "1" ; rdf:_2 "2" , rdf:_3 "3" .
  :b ex:with :seq/b . :seq/b rdf:_1 "4" ; rdf:_2 "5" , rdf:_3 "6" .
  :c ex:with :seq/c . :seq/c rdf:_1 "7" ; rdf:_2 "8" , rdf:_3 "9"  .
</pre>


### Generating well-formed named collections or containers

When generating a [**named collection or container**](#named), it may happen that the same IRI or blank node identifier be generated several times, either across multiple [iterations](#iterations) or because the gather map is [multi-valued](#multivaluedtermmap) as exemplified with the [`rml:cartesianProduct`](#rml-cartesianproduct) strategy.


In this situation, to avoid generating [ill-formed collections or containers](#wellformedness), the processor MUST concatenate (i.e. append) the new collection or container to the previous one. 
In other words, when a gather map creates a [named collection or container](#named), the processor must first check whether a named collection or container with the same head node IRI or blank node identifier already exists, and if so, it must append the terms to the existing one.

Below we exemplify two such situations.


#### Named collections or containers generated across multiple iterations {#named-multi-iterations}

Here we reuse the [running example](#runningexample) yet with a slight variation: there are two JSON objects with the value `"a"` for `"id"`.

<pre class="ex-input">
[ 
  { "id": "a",  "values": [ "1" , "2" , "3" ] },
  { "id": "b",  "values": [ "4" , "5" , "6" ] },
  { "id": "a",  "values": [ "7" , "8" , "9" ] } 
]
</pre>

Let's consider the following mapping:

<pre class="ex-mapping">
  rr:predicateObjectMap [
    rr:predicate ex:with ;
    rr:objectMap [
        rml:gather ( [ rml:reference "values.*" ; ] ) ;
        rml:gatherAs rdf:List ;
    ] ;
  ] ;
</pre>

The gather map has no `rr:template`, `rr:constant` nor `rml:reference` property. The expected output consists of three lists, two related to `:a` and one to `:b`:

<pre class="ex-output">
  :a ex:with ("1" "2" "3"), ("7" "8" "9")  .
  :b ex:with ("4" "5" "6") .
</pre>

Now, when an `rr:template`, `rr:constant` or `rml:reference` is provided, 
the two collections related to id `"a"` cannot be generated separately since they would share the same head node IRI or bank node identifier, thus generating an [ill-formed collection](#wellformedness). Therefore, the processor must concatenate the two collections related to id `"a"`.
With the following predicate mapping:

<pre class="ex-mapping">
  rr:predicateObjectMap [
    rr:predicate ex:with ;
    rr:objectMap [
        rr:template "list/{id}" ;
        rml:gather ( [ rml:reference "values.*" ; ] ) ;
        rml:gatherAs rdf:List ;
    ] ;
  ] ;
</pre>

The processor must generate the following output:

<pre class="ex-output">
  :a ex:with :list/a . :list/a rdf:first "1" ; rdf:rest ("2" "3" "7" "8" "9") .
  :b ex:with :list/b . :list/b rdf:first "4" ; rdf:rest ("5" "6") .
</pre>

It is assumed that a processor will concatenate the collections or containers while respecting the order of the iterations as provided by the logical source.


#### Named collections or containers generated by a multi-valued gather map {#named-multivalued}

Let's consider the following input document:
<pre class="ex-input">
  { 
    "id": "myid",
    "a": [ "1" , "2" , "3" ],
    "b": [ "4" , "5" ] 
  }
</pre>

and the following mapping:

<pre class="ex-mapping">
  rr:subjectMap [ rr:template "{id}" ] ;

  rr:predicateObjectMap [
    rr:predicate ex:with ;
    rr:objectMap [
      rml:gather ( [ rml:reference "a.*" ] [ rml:reference "b.*" ]) ;
      rml:gatherAs rdf:List ;
      rml:strategy rml:cartesianProduct ;
    ] ;
  ] ;
</pre>

The gather map has no `rr:template`, `rr:constant` nor `rml:reference` property. 
As already illustrated, the [`rml:cartesianProduct`](#rml-cartesianproduct) strategy will generate multiple collections, yielding the output:

<pre class="ex-output">
:a ex:with ("1" "4"), ("1" "5"), ("2" "4"), ("2" "5"), ("3" "4"), ("3" "5") .
</pre>


Now, when an `rr:template`, `rr:constant` or `rml:reference` is provided, to avoid generating [ill-formed lists](#wellformedness) that would share the same head node IRI, the processor must concatenate the lists.

If we add an `rr:template` in the object map:
<pre class="ex-mapping">
    rr:objectMap [
      rr:template "list/{id}" ;
      rml:gather ( [ rml:reference "a.*" ] [ rml:reference "b.*" ]) ;
      rml:gatherAs rdf:List ;
      rml:strategy rml:cartesianProduct ;
    ] ;
</pre>

The processor must now generate the following output:

<pre class="ex-output">
:a ex:with ("1" "4" "1" "5" "2" "4" "2" "5" "3" "4" "3" "5" ).
</pre>



#### Named collections or containers generated across multiple iterations and with a multi-valued term map

An even more tricky situation combines the two previous sections, involving at the same time multiple iterations and multi-valued gather maps.

Let's consider the following document and mapping:

<pre class="ex-input">
[ 
  { "id": "a",  "values1": [ "1" ],       "values2": [ "a" , "b" ] },
  { "id": "b",  "values1": [ "3" , "4" ], "values2": [ "c" , "d" ] },
  { "id": "a",  "values1": [ "5" , "6" ], "values2": [ "e" ] } 
]
</pre>

<pre class="ex-mapping">
  rml:logicalSource [
    ...
    rml:iterator "$.*" ;
  ];

  rr:subjectMap [ rr:template "{id}" ] ;

  rr:predicateObjectMap [
    rr:predicate ex:with ;
    rr:objectMap [
        rr:template "list/{id}" ;
        rml:gather ( [ rml:reference "values1.*" ; ] [ rml:reference "values2.*" ; ] ) ;
        rml:gatherAs rdf:List ;
    ] ;
  ] ;
</pre>

For each document, the values of `values1` and `values2` are appended in the same list, as per the default [`rml:append`](#rml-append) strategy.
Furthermore, the lists generated for id `"a"` must be concatenated since they share the same head node IRI, as explained in the [multiple iterations](#named-multi-iterations) case.
The expected output is:

<pre class="ex-output">
  :a ex:with :list/a .
  :list/a rdf:first "1" ; rdf:rest ("a" "b" "5" "6" "e") .
  :b ex:with :list/b .
  :list/b rdf:first "3" ; rdf:rest ("4" "c" "d") .
</pre>

Now let's change the default strategy to `rml:cartesianProduct`:

<pre class="ex-mapping">
    rr:objectMap [
        rr:template "list/{id}" ;
        rml:gather ( [ rml:reference "values1.*" ; ] [ rml:reference "values2.*" ; ] ) ;
        rml:gatherAs rdf:List ;
        rml:strategy rml:cartesianProduct ;
    ] ;
</pre>

Each [iteration](#iterations) will now yield multiple lists by combining the values of `values1` and `values2`. 

For the document with id `"b"`, there are `("3" "c") ("3" "d") ("4" "c") ("4" "d")`.
But since the template generates the same IRI for all of them, they must be concatenated into a single list: `("3" "c" "3" "d" "4" "c" "4" "d")`, as explained in the [multi-valued gather map](#named-multivalued) case.

Similarly, for the documents with id `"a"`, the result is: `("1" "a" "1" "b")` and `("5" "e" "6" "e")`.
But again, these lists must be concatenated since they share the same head node IRI, as explained in the [multiple iterations](#named-multi-iterations) case.

Therefore, the processor must now generate the following output:

<pre class="ex-output">
  :a ex:with :list/a .
  :list/a rdf:first "1" ; rdf:rest ("a" "1" "b" "5" "e" "6" "e") .
  :b ex:with :list/b .
  :list/b rdf:first "3" ; rdf:rest ("c" "3" "d" "4" "c" "4" "d") .
</pre>