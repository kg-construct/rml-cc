## RMLTC-CC-0011-Const

**Title**: Constant mapping can omit logical source

**Description**: Test a Triples Map with only constant expression in a gather map can omit logical source

**Error expected?** No

**Input**
 [http://w3id.org/rml/resources/rml-io/RMLTC-CC-0011-Const/Friends.json](http://w3id.org/rml/resources/rml-io/RMLTC-CC-0011-Const/Friends.json)

**Mapping**
```
@prefix rml: <http://w3id.org/rml/>.
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#>.
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>.

<http://example.com/base/TriplesMap1> a rml:TriplesMap;
  rml:subjectMap [ rml:constant <http://example.com/Gaston> ];
  rml:predicateObjectMap [
    rml:predicate rdfs:label;
    rml:objectMap [
      rml:gather ( [ rml:constant "Gaston Lagaffe" ] );
      rml:gatherAs rdf:Alt;
    ];
  ].


```

**Output**
```
<http://example.com/Gaston> <http://www.w3.org/2000/01/rdf-schema#label> _:b0 .
_:b0 <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://www.w3.org/1999/02/22-rdf-syntax-ns#Alt> .
_:b0 <http://www.w3.org/1999/02/22-rdf-syntax-ns#_1> "Gaston Lagaffe" .
```

