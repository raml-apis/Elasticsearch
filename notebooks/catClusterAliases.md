---
site: https://api-notebook.anypoint.mulesoft.com/notebooks#072184af1121677de6c9
apiNotebookVersion: 1.1.66
title: Elasticsearch (cat, cluster, aliases)
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
alias = "notebook-alias"
```

Delete an index that could have been created during earlier notebook runs. If the index does not exist, the method will return 404.

```javascript
deleteIndexResponse = client._index( index ).delete()
```

Create a new index for our tests.

```javascript
postMappingsIndexResponse = client._index( index ).post({
    "settings" : {
      "number_of_shards" : 2 ,
      "number_of_replicas" : 1
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



The nodes command shows the cluster topology

```javascript
nodesResponse = client._cat.nodes.get()
```

```javascript
assert.equal( nodesResponse.status, 200 )
```

health is a terse, one-line representation of the same information from /_cluster/health. It has one option ts to disable the timestamping

```javascript
healthResponse = client._cat.health.get()
```

```javascript
assert.equal( healthResponse.status, 200 )
```

The indices command provides a cross-section of each index. This information spans nodes

```javascript
indicesResponse = client._cat.indices.get()
```

```javascript
assert.equal( indicesResponse.status, 200 )
```

fielddata shows information about currently loaded fielddata on a per-node basis

```javascript
fielddataResponse = client._cat.fielddata.get()
```

```javascript
assert.equal( fielddataResponse.status, 200 )
```

pending_tasks provides the same information as the /_cluster/pending_tasks API in a convenient tabular format

```javascript
pending_tasksResponse = client._cat.pending_tasks.get()
```

```javascript
assert.equal( pending_tasksResponse.status, 200 )
```

allocation provides a snapshot of how shards have located around the cluster and the state of disk usage

```javascript
allocationResponse = client._cat.allocation.get()
```

```javascript
assert.equal( allocationResponse.status, 200 )
```

The thread_pool command shows cluster wide thread pool statistics per node. By default the active, queue and rejected statistics are returned for the bulk, index and search thread pools

```javascript
thread_poolResponse = client._cat.thread_pool.get()
```

```javascript
assert.equal( thread_poolResponse.status, 200 )
```

The shards command is the detailed view of what nodes contain which shards. It will tell you if it's 
a primary or replica, the number of docs, the bytes it takes on disk, and the node where it's located.

```javascript
shardsResponse = client._cat.shards.get()
```

```javascript
assert.equal( shardsResponse.status, 200 )
```

Let's delete an alias that could have been created during earlier notebook runs. If the alias does not exist, the method will return 404.

```javascript
postAliasesResponse = client._aliases.post({
  "actions" : [
    {
      "remove" : {
        "index" : index ,
        "alias" : alias
      }
    }
  ]
})
```

APIs in elasticsearch accept an index name when working against a specific index, and several indices when applicable. The index aliases API allow to alias an index with a name, with all APIs automatically converting the alias name to the actual index name. An alias can also be mapped to more than one index, and when specifying it, the alias will automatically expand to the aliases indices. An alias can also be associated with a filter that will automatically be applied when searching, and routing values.

```javascript
postAliasesResponse = client._aliases.post({
  "actions" : [
    {
      "add" : {
        "index" : index ,
        "alias" : alias
      }
    }
  ]
})
```

```javascript
assert.equal( postAliasesResponse.status, 200 )
```

aliases shows information about currently configured aliases to indices including filter and 
routing infos.

```javascript
aliasesResponse = client._cat.aliases.get()
```

```javascript
assert.equal( aliasesResponse.status, 200 )
```

Remove the alias.

```javascript
postAliasesResponse = client._aliases.post({
  "actions" : [
    {
      "remove" : {
        "index" : index ,
        "alias" : alias
      }
    }
  ]
})
```

The recovery command is a view of index shard recoveries, both on-going and previously completed. It is a more compact view of the JSON recovery API

```javascript
recoveryResponse = client._cat.recovery.get()
```

```javascript
assert.equal( recoveryResponse.status, 200 )
```

The plugins command provides a view per node of running plugins. This information spans nodes

```javascript
pluginsResponse = client._cat.plugins.get()
```

```javascript
assert.equal( pluginsResponse.status, 200 )
```

master doesn't have any extra options. It simply displays the master's node ID, bound IP address, and node name

```javascript
masterResponse = client._cat.master.get()
```

```javascript
assert.equal( masterResponse.status, 200 )
```

count provides quick access to the document count of the entire cluster, or individual indices

```javascript
countIndicesSelectionResponse = client._cat.count.indicesSelection( index ).get()
```

```javascript
assert.equal( countIndicesSelectionResponse.status, 200 )
```

The pending cluster tasks API returns a list of any cluster-level changes (e.g. create index, update mapping, allocate or fail shard) which have not yet been executed

```javascript
pending_tasksClusterResponse = client._cluster.pending_tasks.get()
```

```javascript
assert.equal( pending_tasksClusterResponse.status, 200 )
```

The Cluster Stats API allows to retrieve statistics from a cluster wide perspective. The API returns basic index metrics (shard numbers, store size, memory usage) and information about the current nodes that form the cluster (number, roles, os, jvm versions, memory usage, cpu and installed plugins)

```javascript
statsResponse = client._cluster.stats.get()
```

```javascript
assert.equal( statsResponse.status, 200 )
```

The cluster state API allows to get a comprehensive state information of the whole cluster

```javascript
stateResponse = client._cluster.state.get()
```

```javascript
assert.equal( stateResponse.status, 200 )
```

As the cluster state can grow (depending on the number of shards and indices, your mapping, templates), it is possible to filter the cluster state response specifying the parts in the URL.

```javascript
metricsIndicesResponse = client._cluster.state.metrics("_all").indices( index ).get()
```

```javascript
assert.equal( metricsIndicesResponse.status, 200 )
```

Cluster wide settings can be returned usin

```javascript
settingsResponse = client._cluster.settings.get()
```

```javascript
assert.equal( settingsResponse.status, 200 )
```

Check if the cluster setting is already assigned

```javascript
clusterRoutingAllocationEnable = "all"
var persistent = settingsResponse.body.persistent
if(persistent){
  var cluster = persistent.cluster
  if(cluster){
    var routing = cluster.routing
    if(routing){
      var allocation = routing.allocation
      if(allocation){
        var enable = allocation.enable
        if(enable){
          clusterRoutingAllocationEnable = enable
        }
      }
    }
  }
}
```

Allows to update cluster wide specific settings. Settings updated can either be persistent (applied cross restarts) or transient (will not survive a full cluster restart.

```javascript
putSettingsResponse = client._cluster.settings.put({
  "persistent" : {
    "indices.cache.filter.size" : clusterRoutingAllocationEnable
  },
  "transient" : {
  }
})
```

```javascript
assert.equal( putSettingsResponse.status, 200 )
```

The cluster health API allows to get a very simple status on the health of the cluster

```javascript
healthIndicesListResponse = client._cluster.health.indicesList( index ).get()
```

```javascript
assert.equal( healthIndicesListResponse.status, 200 )
```

Get a shard of out index and a node it is hosted on.

```javascript
shard = null
node = null
nodesResponse = client._index(index).head()
for( var i in shardsResponse.body ){
  if( shardsResponse.body[i].index == index && shardsResponse.body[i].node ){
    shard = shardsResponse.body[i].shard
    node = shardsResponse.body[i].node
  }
}
```

The reroute command allows to explicitly execute a cluster reroute allocation command including specific commands. For example, a shard can be moved from one node to another explicitly, an allocation can be canceled, or an unassigned shard can be explicitly allocated on a specific node.

```javascript
postRerouteResponse = client._cluster.reroute.post({
  "commands" : [
    {
      "cancel" : {
        "index" : index ,
        "shard" : shard ,
        "node" : node ,
        "allow_primary" : true
      }
    }
  ]
})
```

```javascript
assert.equal( postRerouteResponse.status, 200 )
```

The nodes shutdown API allows to shutdown one or more (or all) nodes in the cluster.

```javascript
doShutdown = window.confirm("Do you want to shut down all of your Elasticsearch nodes?\n")
```

```javascript
if(doShutdown){
  postShutdownClusterResponse = client._cluster.nodes.nodeList( "" )._shutdown.post()
}
```

```javascript
if(doShutdown){
  assert.equal( postShutdownClusterResponse.status, 200 )
}
```

Garbage Collection. Delete the created index.

```javascript
deleteIndexResponse = client._index( index ).delete()
```