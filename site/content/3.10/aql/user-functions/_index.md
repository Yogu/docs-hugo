---
title: Extending AQL with User Functions
menuTitle: User Functions
weight: 50
description: >-
  You can write UDFs in JavaScript to extend AQL or to simplify queries
---
AQL comes with a [built-in set of functions](../functions/_index.md), but it is
not a fully-featured programming language.

To add missing functionality or to simplify queries, users may add their own
functions to AQL in the selected database. These functions are written in
JavaScript, and are deployed via an API; see [Registering Functions](registering-functions.md).

In order to avoid conflicts with existing or future built-in function names,
all user defined functions (**UDF**) have to be put into separate namespaces.
Invoking a UDF is then possible by referring to the fully-qualified function name,
which includes the namespace, too; see [Conventions](conventions.md).

## Technical Details

### Known Limitations

{{< warning >}}
UDFs can have serious effects on the performance of your queries and the resource
usage in ArangoDB. Especially in cluster setups they should not be used against
much data, because this data will need to be sent over the network back and forth
between _DB-Servers_ and _Coordinators_, potentially adding a lot of latency.
This can be mitigated by very selective `FILTER`s before calls to UDFs.
{{< /warning >}}

Since the optimizer doesn't know anything about the nature of your function,
**the optimizer can't use indexes for UDFs**. So you should never lean on a UDF
as the primary criterion for a `FILTER` statement to reduce your query result set.
Instead, put a another `FILTER` statement in front of it. You should make sure
that this [**`FILTER` statement** is effective](../execution-and-performance/query-optimization.md)
to reduce the query result before passing it to your UDF.

Rule of thumb is, the closer the UDF is to your final `RETURN` statement
(or maybe even inside it), the better. 

When used in clusters, UDFs are always executed on a
[Coordinator](../../deploy/deployment/cluster/_index.md).
It is not possible to execute UDFs on DB-Servers, as no JavaScript execution
engine is available on DB-Servers. Queries that would push UDF execution to
DB-Servers are aborted with a parse error. This includes using UDFs in traversal
`PRUNE` conditions, as well as `FILTER` conditions that can be moved into the
traversal execution on a DB-Server. These limitations also apply to the
single server deployment mode to keep the differences to cluster deployments minimal.

As UDFs are written in JavaScript, each query that executes a UDF will acquire
one V8 context to execute the UDFs in it. V8 contexts can be re-used across subsequent
queries, but when UDF-invoking queries run in parallel, they will each require a 
dedicated V8 context.

Because UDFs use the V8 JavaScript engine, the engine's default memory limit of 512 MB is applied.

Using UDFs in clusters may thus result in a higher resource allocation
in terms of used V8 contexts and server threads. If you run out 
of these resources, your query may abort with a
[**cluster backend unavailable**](../../develop/error-codes-and-meanings.md) error.

To overcome these mentioned limitations, you may want to increase the
[number of available V8 contexts](../../components/arangodb-server/options.md#--javascriptv8-contexts)
(at the expense of increased memory usage), and the
[number of available server threads](../../components/arangodb-server/options.md#--servermaximal-threads).

In addition, modification of global JavaScript variables from inside UDFs is 
unsupported, as is reading or changing the data of any collection or running
queries from inside an AQL user function.

### Deployment Details

Internally, UDFs are stored in a system collection named `_aqlfunctions`
of the selected database. When an AQL statement refers to such a UDF,
it is loaded from that collection. The UDFs will be exclusively
available for queries in that particular database.

Since the Coordinator doesn't have own local collections, the `_aqlfunctions`
collection is sharded across the cluster. Therefore (as usual), it has to be
accessed through a Coordinator - you mustn't talk to the shards directly.
Once it is in the `_aqlfunctions` collection, it is available on all
Coordinators without additional effort.

Keep in mind that system collections are excluded from dumps created with
[arangodump](../../components/tools/arangodump/_index.md) by default.
To include AQL UDF in a dump, the dump needs to be started with
the option *--include-system-collections true*.