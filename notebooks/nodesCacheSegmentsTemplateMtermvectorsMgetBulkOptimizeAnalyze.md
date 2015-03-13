---
site: https://api-notebook.anypoint.mulesoft.com/notebooks#216c12d91095f2999799
apiNotebookVersion: 1.1.66
title: Nodes, cache, segments, optimize, analyze
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

Id of template operated by the notebook.

```javascript
templateId = "notebook-template-id"
```

Performs the analysis process on a text and return the tokens breakdown of the text.

```javascript
analyzeResponse = client._analyze.get({"text": "Analyze+text" })
```

```javascript
assert.equal( analyzeResponse.status, 200 )
```

Create a new index for our tests.

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

The clear cache API allows to clear either all caches or specific cached associated with one ore more indices

```javascript
doClear = window.confirm("Do you want to clean all caches for all indices?")
```

```javascript
if(doClear){
  cacheclearResponse = client._cache.clear.post()
}
```

```javascript
if(doClear){
  assert.equal( cacheclearResponse.status, 200 )
}
```

Provide low level segments information that a Lucene index (shard level) is built with. Allows to be used to provide more information on the state of a shard and an index, possibly optimization information, data "wasted" on deletes, and so on

```javascript
segmentsResponse = client._segments.get()
```

```javascript
assert.equal( segmentsResponse.status, 200 )
```

Create index template

```javascript
putTemplateResponse = client._template.templateId( templateId ).put({
  "template" : "te*" ,
  "settings" : {
        "number_of_shards" : 1
    },
    "mappings" : {
        "type1" : {
            "_source" : { "enabled" : false }
        }
    }
})
```

```javascript
assert.equal( putTemplateResponse.status, 200 )
```

Return index template

```javascript
templateResponse = client._template.templateId( templateId ).get()
```

```javascript
assert.equal( templateResponse.status, 200 )
```

Index templates are identified by a name and can be deleted as well

```javascript
deleteTemplateResponse = client._template.templateId( templateId ).delete()
```

```javascript
assert.equal( deleteTemplateResponse.status, 200 )
```

Multi termvectors API allows to get multiple termvectors based on an index, type and id. The response includes a docs array with all the fetched termvectors, each element having the 
structure provided by the termvectors API

```javascript
// not supported
//mtermvectorsResponse = client._mtermvectors.get()
```

```javascript
//assert.equal( mtermvectorsResponse.status, 200 )
```

Multi GET API allows to get multiple documents based on an index, type (optional) and id (and possibly routing). The response includes a docs array with all the fetched documents, each element similar in structure to a document provided by the get API

```javascript
// not supported
// mgetResponse = client._mget.get({
//   "docs" : [
//     {
//       "_index" : index ,
//       "_type" : type ,
//       "_id" : documentId
//     }
//   ]
// })
```

```javascript
//assert.equal( mgetResponse.status, 200 )
```

The possible actions are index, create, delete and update. index and create expect a source on the next line, and have the same semantics as the op_type parameter to the standard index API (i.e. create will fail if a document with the same index and type exists already, whereas index will add or replace a document as necessary). delete does not expect a source on the following line, and has the same semantics as the standard delete API. update expects that the partial doc, upsert and script and its options are specified on the next line.

```javascript
postBulkResponse = client._bulk.post("{\"create\":{\"_index\":\"notebook-index\",\"_type\":\"notebook-type\",\"_id\":\"2\"}} \n {\"user\":\"Jack\",\"message\":\"asd\",\"post_date\":\"2003-12-12\"} \n")
```

```javascript
assert.equal( postBulkResponse.status, 200 )
```

The cluster nodes info API allows to retrieve one or more (or all) of the cluster nodes information

```javascript
nodeInfoSpecResponse = client._nodes.nodeInfoSpec( "_all" ).get()
```

```javascript
assert.equal( nodeInfoSpecResponse.status, 200 )
```

The optimize API allows to optimize one or more indices through an API. The optimize process basically optimizes the index for faster search operations (and relates to the number of segments a Lucene index holds within each shard). The optimize operation allows to reduce the number of segments by merging them

```javascript
optimizeResponse = client._optimize.post()
```

```javascript
assert.equal( optimizeResponse.status, 200 )
```