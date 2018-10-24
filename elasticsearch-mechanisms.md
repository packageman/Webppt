title: Elasticsearch 实现原理
speaker: Byron Zhang
url: https://github.com/packageman/webppt
transition: slide
theme: dark
files: /js/elasticsearch.js,/css/elasticsearch.css

[slide]

# Elasticsearch 实现原理
<br/>
## Speaker: Byron Zhang

[slide]

## 分布式特性
---

- 集群扩容 {:&.moveIn}
- 故障转移
- 分布式文档存储
- 分布式文档检索
- 分片(shard)

[note]
- 分配文档到不同的 *分片(shard)* 中，文档可以储存在一个或多个节点中
- 按集群节点来均衡分配这些分片，从而对索引和搜索过程进行负载均衡
- 复制每个分片以支持数据冗余，从而防止硬件故障导致的数据丢失
- 将集群中任一节点的请求路由到存有相关数据的节点
- 集群扩容时无缝整合新节点，重新分配分片以便从离群节点恢复


分片是一个功能完整的搜索引擎，它拥有使用一个节点上的所有资源的能力(lucene实例)
[/note]

[slide]

## 空集群
---
![Empty Cluster](/img/elas_0201.png)

一个运行中的 Elasticsearch 实例称为一个 节点，而集群是由一个或者多个拥有相同 cluster.name 配置的节点组成

[slide]

## 拥有一个索引的单节点集群
---
![A single node cluster with an index](/img/elas_0202.png)

- 集群 status 值为 yellow {:&.moveIn}
- 没有被分配到任何节点的副本数

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

## 集群健康
---
| Color | Description |
| ------------- | :-------------: |
| green | All primary and replica shards are active |
| yellow | All primary shards are active, but not all replica shards are active |
| red | Not all primary shards are active |

[slide]

## 添加故障转移
---
![A two-node cluster](/img/elas_0203.png)

拥有两个节点的集群——所有主分片和副本分片都已被分配

[note]
cluster-health 现在展示的状态为 green ，这表示所有6个分片（包括3个主分片和3个副本分片）都在正常运行。
[/note]

[slide]

## 水平扩容
---
![A three node cluster](/img/elas_0204.png)

拥有三个节点的集群——为了分散负载而对分片进行重新分配

[note]
Node 1 和 Node 2 上各有一个分片被迁移到了新的 Node 3 节点，现在每个节点上都拥有2个分片，而不是之前的3个。 这表示每个节点的硬件资源（CPU, RAM, I/O）将被更少的分片所共享，每个分片的性能将会得到提升。

分片是一个功能完整的搜索引擎，它拥有使用一个节点上的所有资源的能力。 我们这个拥有6个分片（3个主分片和3个副本分片）的索引可以最大扩容到6个节点，每个节点上存在一个分片，并且每个分片拥有所在节点的全部资源。
[/note]

[slide]

## 更多的扩容
---
如果我们想要扩容超过6个节点怎么办呢？

[note]
```
PUT /test/_settings
{
   "number_of_replicas" : 2
}
```
[/note]

[slide]

## 调整副本分片数目
---
![A three-node cluster with two replica shards](/img/elas_0205.png)

将参数 number_of_replicas 调大到 2

[note]
当然，如果只是在相同节点数目的集群上增加更多的副本分片并不能提高性能，因为每个分片从节点上获得的资源会变少。 你需要增加更多的硬件资源来提升吞吐量。

但是更多的副本分片数提高了数据冗余量：按照上面的节点配置，我们可以在失去2个节点的情况下不丢失任何数据。
[/note]

[slide]

## 应对故障
---
![The cluster after killing one node](/img/elas_0206.png)

关闭了一个节点后的集群

[note]
1. 我们关闭的节点是一个主节点。而集群必须拥有一个主节点来保证正常工作，所以发生的第一件事情就是选举一个新的主节点： Node 2
2. 新的主节点立即将这些分片在 Node 2 和 Node 3 上对应的副本分片提升为主分片
[/note]

[slide]

## 分布式文档存储
---

[slide]

## 路由一个文档到一个分片中
---
<br/>
<code>shard = hash(routing) % number_of_primary_shards</code>

[note]
routing 是一个可变值，默认是文档的 _id ，也可以设置成一个自定义的值。 routing 通过 hash 函数生成一个数字，然后这个数字再除以 number_of_primary_shards （主分片的数量）后得到 余数 。这个分布在 0 到 number_of_primary_shards-1 之间的余数，就是我们所寻求的文档所在分片的位置。
[/note]

[slide]

## 新建、索引和删除文档
---
![Creating, indexing, and deleting a single document](/img/elas_0402.png)

[note]
1. 客户端向 Node 1 发送新建、索引或者删除请求。
2. 节点使用文档的 _id 确定文档属于分片 0 。请求会被转发到 Node 3`，因为分片 0 的主分片目前被分配在 `Node 3 上。
3. Node 3 在主分片上面执行请求。如果成功了，它将请求并行转发到 Node 1 和 Node 2 的副本分片上。一旦所有的副本分片都报告成功, Node 3 将向协调节点报告成功，协调节点向客户端报告成功。
[/note]

[slide]

## 执行分布式检索
---
- 查询阶段 {:&.moveIn}
- 取回阶段

[slide]

## 查询阶段
---
![Query phase of distributed search](/img/elas_0901.png)

[note]
当一个搜索请求被发送到某个节点时，这个节点就变成了协调节点。 这个节点的任务是广播查询请求到所有相关分片并将它们的响应整合成全局排序后的结果集合，这个结果集合会返回给客户端。

- 客户端发送一个 search 请求到 Node 3 ， Node 3 会创建一个大小为 from + size 的空优先队列。
- Node 3 将查询请求转发到索引的每个主分片或副本分片中。每个分片在本地执行查询并添加结果到大小为 from + size 的本地有序优先队列中。
- 每个分片返回各自优先队列中所有文档的 ID 和排序值给协调节点，也就是 Node 3 ，它合并这些值到自己的优先队列中来产生一个全局排序后的结果列表。
[/note]

[slide]

## 取回阶段
---
![Fetch phase of distributed search](/img/elas_0902.png)

[note]
1. 协调节点辨别出哪些文档需要被取回并向相关的分片提交多个 GET 请求。
2. 每个分片加载并 **丰富** 文档，如果有需要的话，接着返回文档给协调节点。
3. 一旦所有的文档都被取回了，协调节点返回结果给客户端。
[/note]

[slide]

## 分片内部原理
---

- 为什么搜索是 **近** 实时的？ {:&.moveIn}
- Elasticsearch 是怎样保证更新被持久化在断电时也不丢失数据?
- 为什么删除文档不会立刻释放空间？
- `refresh`, `flush`, 和 `optimize` API 都做了什么, 你什么情况下应该是用他们？

[slide]

## 使文本可被搜索
---

Term  | Doc 1 | Doc 2 | Doc 3 | ... |
------|-------|-------|-------|-----|
brown |   X   |       |  X    | ... |
fox   |   X   |   X   |  X    | ... |
quick |   X   |   X   |       | ... |
the   |   X   |       |  X    | ... |

[note]
倒排索引包含一个有序列表，列表包含所有文档出现过的不重复个体，或称为 词项 ，对于每一个词项，包含了它所有曾出现过文档的列表。
[/note]

[slide]

## 不变性
---

- 不需要锁。如果你从来不更新索引，你就不需要担心多进程同时修改数据的问题。 {:&.moveIn}
- 一旦索引被读入内核的文件系统缓存，便会留在哪里，由于其不变性。只要文件系统缓存中还有足够的空间，那么大部分读请求会直接请求内存，而不会命中磁盘。这提供了很大的性能提升。
- 其它缓存(像filter缓存)，在索引的生命周期内始终有效。它们不需要在每次数据改变时被重建，因为数据不会变化。
- 写入单个大的倒排索引允许数据被压缩，减少磁盘 I/O 和 需要被缓存到内存的索引的使用量。

[note]
当然，一个不变的索引也有不好的地方。主要事实是它是不可变的! 你不能修改它。如果你需要让一个新的文档 可被搜索，你需要重建整个索引。这要么对一个索引所能包含的数据量造成了很大的限制，要么对索引可被更新的频率造成了很大的限制。
[/note]

[slide]

## 动态更新索引
---
![Per-segment Search](/img/elas_1101.png)

一个 Lucene 索引包含一个提交点和三个段

[note]
下一个需要被解决的问题是怎样在保留不变性的前提下实现倒排索引的更新？ 答案是: **用更多的索引**。
<br/>
通过增加新的补充索引来反映新近的修改，而不是直接重写整个倒排索引。每一个倒排索引都会被轮流查询到--从最早的开始--查询完后再对结果进行合并。
<br/>
Elasticsearch 基于 Lucene, 这个 java 库引入了 按段搜索 的概念。 每一 段 本身都是一个倒排索引， 但 索引 在 Lucene 中除表示所有 段 的集合外， 还增加了 提交点 的概念 — 一个列出了所有已知段的文件。
[/note]

[slide]

## 索引与分片的比较
---

![Elasticsearch-lucene](/img/elas_lucene_1.png)

[note]
In Elasticsearch, the most basic unit of storage of data is a shard. But, looking through the Lucene lens makes things a bit different. Here, each Elasticsearch shard is a Lucene index, and each Lucene index consists of several Lucene segments. A segment is an inverted index of the mapping of terms to the documents containing those terms.
[/note]

[slide]

## 逐段搜索流程
---
- 新文档被收集到内存索引缓存。 {:&.moveIn}
- 不时地，缓存被 *提交* 
  - 一个新的段（一个追加的倒排索引）被写入磁盘。
  - 一个新的包含新段名字的 *提交点* 被写入磁盘。
  - 磁盘进行 *同步*，所有在文件系统缓存中等待的写入都刷新到磁盘，以确保它们被写入物理文件。
- 新的段被开启，让它包含的文档可见以被搜索。
- 内存缓存被清空，等待接收新的文档。

[slide]

## 一个在内存缓存中包含新文档的 Lucene 索引
---
![A Lucene index with new documents in the in-memory buffer, ready to commit](/img/elas_1102.png)


[slide]

## 在一次提交后，一个新的段被添加到提交点而且缓存被清空。
---
![After a commit, a new segment is added to the index and the buffer is clea](/img/elas_1103.png)

[note]
当一个查询被触发，所有已知的段按顺序被查询。词项统计会对所有段的结果进行聚合，以保证每个词和每个文档的关联都被准确计算。 这种方式可以用相对较低的成本将新文档添加到索引。
[/note]

[slide]

## 删除和更新
---
段是不可改变的，所以既不能从把文档从旧的段中移除，也不能修改旧的段来进行反映文档的更新。 取而代之的是，每个提交点会包含一个 .del 文件，文件中会列出这些被删除文档的段信息。

[slide]

## 近实时搜索
---

[note]
随着按段（per-segment）搜索的发展， 一个新的文档从索引到可被搜索的延迟显著降低了。新文档在几分钟之内即可被检索，但这样还是不够快。

磁盘在这里成为了瓶颈。 **提交（Commiting）一个新的段到磁盘需要一个 fsync 来确保段被物理性地写入磁盘**，这样在断电的时候就不会丢失数据。 但是 fsync 操作代价很大; 如果每次索引一个文档都去执行一次的话会造成很大的性能问题。

我们需要的是一个更轻量的方式来使一个文档可被搜索，这意味着 fsync 要从整个过程中被移除
[/note]

[slide]

## 缓冲区的内容已经被写入一个可被搜索的段中，但还没有进行提交
---
![The buffer contents have been written to a segment, which is searchable, but is not yet commited](/img/elas_1105.png)

[note]
新段会被先写入到 **文件系统缓存** --这一步代价会比较低，稍后再被刷新到磁盘--这一步代价比较高。不过只要文件已经在缓存中， 就可以像其它文件一样被打开和读取了。
[/note]

[slide]

## refresh API
---
在 Elasticsearch 中，**写入和打开一个新段** 的轻量的过程叫做 refresh 。 默认情况下每个分片会每秒自动刷新一次。这就是为什么我们说 Elasticsearch 是 **近** 实时搜索: 文档的变化并不是立即对搜索可见，但会在一秒之内变为可见。

[slide]

## 持久化更新
---
如果没有用 fsync 把数据从文件系统缓存刷（flush）到硬盘，我们不能保证数据在断电甚至是程序正常退出之后依然存在。为了保证 Elasticsearch 的可靠性，需要确保数据变化被持久化到磁盘。

[slide]

## 新的文档被添加到内存缓冲区并且被追加到了事务日志
---
![New documents are added to the in-memory buffer and appended to the transaction log](/img/elas_1106.png)

[note]
Elasticsearch 增加了一个 translog ，或者叫事务日志，在每一次对 Elasticsearch 进行操作时均进行了日志记录。通过 translog ，整个流程看起来是下面这样：

1. 一个文档被索引之后，就会被添加到内存缓冲区，并且 追加到了 translog
2. 刷新（refresh）使分片处于 图 22 “刷新（refresh）完成后, 缓存被清空但是事务日志不会” 描述的状态，分片每秒被刷新（refresh）一次：
- 这些在内存缓冲区的文档被写入到一个新的段中，且没有进行 fsync 操作。
- 这个段被打开，使其可被搜索。
- 内存缓冲区被清空。
[/note]

[slide]

## 刷新（refresh）完成后, 缓存被清空但是事务日志不会
---
![After a refresh, the buffer is cleared but the transaction log is not](/img/elas_1107.png)

[slide]

## 事务日志不断积累文档
---
![The transaction log keeps accumulating documents](/img/elas_1108.png)

[note]
每隔一段时间--例如 translog 变得越来越大--索引被刷新（flush）；一个新的 translog 被创建，并且一个全量提交被执行：

- 所有在内存缓冲区的文档都被写入一个新的段。
- 缓冲区被清空。
- 一个提交点被写入硬盘。
- 文件系统缓存通过 fsync 被刷新（flush）。
- 老的 translog 被删除。
[/note]

[slide]

## 在刷新（flush）之后，段被全量提交，并且事务日志被清空
---
![After a flush, the segments are fully commited and the transaction log is cleared](/img/elas_1109.png)

[slide]

## flush API
---
这个会执行一个提交并且截断 translog 的行为在 Elasticsearch 被称作一次 flush 。 分片每30分钟被自动刷新（flush），或者在 translog 太大的时候也会刷新。

[note]
在重启节点或关闭索引之前执行 flush 有益于你的索引。当 Elasticsearch 尝试恢复或重新打开一个索引， 它需要重放 translog 中所有的操作，所以如果日志越短，恢复越快。
[/note]

[slide]

## 段合并
---

[note]
- 由于自动刷新流程每秒会创建一个新的段 ，这样会导致短时间内的段数量暴增。而段数目太多会带来较大的麻烦。 每一个段都会消耗文件句柄、内存和cpu运行周期。更重要的是，每个搜索请求都必须轮流检查每个段；所以段越多，搜索也就越慢。

- Elasticsearch通过在后台进行段合并来解决这个问题。小的段被合并到大的段，然后这些大的段再被合并到更大的段。

- 段合并的时候会将那些旧的已删除文档 从文件系统中清除。 被删除的文档（或被更新文档的旧版本）不会被拷贝到新的大段中。
[/note]

[slide]
## 两个提交了的段和一个未提交的段正在被合并到一个更大的段
---
![Two commited segments and one uncommited segment in the process of being merged into a bigger segment](/img/elas_1110.png)

[note]
1. 当索引的时候，刷新（refresh）操作会创建新的段并将段打开以供搜索使用。
2. 合并进程选择一小部分大小相似的段，并且在后台将它们合并到更大的段中。这并不会中断索引和搜索。
[/note]

[slide]

## 一旦合并结束，老的段被删除
---
![Once merging has finished, the old segments are deleted](/img/elas_1111.png)

[note]
- 新的段被刷新（flush）到了磁盘。写入一个包含新段且排除旧的和较小的段的新提交点。
- 新的段被打开用来搜索。
- 老的段被删除。
[/note]

[slide]

## optimize API
---
optimize API大可看做是 强制合并 API 。它会将一个分片强制合并到 max_num_segments 参数指定大小的段数目。 这样做的意图是减少段的数量（通常减少到一个），来提升搜索性能。

[note]
在特定情况下，使用 optimize API 颇有益处。例如在日志这种用例下，每天、每周、每月的日志被存储在一个索引中。 老的索引实质上是只读的；它们也并不太可能会发生变化。

在这种情况下，使用optimize优化老的索引，将每一个分片合并为一个单独的段就很有用了；这样既可以节省资源，也可以使搜索更加快速：
```
POST /logstash-2014-10/_optimize?max_num_segments=1
```
[/note]

[slide]
---
参考文章
- [Guide to Refresh and Flush Operations in Elasticsearch](https://qbox.io/blog/refresh-flush-operations-elasticsearch-guide)
- [Avoiding the Split Brain Problem in Elasticsearch](https://qbox.io/blog/split-brain-problem-elasticsearch)
- [Elasticsearch Reference](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/index.html)


[slide]

## Thanks for watching
---