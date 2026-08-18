## RMLTC-CC-0012-NonConst

**Title**: Non constant mapping, while ommiting logical source

**Description**: Test a Triples Map with only reference expression in a gather map cannot omit logical source

**Error expected?** Yes

**Input**
 [http://w3id.org/rml/resources/rml-io/RMLTC-CC-0012-NonConst/Friends.json](http://w3id.org/rml/resources/rml-io/RMLTC-CC-0012-NonConst/Friends.json)

**Mapping**
```
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>.
@prefix rml: <http://w3id.org/rml/>.
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#>.

<http://example.com/base/TriplesMap1> a rml:TriplesMap;
  rml:subjectMap [ rml:constant <http://example.com/Gaston> ];
  rml:predicateObjectMap [
    rml:predicate rdfs:label;
    rml:objectMap [
      rml:gather ( [ rml:reference "{name}" ] );
      rml:gatherAs rdf:Alt;
    ];
  ].


```

