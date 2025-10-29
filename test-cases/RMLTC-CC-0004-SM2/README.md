## RMLTC-CC-0004-SM2

**Title**: GatherMap in subject map (list)

**Description**: Tests if the use of a gather map in the subject map is supported. This test has no predicate-object maps and no class declarations in the subject map. It still needs to generate triples as the lists consists of at least one cons-pair.

**Error expected?** No

**Input**
 [http://w3id.org/rml/resources/rml-io/RMLTC-CC-0004-SM2/Friends.json](http://w3id.org/rml/resources/rml-io/RMLTC-CC-0004-SM2/Friends.json)

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
        rml:gather ( [ rml:reference "$.values.*" ; ] ) ;
        rml:gatherAs rdf:List ;
    ] ;
.

_:b738439 a rml:RelativePathSource ;
    rml:root rml:MappingDirectory ;
    rml:path "data.json" .
```

**Output**
```
_:n302eecc856b44ed5b76965d9be2f5213b1 <http://www.w3.org/1999/02/22-rdf-syntax-ns#first> "1" .
_:n302eecc856b44ed5b76965d9be2f5213b1 <http://www.w3.org/1999/02/22-rdf-syntax-ns#rest> _:n302eecc856b44ed5b76965d9be2f5213b2 .
_:n302eecc856b44ed5b76965d9be2f5213b2 <http://www.w3.org/1999/02/22-rdf-syntax-ns#first> "2" .
_:n302eecc856b44ed5b76965d9be2f5213b2 <http://www.w3.org/1999/02/22-rdf-syntax-ns#rest> _:n302eecc856b44ed5b76965d9be2f5213b3 .
_:n302eecc856b44ed5b76965d9be2f5213b3 <http://www.w3.org/1999/02/22-rdf-syntax-ns#first> "3" .
_:n302eecc856b44ed5b76965d9be2f5213b3 <http://www.w3.org/1999/02/22-rdf-syntax-ns#rest> <http://www.w3.org/1999/02/22-rdf-syntax-ns#nil> .
_:n1d393f12e993411498da676131f81ca4b1 <http://www.w3.org/1999/02/22-rdf-syntax-ns#first> "4" .
_:n1d393f12e993411498da676131f81ca4b1 <http://www.w3.org/1999/02/22-rdf-syntax-ns#rest> _:n1d393f12e993411498da676131f81ca4b2 .
_:n1d393f12e993411498da676131f81ca4b2 <http://www.w3.org/1999/02/22-rdf-syntax-ns#first> "5" .
_:n1d393f12e993411498da676131f81ca4b2 <http://www.w3.org/1999/02/22-rdf-syntax-ns#rest> _:n1d393f12e993411498da676131f81ca4b3 .
_:n1d393f12e993411498da676131f81ca4b3 <http://www.w3.org/1999/02/22-rdf-syntax-ns#first> "6" .
_:n1d393f12e993411498da676131f81ca4b3 <http://www.w3.org/1999/02/22-rdf-syntax-ns#rest> <http://www.w3.org/1999/02/22-rdf-syntax-ns#nil> .

```

