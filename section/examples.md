## Examples {#examples}

In this section, we present additional examples and describe the expected output.


### Dealing with empty collections and containers

By default, [`rml:allowEmptyListAndContainer`](#rml-allowemptylistandcontainer)` is false. 
Thus, processing the following JSON document with the predicate object map provided in the [running example](#runningexample) would not yield any result for the docmment with `"id": "d"`.

```
[ 
  { "id": "a",  "values": [ "1" , "2" , "3" ] },
  { "id": "b",  "values": [ "4" , "5" , "6" ] },
  { "id": "c",  "values": [ "7" , "8" , "9" ] },
  { "id": "d",  "values": [] } 
]
```

However, when we override the value for this property and set it to true:

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

then the predicate object map will generate:

```
  <a> ex:with ("1" "2" "3") .
  <b> ex:with ("4" "5" "6") .
  <c> ex:with ("7" "8" "9") .
  <d> ex:with () .
```

There is one special case when dealing with empty *collections*. Since `rdf:nil` is reserved for the empty list, an RML processor MUST replace each IRI or blank node that is an empty list with `rdf:nil`. 
In other words, when the following predicate object map is used:

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

then the docmment with `"id": "d"` entails an empty list, that is a list whose head node is `rdf:nil` and therefore has no IRI.
We expect the following output where

```
  <a> ex:with <list/a> .
  <list/a> rdf:first "1" ; rdf:rest ("2" "3") .
  <b> ex:with <list/b> .
  <list/b> rdf:first "4" ; rdf:rest ("5" "6") .
  <c> ex:with <list/c> .
  <list/c> rdf:first "7" ; rdf:rest ("8" "9") .
  <d> ex:with () . 
```


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

```
<#AuthorTM>
    rr:logicalTable [ rr:tableName "AUTHOR" ; ] ;
    rr:subjectMap [ rr:template "/person/{ID}" ; ] ;
    rr:predicateObjectMap [
        rr:predicate ex:name ;
        rr:objectMap [
            rml:reference "ID" ; rr:termType rr:BlankNode ;
            rml:gather ( 
                [ rml:reference "TITLE" ]  [ rml:reference "FNAME" ]  [ rml:reference "LNAME" ] 
            ) ;
            rml:gatherAs rdf:Bag ;
        ] ;
    ] ;
.
```

In this example we generate, for each row in table `AUTHOR`, an blank node of type `rdf:Bag`. Each such bag "gathers" values from different term maps. The execution of this mapping will produce the following result:

```
<person/1> ex:name [ a rdf:Bag; rdf:_1 "Mary"; rdf:_2 "Shelley" ] . 
<person/2> ex:name [ a rdf:Bag; rdf:_1 "Sir"; rdf:_2 "Terry"; rdf:_3 "Pratchett" ] . 
<person/3> ex:name [ a rdf:Bag; rdf:_1 "Stephen"; rdf:_2 "Baxter" ] .
```

While not shown in this example, different term maps allow to collect terms of different types: resources, literals, typed or language-tagged literals, etc. The fourth record in the table did not generate a bag, since each term map in the gather map did not yield a value. 
By default, empty lists and containers are withheld. One does have the possibility to keep those with [`rml:allowEmptyListAndContainer`](#rml-allowemptylistandcontainer)`.


### Using referencing object map

Continuing with the [relational data example](#relationalexample), here we relate books to authors with a `rr:parentTriplesMap`. The authors of a book are represented as a list.

```
<#BookTM>
    rr:logicalTable [ rr:tableName "BOOK" ; ] ;
    rr:subjectMap [ rr:template "/book/{ID}" ; ] ;
    rr:predicateObjectMap [
        rr:predicate ex:writtenBy ;
        rr:objectMap [
            rml:reference "ID" ; rr:termType rr:BlankNode ;
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
