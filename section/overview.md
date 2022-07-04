## Overview and Example {#overview}
This section gives a brief overview of the RML mapping language. 
It also provides simple examples of the generation of RDF collections and containers from documents and relational data.

Herebelow we present the three main constructs for generating collections and containers. Other predicates, and their use in examples, will be explained further down this document.

An `rml:GatherMap` is an term map that generates a collection (rdf:List) or container (rdf:Bag, rdf:Seq, rdf:Alt). 
A gather map has a list of term maps that inform the RML processor which RDF terms have to be generated as members of the list or container. 
The `rml:gather` predicate is used to link an instance of `rml:GatherMap` with a list of term maps. The generation of a collection or container depends on the `rml:gatherAs` predicate, which may take on any of the following values: `rdf:List`, `rdf:Bag`, `rdf:Seq`,  and `rdf:Alt`.


### Example
In this section, we use the following relational database and document for our example.

Table `BOOK`:

| ID  | TITLE |
| --- | --- |
|  1  | Frankenstein |
|  2  | The Long Earth |


Table `AUTHOR`:

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

In this example we generate, for each row in the table, an blank resource of type `rdf:Bag`. Each such bag "gathers" values from different term maps. The execution of this mapping will produce the following RDF:

```
<person/1> ex:name [ a rdf:Bag; rdf:_1 "Mary"; rdf:_2 "Shelley" ] . 
<person/2> ex:name [ a rdf:Bag; rdf:_1 "Sir"; rdf:_2 "Terry"; rdf:_3 "Pratchett" ] . 
<person/3> ex:name [ a rdf:Bag; rdf:_1 "Stephen"; rdf:_2 "Baxter" ] .
```

While not shown in this example, different term maps allow to collect terms of different types: resources, literals, typed literals, language-tagged, etc. The fourth record in the table did not generate a bag, since each term map in the gather map did not yield a value. 
By default, empty lists and containers are withheld. One does have the possibility to keep those with `rml:allowEmptyListAndContainer`.

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

Intuitively, we will join each record (or iteration) with data from the parent triples map. The join may yield one or more results, which are then gathered into a list. The execution of this mapping will produce the following RDF:

```
<book/1> ex:writtenby ( <person/1> ) . 
<book/2> ex:writtenby ( <person/2> <person/3> ) .
```

In RML, it is assumed that each term map is multi-valued. That this, each term map may return one or more values. The default behavior is to append the values in the order of the term maps appearing in the gather map.