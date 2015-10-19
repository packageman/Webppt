title: Elasticsearch
speaker: Byron Zhang
url: https://github.com/packageman/Webppt
transition: slide3
theme: moon
files: /js/elastic.js,/css/elastic.css

[slide]

# Elasticsearch
## 演讲者：Byron Zhang

[slide]

## About Elasticsearch
----

- Elasticsearch is an open-source search engine built on top of [Apache Lucene™](https://lucene.apache.org/core/)
- Elasticsearch is also written in Java and uses Lucene internally for all of its indexing and searching, but it aims to make full-text search easy by hiding the complexities of Lucene behind a simple, coherent, RESTful API.

[slide]

## Much more than just Lucene
----
- A distributed real-time document store where **every field** is indexed and searchable
- A distributed search engine with real-time analytics
- Capable of scaling to hundreds of servers and petabytes of structured and unstructured data

[note]
And it packages up all this functionality into a standalone server that your application can talk to via a simple RESTful API, using a web client from your favorite programming language, or even from the command line.
[/note]

[slide]

## Get started with Elasticsearch
----
- A **node** is a running instance of Elasticsearch
- A **cluster** is a group of nodes with the same `cluster.name` that are working together to share data and to provide *failover* and *scale*.

[note]
```shell
curl -XPOST 'http://localhost:9200/_shutdown'

./bin/plugin -i elasticsearch/marvel/latest

./bin/kibana plugin --install elastic/sense

http://localhost:5601/app/sense
```
[/note]

[slide]

## Customize your elasticsearch
----

You can configure your elasticsearch by editing the `elasticsearch.yml` file in the `config/` directory and then restarting Elasticsearch.

[slide]

## Talking to Elasticsearch
----
- Java API
- RESTful API with JSON over HTTP

[slide]

## Document Oriented
----
- Elasticsearch is **document oriented**, meaning that it stores entire objects or documents. It not only stores them, but also **indexes the contents** of each document in order to **make them searchable**. In Elasticsearch, you index, search, sort, and filter documents—​not rows of columnar data. This is a fundamentally different way of thinking about data and is one of the reasons Elasticsearch can perform complex full-text search.

[slide]

## Indices, Types, Documents
---

```
Relational DB  ⇒ Databases ⇒ Tables      ⇒ Rows      ⇒ Columns
MongoDB        ⇒ Databases ⇒ Collections ⇒ Documents ⇒ Fields
Elasticsearch  ⇒ Indices   ⇒ Types       ⇒ Documents ⇒ Fields
```

An Elasticsearch cluster can contain multiple indices (databases), which in turn contain multiple types (tables). These types hold multiple documents (rows), and each document has multiple fields (columns).

[slide]

## Index Versus Index Versus Index
---
| Name | Description |
|:-------------|:-------------|:
| Index (noun) | As explained previously, an index is like a database in a traditional relational database. It is the place to store related documents. The plural of index is indices or indexes. |
| Index (verb) | To index a document is to store a document in an index (noun) so that it can be retrieved and queried. It is much like the INSERT keyword in SQL except that, if the document already exists, the new document would replace the old. |
| Inverted index | Relational databases add an index, such as a B-tree index, to specific columns in order to improve the speed of data retrieval. Elasticsearch and Lucene use a structure called an inverted index for exactly the same purpose. |

[note]
By default, every field in a document is indexed (has an inverted index) and thus is searchable. A field without an inverted index is not searchable. More detail see [<font class="text-primary">here</font>](https://github.com/elastic/elasticsearch-definitive-guide/blob/master/010_Intro/25_Tutorial_Indexing.asciidoc#inverted-index).
[/note]

[slide]

## Kibana
---
- Kibana is an open source **analytics** and **visualization** platform designed to work with Elasticsearch.
- With it, you can easily perform advanced **data analysis** and **visualize** your data in a variety of charts, tables, and maps.
- Installation: https://www.elastic.co/downloads/kibana


[slide]

## Marvel — Your Elasticsearch Monitor
---
- Marvel enables you to easily monitor Elasticsearch through Kibana. You can view your cluster’s health and performance in real time as well as analyze past cluster, index, and node metrics.
- Installation: https://www.elastic.co/guide/en/marvel/current/installing-marvel.html

[slide]

## Sense — Your Elasticsearch Console
---
- Sense is a handy console for interacting with the REST API of Elasticsearch.
- Installation: https://www.elastic.co/guide/en/sense/current/installing.html

[slide]

## Index a document
---
See sample in [**sense**](http://localhost:5601/app/sense)

[slide]

## Retrieve a document
---
- Search Lite

```
GET /megacorp/employee/[ID]

GET /megacorp/employee/_search?q=last_name:Smith
```

- Search with Query DSL(domain-specific language)

```
GET /megacorp/employee/_search
{
    "query" : {
        "match" : {
            "last_name" : "Smith"
        }
    }
}
```

[slide]

## More-Complicated Searches
---
```
GET /megacorp/employee/_search
{
    "query" : {
        "filtered" : {
            "filter" : {
                "range" : {
                    "age" : { "gt" : 30 }
                }
            },
            "query" : {
                "match" : {
                    "last_name" : "smith"
                }
            }
        }
    }
}
```

[slide]

## Full-Text Search
---
```
GET /megacorp/employee/_search
{
    "query" : {
        "match" : {
            "about" : "rock climbing"
        }
    }
}
```

[slide]

## Phrase Search
---
Sometimes you want to match exact sequences of words or phrases, For instance, we could perform a query that will match only employee records that contain both 'rock' and 'climbing' and that display the words next to each other in the phrase **rock climbing**.
```
GET /megacorp/employee/_search
{
    "query" : {
        "match_phrase" : {
            "about" : "rock climbing"
        }
    }
}
```


[slide]

## Distributed
---
Elasticsearch is distributed by nature, and it is designed to hide the complexity that comes with being distributed.

[note]
### <span class="text-primary">Here are some of the operations happening automatically under the hood:</span>

<ul class="small-font">
    <li>Partitioning your documents into different containers or shards, which can be stored on a single node or on multiple nodes</li>
    <li>Balancing these shards across the nodes in your cluster to spread the indexing and search load</li>
    <li>Duplicating each shard to provide redundant copies of your data, to prevent data loss in case of hardware failure</li>
    <li>Routing requests from any node in the cluster to the nodes that hold the data you’re interested in</li>
    <li>Seamlessly integrating new nodes as your cluster grows or redistributing shards to recover from node loss</li>
</ul>
[/note]

[slide]

## Cluster, Node, Shard
---

[note]
Scale can come from buying bigger servers (vertical scale, or scaling up) or from buying more servers (horizontal scale, or scaling out).
[/note]

[slide]

## An Empty Cluster
---
![Empty Cluster](/img/elas_0201.png)

A node is a running instance of Elasticsearch, a cluster consists of one or more nodes with the same `cluster.name` that are working together to share their data and workload.

[note]
One node in the cluster is elected to be the <font class="text-primary">master node</font>, which is in charge of managing <font class="text-primary">cluster-wide</font> changes. like creating or deleting an index, or adding or removing a node from the cluster.<br/><br/>
The master node does not need to be involved in document-level changes or searches, which means <font class="text-primary">every node</font> can handle <font class="text-primary">document-level</font> changes or searches.
[/note]

[slide]

## Cluster Health
---
| Color | Description |
| ------------- | :-------------: |
| green | All primary and replica shards are active |
| yellow | All primary shards are active, but not all replica shards are active |
| red | Not all primary shards are active |

[note]
Many statistics can be monitored in an Elasticsearch cluster, but the single most important one is cluster health, which reports a status of either green, yellow, or red:

```
GET /_cluster/health
```
[/note]

[slide]

## Add an Index
---
Index—a place to store related data. In reality, an index is just a **logical namespace** that points to one or more physical shards.

- A shard is a single instance of Lucene
- Our documents are stored and indexed in shards
- Our applications don’t talk to documents directly. Instead, they talk to an index.

[note]
Think of shards as containers for data. Documents are stored in shards, and shards are allocated to nodes in your cluster. As your cluster grows or shrinks, Elasticsearch will automatically migrate shards between nodes so that the cluster remains balanced.
[/note]

[slide]

## Primary shard & Replica shard
---
- Each document in your index belongs to a single primary shard
- A replica shard is just a copy of a primary shard to protect against hardware failure

[slide]

## A single node cluster with an index
---
Let’s create an index called blogs in our empty one-node cluster

![A single node cluster with an index](/img/elas_0202.png)

1. Cluster status is yellow.
2. Our three replica shards have not been allocated to a node.

[note]
By default, indices are assigned five primary shards, but for the purpose of this demonstration, we’ll assign just three primary shards and one replica (one replica of every primary shard)
```
PUT /test
{
   "settings" : {
      "number_of_shards" : 3,
      "number_of_replicas" : 1 // one replica of every primary shard
   }
}
```
[/note]

[slide]

## Add Failover
---
- Running a single node means that you have a single point of failure—​there is no redundancy.
- If we start a second node, our cluster would look like A two-node cluster—​all primary and replica shards are allocated.

![A two-node cluster](/img/elas_0203.png)

The cluster-health now shows a status of **green**

[note]
To test what happens when you add a second node, you can start a new node in exactly the same way as you started the first one
[/note]

[slide]

## Scale Horizontally
---

![A three node cluster](/img/elas_0204.png)

1. A three-node cluster—​shards have been reallocated to spread the load.
2. This means that the hardware resources (CPU, RAM, I/O) of each node are being shared among fewer shards

[slide]

## Then Scale Some More
---

![A three-node cluster with two replica shards](/img/elas_0205.png)

- The `test` index now has nine shards: three primaries and six replicas. This means that we can scale out to a total of nine nodes, again with one shard per node. This would allow us to *triple search performance* compared to our original three-node cluster.
- we can now afford to lose two nodes without losing any data.
[note]
The number of replica shards can be changed dynamically on a live cluster. Let’s increase the number of replicas from the default of 1 to 2:
<br/>
<br/>
```
PUT /test/_settings
{
   "number_of_replicas" : 2
}
```
[/note]

[slide]

## Coping with Failure
---
![The cluster after killing one node](/img/elas_0206.png)

[note]
<ul class="tiny-font">
    <li>The node we killed was the master node. <font class="text-primary">A cluster must have a master node</font> in order to function correctly, so the first thing that happened was that the nodes elected a new master: Node 2
</li>
    <li>Primary shards 1 and 2 were lost when we killed Node 1, and our index cannot function properly if it is missing primary shards. If we had checked the cluster health at this point, we would have seen status red: not all primary shards are active!
</li>
    <li>Fortunately, a complete copy of the two lost primary shards exists on other nodes, so the first thing that the new master node did was to <font class="text-primary">promote the replicas of these shards on Node 2 and Node 3 to be primaries</font>, putting us back into cluster health yellow. This promotion process was instantaneous, like the flick of a switch.</li>
    <li>So why is our cluster health yellow and not green? We have all three primary shards, but we specified that we wanted two replicas of each primary, and currently only one replica is assigned. This prevents us from reaching green, but we’re not too worried here: were we to kill Node 2 as well, our application could still keep running without data loss, because Node 3 contains a copy of every shard.</li>
    <li>If we restart Node 1, the cluster would <font class="text-primary">be able to allocate the missing replica shards</font>, resulting in a state similar to the one described in [cluster-three-nodes-two-replicas]. If Node 1 still has copies of the old shards, it will try to reuse them, copying over from the primary shard only the files that have changed in the meantime.</li>
</ul>
[/note]

[slide]

## Data In, Data Out
---
- Document {:&.fadeIn}
- **Index**
- Get
- Exist
- Update
- Create
- **Version Control**
- **Partial Update**
- Mget
- Bulk

[note]
- Elasticsearch is a distributed document store. It can store and retrieve complex data structures—​serialized as JSON documents—​<font class="text-primary">in real time</font>. In other words, as soon as a document has been stored in Elasticsearch, it can be retrieved from any node in the cluster.
<br/>
<br/>
- In Elasticsearch, <font class="text-primary">all data in every field</font> is indexed by default. That is, every field has a dedicated inverted index for fast retrieval. And, unlike most other databases, it can use all of those inverted indices in the same query, to return results at breathtaking speed.
[/note]

[slide]

## Document Metadata
---
| Name | Description |
| ------------- | :-------------: |
| _index | Where the document lives |
| _type | The class of object that the document represents |
| _id | The unique identifier for the document |

[slide]

## Updating a Whole Document
---
- Documents in Elasticsearch are **immutable**, we cannot change them. Instead, if we need to update an existing document, we reindex or replace it, which we can do using the same index API.
- Internally, Elasticsearch has **marked the old document as deleted and added an entirely new document**.

[slide]

## Dealing with Conflicts
---
<div class="left"><img src="/img/elas_0301.png"></img></div>
<div>
    <br/><br/><br/><br/><br/><br/><br/><br/><br/>
    <ul>
        <li class="text-primary">Optimistic concurrency control</li>
        <li>Pessimistic concurrency control</li>
    </ul>
</div>

[slide]

## Optimistic concurrency control
---
- Used by Elasticsearch, this approach assumes that conflicts are unlikely to happen and `doesn’t block operations` from being attempted. However, if the underlying data has been modified between reading and writing, the update will fail.
- Elasticsearch take advantage of the `_version` number to ensure that conflicting changes made by our application do not result in data loss

[note]
<div class="center text-primary">Pessimistic concurrency control</div>
<br/>
Widely used by relational databases, this approach assumes that conflicting changes are likely to happen and so blocks access to a resource in order to prevent conflicts. A typical example is locking a row before reading its data, ensuring that only the thread that placed the lock is able to make changes to the data in that row.
[/note]
[slide]

## Partial Updates to Documents
---
1. Retrieve the JSON from the old document
2. Change it
3. Delete the old document
4. Index a new document

[note]
The simplest form of the update request accepts a partial document as the `doc` parameter.
<br/><br/>
```
POST /website/blog/1/_update
{
   "doc" : {
      "tags" : [ "testing" ],
      "views": 0
   }
}
```
[/note]

[slide]

## Using Scripts to Make Partial Updates
---
Scripts can be used in the update API to change the contents of the `_source` field, which is referred to inside an update script as `ctx._source`. For instance, we could use a script to increment the number of views that our blog post has had:
<br/><br/>
```
POST /website/blog/1/_update
{
   "script" : "ctx._source.views+=1"
}
```
[note]
The default scripting language is Groovy, a fast and expressive scripting language, similar in syntax to JavaScript. It was first introduced in Elasticsearch version v1.3.0 and it runs in a sandbox, however there is vulnerability in the Groovy scripting engine that allows an attacker to construct Groovy scripts that escape the sandbox and execute shell commands as the user running the Elasticsearch Java VM.
[/note]

[slide]

## Updating a Document That May Not Yet Exist
---
In cases like these, we can use the `upsert` parameter to specify the document that should be created if it doesn’t already exist:
<br/><br/>
```
POST /website/pageviews/1/_update
{
   "script" : "ctx._source.views+=1",
   "upsert": {
       "views": 1
   }
}
```

[slide]

## Updates and Conflicts
---
1. To avoid losing data, the update API retrieves the current **version** of the document in the **retrieve** step,
2. And passes that to the index request during the **reindex** step,
3. If another process has changed the document between **retrieve** and **reindex**, then the `_version` number won’t match and the update request will fail.

[note]
We can set the `retry_on_conflict` parameter to the number of times that update should retry before failing; it defaults to 0.
<br/><br/>
```
POST /website/pageviews/1/_update?retry_on_conflict=5
{
   "script" : "ctx._source.views+=1",
   "upsert": {
       "views": 0
   }
}
```
[/note]

[slide]

## Cheaper in Bulk
---
- In the same way that `mget` allows us to retrieve multiple documents at once, the `bulk` API allows us to make multiple **create**, **index**, **update**, or **delete** requests in a single step.
- The bulk request body has the following, slightly unusual, format:
```
{ action: { metadata }}\n
{ request body        }\n
{ action: { metadata }}\n
{ request body        }\n
...
```

[note]
### Two important points to note:
<ul>
    <li>Every line must end with a <font class="text-primary">newline character (\n)</font>, including the last line. These are used as markers to allow for efficient line separation.</li>
    <li>The lines cannot contain unescaped newline characters, as they would interfere with parsing. This means that the <font class="text-primary">JSON must not be pretty-printed</font>.</li>
</ul>
[/note]

[slide]

## Why the Funny Format?
---
If the individual requests were wrapped up in a **JSON** array, that would mean that Elasticsearch would need to do the following:

- *Parse* the JSON into an array (including the document data, which can be very large)
- *Look* at each request to determine which shard it should go to
- *Create* an array of requests for each shard
- *Serialize* these arrays into the internal transport format
- Send the requests to each shard

[note]
It would work, but would need <font class="text-danger">a lot of RAM</font> to hold copies of essentially the same data, and would create many more data structures that the Java Virtual Machine (JVM) would have to <font class="text-danger">spend time garbage collecting</font>.
[/note]

[slide]

## Distributed CRUD
---
Hey, we are going to dive into those internal, technical details to help you understand **how your data is stored in a distributed system**. What an exciting thing.

[slide]

## Routing a Document to a Shard
---
The process can’t be random, since we may need to retrieve the document in the future. In fact, it is determined by a simple formula:
```
shard = hash(routing) % number_of_primary_shards
```
- The `routing` value is an arbitrary string, which defaults to the **document’s id** but can also be set to a custom value.

[note]
All document APIs (`get`, `index`, `delete`, `bulk`, `update`, and `mget`) accept a `routing` parameter that can be used to customize the document-to-shard mapping. A custom routing value could be used to ensure that all <font class="text-primary">related documents</font>—​for instance, all the documents belonging to the same user—​are stored on the same shard.
[/note]

[slide]

## How Primary and Replica Shards Interact
---
![A cluster with three nodes and one index](/img/elas_0401.png)

We can send our requests to **any node** in the cluster. Every node is fully capable of **serving any request**. Every node knows the location of every document in the cluster and so can **forward requests** directly to the required node.

[slide]

## Creating, Indexing, and Deleting a Document
---
![Creating, indexing, and deleting a single document](/img/elas_0402.png)

[note]
### <font class="text-primary">Here is the sequence of steps necessary to successfully create, index, or delete a document on both the primary and any replica shards:</font>

<ul class="small-font">
  <li>The client sends a create, index, or delete request to `Node 1`</li>
  <li>The node uses the document’s `_id` to determine that the document belongs to shard 0. It forwards the request to `Node 3`, where the primary copy of shard 0 is currently allocated</li>
  <li>`Node 3` executes the request on the primary shard. If it is successful, it forwards the request in parallel to the replica shards on Node 1 and Node 2. Once all of the replica shards report success, `Node 3` reports success to the coordinating node, which reports success to the client.</li>
</ul>

[/note]

[slide]

## Retrieving a Document
---

![Retrieving a document](/img/elas_0403.png)

For read requests, the **coordinating node** will choose a **different shard copy on every request** in order to balance the load; it round-robins through all shard copies.

[slide]

## Partial Updates to a Document
---
![Partial update a document](/img/elas_0404.png)

The update API , as shown before, combines the **read** and **write** patterns explained previously.

[slide]

## Retrieve multiple documents with `mget`
---
![Retrieve multiple documents with `mget`](/img/elas_0405.png)

It **breaks up** the multidocument request into a multidocument request **per shard**, and forwards these in parallel to each participating node.

[slide]

## Multiple documents changes with `bulk`
![Multiple documents changes with `bulk`](/img/elas_0406.png)

The primary shard executes each action **serially**, one after another. As each action succeeds, the primary **forwards** the new document (or deletion) to its **replica shards** in parallel, and then moves on to the next action.

[slide]

## Pagination
---
```
GET /_search?size=5
GET /_search?size=5&from=5
GET /_search?size=5&from=10
```

Results are sorted **before being returned**. But remember that a search request usually spans **multiple shards**. Each shard generates its own sorted results, which then need to be sorted centrally to ensure that the overall order is correct.

[slide]

## Deep Paging in Distributed Systems
---
- Understand why deep paging is <font class="text-danger">problematic</font>.
- In a distributed system, the cost of sorting results <font class="text-danger">grows exponentially the deeper we page</font>. There is a good reason that web search engines don’t return more than 1,000 results for any query.

[slide]

## Query String
---
<div class="left">Search Lite</div>
<div class="clearfix"></div>
```
GET /test/user/_search?q=-first_name:byron +interests:music
```
<br/>
<div class="left">The <code>_all</code> Field</div>
<div class="clearfix"></div>
```
GET /test/user/_search?q=(byron john)
```

[slide]

# Mapping and Analysis

[slide]

## Let's Explore it
---

I have 4 user in my indices, and only one of them contains the date `2015-12-21`, but have a look at the total hits for the following queries:

```
GET /test/user/_search?q=2015              # 4 results
GET /test/user/_search?q=2015-12-21        # 4 results !
GET /test/user/_search?q=date:2015-12-21   # 1 result
GET /test/user/_search?q=date:2015         # 0 results !
```

1. Why does querying the `_all` field for the full date return all users?
2. and querying the `date` field for just the year return no results?

[slide]

## Cause
---
In Elasticsearch, fields of type `date` and fields of type `string` are indexed differently, and can thus be searched differently.

[slide]

## Exact Values Versus Full Text
---
Data in Elasticsearch can be broadly divided into two types: **exact values** and **full text**.

[slide]

## Full Text
---
Querying full-text data is much more subtle. We are not just asking:

>Does this document match the query?

but

> How well does this document match the query?
or how relevant is this document to the given query?

[note]
- A search for `UK` should also return documents mentioning the `United Kingdom`.
- A search for `jump` should also match `jumped`, `jumps`, `jumping`, and perhaps even `leap`.
- johnny walker should match Johnnie Walker, and johnnie depp should match Johnny Depp.
- fox news hunting should return stories about hunting on Fox News, while fox hunting news should return news stories about fox hunting.
[/note]

[slide]

## Inverted Index
---
Elasticsearch uses a structure called an **inverted index**, which is designed to allow very fast full-text searches.
<br/><br/>
- a list of all the unique words that appear in any document
- for each word, a list of the documents in which it appears

|   Token   | DocIDs |
|-----------|--------|
| search    | [1, 2] |
| real_time | [1]    |

[slide]

## Analysis and Analyzers
---
![Analysis](/img/elas_0501.png)

[slide]

## Built-in Analyzers
---
- Standard analyzer (default)
- Simple analyzer
- Whitespace analyzer
- Language analyzers

[slide]

## Core Simple Field Types
---
Elasticsearch supports the following simple field types:

| Type | Type in Elasticsearch |
| ------------- | ------------- |
| String | `string` |
| Whole number | `byte`, `short`, `integer`, `long` |
| Floating-point | `float`, `double` |
| Boolean | `boolean` |
| Date | `date` |

[slide]

## Customizing Field Mappings
---
- Distinguish between **full-text** string fields and **exact value** string fields
- Use **language-specific** analyzers
- Optimize a field for partial matching
- Specify **custom date formats**
- And much more

[slide]

## Update Index
---
The `index` attribute controls how the string will be indexed. It can contain one of three values:

| Type | Description |
| ------------- | ------------- |
| analyzed | First analyze the string and then index it. In other words, index this field as full text. |
| not_analyzed | Index this field, so it is searchable, but index the value exactly as specified. Do not analyze it. |
| no | Don’t index this field at all. This field will not be searchable. |

[note]
#### You can change it by performing following request:
<br/>
```
PUT /test/_mapping
{
    "tag": {
        "type":     "string",
        "index":    "not_analyzed"
    }
}
```
[/note]

[slide]

## Update Analyzer
---
- The `analyzer` attribute to specify which analyzer to apply both at search time and at index time. By default, Elasticsearch uses the standard analyzer.

- You can change it by performing following request:
<br/><br/>
```
PUT /test/user/_mapping
{
    "tweet": {
        "type":     "string",
        "analyzer": "english"
    }
}
```

[slide]

## Updating a Mapping
---
- You can specify the mapping for a type when you first create an index.
- Alternatively, you can add the mapping for a new type later using the `/_mapping` endpoint.
<br/><br/>
```
PUT /test/user/_mapping
{
    "properties" : {
      "tag" : {
        "type" : "string",
        "index": "not_analyzed"
      }
    }
}
```

[slide]

## Complex Core Field Types
---
- Multivalue Fields
- Empty Fields
- Multilevel Objects
- Mapping for Inner Objects
- **Arrays of Inner Objects**

[slide]

## Request Body Search
---
- The authors of Elasticsearch prefer using `GET` for a search request because they feel that it describes the action—**​retrieving information**—​better than the `POST` verb
- However, because GET with a request body is not universally supported, the search API also accepts `POST` requests.
- Following is an empty search, which returns all documents in all indices:
<br/><br/>
```
GET /_search
{}
```

[slide]

## Query DSL
---
<div class="left">
    <ul>
        <li>To use the Query DSL, pass a `query` in the query parameter:</li>
    </ul>
</div>
<div class="clearfix"></div>
```
GET /_search
{
    "query": YOUR_QUERY_HERE
}
```
<div class="left">
    <ul>
        <li>Structure of a Query Clause</li>
        <li>Combining Multiple Clauses <br/><code>bool, must, should, must_not</code></li>
    </ul>
</div>

[slide]

## Filter DSL
---
### <font class="text-primary">Query clauses</font> and <font class="text-primary">filter clauses</font> are similar in nature, but have slightly different purposes.
<br/>
- A filter asks a `yes|no` question of every document and is used for fields that contain **exact values**
- A query is similar to a filter, but also asks the question: **How well does this document match?**
- A typical use for a query is to **find** documents

[slide]

## Performance Differences
---
|  Type  | Description |
|--------|-------------|
| Filter | The output from most filter clauses—​a simple list of the documents that match the filter—​is quick to calculate and easy to cache in memory, using only 1 bit per document. These cached filters can **be reused efficiently for subsequent requests**.            |
| Query  | Queries have to not only find matching documents, but also **calculate how relevant each document is**, which typically makes queries heavier than filters. Also, query results are **not cachable**. <br/>Thanks to the **inverted index**, a simple query that matches just a few documents may perform as well or better than a cached filter that spans millions of documents.|

[note]
The goal of filters is to <font class="text-primary">reduce the number of documents</font> that have to be examined by the query.
[/note]

[slide]

## When to Use Which
---
As a general rule, use query clauses for **full-text search** or for any condition that should **affect the relevance score**, and use filter clauses for everything else.

[slide]

## Most Important Queries and Filters
---
|   Type  |                                     Description                                     |
|---------|-------------------------------------------------------------------------------------|
| Filters | `term`, `terms`, `range`, `exists`, `missing`, `bool`(`must`, `should`, `must_not`) |
| Queries | `match_all`, `match`, `multh_match`, `bool`                                         |

[slide]

## Validating Queries
---

<div class="columns-2">
<pre>Understanding Errors<br/>
<code class="json">
GET /_validate/query?explain
{
   "query": {
      "content" : {
         "match" : "really powerful"
      }
   }
}
</code></pre>
<pre>Understanding Querys<br/>
<code class="json">
GET /_validate/query?explain
{
   "query": {
      "match" : {
         "content" : "really powerful"
      }
   }
}
</code></pre>
</div>

[note]
```json
{
  "valid": true,
  "_shards": {
    "total": 4,
    "successful": 4,
    "failed": 0
  },
  "explanations": [
    {
      "index": ".kibana",
      "valid": true,
      "explanation": "text:really text:powerful"
    },
    {
      "index": "website",
      "valid": true,
      "explanation": "text:realli text:power"
    }
  ]
}
```
[/note]

[slide]

## String Sorting and Multifields
---
```
"tweet": { (1)
    "type":     "string",
    "analyzer": "english",
    "fields": {
        "raw": { (2)
            "type":  "string",
            "index": "not_analyzed"
        }
    }
}
```

1. The main `tweet` field is just the same as before: **an analyzed full-text field**.
2. The new `tweet.raw` subfield is **not_analyzed**.

[note]
Now, or at least as soon as we have reindexed our data, we can use the `tweet` field for search and the `tweet.raw` field for sorting:
<br/>
```
GET /_search
{
    "query": {
        "match": {
            "tweet": "elasticsearch"
        }
    },
    "sort": "tweet.raw"
}
```
[/note]

[slide]

## What Is Relevance?
---
The standard similarity algorithm used in Elasticsearch is known as **term frequency/inverse document frequency**, or **TF/IDF**, which takes the following factors into account:

![TF/IDF](/img/elas_0701.png)

[slide]

## Distributed Search Execution
---
Search is executed in a two-phase process called `query then fetch`.

[slide]

## Query Phase
---
![Query phase of distributed search](/img/elas_0901.png)

**Priority Queue**: A priority queue is just **a sorted list** that holds the *top-n* matching documents.The size of the priority queue depends on the pagination parameters `from` and `size`.

[note]
Each shard returns a lightweight list of results to the coordinating node, which contains just the <font class="text-danger">doc IDs</font> and <font class="text-danger">any values required for sorting</font>, such as the `_score`.
[/note]

[slide]

## Fetch Phase
---
The query phase identifies which documents satisfy the search request, but we still need to **retrieve** the documents themselves. This is the job of the **fetch phase**, shown in following:

![Fetch phase of distributed search](/img/elas_0902.png)

[slide]

## Search Options
---
- preference
- timeout
- routing
- search_type
    - `count`
    - `query_and_fetch`
    - `dfs_query_then_fetch` and `dfs_query_and_fetch`
    - `scan` (disable sorting)

[slide]

## Index Management
---
- Create/Delete Index
- Setting (primary shard, replica shard)
- Configure Analyzers
- **Custom Analyzers**(character filters, tokenizers, token filters)
- Types and Mappings

[note]
```
PUT /my_index
{
    "settings": {
        "analysis": {
            "char_filter": {
                "&_to_and": {
                    "type":       "mapping",
                    "mappings": [ "&=> and "]
            }},
            "filter": {
                "my_stopwords": {
                    "type":       "stop",
                    "stopwords": [ "the", "a" ]
            }},
            "analyzer": {
                "my_analyzer": {
                    "type":         "custom",
                    "char_filter":  [ "html_strip", "&_to_and" ],
                    "tokenizer":    "standard",
                    "filter":       [ "lowercase", "my_stopwords" ]
            }}
}}}
```
[/note]

[slide]

## Index Management
---
- The Root Object
- `_source` Field
- `_all` Field
- Document Identity (`_id`, `_type`, `_index`, `_uid` Field)
- Dynamic Mapping
- Custom Dynamic Mapping
- Default Mapping

[slide]

## Switch Index
---
- **Reindexing**
- **Aliases**

[slide]

## Inside a Shard
---
- Why is search near **real-time**?
- Why are document CRUD (create-read-update-delete) operations **real-time**?
- How does Elasticsearch ensure that the changes you make are **durable**, that they won’t be lost if there is a power failure?
- Why does deleting documents not free up space immediately?
- What do the `refresh`, `flush`, and `optimize` APIs do, and when should you use them?

[slide]

## Immutability
---
- There is **no need for locking**
- Once the index has been read into the kernel’s **filesystem cache**, it stays there, because it never changes.
- Any other caches (like the filter cache) remain valid for the life of the index.
- Writing a single large inverted index allows the data to be compressed, reducing costly disk I/O and the amount of RAM needed to cache the index.

[slide]

## Dynamtic Indices(Per-segment Search)
---
![Per-segment Search](/img/elas_1101.png)

[note]
how to make an inverted index updatable without losing the benefits of immutability? The answer turned out to be: <font class="text-primary">use more than one index</font>.
[/note]

[slide]

## Ready to Commit
---
![A Lucene index with new documents in the in-memory buffer, ready to commit](/img/elas_1102.png)

[slide]

## After a Commit
---
![After a commit, a new segment is added to the index and the buffer is clea](/img/elas_1103.png)

[slide]

## Near Real Time
---
![A Lucene index with new documents in the in-memory buffer](/img/elas_1104.png)

[slide]

## Searchable, but is not yet commited
---
![The buffer contents have been written to a segment, which is searchable, but is not yet commited](/img/elas_1105.png)

[note]
Lucene allows new segments to be written and opened— <font class="text-primary">making the documents they contain visible to search—​without performing a full commit. </font>
<br/><br/>
In Elasticsearch, this lightweight process of writing and opening a new segment is called a refresh. By default, every shard is refreshed automatically once every second.
(`refresh` API)
[/note]

[slide]

## Persistent Changes
---
![New documents are added to the in-memory buffer and appended to the transaction log](/img/elas_1106.png)

[slide]

## After a refresh
---
![After a refresh, the buffer is cleared but the transaction log is not](/img/elas_1107.png)

[slide]

## The transaction log keeps accumulating documents
---
![The transaction log keeps accumulating documents](/img/elas_1108.png)

[slide]

## After a flush
---
![After a flush, the segments are fully commited and the transaction log is cleared](/img/elas_1109.png)

[note]
The action of performing a commit and truncating the translog is known in Elasticsearch as a flush. Shards are flushed automatically <font class="text-danger">every 30 minutes</font>, or when the translog becomes <font class="text-danger">too big</font>.(`flush` API)
[/note]

[slide]

## Segment Merging
---
![Two commited segments and one uncommited segment in the process of being merged into a bigger segment](/img/elas_1110.png)

[slide]

## Once merging has finished, the old segments are deleted
---
![Once merging has finished, the old segments are deleted](/img/elas_1111.png)

[note]
The `optimize` API is best described as the forced merge API. It forces a shard to be merged down to the number of segments specified in the `max_num_segments` parameter. The intention is to reduce the number of segments (<font class="text-primary">usually to one</font>) in order to speed up search performance.
[/note]

[slide]

## Thanks for watching
---
