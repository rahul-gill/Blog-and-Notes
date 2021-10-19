---
layout: default
name: _notes/dbms/NoSQL.md
categories: Databases
date: 2021-10-19
---
<script 
    type="text/javascript"
    src="https://unpkg.com/mermaid@8.13.2/dist/mermaid.min.js">
</script>

<link 
  rel="stylesheet" 
  href="https://cdn.jsdelivr.net/npm/katex@0.13.18/dist/katex.min.css" integrity="sha384-zTROYFVGOfTw7JV7KUu8udsvW2fx4lWOsCEDqhBreBwlHI4ioVRtmIvEThzJHGET" crossorigin="anonymous">

<script defer 
  src="https://cdn.jsdelivr.net/npm/katex@0.13.18/dist/katex.min.js" integrity="sha384-GxNFqL3r9uRJQhR+47eDxuPoNE7yLftQM8LcxzgS4HT73tp970WS/wV5p8UzCOmb" crossorigin="anonymous">
</script>

<script defer 
  src="https://cdn.jsdelivr.net/npm/katex@0.13.18/dist/contrib/auto-render.min.js" integrity="sha384-vZTG03m+2yp6N6BNi5iM4rW4oIwk5DfcNdFfxkk9ZWpDriOkXX8voJBFrAO7MpVl" crossorigin="anonymous">
</script>
<script>
    document.addEventListener("DOMContentLoaded", function() {
        renderMathInElement(document.body, {
          // customised options
          // • auto-render specific keys, e.g.:
          delimiters: [
              {left: '$$', right: '$$', display: true},
              {left: '$', right: '$', display: false},
              {left: '\[', right: '\]', dispaly: true}
          ],
          throwOnError : false
        });
    });
</script>
# NoSQL Properties
## Distributed database related properties
### Scalability 
- in NoSQL system, horizontal scalability is employed while the system is operational
- traditional system reads will be slow with this approach, for example when we want to do operation of some data which is stored in mutiple nodes
- NoSQL systems are scalable by this limits the functionalities provided by it
### Availability and eventual consistency:
- with replicating(copying) data across mutiple nodes, NoSQL systems prived higher availability
- this will make traditional systems slow, because due to consistency requirements, data needs to be updated on all replication nodes which result in slower writes
- NoSQL do this by giving up consistency and having eventual consistency: the data will eventually be synced across all nodes
- two major replication models
	- **master-slave replication**
		- requires one copy to be master copy
		- all write operations are applied to the master copy and then propagated to the slave copies
	- **master-master replication**
		- allows reads and writes at any of the replicas
# NoSQL data models
## Key-value store
- everythin stored as (key, values) pairs
- key: unique identifier string/integer
- value: can be anything
- two operations
	- get(key)
	- store(key, value)
- distribution
	- without replication: key k is stored at server h(k) (hash of k)
	- with replication: key k is stored at server h1(k), h2(k), h3(k), ...
## wide column stores
- variant 1: key = rowID, value = record
- variant 2: key = (rowID, columnID), value = record
	- or mutiple columnIDs in the  key
## Document Store
- in (key, value), value is semi-structured in document store data model
	- JSON or Protobuf or XML
	- value is called a document, but its basically data
### JSON
- flexible, nested strucutre(tree like)
- self-describing(doesn't require pre-defined schema)
- text representation: good for exchange(among multiple database), bad in performance
- queries done usually via an API
<br>

- types:
	- primitive: number, string, boolean, null
	- object: collection of name-value pairs; name also called keys ex
	- ordered arrays
- example
```json
	{
		"name": "rahul",
		"age": 20,
		"good_man": true,
		"girlfriend": null,
		"skills": ["android", "linux"]
	}
```
	
# MongoDB
- BASE
- consists collections of documents
	- documents are of form `{..., field: value, ...}`
	- where value can be atomic/document/atomic_array/document_array

|MongoDB|Relational|
|-|-|
|collection|relation|
|document|row/record|
|field|column|
- internally JSON stored as BSON(binary JSON)
- each document also have a special field `_id`
	- as primary key

