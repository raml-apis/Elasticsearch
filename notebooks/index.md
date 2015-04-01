---
site: https://api-notebook.anypoint.mulesoft.com/notebooks#e0851d7799e3e413e125
apiNotebookVersion: 1.1.66
title: Elasticsearch (index) 
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

Alias operated by the notebook.

```javascript
aliasName = "notebook-alias"
```

Name of warmer operated by the notebook.

```javascript
warmerName = "notebook-warmer"
```

Delete an index that could have been created during earlier notebook runs. If the index does not exist, the method will return 404.

```javascript
deleteIndexResponse = client._index( index ).delete()
```

The index operation automatically creates an index if it has not been created before (check out the create index API for manually creating an index), and also automatically creates a dynamic type mapping for the specific type if one has not yet been created (check out the put mapping API for manually creating a type mapping).

```javascript
postIndexResponse = client._index( index ).post({
  "settings" : {
    "index" : {
      "mapping.allow_type_wrapper" : true
    }
  }
})
```

```javascript
assert.equal( postIndexResponse.status, 200 )
```

Retrieve settings of index/indices.

```javascript
settingsResponse = client._index(index)._settings.get()
```

```javascript
assert.equal( settingsResponse.status, 200 );
```

Allows to control the allocation of indices on nodes based on include/exclude filters. The filters can be set both on the index level and on the cluster level. Lets start with an example of setting it on the cluster level. We can create an index that will only deploy on nodes that have tag set to value1 and value2 by setting index.routing.allocation.include.tag to value1,value2.

```javascript
settingsUpdateResponse = client._settings.put({
  "index.blocks.read" : false
});
```

```javascript
assert.equal( settingsUpdateResponse.status, 200 );
```

The delete index API allows to delete an existing index.

```javascript
deleteIndexResponse = client._index( index ).delete()
```

```javascript
assert.equal( deleteIndexResponse.status, 200 )
```

The create index API allows to instantiate an index. Elasticsearch provides support for multiple indices, including executing operations across several indices.

```javascript
putIndexResponse = client._index( index ).put()
```

```javascript
assert.equal( putIndexResponse.status, 200 )
```

The create index API allows to provide a set of one or more mappings   

```javascript
deleteIndexResponse = client._index( index ).delete()
```

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

Used to check if the index (indices) exists or not.

```javascript
headIndexResponse = client._index( index ).head()
```

```javascript
assert.equal( headIndexResponse.status, 200 )
```

The open and close index APIs allow to close an index, and later on opening it. 

```javascript
openIndexResponse = client._index( index )._open.post()
```

```javascript
assert.equal( openIndexResponse.status, 200 )
```

The mget endpoint can also be used against an index (in which case it is not required in the body)

```javascript
// not supported
// indexResponse = client._index( index )._mget.get({
//   "docs" : [
//     {
//       "_type" : type ,
//       "_id" : documentId
//     }
//   ]
// })
```

```javascript
//assert.equal( indexResponse.status, 200 )
```

The bulk API makes it possible to perform many index/delete operations in a single API call. This can greatly increase the indexing speed

```javascript
requestString = "{\"create\":{\"_index\":\""+index+"\",\"_type\":\""+type+"\",\"_id\":\"1100\"}} \n {\"user\":\"Jack\",\"message\":\"asd\",\"post_date\":\"2003-12-12\"} \n"
```

```javascript
postBulkResponse = client._index( index )._bulk.post( requestString )
```

```javascript
assert.equal( postBulkResponse.status, 200 )
```

Create new document for testing.

```javascript
postDocumentResponse = client._index( index )._type( type ).post({
  "user" : "kimchy" ,
  "post_date" : "2009-11-15T14:12:12" ,
  "message" : "trying out Elasticsearch"
})
```

The search shards api returns the indices and shards that a search request would be executed against. This can give useful feedback for working out issues or planning optimizations with routing and shard preferences.

```javascript
search_shardsResponse = client._index( index )._search_shards.get()
```

```javascript
assert.equal( search_shardsResponse.status, 200 )
```

The search API allows to execute a search query and get back search hits that match the query. The query can either be provided using a simple query string as a parameter, or using a request body.

```javascript
searchResponse = client._index( index )._search.get({
  "query" : {
    "term" : {
      "user" : "kimchy"
    }
  }
})
```

```javascript
assert.equal( searchResponse.status, 200 )
```

The count API allows to easily execute a query and get the number of matches for that query. It can be executed across one or more indices and across one or more types. The query can either be provided using a simple query string as a parameter, or using the Query DSL defined within the request body. 

```javascript
countResponse = client._index( index )._count.get({
  "query" : {
    "term" : {
      "user" : "kimchy"
    }
  }
})
```

```javascript
assert.equal( countResponse.status, 200 )
```

The validate API allows a user to validate a potentially expensive query without executing it

```javascript
validateResponse = client._index( index )._validate.query.get({
  "query" : {
    "filtered" : {
      "query" : {
        "query_string" : {
          "query" : "*:*"
        }
      },
      "filter" : {
        "term" : { "user" : "kimchy" }
      }
    }
  }
})
```

```javascript
assert.equal( validateResponse.status, 200 )
```

The delete by query API allows to delete documents from one or more indices and one or more types based on a query. The query can either be provided using a simple query string as a parameter, or using the Query DSL defined within the request body.

```javascript
deleteQueryResponse = client._index( index )._query.delete({
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

The get mapping API allows to retrieve mapping definitions for an index.

```javascript
mappingResponse = client._index( index )._mapping.get()
```

```javascript
assert.equal( mappingResponse.status, 200 )
```

The get mapping API allows to retrieve mapping definitions for an index/type.

```javascript
mappingTypesResponse = client._index( index )._mapping.types( type ).get()
```

```javascript
assert.equal( mappingTypesResponse.status, 200 )
```

Allow to delete a mapping (type) along with its data

```javascript
deleteMappingTypesResponse = client._index( index )._mapping.types( type ).delete()
```

```javascript
assert.equal( deleteMappingTypesResponse.status, 200 )
```

An alias can also be added to index with this endpoin

```javascript
putAliasResponse = client._index( index )._alias.aliasName( aliasName ).put()
```

```javascript
assert.equal( putAliasResponse.status, 200 )
```

The get index alias api allows to filter by alias name and index name. This api redirects to the master and fetches the requested index aliases, if available. This api only serialises the found index aliases

```javascript
aliasNameResponse = client._index( index )._alias.aliasName( aliasName ).get()
```

```javascript
assert.equal( aliasNameResponse.status, 200 )
```

Deletes alias with a given name

```javascript
deleteAliasNameResponse = client._index( index )._alias.aliasName( aliasName ).delete()
```

```javascript
assert.equal( deleteAliasNameResponse.status, 200 )
```

Performs the analysis process on a text and return the tokens breakdown of the text.

```javascript
analyzeResponse = client._index( index )._analyze.get({"text": "Analyze+text"})
```

```javascript
assert.equal( analyzeResponse.status, 200 )
```

Allows to put a warmup search request on a specific index (or indices), with the body composing of a regular search request. Types can be provided as part of the URI if the search request is designed to be run only against the specific types.

```javascript
putWarmerNameResponse = client._index( index )._warmer.warmerName( warmerName ).put({
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
warmerNameResponse = client._index( index )._warmer.warmerName( warmerName ).get()
```

```javascript
assert.equal( warmerNameResponse.status, 200 )
```

The indices status API allows to get a comprehensive status information of one or more indices.

```javascript
statusResponse = client._index( index )._status.get()
```

```javascript
assert.equal( statusResponse.status, 200 )
```

Indices level stats provide statistics on different operations happening on an index. The API provides statistics on the index level scope (though most stats can also be retrieved using node level scope).

```javascript
statSpecResponse = client._index( index )._stats.statSpec("indexing").get()
```

```javascript
assert.equal( statSpecResponse.status, 200 )
```

Provide low level segments information that a Lucene index (shard level) is built with. Allows to be used to provide more information on the state of a shard and an index, possibly optimization information, data "wasted" on deletes, and so on

```javascript
segmentsResponse = client._index( index )._segments.get()
```

```javascript
assert.equal( segmentsResponse.status, 200 )
```

The indices recovery API provides insight into on-going index shard recoveries. Recovery status may be reported for specific indices, or cluster-wide

```javascript
recoveryResponse = client._index( index )._recovery.get()
```

```javascript
assert.equal( recoveryResponse.status, 200 )
```

The clear cache API allows to clear either all caches or specific cached associated with one ore more indices

```javascript
postCacheClearResponse = client._index( index )._cache.clear.post()
```

```javascript
assert.equal( postCacheClearResponse.status, 200 )
```

The flush API allows to flush one or more indices through an API. The flush process of an index basically frees memory from the index by flushing data to the index storage and clearing the internal transaction log. By default, Elasticsearch uses memory heuristics in order to automatically trigger flush operations as required in order to clear memory

```javascript
postFlushResponse = client._index( index )._flush.post()
```

```javascript
assert.equal( postFlushResponse.status, 200 )
```

The refresh API allows to explicitly refresh one or more index, making all operations performed since the last refresh available for search. The (near) real-time capabilities depend on the index engine used. For example, the internal one requires refresh to be called, but by default a refresh is scheduled periodically

```javascript
postRefreshResponse = client._index( index )._refresh.post()
```

```javascript
assert.equal( postRefreshResponse.status, 200 )
```

The optimize API allows to optimize one or more indices through an API. The optimize process basically optimizes the index for faster search operations (and relates to the number of segments a Lucene index holds within each shard). The optimize operation allows to reduce the number of segments by merging them

```javascript
postOptimizeResponse = client._index( index )._optimize.post()
```

```javascript
assert.equal( postOptimizeResponse.status, 200 )
```

Adding a query to the percolator.

```javascript
putPercolatorResponse = client("/{_index}/.percolator/1000", { "_index": index }).put({
    "query" : {
        "match" : {
            "message" : "bonsai tree"
        }
    }
})
```

```javascript
assert.equal( putPercolatorResponse.status, 201 )
```

Returns percolator

```javascript
percolatorResponse = client("/{_index}/.percolator/1000", { "_index": index }).get()
```

```javascript
assert.equal( percolatorResponse.status, 200 )
```

In order to un-register a percolate query the delete api can be used.

```javascript
deletePercolatorResponse = client("/{_index}/.percolator/1000", { "_index": index }).delete()
```

```javascript
assert.equal( deletePercolatorResponse.status, 200 )
```

There are several percolate actions, because there are multiple types of percolate requests.

```javascript
mpercolateResponse = client._index( index )._mpercolate.post("{\"percolate\" : {\"type\" : \"user\", \"id\" : \"3\"}} \n {\"size\" : 10} \n")
```

```javascript
assert.equal( mpercolateResponse.status, 200 )
```

There are several percolate actions, because there are multiple types of percolate requests.

```javascript
// not supported
//mpercolateResponse = client._index( index )._mpercolate.get()
```

```javascript
//assert.equal( _mpercolateResponse.status, 200 )
```

The multi search API allows to execute several search requests within the same API.

```javascript
msearchResponse = client._index( index )._msearch.post("{} \n {\"query\" : {\"match_all\" : {}}} \n")
```

```javascript
assert.equal( msearchResponse.status, 200 )
```

The multi search API allows to execute several search requests within the same API. The endpoint for it is _msearch.

```javascript
// not supported
//msearchResponse = client._index( index )._msearch.get()
```

```javascript
//assert.equal( msearchResponse.status, 200 )
```


 A closed index has almost no overhead on the cluster (except for maintaining its metadata), and is blocked for read/write operations.

```javascript
closeIndexResponse = client._index( index )._close.post()
```

```javascript
assert.equal( closeIndexResponse.status, 200 )
```

Garbage collection. Delete the created index.

```javascript
client._index( index ).delete()
```