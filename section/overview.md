## Overview and Example {#overview}
This section gives a brief overview of the R2RML mapping language. This section also provides simple examples that output collections and containers from documents and relational data.

A `rml:GatherMap` is an object map that generates collections (rdf:List) or containers (rdf:Bag, rdf:Seq, rdf:Alt). A gathermap has a list of term maps that inform the RML processor which terms have to be generated for the list or container. The predicate `rml:gather` is used to link these lists with instances of `rml:GatherMap`. The generation of a collection or container depends on the `rml:gatherAs` predicate, which may take on any of the following values: `rdf:List`, `rdf:Bag`, `rdf:Seq`,  and `rdf:Alt`. These are the three important construct for generating containers and containers. Other predicats, and their use in examples, will be explained further down this document.

### Example
In this section, we use the following relational database and document for our example.

The table `BOOK`:

| ID  | TITLE |
| --- | --- |
|  1  | Frankenstein |
|  2  | The Long Earth |


The table `AUTHOR`:

| ID | BOOKID | TITLE | FNAME | LNAME |
| --- | --- | --- | --- | --- |
| 1 | 1 | | Mary | Shelley |
| 2 | 2 | Sir | Terry | Pratchett |
| 3 | 2 | | Stephen | Baxter |
| 4 |||||

The following mapping will relate instances of authors to names. The names of authors are, for the sake of the example, represented as bags containing a title, first name, and lastname.

```
<#AuthorTM>
    rr:logicalTable [ rr:tableName "AUTHOR" ; ] ;
    rr:subjectMap [
        rr:template "/person/{ID}" ;
    ] ;
    rr:predicateObjectMap [
    rr:predicate ex:name ;
        rr:objectMap [
            rr:column "ID" ; rr:termType rr:BlankNode ;
            rml:gather ( 
                [ rr:column "TITLE" ]  [ rr:column "FNAME" ]  [ rr:column "LNAME" ] 
            ) ;
            rml:gatherAs rdf:Bag ;
        ] ;
    ] ;
.
```

In this example, we generate for each row in the table, an blank resource of the type `rdf:Bag`. Each such bag "gathers" values from different term maps. The execution of this mapping will produce the following RDF:

```
<person/1> ex:name [ a rdf:Bag; rdf:_1 "Mary"; rdf:_2 "Shelley" ] . 
<person/2> ex:name [ a rdf:Bag; rdf:_1 "Sir"; rdf:_2 "Terry"; rdf:_3 "Pratchett" ] . 
<person/3> ex:name [ a rdf:Bag; rdf:_1 "Stephen"; rdf:_2 "Baxter" ] .
```

While not shown in this example, different term maps allow to collect terms of different types: resources, literals, typed literals, language-tagged, etc. The fourth record in the table did not generate a bag, since each term map in the gather map did not yield a value. By default, empty lists and containers are wittheld. One does have the possibility to keep those with `rml:allowEmptyListAndContainer`.

In the following example, we relate books to authors with a `rr:parentTriplesMap`. Authors are represented by a list.

```
<#BookTM>
    rr:logicalTable [ rr:tableName "BOOK" ; ] ;
    rr:subjectMap [
        rr:template "/book/{ID}" ;
    ] ;
    rr:predicateObjectMap [
    rr:predicate ex:writtenBy ;
        rr:objectMap [
            rr:column "ID" ; rr:termType rr:BlankNode ;
            rml:gather ( 
                [ 
                    rr:parentTriplesMap <#AuthorTM>;
                    rr:joinCondition [ rr:child "ID" ; rr:parent "BOOKID" ; ] ;
                ] 
            ) ;
            rml:gatherAs rdf:List;
        ] ;
    ] ;
.
```

The execution of this mapping will produce the following RDF:


```
<book/1> ex:writtenby ( <person/1> ) . 
<book/2> ex:writtenby ( <person/2> <person/3> ) .
```