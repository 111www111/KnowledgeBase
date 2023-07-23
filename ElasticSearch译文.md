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

## 本地运行ElastiSearch

https://www.elastic.co/guide/en/elasticsearch/reference/8.8/run-elasticsearch-locally.html

如果您想尝试在您自己的机器上使用ElasticSearch , 我们推荐docker 部署Es和KiBana,[获取docker 镜像](https://www.docker.elastic.co/)

```
从8.0版本开始,我们将默认启用安全配置,第一次启动Es,TLS加密会自动配置,为elastic 用户生成密码，并且Kibana注册Token,链接您的集群
```

1. 安装桌面版Docker,并且保证WSL中拥有4G内存

```
#作者配置
#C:\Users\<UserName>\.wslconfig创建一个文件，配置如下所示。
[wsl2]
processors=8
memory=4GB
swap=8GB
localhostForwarding=true
```

2. 启动ElasticSearch容器

```shell
docker network create elastic

docker pull docker.elastic.co/elasticsearch/elasticsearch:8.8.2

docker run --name elasticsearch --net elastic -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -t docker.elastic.co/elasticsearch/elasticsearch:8.8.2
```

当您第一次启动 Elasticsearch 时 ,生成的弹性用户密码和 Kibana 注册令牌将输出到终端。 您可能需要在终端中向后滚动一点才能查看密码和注册令牌。 

3. 复制生成的密码和注册的token并保存,他们只会在你第一次启动后的时候展示您将使用它们在 Elasticsearch 集群中注册 Kibana 并登录。 

## 运行kibana

kibana可以使您更容易操作并分析Es中的数据,可以通过可视化交互式管理数据

1. 在新的终端会话中启动kibana,并且链接您的ES

   ```
   docker pull docker.elastic.co/kibana/kibana:8.8.2
   
   docker run --name kibana --net elastic -p 5601:5601 docker.elastic.co/kibana/kibana:8.8.2
   ```

   当您启动 Kibana 时，一个唯一的 URL 会输出到您的终端。 

2. 要访问 Kibana，请在浏览器中打开生成的 URL。 

   a. 粘贴您在启动 Elasticsearch 时复制的注册令牌，然后单击按钮将您的 Kibana 实例与 Elasticsearch 连接。 

   b. 使用启动 Elasticsearch 时生成的密码以 Elastic 用户身份登录 Kibana。 

## 发送请求到ES

你可以通过REST风格的API,发送数据和其他的请求到ES,您可以使用任何发送 HTTP 请求的客户端与 Elasticsearch 交互,例如Elasticsearch语言客户端和curl。 Kibana 的开发者控制台提供了一种简单的方法来实验和测试请求。要访问控制台，请 **Management > Dev Tools**. 

## 添加数据

您可以通过 REST API 发送 JSON 对象（文档），将数据索引到Elasticsearch 中。 无论是结构化数据还是非结构化数据,数值 或者地理空间,Elasticsearch 以支持快速搜索的方式有效地存储和索引它。 

对于带有时间戳的数据（例如日志和指标），您通常将文档添加到由多个自动生成的支持索引组成的数据流中。 

要将单个文档添加到索引，请提交针对该索引的 HTTP post 请求。 

```
POST /customer/_doc/1
{
  "firstname": "Jennifer",
  "lastname": "Walters"
}
```

在这个请求中,如果Customer的索引(index)不存在,Es会自动创建该索引并添加一个ID为1的文档(document),并储存名字和姓氏两个字段

这个新的文档是即时生效的,可以在任意节点读取,你可以通过GET请求指定的文档ID 取回数据

```
GET /customer/_doc/1
```

当然也可以在一次请求中添加多个文档,这里需要使用_bulk的API。 批量数据必须是换行符分隔的 JSON (NDJSON) ,每行必须以换行符 (\n) 结尾，包括最后一行。 

```
PUT customer/_bulk
{ "create": { } }
{ "firstname": "Monica","lastname":"Rambeau"}
{ "create": { } }
{ "firstname": "Carol","lastname":"Danvers"}
{ "create": { } }
{ "firstname": "Wanda","lastname":"Maximoff"}
{ "create": { } }
{ "firstname": "Jennifer","lastname":"Takeda"}
```

## 查询

索引文档可近乎实时地搜索。 以下搜索与客户索引中名字为 Jennifer 的所有客户相匹配。 

```
GET customer/_search
{
  "query" : {
    "match" : { "firstname": "Jennifer" }
  }
}
```

## 探索

你可以使用kibana交互式的查询和过滤数据,从那里，您可以开始创建可视化以及构建和共享仪表板。 首先，创建一个连接到一个或多个 Elasticsearch 索引、数据流或索引别名的数据视图。 

1. 前往  **Management > Stack Management > Kibana > Data Views**.
2. 查看  **Create data view**. 
3. 输入数据视图的名称以及与一个或多个索引（例如*customer* ）匹配的模式。 
4. 查看 **Save data view to Kibana**. 

开始探索,请前往 **Analytics > Discover**. 

# 配置ElasticSearch

ES具有良好的默认配置,所以需要我们配置的东西很少,可以使用集群更新设置 API 在正在运行的集群上[更改](https://www.elastic.co/guide/en/elasticsearch/reference/8.8/cluster-update-settings.html)大多数设置。 

配置文件应包含特定于节点的设置(例如node.name和路径),或节点加入集群所需的设置，例如 cluster.name 和 network.host。 

## 配置文件位置

Es拥有三个配置文件:

1. **elasticsearch.yml** 用于配置Es
2. **jvm.options **用于Es的JVM设置
3. **log4j2.properties** 用于配置Es 日志

这些配置文件位于config的目录下,其默认位置取决于安装是来自存档发行版（tar.gz 或 zip）还是软件包发行版(Debian 或 RPM 软件包).对于默认版本,config 目录位置默认为 $ES_HOME/config。 config 目录的位置可以通过 ES_PATH_CONF 环境变量更改，如下所示： 

```
ES_PATH_CONF=/path/to/my/config ./bin/elasticsearch
```

或者，您可以通过命令行或 shell 配置文件导出 ES_PATH_CONF 环境变量 

配置目录位置默认为 /etc/elasticsearch。 config 目录的位置也可以通过 ES_PATH_CONF 环境变量进行更改，但请注意，在 shell 中设置此位置是不够的。相反，此变量源自 /etc/default/elasticsearch （对于 Debian 软件包）和 /etc/sysconfig/elasticsearch （对于 RPM 软件包）。您将需要相应地编辑这些文件之一中的 ES_PATH_CONF=/etc/elasticsearch 条目以更改配置目录位置。 

## 配置文件格式

这些配置文件的书写格式均为YMAL,以下是更改数据和日志文件的示例

```
path:
    data: /var/lib/elasticsearch
    logs: /var/log/elasticsearch
```

也可以按照如下格式展开设置

```
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
```

在 YAML 中，您可以将非标量值格式化为序列： 

```
discovery.seed_hosts:
   - 192.168.1.10:9300
   - 192.168.1.11
   - seeds.mydomain.com
```

虽然不太常见，但您也可以将非标量值格式化为数组： 

```
discovery.seed_hosts: ["192.168.1.10:9300", "192.168.1.11", "seeds.mydomain.com"]
```

## 使用环境变量替换编辑

环境变量中所引用的 ${...} 也可以在配置文件中引用

```
node.name:    ${HOSTNAME}
network.host: ${ES_NETWORK_HOST}
```

不过,环境变量的值必须是简单的字符串,使用逗号分隔的字符串提供 Elasticsearch 将解析为列表的值。例如，Elasticsearch 会将以下字符串拆分为 ${HOSTNAME} 环境变量的值列表： 

```
export HOSTNAME="host1,host2"
```

## 集群与节点的类型设置

集群与节点的设置可以根据配置方式进行分类:

动态的:

​	你可以使用集群更新[设置 API](https://www.elastic.co/guide/en/elasticsearch/reference/8.8/cluster-update-settings.html) 在正在运行的集群上配置和更新动态设置。您还可以使用elasticsearch.yml 在未启动或关闭的节点上本地配置动态设置。 

​	使用集群更新设置 API 进行的更新可以是持久性的（在集群重新启动时应用），也可以是暂时性的（在集群重新启动后重置）。您还可以使用 API 为临时或持久设置分配空值来重置它们。 

​	如果您使用多种方法配置相同的设置，Elasticsearch 将按以下优先顺序应用设置： 

- 瞬态设置 

- 持续设置 

- elasticsearch.yml 

- 设置 默认设定值 

  举个例子,您可以应用瞬态设置来覆盖持久设置或elasticsearch.yml 设置。但是，对 elasticsearch.yml 设置的更改不会覆盖定义的瞬态或持久设置。 

如果您使用 Elasticsearch Service，请使用[用户设置](https://www.elastic.co/guide/en/cloud/current/ec-add-user-settings.html) 功能来配置所有集群设置。此方法可让 Elasticsearch Service 自动拒绝可能破坏集群的不安全设置。

如果您在自己的硬件上运行 Elasticsearch，请使用 [集群更新设置](https://www.elastic.co/guide/en/elasticsearch/reference/8.8/cluster-update-settings.html) API 来配置动态集群设置。仅用于elasticsearch.yml静态集群设置和节点设置。该 API 不需要重新启动并确保所有节点上的设置值相同

我们不再建议使用临时集群设置。请改用持久集群设置。如果集群变得不稳定，瞬态设置可能会意外清除，从而导致可能出现不需要的集群配置。请参阅[瞬态设置迁移指南](https://www.elastic.co/guide/en/elasticsearch/reference/8.8/transient-settings-migration-guide.html)。 

静态设置只能在未启动或关闭的节点上使用 进行配置 `elasticsearch.yml`。 必须在集群中的每个相关节点上设置静态设置。 

# 重要的ElasticSearch配置

https://www.elastic.co/guide/en/elasticsearch/reference/8.8/important-settings.html