---
site: https://api-notebook.anypoint.mulesoft.com/notebooks/#3c072b9f6a6e7cf8fd69
apiNotebookVersion: 1.1.66
title: Warmer, status, flush, bench, stats
---

```javascript
load('https://github.com/chaijs/chai/releases/download/1.9.0/chai.js');
```

```javascript
var assert = chai.assert;
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

Name of warmer operated bt the notebook.

```javascript
warmerName = "notebook-warmer"
```

The flush API allows to flush one or more indices through an API. The flush process of an index basically frees memory from the index by flushing data to the index storage and clearing the internal transaction log. By default, Elasticsearch uses memory heuristics in order to automatically trigger flush operations as required in order to clear memory

```javascript
flushResponse = client._flush.post();
```

```javascript
assert.equal(flushResponse.status, 200);
```


Allows to put a warmup search request on a specific index (or indices), with the body composing of a regular search request. Types can be provided as part of the URI if the search request is designed to be run only against the specific types.

```javascript
warmerResponse = client._warmer.warmer_name(warmerName).put({
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
});
```

```javascript
assert.equal(warmerResponse.status, 200);
```

Allows to put a warmup search request on a specific index (or indices), with the body composing of a regular search request. Types can be provided as part of the URI if the search request is designed to be run only against the specific types.

```javascript
warmerUpdateResponse = client._warmers.warmer_name(warmerName).put({
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
});
```

```javascript
assert.equal(warmerUpdateResponse.status, 200);
```

The refresh API allows to explicitly refresh one or more index, making all operations performed since the last refresh available for search. The (near) real-time capabilities depend on the index engine used. For example, the internal one requires refresh to be called, but by default a refresh is scheduled periodically

```javascript
refreshResponse = client._refresh.post();
```

```javascript
assert.equal( refreshResponse.status, 200 );
```

Indices level stats provide statistics on different operations happening on an index. The API provides statistics on the index level scope (though most stats can also be retrieved using node level scope).

The following returns high level aggregation and index level stats for all indices

```javascript
statsResponse = client._stats.get();
```

```javascript
assert.equal(statsResponse.status, 200 );
```

Indices level stats provide statistics on different operations happening on an index. 

```javascript
statSpecResponse = client._stats.statSpec("statSpecValue").get();
```

```javascript
assert.equal(statSpecResponse.status, 200 );
```


The indices status API allows to get a comprehensive status information of one or more indices

```javascript
statusResponse = client._status.get();
```

```javascript
assert.equal( statusResponse.status, 200 );
```

The suggest feature suggests similar looking terms based on a provided text by using a suggester. Parts of the suggest feature are still under development

```javascript
suggestResponse = client._suggest.post({
  "my-suggestion" : {
    "text" : "the amsterdma meetpu" ,
    "term" : {
      "field" : "body"
    }
  }
})
```

```javascript
assert.equal( suggestResponse.status, 200 )
```

The indices recovery API provides insight into on-going index shard recoveries. Recovery status may be reported for specific indices, or cluster-wide

```javascript
recoveryResponse = client._recovery.get()
```

```javascript
assert.equal( recoveryResponse.status, 200 )
```


View all active benchmarks

```javascript
// not supported
// benchResponse = client._bench.get()
```

```javascript
// assert.equal( benchResponse.status, 200 )
```

The benchmark API provides a standard mechanism for submitting queries and measuring their performance relative to one another

```javascript
// not supported
// benchCreateResponse = client._bench.put({
//   "name" : "my_benchmark" ,
//   "competitors" : [
//     {
//       "name" : "my_competitor" ,
//       "requests" : [
//         {
//           "query" : {
//             "match" : {
//               "_all" : "a*"
//             }
//           }
//         }
//       ]
//     }
//   ]
// })
```

```javascript
// assert.equal( benchCreateResponse.status, 200 )
```

Abort a benchmark

```javascript
// not supported
// benchAbortResponse = client._bench.abort.benchName("benchNameValue").post()
```

```javascript
// assert.equal( benchAbortResponse.status, 200 )
```