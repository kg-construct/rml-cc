## RMLTC-CC-0003-EL

**Title**: Allow the generation of empty lists

**Description**: Tests if the use of rml:allowEmptyListAndContainer yields an empty list.

**Error expected?** No

**Input**
 [http://w3id.org/rml/resources/rml-io/RMLTC-CC-0003-EL/Friends.json](http://w3id.org/rml/resources/rml-io/RMLTC-CC-0003-EL/Friends.json)

**Mapping**
```
@prefix rml: <http://w3id.org/rml/>.
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>.
@prefix ex:  <http://example.com/ns#>.
@base        <http://example.com/>.

<#TM> a rml:TriplesMap;
    rml:logicalSource [
        rml:source _:b738439 ;
        rml:referenceFormulation rml:JSONPath ;
        rml:iterator "$.*" ;
    ] ;

    rml:subjectMap [
        rml:template "e/{$.id}" ;
    ] ;

    rml:predicateObjectMap [
        rml:predicate ex:with ;
        rml:objectMap [
            rml:allowEmptyListAndContainer true ;
            rml:gather ( [ rml:reference "$.values.*" ; ] ) ;
            rml:gatherAs rdf:List ;
        ] ;
    ] ;
.

_:b738439 a rml:RelativePathSource ;
    rml:path "data.json" .
```

**Output**
```
_:n302eecc856b44ed5b76965d9be2f5213b3 <http://www.w3.org/1999/02/22-rdf-syntax-ns#rest> <http://www.w3.org/1999/02/22-rdf-syntax-ns#nil> .
<http://example.com/base/e/b> <http://example.com/ns#with> <http://www.w3.org/1999/02/22-rdf-syntax-ns#nil> .
_:n302eecc856b44ed5b76965d9be2f5213b2 <http://www.w3.org/1999/02/22-rdf-syntax-ns#rest> _:n302eecc856b44ed5b76965d9be2f5213b3 .
_:n302eecc856b44ed5b76965d9be2f5213b2 <http://www.w3.org/1999/02/22-rdf-syntax-ns#first> "2" .
_:n302eecc856b44ed5b76965d9be2f5213b3 <http://www.w3.org/1999/02/22-rdf-syntax-ns#first> "3" .
_:n302eecc856b44ed5b76965d9be2f5213b1 <http://www.w3.org/1999/02/22-rdf-syntax-ns#rest> _:n302eecc856b44ed5b76965d9be2f5213b2 .
<http://example.com/base/e/a> <http://example.com/ns#with> _:n302eecc856b44ed5b76965d9be2f5213b1 .
_:n302eecc856b44ed5b76965d9be2f5213b1 <http://www.w3.org/1999/02/22-rdf-syntax-ns#first> "1" .
```

