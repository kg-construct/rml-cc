## RMLTC-CC-0006-IT4

**Title**: Gather values across iterations to create a container.

**Description**: When using a template, constant, or reference for a gather map, this tests determines whether the values are correctly appended to the container. The natural order of the term maps inside the gather map as well as the iteration are respected. This test covers one term map in the gather map.

**Error expected?** No

**Input**
 [http://w3id.org/rml/resources/rml-io/RMLTC-CC-0006-IT4/Friends.json](http://w3id.org/rml/resources/rml-io/RMLTC-CC-0006-IT4/Friends.json)

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
            rml:template "c/{$.id}" ;
            rml:gather ( [ rml:reference "$.v1.*" ; ] ) ;
            rml:gatherAs rdf:Bag ;
        ] ;
    ] ;
.

_:b738439 a rml:RelativePathSource ;
    rml:path "data.json" .
```

**Output**
```
<http://example.com/base/e/a> <http://example.com/ns#with> <http://example.com/base/c/a> .
<http://example.com/base/c/a> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://www.w3.org/1999/02/22-rdf-syntax-ns#Bag> .
<http://example.com/base/c/a> <http://www.w3.org/1999/02/22-rdf-syntax-ns#_1> "1" .
<http://example.com/base/c/a> <http://www.w3.org/1999/02/22-rdf-syntax-ns#_2> "2" .
<http://example.com/base/c/a> <http://www.w3.org/1999/02/22-rdf-syntax-ns#_3> "5" .
<http://example.com/base/c/a> <http://www.w3.org/1999/02/22-rdf-syntax-ns#_4> "6" .

<http://example.com/base/e/b> <http://example.com/ns#with> <http://example.com/base/c/b> .
<http://example.com/base/c/b> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://www.w3.org/1999/02/22-rdf-syntax-ns#Bag> .
<http://example.com/base/c/b> <http://www.w3.org/1999/02/22-rdf-syntax-ns#_1> "3" .
<http://example.com/base/c/b> <http://www.w3.org/1999/02/22-rdf-syntax-ns#_2> "4" .
```

