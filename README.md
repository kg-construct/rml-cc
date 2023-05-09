# RML-CC

Collections and Containers in RML specification, SHACL shapes, ontology, and test-cases.

- Specification: http://w3id.org/rml/cc/spec
- Ontology: http://w3id.org/rml/cc/
- SHACL shapes: http://w3id.org/rml/cc/shapes
- Test-cases: [test-cases](./test-cases)

## Specification

The specification webpage is generated from directory [/spec](spec).

It is accessible using URL https://w3id.org/rml/cc/spec.

#### Edition quickstart

- cd to [/spec](spec)
- Edit dev.html
- Make sure all your local assets are in the `resources` folder, 
  and the links in your `dev.html` file are relative 
  (important because the publishing script creates multiple nested paths)
- Run a HTTP server in this directory: `python3 -m http.server`
- Open `dev.html` with a browser as http://localhost:8000/dev.html
- Save as snapshot to `rendered.html` [using the respec functionality](https://respec.org/docs/#using-browser) (button ReSpec/Export/HTML) and copy it to this same folder
- Run `node publish.js` to get the `index.html` + archived version in the [`docs`](docs) folder

URL https://w3id.org/rml/cc/spec must redirect to the `/spec/docs`(docs) folder.

## License

RML-CC (c) by W3C Community Group on Knowledge Graph Construction

RML-CC is licensed under a
Creative Commons Attribution-ShareAlike 4.0 International License.

You should have received a copy of the license along with this
work.  If not, see <http://creativecommons.org/licenses/by-sa/4.0/>.
