## RMLTC-CC-0009-DUP-Bag

**Title**: Gather duplicate values in an RDF Bag

**Description**: Testing the expected behavior of multi-valued expression maps containing duplicate values when these expression maps are used in a gather map to create an RDF Bag.

**Error expected?** No

**Input**
 [http://w3id.org/rml/resources/rml-io/RMLTC-CC-0009-DUP-Bag/Friends.json](http://w3id.org/rml/resources/rml-io/RMLTC-CC-0009-DUP-Bag/Friends.json)

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
            rml:gather ( [ rml:reference "$.values.*" ; ] ) ;
            rml:gatherAs rdf:Bag ;
        ] ;
    ] ;
.

_:b738439 a rml:RelativePathSource ;
    rml:root rml:MappingDirectory ;
    rml:path "data.json" .
```

**Output**
```
_:nc808fbd2e11a4baa939c61c8210b5909b1 <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://www.w3.org/1999/02/22-rdf-syntax-ns#Bag> .
_:nc808fbd2e11a4baa939c61c8210b5909b2 <http://www.w3.org/1999/02/22-rdf-syntax-ns#_2> "2" .
_:nc808fbd2e11a4baa939c61c8210b5909b2 <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://www.w3.org/1999/02/22-rdf-syntax-ns#Bag> .
_:nc808fbd2e11a4baa939c61c8210b5909b3 <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://www.w3.org/1999/02/22-rdf-syntax-ns#Bag> .
_:nc808fbd2e11a4baa939c61c8210b5909b3 <http://www.w3.org/1999/02/22-rdf-syntax-ns#_3> "9" .
_:nc808fbd2e11a4baa939c61c8210b5909b1 <http://www.w3.org/1999/02/22-rdf-syntax-ns#_2> "1" .
<http://example.com/base/e/b> <http://example.com/ns#with> _:nc808fbd2e11a4baa939c61c8210b5909b2 .
_:nc808fbd2e11a4baa939c61c8210b5909b1 <http://www.w3.org/1999/02/22-rdf-syntax-ns#_1> "1" .
<http://example.com/base/e/c> <http://example.com/ns#with> _:nc808fbd2e11a4baa939c61c8210b5909b3 .
<http://example.com/base/e/a> <http://example.com/ns#with> _:nc808fbd2e11a4baa939c61c8210b5909b1 .
_:nc808fbd2e11a4baa939c61c8210b5909b2 <http://www.w3.org/1999/02/22-rdf-syntax-ns#_1> "2" .
_:nc808fbd2e11a4baa939c61c8210b5909b3 <http://www.w3.org/1999/02/22-rdf-syntax-ns#_1> "7" .
_:nc808fbd2e11a4baa939c61c8210b5909b2 <http://www.w3.org/1999/02/22-rdf-syntax-ns#_3> "6" .
_:nc808fbd2e11a4baa939c61c8210b5909b1 <http://www.w3.org/1999/02/22-rdf-syntax-ns#_3> "1" .
_:nc808fbd2e11a4baa939c61c8210b5909b3 <http://www.w3.org/1999/02/22-rdf-syntax-ns#_2> "8" .
```

