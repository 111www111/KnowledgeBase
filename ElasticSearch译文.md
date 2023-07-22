# ElasticSearch译文 版本8.8

# 1.什么是ElasticSearch

你知道的，为了搜索（还有是分析）

Elasticsearch是一个分布式搜索引擎和分析引擎

Logstash和Beats便于收集、聚合和丰富数据，并将其存储在Elasticsearch中，

Kibana使您能够交互式地探索、可视化和共享对数据的见解，并管理和监视堆栈。

请将ELK组合使用，体验索引、搜索和分析的神奇之处。

Elasticsearch为所有类型的数据提供近乎实时的搜索和分析。无论您拥有结构化或非结构化文本、数字数据或地理空间数据，Elasticsearch都可以以支持快速搜索的方式高效地存储和索引数据。您可以超越简单的数据检索和聚合信息来发现数据中的趋势和模式。随着数据和查询量的增长，Elasticsearch的分布式特性使您的部署能够与之无缝增长。

虽然所有问题都并非是搜索的问题，但是Elasticsearch仍然提供了在各种情况下处理数据的速度和灵活性，例如

- 向应用程序或网站提供搜索框

- 存储和分析日志，指标以及安全数据

- 使用机器学习实时自动模拟数据的行为

- 使用Elasticsearch作为存储引擎自动化业务工作流

- 使用Elasticsearch分析，管理，继承地理信息系统

- 使用Elasticsearch作为生物信息学研究工具存储和处理遗传数据

我们对人们使用搜索的新奇方式不断感到惊讶。但是，无论您的用例与其中一个相似，还是您正在使用Elasticsearch解决一个新问题，您在Elasticsearch中处理数据、文档和索引的方式都是相同的。

## 数据来源：文件和索引

Elasticsearch是一个分布式的存储文档，他并不是将信息存储为列数据（参考MySQL），而是将数据存储为序列化后的JSON文档的复杂数据结构。当集群有多个Elasticsearch节点时。存储的文档分布在整个集群中，可以从任何节点访问

当一个文档被存储时，它几乎可以在一秒内实时的完成搜索。Elasticsearch使用了一种反向索引的数据结构，支持非常快速的全文搜索，而反向索引列出了任何文档中出现的每个唯一单词，并标识了每个单词所在的所有文档。

索引是一个可以被优化的集合文件，和每个文档一样，都是字段的集合。他们是由键值对构成的数据，默认情况下，Elasticsearch会为每个领域的数据编排索引，并且每个领域的索引字段都是专用的集合随手记

 Elasticsearch 还具有无模式的能力，这意味着无需明确指定如何处理文档中可能出现的每个不同字段即可对文档进行索引。启用动态映射后，Elasticsearch 会自动检测新字段并将其添加到索引中。这种默认行为使索引和探索数据变得很容易 - 只需开始索引文档，Elasticsearch 就会检测并将布尔值、浮点和整数值、日期和字符串映射到适当的 Elasticsearch 数据类型。 

但最终，您比 Elasticsearch 更了解您的数据以及您想要如何使用它。您可以定义规则来控制动态映射并显式定义映射以完全控制字段的存储和索引方式。

定义您自己的映射使您能够：

- 区分全文字符串字段和精确值字符串字段
- 执行特定于语言的文本分析
- 优化部分匹配的字段
- 使用自定义日期格式
- 使用无法自动检测到的 数据类型，例如`geo_point`和`geo_shape`

为了不同的目的以不同的方式对同一字段建立索引通常很有用。例如，您可能希望将字符串字段索引为全文搜索的文本字段和排序或聚合数据的关键字字段。或者，您可以选择使用多个语言分析器来处理包含用户输入的字符串字段的内容。

在索引期间应用于全文字段的分析链也在搜索时使用。当您查询全文字段时，在索引中查找术语之前，查询文本会经历相同的分析。



## 信息输出:搜索与分析

虽然您可以使用 Elasticsearch 作为文档存储并检索文档及其元数据，但真正的强大之处在于能够轻松访问基于 Apache Lucene 搜索引擎库构建的全套搜索功能 

Elasticsearch提供简单一致的REST风格API, 用于管理集群以及索引和搜索数据。出于测试目的，您可以直接从命令行或通过 Kibana 中的开发者控制台轻松提交请求。在您的应用程序中，您可以使用 适合您选择的语言的 的API操作 [Elasticsearch 客户端：Java、JavaScript、Go、.NET、PHP、Perl、Python 或 Ruby。](https://www.elastic.co/guide/en/elasticsearch/client/index.html) 

##  查询您的数据

Elastisearch 支持 REST 风格API的结构化查询, 全文查询以及两者结合的复杂查询,结构化查询与您使用的SQL查询类似,例如,您可以在员工索引(index)中,搜索性别和年龄字段,并且按照雇佣日期排序,全文查找与您输入的字符串相匹配的文档,并且您的排序规则返回他们与搜索词的相关匹配程度

除了单个查询以外,还可以执行短语搜索,相似度搜索,前缀搜索,并自动比对相似度

那么你是否有搜索的地理空间或其他数字数据的需求？Elasticsearch在支持高性能地理和数字查询的优化数据结构中对非文本数据进行索引。

总的来说你可以使用ElasticSearch的JSON 查询语句访问所有的搜索功能,当然您也可以构建sql风格的查询,以便本地搜索和聚合数据,也可以通过JDBC和ODBC驱动程序使广泛的第三方应用程序能够通过SQL与Elasticsearch进行交互。

## 分析您的数据

Elasticsearch聚合使您能够构建复杂的数据摘要，并深入了解关键指标、模式和趋势。聚合使您能够回答以下问题，而不仅仅是众所周知的“大海捞针”：

- 请问干草堆里藏有多少根针?
- 针的平均长度是多少?
- 按照制造商分组,针头长度中位数长度是多少?
- 再过去的六个月时间增加了多少根针?

您还可以使用聚合来回答更微妙的问题，例如：

- 最受欢迎的针头生产厂家是谁?
- 是否有坏的异常的针头出现?异常率是多少?

因为聚合使用了类似于查询的数据结构使得聚合的查询速度也非常可观,这使得你对数据的分析,可视化可以达到接近实时的地步,你的报告和可视化是根据数据变动的,所以这个功能可以使您的信息更加准确

更重要的是,聚合操作与搜索一起进行,你可以对文档进行查询,过滤结果,并且在单个请求下,短时间内,相同条件限制下的数据,完成分析聚合,由于聚合操作是在查询的上下文进行的,所以你不能仅仅显示某一条参数.

等等 还有更多

ElasticSearch具有分析时间序列数据的特效,所以基于这个特性,es可以作为机器学习的数据分析引擎



## 关于可扩展性,集群,节点和分片

ElasticSearch 可以根据您的想法通过分布式,构建高可用及其集群,你可以添加服务器(节点),ElasticSearch会自动为节点分配数据和查询负载,不需要更改底层的App即可完成,ElasticSearch知道如何平衡多节点以便达到分布式下的高可用,节点越多,效果越好

那么 这是如何做到的呢? 在后台,ES的索引实际上只是一个或多个物理分片的逻辑分组,其中每个碎片实际上是一个独立的索引, 通过将索引中的文档分布到多个分片,再通过分发这些分片到多个节点,当然,ES可以确保冗余,所以当新的节点添加至集群的时候,可以防止硬件故障,又可以提高查询能力,伴随着集群的膨胀或者缩小,ES会自动迁移切片以达到重新平衡集群的目的

在ES中 分片有两种类型,  主分片(primaries)  和  副本(replicas), 索引(index)中的每个文档(document)都属于一个主分片。 而副本分片将复制主分片,副本分片提供数据的冗余,防止硬件故障,并提供高可用的读取能力.

 索引中主分片的数量在创建索引时是固定的,而副本分片随时可以创建,不会中断主分片的运行或者查询操作.

当然,这些取决于..

对于分片大小和为索引配置的主分片数量，存在许多性能考虑因素和权衡, 分片越多，维护这些索引的开销就越大。副本数越多,Es的自我平衡时间就会越长,查询大量的小分片会使得每个分片的处理速度更快,但是更多的查询意味着更多的开销,所以说,查询较少数量的较大分片可能会更快,这取决于以下几点

- 目标是将平均分片大小保持在几 GB 到几十 GB 之间。对于基于时间的数据的用例，通常会看到 20GB 到 40GB 范围内的分片。 
- 避免无数碎片问题。节点可以容纳的分片数量与可用节点的堆空间成正比。作为一般规则，每 GB 堆空间的分片数量应小于 20。 

确定适合您的用例的最佳配置的最佳方法是通过[您自己的数据和查询进行测试。](https://www.elastic.co/cn/elasticon/conf/2016/sf/quantitative-cluster-sizing) 

## 说到容灾

集群的节点之间需要良好、可靠的连接.为了提供更好的连接， 通常会采用将节点放在同一个数据中心或者附近的数据中心(中心化),然而,为了保持高可用,需要避免任何情况下的单点故障,如果某个节点不幸挂掉了(例如停电),其他地点的服务需要能够接管整个烂摊子,那么答案就是 跨集群复制 Cross-cluster replication (CCR). 

CCR 提供了一种自动将索引从主集群同步到可用作热备份(hot backup)的辅助远程集群的方法。 如果主集群不幸挂了,辅助集群可以接管服务,你可以通过使用CCR 创建辅助集群用来服务近期用户的读请求.

跨集群复制是主动-被动的。 主集群上的索引是活动领导索引并处理所有写入请求。复制到辅助集群的索引是只读追随者。  

## Care and feeding

与任何企业系统一样，您需要工具来保护、管理和监控您的Elasticsearch集群。集成到Elasticsearch中的安全、监控和管理功能使您能够使用Kibana作为管理集群的控制中心。数据汇总和索引生命周期管理等功能可帮助您随着时间的推移智能地管理数据。

# 安装ElasticSearch

## 托管ElasticSearch服务

Elastic Cloud 提供 Elasticsearch、Kibana 以及 Elastic 的可观测性、企业搜索和 Elastic Security 解决方案的所有功能，作为 AWS、GCP 和 Azure 上提供的托管服务。 

可以[前往cloud](https://www.elastic.co/cloud/elasticsearch-service/signup?baymax=docs-body&elektra=docs)中注册免费试用版

## 在机器上安装 ElasticSearch

如果你想自己安装管理ElasticSearch,你可以尝试:

- 在 Linux,MacOS 还有windows等系统的机器上运行ElasticSearch 
- [在Docker 容器中运行Es](https://www.elastic.co/guide/en/elasticsearch/reference/8.8/docker.html)
- 设置管理ES,kibana 等服务在[Kubernetes](https://www.elastic.co/guide/en/cloud-on-k8s/current/index.html)上

## ElasticSearch 安装包

ElasticSearch 提供下列的安装包格式

注:安装的具体细节,可以查看以下地址中的细节部分,这里的翻译省略

| 服务                    | 地址                                                         |
| ----------------------- | ------------------------------------------------------------ |
| Linux 与 MacOS (tar.gz) | tar.gz可以安装在任何linux发行版和macos上,[链接](https://www.elastic.co/guide/en/elasticsearch/reference/8.8/targz.html) |
| windows (.zip)          | zip格式适合安装在windows上,[链接](https://www.elastic.co/guide/en/elasticsearch/reference/8.8/zip-windows.html) |
| deb                     | deb格式的安装包适合在Debian,Ubuntu等系统上下,[链接](https://www.elastic.co/guide/en/elasticsearch/reference/8.8/deb.html) |
| rpm                     | rpm适用于  Red Hat, Centos, SLES, 等系统,[链接](https://www.elastic.co/guide/en/elasticsearch/reference/8.8/rpm.html) |
| docker                  | 镜像可用于es作为docker运行,可以从Es docker 下载,[链接](https://www.elastic.co/guide/en/elasticsearch/reference/8.8/docker.html) |

## 关于JVM版本

ElasticSearch的构建依赖于Java,并在每个发行版中包含来自 JDK 维护者 (GPLv2+CE) 的 OpenJDK 捆绑版本。捆绑的 JVM 是推荐的 JVM。 

这里需要使用您自己的java版本,书写ES_JAVA_HOME的环境变量,如果你必须使用与捆绑的JVM不同版本的java,那么最好使用受支持的版本([查看](https://www.elastic.co/cn/support/matrix)),因为es与某些特定与OpenJDK的功能紧密耦合,所以如果版本不同可能无法正常运行,或者拒绝启动

如果您使用的JVM不是捆绑的JVM，那么您有责任对与其安全问题和错误修复相关的公告做出反应，并且必须自己确定每次更新是否必要。相比之下，捆绑的JVM被视为Elasticsearch不可分割的一部分，这意味着Elastic负责保持其最新。捆绑JVM中的安全问题和错误被视为在Elasticsearch本身中。捆绑的 JVM 位于 Elasticsearch 主目录的 jdk 子目录中。如果使用您自己的 JVM，您可以删除此目录。