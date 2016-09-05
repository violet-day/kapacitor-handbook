Kapacitor 使用名为`TICKscript` 的DSL，用来定义在Kapacitor中处理数据的pipeline。

TICKscript 是链式调用语法。 Each script has a flat scope and each variable in the scope defines methods that can be called on it. These methods come in two flavors.

* Property methods -- Modifies the node they are called on and returns a reference to the same node.
* Chaining methods -- Creates a new node as a child of the node they are called on and returns a reference to the new node.

The following reference documentation list each node's `Property` methods and `Chaining` methods along with examples and descriptions of the function of the node.

Every TICKscript will have either a `stream` or `batch` variable defined depending on the type of task you want to run. The `stream` and `batch` variables are an instance of a [StreamNode](https://github.com/influxdata/docs.influxdata.com/blob/master/kapacitor/v0.13/nodes/stream_node) or [SourceBatchNode](https://github.com/influxdata/docs.influxdata.com/blob/master/kapacitor/v0.11/nodes/source_batch_node) respectively.

## **Pipelines**

Kapacitor 使用 TICKscripts 来定义数据处理pipelines。 A pipeline is set of nodes that process data and edges that connect the nodes. Pipelines in Kapacitor are 有向无环图directed acyclic graphs \([DAGs](https://en.wikipedia.org/wiki/Directed_acyclic_graph)\) meaning each edge has a direction that data flows and there cannot be any cycles in the pipeline.

Each edge has a type, one of the following:

* StreamEdge -- an edge that transfers data a single data point at a time.
* BatchEdge -- an edge that transfers data in chunks instead of one point at a time.

When connecting nodes the TICKscript language will not prevent you from connecting edges of the wrong type but rather the check will be performed at runtime. So just be aware that a syntactically correct script may define a pipeline that is invalid.

## **Example**

```
stream
    |from()
        .measurement('app')
    |eval(lambda: "errors" / "total")
        .as('error_percent')
    // Write the transformed data to InfluxDB
    |influxDBOut()
        .database('mydb')
        .retentionPolicy('myrp')
        .measurement('errors')
        .tag('kapacitor', 'true')
        .tag('version', '0.2')
```

