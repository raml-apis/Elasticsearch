---
site: https://api-notebook.anypoint.mulesoft.com/notebooks#255c6ef832e2f2b4a54e
apiNotebookVersion: 1.1.66
title: Elasticsearch (index/type) 
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
API.createClient('client', '/apiplatform/repository/public/organizations/30/apis/7941/versions/8099/definition',{
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

Name of warmer operated by the notebook.

```javascript
warmerName = "notebook-warmer"
```

Delete an index that could have been created during earlier notebook runs. If the index does not exist, the method will return 404.

```javascript
deleteIndexResponse = client._index( index ).delete()
```

Let's create a new index for our tests.

```javascript
postMappingsIndexResponse = client._index( index ).post({
    "settings" : {
        "number_of_shards" : 1
    },
    "mappings" : {
        type : {
            "_source" : { "enabled" : true },
            "properties" : {
                "field1" : { "type" : "string" }
            }
        }
    }
})
```

```javascript
assert.equal( postMappingsIndexResponse.status, 200 )
```

Create new document for testing.

```javascript
postDocumentResponse = client._index( index )._type( type ).post({
  "user" : "kimchy" ,
  "post_date" : "2009-11-15T14:12:12" ,
  "message" : "trying out Elasticsearch"
})
```

```javascript
assert.equal( postDocumentResponse.status, 201 )
```

Used to check if a type/types exists in an index/indices.The HTTP status code indicates if the type exists or not. A 404 means it does not exist, and  200  means it does.

```javascript
existTypeResponse = client._index( index )._type( type ).head()
```

```javascript
assert.equal( existTypeResponse.status, 200 )
```

The bulk API makes it possible to perform many index/delete operations in a single API call. This can greatly increase the indexing speed.

```javascript
requestString = "{\"create\":{\"_index\":\""+index+"\",\"_type\":\""+type+"\",\"_id\":\"1000 \"}} \n {\"user\":\"Jack\",\"message\":\"asd\",\"post_date\":\"2003-12-12\"} \n"
```

```javascript
postBulkResponse = client._index( index )._type( type )._bulk.post( requestString )
```

```javascript
assert.equal( postBulkResponse.status, 200 )
```

Search documents

```javascript
searchTypeResponse = client._index( index )._type( type )._search.get({
  "query" : {
    "term" : {
      "user" : "kimchy"
    }
  }
})
```

```javascript
assert.equal( searchTypeResponse.status, 200 )
```

The count API allows to easily execute a query and get the number of matches for that query.

```javascript
countTypeResponse = client._index( index )._type( type )._count.get({
  "query" : {
    "term" : {
      "user" : "kimchy"
    }
  }
})
```

```javascript
assert.equal( countTypeResponse.status, 200 )
```

The delete by query API allows to delete documents from one or more indices and one or more types based on a query. The query can either be provided using a simple query string as a parameter, or using the Query DSL defined within the request body

```javascript
deleteQueryResponse = client._index( index )._type( type )._query.delete({
  "query" : {
    "term" : {
      "user" : "kimchy"
    }
  }
})
```

```javascript
assert.equal( deleteQueryResponse.status, 200 )
```

Check the deleting

```javascript
countTypeResponse = client._index( index )._type( type )._count.get({
  "query" : {
    "term" : {
      "user" : "kimchy"
    }
  }
})
```

The put mapping API allows to register specific mapping definition for a specific type

```javascript
putMappingResponse = client._index( index )._type( type )._mapping.put({
    "properties" : {
      "message" : {
        "type" : "string" 
      }
    }
})
```

```javascript
assert.equal( putMappingResponse.status, 200 )
```

Allow to delete a mapping (type) along with its dat

```javascript
deleteMappingResponse = client._index( index )._type( type )._mapping.delete()
```

```javascript
assert.equal( deleteMappingResponse.status, 200 )
```

Allows to put a warmup search request on a specific index (or indices), with the body composing of a regular search request. Types can be provided as part of the URI if the search request is designed to be run only against the specific types.

```javascript
putWarmerNameResponse = client._index( index )._type( type )._warmer.warmer( warmerName ).put({
  "query" : {
    "match_all" : {
    }
  } ,
  "aggs" : {
    "aggs_1" : {
      "terms" : {
        "field" : "field"
      }
    }
  }
})
```

```javascript
assert.equal( putWarmerNameResponse.status, 200 )
```

Getting a warmer for specific index (or alias, or several indices) based on its name. The provided name can be a simple wildcard expression or omitted to get all warmers

```javascript
warmerNameResponse = client._index( index )._type( type )._warmer.warmer( warmerName ).get()
```

```javascript
assert.equal( warmerNameResponse.status, 200 )
```

Multi GET API allows to get multiple documents based on an index, type (optional) and id (and possibly routing). The response includes a docs array with all the fetched documents, each element similar in structure to a document provided by the get API

```javascript
// not supported
// mgetTypeResponse = client._index( index )._type( type )._mget.get({
//   "docs" : [
//     {
//       "_id" : documentId ,
//     } 
//   ]
// })
```

```javascript
//assert.equal( mgetTypeResponse.status, 200 )
```

The percolator works in the opposite direction, first you store queries into an index and then via the percolate api you define documents in order to retrieve these queries.

```javascript
percolateResponse = client._index( index )._type( type )._percolate.get()
```

```javascript
assert.equal( percolateResponse.status, 200 )
```

The count percolate api, only keeps track of the number of matches and doesn't keep track of the actual matches.

```javascript
countResponse = client._index( index )._type( type )._percolate.count.get()
```

```javascript
assert.equal( countResponse.status, 200 )
```

There are several percolate actions, because there are multiple types of percolate requests.

```javascript
postMpercolateResponse = client._index( index )._type( type )._mpercolate.post("{\"percolate\" : {\"id\" : \"3\"}} \n {\"size\" : 10} \n")
```

```javascript
assert.equal( postMpercolateResponse.status, 200 )
```

There are several percolate actions, because there are multiple types of percolate requests.

```javascript
// not supported
//mpercolateResponse = client._index( index )._type( type )._mpercolate.get()
```

```javascript
//assert.equal(  mpercolateResponse.status, 200 )
```

The multi search API allows to execute several search requests within the same API.

```javascript
msearchResponse = client._index( index )._type( type )._msearch.post("{} \n {\"query\" : {\"match_all\" : {}}} \n")
```

```javascript
assert.equal(  msearchResponse.status, 200 )
```

The multi search API allows to execute several search requests within the same API. The endpoint for it is _msearch.

```javascript
// not supported
//msearchResponse = client._index( index )._type( type )._msearch.get("\"query\":{\"term\":{\"user\":\"kimchy\"}} \n")
```

```javascript
//assert.equal( msearchResponse.status, 200 )
```

The validate API allows a user to validate a potentially expensive query without executing it

```javascript
validateQueryResponse = client._index( index )._type( type )._validate.query.get({ "text": "Validation+query+text" })
```

```javascript
assert.equal( validateQueryResponse.status, 200 )
```

The delete index API allows to delete an existing index.

```javascript
deleteIndexResponse = client._index( index ).delete()
```

```javascript
assert.equal( deleteIndexResponse.status, 200 )
```