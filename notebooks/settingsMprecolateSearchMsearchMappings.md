---
site: https://api-notebook.anypoint.mulesoft.com/notebooks#62f78d7c15dea3e8adbc
apiNotebookVersion: 1.1.66
title: Settings, mprecolate, search, mappings
---

```javascript
load('https://github.com/chaijs/chai/releases/download/1.9.0/chai.js');
```

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

Retrieve settings of all indices.

```javascript
settingsResponse = client._settings.get()
```

```javascript
assert.equal( settingsResponse.status, 200 );
```

Allows to control the allocation of indices on nodes based on include/exclude filters. The filters can be set both on the index level and on the cluster level. Lets start with an example of setting it on the cluster level. We can create an index that will only deploy on nodes that have tag set to value1 and value2 by setting index.routing.allocation.include.tag to value1,value2.

```javascript
doSet = window.confirm("Dou you wish to enable read operations for all of your indices i.e. set \"index.blocks.read\" to false?")
```

```javascript
if(doSet){
  settingsUpdateResponse = client._settings.put({
    "index.blocks.read" : false
  });
}
```

```javascript
if(doSet){
  assert.equal( settingsUpdateResponse.status, 200 );
}
```

The multi percolate api allows to bundle multiple percolate requests into a single request, similar to what the multi search api does to search requests. The request body format is line based. Each percolate request item takes two lines, the first line is the header and the second line is the body.

The header can contain any parameter that normally would be set via the request path or query string parameters. T here are several percolate actions, because there are multiple types of percolate requests.

```javascript
// not supported
//mpercolateResponse = client._mpercolate.get("{\"percolate\":{\"index\":\"twitter\",\"type\":\"tweet\"}}\n{\"doc\":{\"message\":\"sometext\"}}\n")
```

```javascript
//assert.equal( mpercolateResponse.status, 200 )
```

The multi percolate api allows to bundle multiple percolate requests into a single request, similar to what the multi search api does to search requests. The request body format is line based. Each percolate request item takes two lines, the first line is the header and the second line is the body.

The header can contain any parameter that normally would be set via the request path or query string parameters. T here are several percolate actions, because there are multiple types of percolate requests.

```javascript
mpercolateResponse = client._mpercolate.post("{\"percolate\":{\"index\":\"twitter\",\"type\":\"tweet\"}}\n{\"doc\":{\"message\":\"sometext\"}}\n")
```

```javascript
assert.equal( mpercolateResponse.status, 200 )
```

```javascript
searchResponse = client._search.get({
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

The /_search/template endpoint allows to use the mustache language to pre render search 
requests, before they are executed and fill existing templates with template parameters.

```javascript
templateResponse = client._search.template.get({
  "template" : {
    "query" : {
      "match" : {
        "title" : "{{query_string}}"
      }
    }
  } ,
  "params" : {
    "query_string" : "search for these words"
  }
})
```

```javascript
assert.equal( templateResponse.status, 200 )
```

The get mapping API allows to retrieve mapping definitions for an index or index/type

```javascript
mappingResponse = client._mapping.get()
```

```javascript
assert.equal( mappingResponse.status, 200 )
```

The get mapping API allows to retrieve mapping definitions for an index or index/type

```javascript
indexMappingResponse = client._mapping._index("").get()
```

```javascript
assert.equal( indexMappingResponse.status, 200 )
```

The multi search API allows to execute several search requests within the same API. The endpoint for it is _msearch.

The format of the request is similar to the bulk API format, and the structure is as follows (the structure is specifically optimized to reduce parsing if a specific search ends up redirected to another node):

```javascript
//not supported
//msearchResponse = client._msearch.get("{\"index\":\"test\"}\n{\"query\":{\"match_all\":{}},\"from\":0,\"size\":10}\n")
```

```javascript
//assert.equal( msearchResponse.status, 200 )
```

The possible actions are index, create, delete and update. index and create expect a source on the next line, and have the same semantics as the op_type parameter to the standard index API (i.e. create will fail if a document with the same index and type exists already, whereas index will add or replace a document as necessary). delete does not expect a source on the following line, and has the same semantics as the standard delete API. update expects that the partial doc, upsert and script and its options are specified on the next line

```javascript
msearchResponse = client._msearch.post("{\"index\":\"test\"}\n{\"query\":{\"match_all\":{}},\"from\":0,\"size\":10}\n")
```

```javascript
assert.equal( msearchResponse.status, 200 )
```