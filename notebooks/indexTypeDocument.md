---
site: https://api-notebook.anypoint.mulesoft.com/notebooks#db0804fb45e3090f33b7
apiNotebookVersion: 1.1.66
title: Elasticsearch (index/type/document) 
---

```javascript
load('https://github.com/chaijs/chai/releases/download/1.9.0/chai.js')
```

See http://chaijs.com/guide/styles/ for assertion styles



```javascript
assert = chai.assert;
```

```javascript
PROTOCOL = null
{
  useHttps = window.confirm("Do you want to use 'https' protocol?")
  if(useHttps){
    PROTOCOL = "https"
  }
  else{
    PROTOCOL = "http"
    window.alert("The protocol has been set to 'http'")
  }
}
DOMAIN = prompt("Please, enter domain of your Elasticsearch installation. For example, for 'http://192.168.0.1:9200' enter '192.168.0.1'")
PORT = prompt("Please, enter port of your Elasticsearch installation. For example, for 'http://192.168.0.1:9200' enter '9200'")
```

```javascript
API.createClient('client', '#REF_TAG_DEFENITION',{
  baseUriParameters:{
    protocol: PROTOCOL,
    domain: DOMAIN,
    port: PORT
  }
});
```

Name of index operated by the notebook.

```javascript
index = "notebook-index"
```

Name of type operated by the notebook.

```javascript
type = "notebook-type"
```

Delete an index that could have been created during earlier notebook runs. If the index does not exist, the method will return 404.

```javascript
deleteIndexResponse = client._index( index ).delete()
```

The index operation automatically creates an index if it has not been created before (check out the create index API for manually creating an index), and also automatically creates a dynamic type mapping for the specific type if one has not yet been created (check out the put mapping API for manually creating a type mapping).

```javascript
postMappingsIndexResponse = client._index( index ).post({
    "settings" : {
        "number_of_shards" : 1
    },
    "mappings" : {
        type : {
            "_source" : { "enabled" : true },
            "properties" : {
                "field1" : { "type" : "string", "index" : "not_analyzed" }
            }
        }
    }
})
```

```javascript
assert.equal( postMappingsIndexResponse.status, 200 )
```

The index API adds or updates a typed JSON document in a specific index, making it searchable


```javascript
documentId = 1000
```

```javascript
putDocumentResponse = client._index( index )._type( type ).id( documentId ).put({
  "user" : "kimchy" ,
  "post_date" : "2014-11-15T14:12:12" ,
  "message" : "To update the document"
})
```

```javascript
assert.equal( putDocumentResponse.status, 201 )
```

The get API allows to get a typed JSON document from the index based on its id

```javascript
documentResponse = client._index( index )._type( type ).id( documentId ).get()
```

```javascript
assert.equal( documentResponse.status, 200 )
```

The API also allows to check for the existence of a document using HEAD

```javascript
existDocumentResponse = client._index( index )._type( type ).id( documentId ).head()
```

```javascript
assert.equal( existDocumentResponse.status, 200 )
```

Here is an example of using option to specify create

```javascript
createResponse = client._index( index )._type( type ).id( 1 )._create.put({
    "counter" : 1,
    "tags" : ["red"]
})
```

```javascript
assert.equal( createResponse.status, 201 )
```

Use the endpoint to get just the _source field of the document, without any additional content around it

```javascript
sourceResponse = client._index( index )._type( type ).id( documentId )._source.get()
```

```javascript
assert.equal( sourceResponse.status, 200 )
```

Note, there is also a HEAD variant for the _source endpoint to efficiently test for document existence

```javascript
existSourceResponse = client._index( index )._type( type ).id( documentId )._source.head()
```

```javascript
assert.equal( existSourceResponse.status, 200 )
```

The update API allows to update a document based on a script provided. The operation gets the document (collocated with the shard) from the index, runs the script (with optional script language and parameters), and index back the result (also allows to delete, or ignore the operation). It uses versioning to make sure no updates have happened during the "get" and "reindex"

```javascript
updateResponse = client._index( index )._type( type ).id( 1 )._update.post({
    "script" : "ctx._source.counter += count",
    "params" : {
        "count" : 4
    }
})
```

```javascript
assert.equal( updateResponse.status, 200 )
```

The more like this (mlt) API allows to get documents that are "like" a specified document.

```javascript
mltResponse = client._index( index )._type( type ).id( documentId )._mlt.get()
```

```javascript
assert.equal( mltResponse.status, 200 )
```

Percolating an existing document

```javascript
percolateResponse = client._index( index )._type( type ).id( documentId )._percolate.get()
```

```javascript
assert.equal( percolateResponse.status, 200 )
```

The explain api computes a score explanation for a query and a specific document. This can give useful feedback whether a document matches or didn't match a specific query.
The index and type parameters expect a single index and a single type respectively.

```javascript
// not supported
// explainResponse = client._index( index )._type( type ).id( documentId )._explain.get({
//   "query" : {
//     "term" : { "message" : "To update the document" }
//   }
// })
```

```javascript
//assert.equal( explainResponse.status, 200 )
```

Returns information and statistics on terms in the fields of a particular document as stored in the index

```javascript
termvectorResponse = client._index( index )._type( type ).id( documentId )._termvector.get({
  "fields" : [
    "text"
  ] ,
  "offsets" : true ,
  "payloads" : true ,
  "positions" : true ,
  "term_statistics" : true ,
  "field_statistics" : true
})
```

```javascript
assert.equal( termvectorResponse.status, 200 )
```

The delete API allows to delete a typed JSON document from a specific index based on its id

```javascript
deleteDocumentResponse = client._index( index )._type( type ).id( documentId ).delete()
```

```javascript
assert.equal( deleteDocumentResponse.status, 200 )
```

Allow to delete a mapping (type) along with its data

```javascript
deleteTypeResponse = client._index( index )._type( type ).delete()
```

```javascript
assert.equal( deleteTypeResponse.status, 200 )
```

Garbage collection. Delete the created index.

```javascript
deleteIndexResponse = client._index( index ).delete()
```