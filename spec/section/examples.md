## Examples (Informative) {#examples}

In this section, we present additional examples and describe the expected output.


### Dealing with empty collections and containers

By default, [`rml:allowEmptyListAndContainer`](#rml-allowemptylistandcontainer) is false. 
Thus, processing the following JSON document with the predicate object map provided in the [running example](#runningexample) would not yield any result for the document with `"id": "d"`.

<pre class="ex-input">
[ 
  { "id": "a",  "values": [ "1" , "2" , "3" ] },
  { "id": "b",  "values": [ "4" , "5" , "6" ] },
  { "id": "c",  "values": [ "7" , "8" , "9" ] },
  { "id": "d",  "values": [] } 
]
</pre>

However, when we override the value for this property and set it to true:

<pre class="ex-mapping">
  rml:predicateObjectMap [
    rml:predicate ex:with ;
    rml:objectMap [
        rml:allowEmptyListAndContainer true ;
        rml:gather ( [ rml:reference "values.*" ; ] ) ;
        rml:gatherAs rdf:List ;
    ] ;
  ] ;
</pre>

the predicate object map will generate:

<pre class="ex-output">
  :a ex:with ("1" "2" "3") .
  :b ex:with ("4" "5" "6") .
  :c ex:with ("7" "8" "9") .
  :d ex:with () .
</pre>

There is one special case when dealing with empty *collections*. Since `rdf:nil` is reserved for the empty list, an RML processor MUST replace each IRI or blank node that is an empty list with `rdf:nil`. 
In other words, when the following predicate object map is used:

<pre class="ex-mapping">
  rml:predicateObjectMap [
    rml:predicate ex:with ;
    rml:objectMap [
        rml:template "list/{id}" ;
        rml:allowEmptyListAndContainer true ;
        rml:gather ( [ rml:reference "values.*" ; ] ) ;
        rml:gatherAs rdf:List ;
    ] ;
  ] ;
</pre>

then the document with `"id": "d"` entails an empty list, that is a list whose head node is `rdf:nil` and therefore has no IRI.
We expect the following output where

<pre class="ex-output">
  :a ex:with :list/a .
  :list/a rdf:first "1" ; rdf:rest ("2" "3") .
  :b ex:with :list/b .
  :list/b rdf:first "4" ; rdf:rest ("5" "6") .
  :c ex:with :list/c .
  :list/c rdf:first "7" ; rdf:rest ("8" "9") .
  :d ex:with () . 
</pre>


### Relational data example {#relationalexample}

In this section, we use the following relational database and document for our example.

Table `BOOK`:

| ID  | TITLE |
| --- | --- |
|  1  | Frankenstein |
|  2  | The Long Earth |


Table `AUTHOR`:

| ID | TITLE | FNAME | LNAME | BOOKID | 
| --- | --- | --- | --- | --- |
| 1 | | Mary | Shelley | 1 | 
| 2 | Sir | Terry | Pratchett | 2 | 
| 3 | | Stephen | Baxter | 2 | 
| 4 |||||

The following mapping will relate instances of authors to names. The names of authors are, for the sake of the example, represented as bags containing a title, first name, and lastname.

<pre class="ex-mapping">
<#AuthorTM>
    rml:logicalTable [ rml:tableName "AUTHOR" ; ] ;
    rml:subjectMap [ rml:template "/person/{ID}" ; ] ;
    rml:predicateObjectMap [
        rml:predicate ex:name ;
        rml:objectMap [
            rml:reference "ID" ; rml:termType rml:BlankNode ;
            rml:gather ( 
                [ rml:reference "TITLE" ]  [ rml:reference "FNAME" ]  [ rml:reference "LNAME" ] 
            ) ;
            rml:gatherAs rdf:Bag ;
        ] ;
    ] ;
.
</pre>

In this example we generate, for each row in table `AUTHOR`, an blank node of type `rdf:Bag`. Each such bag "gathers" values from different term maps. The execution of this mapping will produce the following result:

<pre class="ex-output">
:person/1 ex:name [ a rdf:Bag; rdf:_1 "Mary"; rdf:_2 "Shelley" ] . 
:person/2 ex:name [ a rdf:Bag; rdf:_1 "Sir"; rdf:_2 "Terry"; rdf:_3 "Pratchett" ] . 
:person/3 ex:name [ a rdf:Bag; rdf:_1 "Stephen"; rdf:_2 "Baxter" ] .
</pre>

While not shown in this example, different term maps allow to collect terms of different types: resources, literals, typed or language-tagged literals, etc. The fourth record in the table did not generate a bag, since each term map in the gather map did not yield a value. 
By default, empty lists and containers are withheld. One does have the possibility to keep those with [`rml:allowEmptyListAndContainer`](#rml-allowemptylistandcontainer)`.


### Using referencing object map

Continuing with the [relational data example](#relationalexample), here we relate books to authors with a `rml:parentTriplesMap`. The authors of a book are represented as a list.

<pre class="ex-mapping">
<#BookTM>
    rml:logicalTable [ rml:tableName "BOOK" ; ] ;
    rml:subjectMap [ rml:template "/book/{ID}" ; ] ;
    rml:predicateObjectMap [
        rml:predicate ex:writtenBy ;
        rml:objectMap [
            rml:reference "ID" ; rml:termType rml:BlankNode ;
            rml:gather ( 
                [ 
                    rml:parentTriplesMap <#AuthorTM>;
                    rml:joinCondition [ rml:child "ID" ; rml:parent "BOOKID" ; ] ;
                ] 
            ) ;
            rml:gatherAs rdf:List;
        ] ;
    ] ;
.
</pre>

Intuitively, we will join each record (or iteration) with data from the parent triples map. The join may yield one or more results, which are then gathered into a list. The execution of this mapping will produce the following RDF:

<pre class="ex-output">
:book/1 ex:writtenby ( :person/1 ) . 
:book/2 ex:writtenby ( :person/2 :person/3 ) .
</pre>

In RML, it is assumed that each term map is multi-valued. That this, each term map may return one or more values. The default behavior is to append the values in the order of the term maps appearing in the gather map.


### Using a gather map in a subject map {#gatherinsubject}

Here we exemplify the use of a term map in a subject map. Continuing with the JSON file from the [running example](#runningexample), the following mapping generates an RDF sequence whose head node is used to state provenance information on that sequence:

<pre class="ex-mapping">
<#TM> a rml:TriplesMap;
  rml:logicalSource [
    rml:source "data.json" ;
    rml:referenceFormulation ql:JSONPath ;
    rml:iterator "$.*" ;
  ];

  rml:subjectMap [
    rml:template "seq/{id}" ;
    rml:gather ( [ rml:reference "values.*" ; ] ) ;
    rml:gatherAs rdf:Seq ;  
  ] ;
  
  rml:predicateObjectMap [
    rml:predicate prov:wasDerivedFrom ;
    rml:object &lt;data.json&gt; ;
  ] .
</pre>

The expected result is:

<pre class="ex-output">
  :seq/a rdf:_1 "1" ; rdf:_2 "2" ; rdf:_3 "3" .
  :seq/a prov:wasDerivedFrom &lt;data.json&gt; .
  
  :seq/b rdf:_1 "4" ; rdf:_2 "5" ; rdf:_3 "6" .
  :seq/b prov:wasDerivedFrom &lt;data.json&gt; .
  
  :seq/c rdf:_1 "7" ; rdf:_2 "8" ; rdf:_3 "9" .
  :seq/c prov:wasDerivedFrom &lt;data.json&gt; .
</pre>
