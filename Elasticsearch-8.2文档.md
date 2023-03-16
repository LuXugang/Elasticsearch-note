# Elasticsearch-8.2文档（2023/03/15）

## What is Elasticsearch?
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/elasticsearch-intro.html)

**You know, for search (and analysis)**

&emsp;&emsp;Elasticsearch是Elastic Stack中核心的分布式搜索和分析引擎。Logstash和Beats帮助收集，聚合以及丰富你的数据并存储在Elasticsearch。Kibana使你能够交互式地探索、可视化和分享对数据的见解，并管理和监视stack。Elasticsearch用于索引，查询以及分析。

&emsp;&emsp;Elasticsearch提供对所有类型的数据的近实时搜索（near real-time）和分析。无论是结构化还是非结构化的文本，数值类型的数据，或者地理位置数据，Elasticsearch都能有效的进行存储并以某种方式进行索引来实现快速查询。你可以不仅仅是简单的数据检索而是可以进一步的对信息进行聚合来发现你数据中的趋势和patterns。随着你的数据和查询体量的增大，Elasticsearch的分布式功能使你的部署能够无缝地（seamless）随之增长。

&emsp;&emsp;虽然不是每一个问题都是一个查询问题，Elasticsearch为在各种用例中处理数据提供了速度（speed）和灵活性（flexibility）。

- 添加一个搜索框（search box）到一个app或者网页中
- 存储和分析日志，指标以及安全事件数据
- 使用machine learning自动对你的数据行为构建实时的模型
- 使用Elasticsearch作为一个存储引擎自动化业务工作流（business workflows）
- 使用Elasticsearch作为一个管理，集成以及分析空间信息地理信息系统（GIS: geographic information system）
- 使用Elasticsearch作为一个生物信息学研究工具（ bioinformatics research tool）来存储以及处理基因数据

&emsp;&emsp;我们不断的因为用户使用新颖（novel）的搜索方式而感到惊讶（amaze）。但是不管你的使用案例是否类似与这些中的一种，或者你正在使用Elasticsearch解决（tackle）一个新的问题，你在Elasticsearch中处理你数据， 文档以及索引都是一样的。

### Data in documents and indices
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/documents-indices.html)

&emsp;&emsp;Elasticsearch是一个分布式的文档存储。Elasticsearch 存储已序列化为 JSON 文档的复杂数据结构，而不是将信息存储为列式数据行。当你的集群中有多个Elasticsearch节点，存储的文档跨集群分布并能立即从任何节点访问。

&emsp;&emsp;文档在存储之后，它会被索引（index）并且在能在[near real-time]()--一秒内完全的用于搜索。Elasticsearch使用了称为倒排表（inverted index）的数据结构，它能够用于快速的全文检索。inverted index列出了出现在所有文档中的每一个unique word并识别出每一个单词所在的所有文档。

&emsp;&emsp;索引（index）可以认为是一个优化后的文档集合，每一篇文档是一个域（field）的集合，每一个域是包含数据的一个键值对（key-value pair）。默认情况下，Elasticsearch会索引所有域中的数据并且每一种索引域（indexed field）都有专门的优化后的数据结构。例如，text field存储在倒排索引（inverted index）中，numeric和geo field存储在[BKD](https://www.amazingkoala.com.cn/Lucene/gongjulei/2019/0422/52.html)树中。使用每一种域的数据结构进行组合（assemble）和返回查询结果的能力使得Elasticsearch特别的快。

&emsp;&emsp;Elasticsearch同样有schema-less的能力，意味着不用显示的指定如何处理一篇文档中的不同的域就可以直接对文档进行索引。当开启dynamic mapping后，Elasticsearch能自动的检测并添加新的域到索引中。默认的行为使得索引以及探索你的数据变得简单。只需要开始索引文档，Elasticsearch就会进行检测并将booleans，floating point，integer values，dates和strings映射成Elasticsearch中合适的数据类型。

&emsp;&emsp;最终，当你比Elasticsearch更了解自己的数据并且想要按照自己的方式来处理。你可以定义规则来控制dynamic mappings以及显示的（explicit）定义mapping来完全的控制如何对域进行存储和索引。

&emsp;&emsp;定义你自己的mapping可以让你：

- 区分出full-text string field跟exact value string field
- 执行特定语言的文本分析
- 为部分匹配对域进行优化
- 使用自定义的date formats
- 使用`geo_point` 和 `geo_shape`这些不能被自动检测的数据类型

&emsp;&emsp;基于不同的目的，用不同的方式索引同一个域通常是很有用的。例如你可能想要将一个string field索引为text field用于全文检索以及keyword域用于排序、聚合。或者你可能会选择使用多个语言分词器来处理包含用户输入的内容。

&emsp;&emsp;在索引期间作用到full-text field的analysis chain在查询期间同样需要使用。当你查询一个full-text field，在索引中查找term前，它的请求文本（query text）也会经历（undergo）相同的analysis。

### Information out: search and analyze
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-analyze.html)

&emsp;&emsp;当使用Elasticsearch作为一个文档存储（document store），检索文档以及文档的元信息（metadata）时，你能够轻松访问全套搜索能力，其能力来源是因为构建在Apache Lucene 搜索引擎库之上。

&emsp;&emsp;Elasticsearch提供了一套简单的，容易理解的（coherent）的REST API，用于管理集群，索引以及查询数据。出于测试的目的，你可以简单的通过命令行或者Kibana中的Developer Console直接提交一个请求。在你的应用中，你可以选择语言并使用[Elasticsearch client](https://www.elastic.co/guide/en/elasticsearch/client/index.html)：Java, JavaScript, Go, .NET, PHP, Perl, Python 或者 Ruby。

#### Searching your data

&emsp;&emsp;Elasticsearch REST APIs支持结构化查询（structured query），全文检索，以及复杂的查询，比如query的组合。结构化查询类似你在SQL中构造的查询类型。例如，你可以在`employee`索引中查询`gender`和`age`域并且根据`hire_date`域对匹配的结果进行排序。全文检索会找到满足查询条件的所有的文档并且根据相关性（relevance，how good a match they are for your search terms）排序。

&emsp;&emsp;除了查询不同的term，你还可以执行短语查询（phrase search），相似度查询（similarity search），前缀查询（prefix search）以及获得autocomplete suggestions。

&emsp;&emsp;想要查询地理位置或者其他数值类型的数据的话，Elasticsearch将这类非文本的数据索引到一个优化后的数据结构（BKD）使得支持高性能的地址位置和数值查询。

&emsp;&emsp;你可以使用Elasticsearch中JSON风格的查询语言（[Query DSL](##Query DSL)）来访问所有的查询能力。你也可以构造[SQL-style query]()查询/聚合数据，以及使用JDBC和ODBC驱动使得更多的第三方应用通过SQL使用Elasticsearch。

#### Analyzing your data

&emsp;&emsp;Elasticsearch的聚合（aggregation）能让你构建复杂的数据汇总并获得关键指标的洞见（insight），模式（pattern）以及趋势（trend）。聚合能让你回答下面的问题，而不是仅仅如谚语中所说的needle in a haystack：

- haystack中有多少个needle？
- needle的平均长度
- 每个生产商（manufacturer）制造的needle的median length
- 过去的六个月中，每个月添加到haystack的needle的数量

&emsp;&emsp;你可以使用聚合回答更多subtle问题，例如：

- 最受欢迎的needle生产商是哪家？
- 是否存在不寻常或者异常的（anomalous）needle？

&emsp;&emsp;由于聚合使用了查询中使用的相同的数据结构，所以非常的快，使得可以实时的分析以及可视化你的数据。报表跟dashboard可以随着你的数据的变更而更新，使得你可以基于最新的信息采取措施（take action）。

&emsp;&emsp;聚合是跟查询请求一起执行的。你可以在单个请求中对相同的数据进行查询，过滤，以及分析。因为聚合要在某个查询的上下文中计算，你不仅仅能展示70号needle的数量统计，你还能展示满足你的策略的needle：比如说70号的不沾针（non-stick embroidery needles）。

#### But wait, there’s more

&emsp;&emsp;想要自动分析你的时序数据吗？你可以使用[machine learning](https://www.elastic.co/guide/en/machine-learning/8.2/ml-ad-overview.html)功能创建你的数据中普通行为（normal behavior）的准确基线以及识别出异常模式（anomalous pattern）。使用machine learning，你可以检测下面的信息：

- Anomalies related to temporal deviations in values, counts, or frequencies
- Statistical rarity
- Unusual behaviors for a member of a population

&emsp;&emsp;And the best part? 你不需要指定算法，模型或者其他数据科学相关的配置就可以实现上面的功能。

### Scalability and resilience: clusters, nodes, and shards
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/scalability.html#scalability)

&emsp;&emsp;Elasticsearch总是可用的（available）并且根据你的需要进行扩展。It does this by being distributed by nature。你可以向一个集群中添加服务（节点）来提高承载力（capacity），Elasticsearch可以跨所有可用的节点，自动的分布/查询（distribute/query）数据。不需要overhaul你的应用，Elasticsearch知道如何平衡多个节点的集群来提供扩展以及高可用能力。The more nodes, the merrier。

&emsp;&emsp;Elasticsearch是如何工作的？在底层实现中，一个Elasticsearch index只是一个或多个物理分片（physical shards）的逻辑组合（logical grouping）。每一个分片实际上是一个self-contained index。通过将索引中的文档分布到多个分片，将分片分布到多个节点，Elasticsearch可以确保冗余（ensure redundancy），使得应对硬件故障以及节点添加到集群后，查询能力的提升。随着集群的增长（收缩），Elasticsearch能自动的迁移（migrate）分片来rebalance集群。

&emsp;&emsp;分片的类型有两种：主分片跟副本分片（primary  and replica shard）。索引中的每一篇文档属于某一个主分片。一个副本分片是某个主分片的拷贝。副本分片提供了数据的冗余来应对硬件故障以及提高例如查询或者检索一篇文档的读取请求的能力。

&emsp;&emsp;某个索引中的主分片数量在索引创建后就固定了。但是副本分配的数量可以在任何时间内更改，不会影响（interrupt）索引或者查询操作。

#### It depends…

&emsp;&emsp;对于分片大小和索引的主分片数量，有很多性能考虑点以及trade off。分片越多，维护这些索引的开销就越大。分片大小越大，当Elasticsearch需要rebalance集群时，移动分片花费的时间越大。

&emsp;&emsp;在很多较小的分片上查询时，在每一个分片上的查询很快，但是分片越多，查询次数就越多，开销就越大，所以在数量较小，体积较大的分片上的查询可能就更快。In short…it depends。

&emsp;&emsp;As a starting point：

- 将分片的平均大小保持在几个GB以及十几个GB之间。对于基于时间的数据，通常来说，分片的大小在20GB到40GB之间
- 避免出现大量分片的问题。一个节点可以拥有的分片数量跟可用的堆内存成正比。一般来说（as a general rule），每1GB的堆内存中的分片数量应该小于20个。

&emsp;&emsp;对于你的用例，确定最佳配置的最好方式是通过[testing with your own data and queries](https://www.elastic.co/cn/elasticon/conf/2016/sf/quantitative-cluster-sizing)。

#### In case of disaster

&emsp;&emsp;集群中的节点之间需要良好的，可靠的连接。若要能提供一个较好的连接，你通常会将节点放在相同的数据中心或者附近的数据中心。然而为了高可用，你同样需要避免单点故障（single point of failure）。某个地区（location）发生停电后，其他地区必须能接管服务。这个问题的答案就是使用CCR（Cross-cluster replication）。

&emsp;&emsp;CCR提供了一种从主集群（primary cluster）自动同步索引到secondary remote cluster的方法，即secondary remote cluster作为一个热备（hot backup）。如果primary cluster发生了故障，secondary cluster可以进行接管。你可以使用CCR创建secondary cluster，给地理上靠近（geo-proximity）这个secondary cluster的用户提供读取请求。

#### Care and feeding

&emsp;&emsp;与任何企业系统一样，你需要工具来secure，manage，以及monitor你的Elasticsearch 集群。Security，monitoring，以及administrative features都集成到了Elasticsearch，使得你可以使用[Kibana](https://www.elastic.co/guide/en/kibana/8.2/introduction.html)作为控制中心来管理集群。比如[data rollups](###Rolling up historical data)、[index lifecycle management](###ILM: Manage the index lifecycle)这些功能能帮助你根据时间来管理你的数据。

## Set up Elasticsearch
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/setup.html)

&emsp;&emsp;这个章节介绍了如何设置Elasticsearch并且使其运行，包含的内容有：

- 下载
- 安装
- 启动
- 配置

#### Supported platforms

&emsp;&emsp;官方给出的操作系统以及JVM的支持列表见[Support Matrix](https://www.elastic.co/support/matrix).Elasticsearch在这些平台上都经过了测试，但是在列表外的其他平台上还是有可能可以正常工作的。

#### Java（JVM）Version

&emsp;&emsp;Elasticsearch使用Java构建，并且每次发布都捆绑了一个OpenJDK，这个OpenJDK由JDK维护者使用GPLv2+CE协议维护。建议使用捆绑的JDK，它位于Elasticsearch home目录中名为jdk的目录中。

&emsp;&emsp;可以通过设置环境变量`ES_JAVA_HOME`来使用你自己的Java版本。如果你一定要使用一个跟捆绑的JVM不一样的Java版本，我们建议你使用Java的长期支持版本[LTS ](https://www.oracle.com/technetwork/java/eol-135779.html)。如果使用了一个错误Java版本的，Elasticsearch将不会启动。当你使用了自己的JVM后，绑定的JVM目录可能会被删除。

#### Use delicated hosts 使用专用的主机

&emsp;&emsp;在生产上，我们建议你在专用的主机或者主服务器上（primary service）运行Elasticsearch。假定Elasticsearch是主机上或者容器上唯一的资源密集型的应用，那么一些Elasticsearch的功能比如自动化分配JVM堆大小就能实现。比如说，你可能同时运行Metribeat跟Elasticsearch来做集群统计，那么就应该将resource-heavy的Logstash部署在它自己的主机上。

### Installing Elasticsearch
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/install-elasticsearch.html)

##### Hosted Elasticsearch

&emsp;&emsp;你可以在自己的设备上运行Elasticsearch或者也可以使用AWS, GCP, and Azure上[专用的Elasticsearch服务器](https://www.elastic.co/cn/cloud/elasticsearch-service/signup?baymax=docs-body&elektra=docs)。

##### Installing Elasticsearch Yourself

&emsp;&emsp;Elasticsearch以下列的打包方式呈现：

##### Configuration Management Tools

&emsp;&emsp;我们同样提供了下列的配置管理工具有助于大型的部署

- [Puppet](https://github.com/voxpupuli/puppet-elasticsearch)
- [Chef](https://github.com/elastic/cookbook-elasticsearch)
- [Ansible](https://github.com/elastic/ansible-elasticsearch)

#### Install Elasticsearch from archive on Linux or MacOS
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/targz.html#install-macos)

&emsp;&emsp;在Linux和MacOS平台下，Elasticsearch是作为一个`.tar.gz`的归档文件。

&emsp;&emsp;这个安装包同时包含了免费和订阅的特性，[30天试用](####License-settings)可以使用所有的功能。

&emsp;&emsp;Elasticsearch最新的稳定版本可以从这个页面下载，其他版本可以从过去的发布页面下载。

>NOTE： Elasticsearch中捆绑了一个OpenJDK，这个OpenJDK由JDK维护者使用GPLv2+CE协议维护，如果要使用自己的Java版本,

##### Download and install archive for Linux

&emsp;&emsp;Linux下Elasticsearch-7.15.2归档文件的下载以及安装如下所示：

```text
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.15.2-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.15.2-linux-x86_64.tar.gz.sha512
shasum -a 512 -c elasticsearch-7.15.2-linux-x86_64.tar.gz.sha512 
tar -xzf elasticsearch-7.15.2-linux-x86_64.tar.gz
cd elasticsearch-7.15.2/ 
```

&emsp;&emsp;上文第三行Compares the SHA of the downloaded `.tar.gz` archive and the published checksum，which should output `elasticsearch-{version}-linux-x86_64.tar.gz: OK`。

&emsp;&emsp;上文第五行就是所谓的 `$ES_HOME`。

##### Download and install archive for MacOS

&emsp;&emsp;MacOS下Elasticsearch-7.15.2归档文件的下载以及安装如下所示：

```text
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.15.2-darwin-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.15.2-darwin-x86_64.tar.gz.sha512
shasum -a 512 -c elasticsearch-7.15.2-darwin-x86_64.tar.gz.sha512 
tar -xzf elasticsearch-7.15.2-darwin-x86_64.tar.gz
cd elasticsearch-7.15.2/ 
```

&emsp;&emsp;上文第三行Compares the SHA of the downloaded `.tar.gz` archive and the published checksum，which should output `elasticsearch-{version}-linux-x86_64.tar.gz: OK`。

&emsp;&emsp;上文第五行就是所谓的 `$ES_HOME`。

##### Enable automatic creation of system indices

&emsp;&emsp;一些商业功能会自动创建索引。默认情况下，Elasticsearch可以被配置为允许自动创建索引，并且不需要再做额外的步骤。然而，如果你关闭了自动创建索引， 你必须在`elasticsearch.yml`中配置[action.auto_create_index](####Index APi)来允许商业功能创建下面的索引：

```text
action.auto_create_index: .monitoring*,.watches,.triggered_watches,.watcher-history*,.ml*
```

>IMPORTANT：如果你正在使用Logstash或者Beats，那么你很有可能需要在action.auto_create_index配置额外的索引名，确切的名字取决去你的本地配置。如果你不能保证正确的名字，你可以考虑将索引名设置为\*，这样就会自动创建所有的索引


##### Running Elasticsearch from the command line

&emsp;&emsp;Elasticsearch可以通过下面的命令行启动：

```text
./bin/elasticsearch
```

&emsp;&emsp;如果Elasticsearch keystore有密码保护，你会被提示（be prompted to）输入keystore's的密码，详细内容见[安全配置](##Secure-settings)。

&emsp;&emsp;默认情况下，Elasticsearch将日志打印到控制台（标准输出）以及[日志目录](##Important-Elasticsearch-configuration)的`<clustername>.log`文件中。Elasticsearch在启动期间会生成一些日志，但一旦初始化完成，它将继续在前台（foreground）运行并且不再生成任何日志直到一些值的记录的信息产生。在Elasticsearch运行期间，你可以通过HTTP接口访问默认的9200端口与Elasticsearch交互。输入`Ctrl-c`来停止Elasticsearch。

>NOTE：Elasticsearch安装包中的所有脚本要求的Bash版本需要支持arrays并且假定/bin/bash是存在的，同样的，Bash在这个路径是存在的或者能通过动态链接找到

##### Checking that Elasticsearch is running

&emsp;&emsp;你能通过HTTP请求访问localhost:9200来测试Elasticsearch节点是否在运行中：

```java
GET /
```

&emsp;&emsp;你应该能收到类似下面的回应（response）

```java
{
  "name" : "Cp8oag6",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "AT69_T_DTp-1qgIJlatQqA",
  "version" : {
    "number" : "7.15.2",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "f27399d",
    "build_date" : "2016-03-30T09:51:41.449Z",
    "build_snapshot" : false,
    "lucene_version" : "8.9.0",
    "minimum_wire_compatibility_version" : "1.2.3",
    "minimum_index_compatibility_version" : "1.2.3"
  },
  "tagline" : "You Know, for Search"
}
```

##### Running as a daemon

&emsp;&emsp;在命令行指定`-d`使得Elasticsearch成为一个后台程序，并且通过参数`-p`生成一个`pid`文件记录进程号。

```text
./bin/elasticsearch -d -p pid
```

&emsp;&emsp;如果Elasticsearch keystore有密码保护，你会被提示（be prompted to）输入keystore's的密码，详细内容见[安全配置](##Secure-settings)。

&emsp;&emsp;在`$ES_HOME/logs/`目录能找到日志信息。

&emsp;&emsp;通过杀死`pid`文件中记录的进程号来关闭Elasticsearch。

```text
pkill -F pid
```

>NOTE：tar.gz的安装包中不包含systemd组件。如果要把Elasticsearch作为一个服务管理，用RPM或者Debian的安装包进行安装。

##### Configuring Elasticsearch on the command line

&emsp;&emsp;Elasticsearch默认装载(load)的配置文件路径为`$ES_HOME/config/elasticsearch.yml`，这个配置文件中的格式见[Configuring Elasticsearch](###Configuring-Elasticsearch)。

&emsp;&emsp;在配置文件中可以指定的任何设置（settings）都可以通过命令行的参数指定，使用`-E`语法：

```text
./bin/elasticsearch -d -Ecluster.name=my_cluster -Enode.name=node_1
```

>TIP：一般来说，任何集群范围（cluster-wide）的配置（比如说 cluster.name）都应该在elasticsearch.yml文件中配置，同时具体节点的任意配置比如说node.name可以通过命令行参数指定

##### Directory layout of archives

&emsp;&emsp;归档发行版 (archive distribution)是self-contained（意思就是所有的文件都在同一个目录中，Elasticsearch还有安装包发行版package distribution，通过这种方式安装后，文件会分散在不同的目录中）。所有的文件和目录默认情况下都在`$ES_HOME`下，`$ES_HOME`在解压归档后会被创建。

&emsp;&emsp;这是一种非常方便的方式因为你不需要创建任何目录来开始使用Elasticsearch，并且当卸载Elasticsearch时只要简单的移除`$ES_HOME`。然而我们建议把配置文件目录config directory、数据目录data directory、以及日志目录log directory的位置进行变更，以免在以后重要的数据被删除。

| 类型Type |                       描述Description                        | 默认位置Default Location |                         设置Setting                          |
| :------: | :----------------------------------------------------------: | :----------------------: | :----------------------------------------------------------: |
|   home   |             Elasticsearch home目录或者`$ES_HOME`             |  解压归档后创建这个目录  |                                                              |
|   bin    | 二进制脚本，包含`elasticsearch`用于启动一个节点以及`elasticsearch-plugin`用于安装插件 |      `$ES_HOME/bin`      | [ES_PATH_CONF](###Configuring-Elasticsearch) |
|   conf   |            包含`elasticsearch.yml`的所有配置文件             |    `$ES_HOME/config`     |                                                              |
|   data   |               每一个索引/分片的数据文件的位置                |     `$ES_HOME/data`      |                          path.data                           |
|   logs   |                         日志文件位置                         |     `$ES_HOME/logs`      |                          path.logs                           |
| plugins  |             插件文件位置。每个插件在一个子目录中             |    `$ES_HOME/plugins`    |                                                              |
|   repo   | Shared file system repository locations. Can hold multiple locations. A file system repository can be placed in to any subdirectory of any directory specified here. |      NOT_configured      |                          path.repo                           |

##### Next steps

&emsp;&emsp;现在你建好了一个Elasticsearch测试环境。在你开始认真做开发护着进入Elasticsearch的生产环境，你必须做一些额外的设置：

- 学习如何[配置Elasticsearch](###Configuring-Elasticsearch)
- 配置[重要的Elasticsearch设置](####Important-Elasticsearch-configuration)
- 配置[重要的系统设置](###Important-system-configuration)

#### Install Elasticsearch with Docker
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/docker.html)

### Configuring Elasticsearch
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/settings.html)

&emsp;&emsp;Elasticsearch附带（ship with）了很好的默认配置并且只需要较小的配置。在一个运行中的集群中，大部分的设置可以通过[Cluster update settings](####Cluster-update-settings-API) API进行更改。

&emsp;&emsp;配置文件应该包含对指定节点（node-specific）的设置，例如`node.name`以及路径，或者是能让节点加入集群的配置，例如`cluster.name`以及`network.host`。

##### Config files location

&emsp;&emsp;Elasticsearch有三个配置文件：

- `elasticsearch.yml` 用于配置 Elasticsearch
- `jvm.options` 用于配置Elasticsearch JVM 设置
- `log4j2.properties` 用于配置 Elasticsearch日志记录

&emsp;&emsp;这三个文件都在config目录中，他们的默认路径取决于使用哪种方式安装：归档发行版（archive distribution）`tar.gz`或者`zip`还是安装包发行版（package distribution）Debian 或者RPM安装包。

&emsp;&emsp;对于归档发行版，config目录位置默认是`$ES_HOME/config`。config目录的位置能通过`ES_PATH_CONFIG`这个环境变量进行更改：

```text
ES_PATH_CONF=/path/to/my/config ./bin/elasticsearch
```

&emsp;&emsp;或者，通过在命令行或profile文件中`export` `ES_PATH_CONFIG`这个环境变量。

&emsp;&emsp;对于安装包发行版，config目录的位置默认在`/etc/elasticsearch`，config目录的位置可以通过`ES_PATH_CONF`这个环境变量变更。注意的是在shell中设置这些是不够的。Instead, this variable is sourced from `/etc/default/elasticsearch` (for the Debian package) and `/etc/sysconfig/elasticsearch` (for the RPM package). You will need to edit the`ES_PATH_CONF=/etc/elasticsearch` entry in one of these files accordingly to change the config directory location.

##### Config file format

&emsp;&emsp;配置格式是[YAML](https://yaml.org)，下面是更改数据和日志目录的例子

```text
path:
    data: /var/lib/elasticsearch
    logs: /var/log/elasticsearch
```

&emsp;&emsp;配置也可以平整化（flattened）

```text
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
```

&emsp;&emsp;在YAML中，你可以格式化non-scalar的值写成序列：

```text
discovery.seed_hosts:
   - 192.168.1.10:9300
   - 192.168.1.11
   - seeds.mydomain.com
```

&emsp;&emsp;尽管下面的方式不是很常见（Thought less common），你也可以把non-scala的值写成数组：

```text
discovery.seed_hosts: ["192.168.1.10:9300", "192.168.1.11", "seeds.mydomain.com"]
```

##### Environment variable substitution

&emsp;&emsp;在配置文件中可以通过使用`${...}`这类符号的方式引用环境变量：

```text
node.name:    ${HOSTNAME}
network.host: ${ES_NETWORK_HOST}
```

&emsp;&emsp;环境变量的值必须是简单的String值，用逗号分隔的String值会被Elasticsearch解析成一个list。例如，Elasticsearch将下面的环境变量的String值根据逗号切分到list中。

```shell
export HOSTNAME="host1,host2"
```

##### Cluster and node setting types

&emsp;&emsp;集群跟节点的设置基于他们是如何被配置可以分类为：

###### Dynamic（settings）

&emsp;&emsp;在一个正在运行的集群上，你可以使用[cluster update settings API](####Cluster-update-settings-API)来配置或更新动态设置。你也可以使用`elasticsearch.yml`对本地未启动或者关闭的节点配置动态设置。

&emsp;&emsp;使用[cluster update settings API](####Cluster-update-settings-API)更新可以是永久的（persistent），即使集群重启也生效，也可以是临时的（transient），集群在重启后就重置了。你也可以通过使用API把配置设置为null也能重置使用persistent或者transient更新过的设置。

&emsp;&emsp;如果你用多种方式配置了一样的设置，那么Elasticsearch会根据下面的优先顺序（order of precedence）来应用设置（apply settings）。

1. Transient Setting
2. Persistent Setting
2. `elasticsearch.yml`  setting
2. Default setting value

&emsp;&emsp;例如，你可以使用一个transient setting覆盖persistent setting或者`elasticsearch.yml`。然而，在`elasticsearch.yml`上的变更不会覆盖定义好的（defined）transient 或者 persistent setting。

>TIP：如果你使用Elasticsearch Service，使用[user settings](https://www.elastic.co/guide/en/cloud/current/ec-add-user-settings.html)功能来配置所有的设置。这个方法能让Elasticsearch自动的拒绝（reject）掉任何会破坏你集群的设置。
如果你在自己的设备（hardware）上运行Elasticsearch，可以使用cluster update settings API来配置集群动态设置。对于集群或者节点的静态设置只使用elasticsearch.yml来配置。使用API不会要求重启并且保证所有节点都被配置成相同的值。

>WARNING: We no longer recommend using transient cluster settings. Use persistent cluster settings instead. If a cluster becomes unstable, transient settings can clear unexpectedly, resulting in a potentially undesired cluster configuration. See the [Transient settings migration guide](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/transient-settings-migration-guide.html).

###### Static（settings） 

&emsp;&emsp;静态设置只能在未启动或者关闭的节点上使用`elasticsearch.yml`来配置。

&emsp;&emsp;静态配置必须在集群中相关的所有节点上一一配置。


#### Important Elasticsearch configuration
[link](https://www.elastic.co/guide/en/elasticsearch/reference/7,15/important-settings.html)

&emsp;&emsp;Elasticsearch只需要很少的配置就能启动。但是在生产中使用你的集群时必须要考虑这些条目：

- [Path settings](#####Path-settings)
- [Cluster name setting](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/important-settings.html#cluster-name)
- [Node name setting](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/important-settings.html#node-name)
- [Network host settings](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/important-settings.html#network.host)
- [Discovery settings](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/important-settings.html#discovery-settings)
- [Heap size settings](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/important-settings.html#heap-size-settings)
- [JVM heap dump path setting](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/important-settings.html#heap-dump-path)
- [GC logging settings](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/important-settings.html#gc-logging)
- [Temporary directory settings](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/important-settings.html#es-tmpdir)
- [JVM fatal error log setting](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/important-settings.html#error-file-path)
- [Cluster backups](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/important-settings.html#important-settings-backups)

&emsp;&emsp;我们的[Elastic Cloud service](https://www.elastic.co/cloud/elasticsearch-service/signup?baymax=docs-body&elektra=docs)能自动配置这些条目，使得你的集群处在生产就绪状态。

##### Path settings

&emsp;&emsp;Elasticsearch把你的索引数据和数据流都写到`data`目录中。

&emsp;&emsp;Elasticsearch本身也会生产应用日志，包含集群健康信息以及集群操作信息，并且写入到`logs`目录中。

&emsp;&emsp;对于在MacOS和Linux下使用`.tar.gz`, 以及Windows下使用`zip`方式安装时，`data`和`logs`目录默认是`$ES_HOME`下的子目录，然而在升级Elasticsearch时，位于`$ES_HOME`下的文件是存在风险的。

&emsp;&emsp;在生产中，我们强烈建议在`elasticsearch.yml`中设置`path.data`和`path.logs`将`data`和`logs`目录放到`$ES_HOME`以外的位置。其他安装方式（rpm、macOS Homebrew、Windows `.msi`）默认会把`data`和`logs`目录放到`$ES_HOME`以外的位置。

&emsp;&emsp;支持在不同的平台，`path.data`和`path.logs`可以是不同的值：

- Unix-like systems
  - Linux and macOS installations support Unix-style paths:
```text
path:
  data: /var/data/elasticsearch
  logs: /var/log/elasticsearch
```

- Windows
  - Windows installations support DOS paths with escaped backslashes:

```text
path:
  data: "C:\\Elastic\\Elasticsearch\\data"
  logs: "C:\\Elastic\\Elasticsearch\\logs"
```

>WARNING：不要更改data目录中的任何内容（content），也不要允许可能会影响data目录内容的程序。如果除了Elasticsearch以外更改了data目录的内容，那么Elasticsearch可能会失败，报告损坏或者其他数据不一致性（data inconsistencies）的问题，也可能工作正常但是丢失了一些你的数据。

##### Multiple data paths

>WARNING: 在7.13.0版本中废弃了

&emsp;&emsp;如果有这个需要，你可以在`path.data`中指定多个路径。Elasticsearch会在这些路径上存储节点的数据，但是会在相同的路径上存储同一个shard的数据。

&emsp;&emsp;Elasticsearch不会在多个路径上去平衡shards的写入。在某个路径上的高磁盘使用率会触发整个节点的[high disk usage watermark](####Cluster-level-shard-allocation-and-routing-settings])。触发后，Elasticsearch不会在这个节点增加shard，尽管这个节点的在其他路径上有可用的空间。如果你需要额外的磁盘空间，我们建议你增加新的节点而不是增加data的路径。

- Unix-like systems
  - Linux and macOS installations support Unix-style paths:
```text
path:
  data:
    - /mnt/elasticsearch_1
    - /mnt/elasticsearch_2
    - /mnt/elasticsearch_3
```

- Windows
  - Windows installations support DOS paths with escaped backslashes:

```text
path:
  data:
    - "C:\\Elastic\\Elasticsearch_1"
    - "E:\\Elastic\\Elasticsearch_1"
    - "F:\\Elastic\\Elasticsearch_3"
```

##### Migrate from multiple data paths

&emsp;&emsp;（未完成）

##### Cluster name setting

&emsp;&emsp;在一个集群中，一个节点只有跟集群中的其他节点共享他的`cluster.name`才能加入到这个集群中。默认的名字是`elasticsearch`，但是你应该把这个名字改成一个合适的名字，这个名字能描述这个集群的目的。

```text
cluster.name: logging-prod
```

>IMPORTANT：在不同的环境中不要重复使用相同的集群名，否则节点可能会加入到错误的集群中。

##### Node name setting

&emsp;&emsp;Elasticsearch使用`node.name`作为可读（human-readable）的一个特定的Elasticsearch实例。在很多APIs的返回中（response）会用到。当Elasticsearch启动后，节点名字默认是服务器的hostname，可以在`elasticsearch.yml`中显示配置：

```text
node.name: prod-data-2
```

##### Network host setting

&emsp;&emsp;默认清下，Elasticsearch只绑定类似127.0.0.1以及\[::1\]的回调地址（lookback address）。这样的话能够在单个服务上允许一个集群的多个节点用于开发以及测试。但是一个[resilient production cluster](###Designing-for-resilience)必须是包含在其他服务上的节点。尽管还有许多[newwork settings](####Networking)，但是通常你只需要配置`network.host`：

```text
network.host: 192.168.1.10
```

> 当你提供了network.host的值，Elasticsearch会假设你正在从开发模式转到生产模式，并且会在系统启动时升级warning到exception的检查。见[development and production modes](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/system-config.html#dev-vs-prod)的差异。

##### Discovery and cluster formation settings(setting)

&emsp;&emsp;在进入生产前配置两个重要的discovery和cluster formation设置，使得这个集群中的节点能互相发现并且选出一个master节点。

###### discovery.seed_hosts

&emsp;&emsp;如果没有配置任何网络设置，Elasticsearch会绑定可见的回调地址并且扫描本地的9300到9305端口去连接同一个服务上的其他节点。这种行为提供了一种自动集群体验，而无需进行任何配置。即开箱即用（out of box）。

&emsp;&emsp;当你想要组成（form）一个集群，它包含的节点在其他服务器上时，可以使用[静态](#####Static)的`discovery.seed_hosts`设置。这个设置可以提供一个集群中其他节点的list，这些节点是候选的主节点（mater-eligible），并且有可能是live的，并且能在[discovery process](####Discovery)中作为可以联系的节点来选出种子选手（and contactable to seed the discovery process。翻译能力有限，只可意会，不可言传）。这个设置可以将集群中所有候选的主节点的地址用YAML中的序列或者数组的风格书写。每一个地址可以是IP地址或者hostname，hostname可以通过DNS关联一个或多个IP地址。

```text
discovery.seed_hosts:
   - 192.168.1.10:9300
   - 192.168.1.11 
   - seeds.mydomain.com 
   - [0:0:0:0:0:ffff:c0a8:10c]:9301 
```

1. 如果IP地址中没有指定端口号，那么就是默认的9300，默认的端口号也可以被[override](####Discovery)
1. 如果hostname关联多个IP 地址，那么当前节点会试图发现hostname关联的所有地址上的节点
1. IPv6地址使用方括号包裹（enclosed in square brackets）

&emsp;&emsp;如果候选的主节点的节点没有固定的名字或者地址，可以使用[alternative hosts provider](####Discovery) 来动态的找到他们的地址。

###### cluster.initial_master_nodes

&emsp;&emsp;当你第一次启动Elasticsearch集群，在[cluster bootstrapping](####Bootstrapping-a-cluster)阶段确定在第一次参与投票的候选主节点。在[development mode](##Bootstrap-Checks)中，with no discovery settings configured, this step is performed automatically by the nodes themselves。

&emsp;&emsp;因为auto-bootstrapping是内在不安全的，在生产模式中启动一个新的集群时，你必须显式列出候选的主节点并且他们的投票要被统计在第一轮的选票中。你可以使用`cluster.initial_master_nodes`设置来列出这些候选的主节点。

> IMPORTANT：当一个新的集群第一次形成之后，从每个节点的配置中移除cluster.initial_master_nodes设置。当重启一个集群或者往已存在的集群中添加一个新的节点时，不要使用该配置。

```text
discovery.seed_hosts:
   - 192.168.1.10:9300
   - 192.168.1.11
   - seeds.mydomain.com
   - [0:0:0:0:0:ffff:c0a8:10c]:9301
cluster.initial_master_nodes: 
   - master-node-a
   - master-node-b
   - master-node-c
```

1. 通过`node.name`来确定最初的master节点身份，默认是hostname。要确保`cluster.initial_master_nodes`跟`node.name`的值是一致的。如果节点的名字使用了全限定域名（fully-qualified domain name），比如master-node-a.example.com，那么你必须在cluster.initial_master_nodes中使用FQDN。相反的，如果只是仅仅使用了hostname而没有尾随的限定符，那么在cluster.initial_master_nodes也不能带尾随的限定符

&emsp;&emsp;见 [bootstrapping a cluster](####Bootstrapping-a-cluster)和[discovery and cluster formation settings](###Discovery-and-cluster-formation)。

##### Heap size settings

&emsp;&emsp;默认情况下，Elasticsearch会根据节点的[roles](####Node)跟内存大小来自动的设置JVM堆的大小。我们建议使用默认的大小，它适用于大部分生产环境。

>NOTE：堆大小的自动配置需要[bundle JDK](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/setup.html#jvm-version)，如果使用自定义的JRE位置，需要Java 14以及更新的JRE。

&emsp;&emsp;如果有必要，你可以通过[setting the JVM heap size](###Advanced-configuration)手动覆盖默认值。

##### JVM heap dump path setting

&emsp;&emsp;默认情况下，Elasticsearch会配置JVM，使得OOM异常转存（dump）到`data`目录。在基于ROM跟Debian的安装包中，`data`目录位于/var/lib/elasticsearch。在Linux、MacOs以及Windows发行版中，`data`目录位于Elasticsearch安装目录的根目录。

&emsp;&emsp;如果默认的路径用于接受heap dump不是很合适，那么可以通过在文件`jvm.options`中修改`-XX:HeapDumpPath=...`。

- 如果你指定了一个目录，JVM将会基于正在运行的实例，用它的PID来为heap dump文件命名
- 如果你指定了一个固定的文件名而不是一个目录，那么这个文件必须不存在，否则heap dump会失败

##### GC logging settings

&emsp;&emsp;默认情况下，Elasticsearch开启了垃圾回收日志。在`jvm.options`文件中配置，并且输出到Elasticsearch的`logs`文件中。默认的配置中，每64M就会轮换一次日志，最多消耗2G的磁盘空间。

&emsp;&emsp;你可以命令行来配置JVM的日志打印，见[JEP 158: Unified JVM Logging](https://openjdk.java.net/jeps/158)。除非你直接更改`jvm.options`文件，Elasticsearch的默认配置会被应用（apply）除了你自己的设置。如果要关闭默认配置，第一步先通过提供参数`-Xlog:disable`关闭日志打印，然后提供你自己的命令选项。以上操作会关闭所有的JVM日志打印，所以要确定好可用的选项来满足你的需求。

&emsp;&emsp;见 [Enable Logging with the JVM Unified Logging Framework](https://docs.oracle.com/en/java/javase/13/docs/specs/man/java.html#enable-logging-with-the-jvm-unified-logging-framework)了解更多在原始的JEP（origin JEP）中不包含的虚拟机选项。

##### Examples

&emsp;&emsp;下面的例子中创建了`$ES_HOME/config/jvm.options.d/gc.options`并包含一些虚拟机选项：把默认的GC 日志输出到`/opt/my-app/gc.log`。

```text
# Turn off all previous logging configuratons
-Xlog:disable

# Default settings from JEP 158, but with `utctime` instead of `uptime` to match the next line
-Xlog:all=warning:stderr:utctime,level,tags

# Enable GC logging to a custom location with a variety of options
-Xlog:gc*,gc+age=trace,safepoint:file=/opt/my-app/gc.log:utctime,pid,tags:filecount=32,filesize=64m
```

&emsp;&emsp;下面的例子中配置一个[Elasticsearch Docker container](#### Install Elasticsearch with Docker)，将GC的debug日志输出到`stderr`，让容器编排器处理输出。

```text
MY_OPTS="-Xlog:disable -Xlog:all=warning:stderr:utctime,level,tags -Xlog:gc=debug:stderr:utctime"
docker run -e ES_JAVA_OPTS="$MY_OPTS"
```

##### Temporary directory settings

&emsp;&emsp;默认情况下，Elasticsearch会使用一个私有的临时目录，它由一个启动脚本创建，并位于系统的临时目录中。

&emsp;&emsp;在一些Linux发行版中，一些系统工具会清楚 `/tmp` 目录下长时间未被访问的文件。这会导致Elasticsearch的私有临时目录可能会被删除。移除私有的临时目录会导致一些问题，比如一些功能随后会访问这些私有临时目录。

&emsp;&emsp;如果你使用`.deb`或者`.rpm`安装的Elasticsearch，并且在`systemd`下运行，那么Elasticsearch使用的私有临时目录不会被周期性的清除（periodic cleanup）。

&emsp;&emsp;如果你想要在Linux跟MacOS上运行`.tar.gz`发行版，可以考虑为Elasticsearch创建一个专用的临时目录，并且该目录所在路径中不包含旧的文件或目录，并且这个目录只有Elasticsearch有访问权限。最后在Elasticsearch启动前设置环境变量`$ES_TMPDIR`。

##### JVM fatal error log setting

&emsp;&emsp;默认情况下，Elasticsearch会配置JVM将fatal错误日志写到默认的日志目录中。在`RPM`和`Debian`安装中，目录位置为`/var/log/elasticsearch`，在`Linux、MacOs、Windows`发行版中，`logs`目录位于Elasticsearch的安装目录。

&emsp;&emsp;当遇到一些fatal错误日志，它由JVM产生，比如说段错误（segmentation fault）。如果接受这些日志的路径不是很合适，可以在`jvm.options`中修改`-XX:ErrorFile=...`。

##### Cluster backups

&emsp;&emsp;在发生灾难时，[snapshot](##Snapshot-and-restore)能防止数据永久丢失。Snapshot lifecycle management是最简单的办法来对集群实现定期备份。见[Create a snapshot](###Create a snapshot)。

>WARNING：快照是唯一可靠并且被支持的方式（supported way）来备份集群。
你不能通过拷贝data目录中的节点数据进行备份。没有一种支持方式（supported way）从文件系统层面的备份来恢复数据。如果你想尝试从这种备份中恢复集群，可能会失败并且Report出corruption或者文件丢失或者其他数据一致性的问题，或者出现成功的悄无声息的丢失一些你的数据

#### Secure settings
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/secure-settings.html)

&emsp;&emsp;有些设置是敏感的（sensitive），仅仅依靠文件系统的权限来保护这些值是不够。对于这种情况，Elasticsearch提供了keystore和 [elasticsearch-keystore tool](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/elasticsearch-keystore.html) 来管理这些设置。

>IMPORTANT：只有一些设置被设计为从keystore中读取。但是keystore没有验证一些不支持的设置。增加不支持的设置会导致Elasticsearch启动失败。

&emsp;&emsp;所有在keystore中的变更只有在Elasticsearch重启后才生效。

&emsp;&emsp;keystore中设置跟配置文件`elasticsearch.yml`中的普通的设置项一样都需要在每个节点上指定。当前，所有的安全设置都是特定于节点的，即所有的节点上必须设置相同的值。

##### Reloadable secure settings

&emsp;&emsp;跟`elasticsearch.yml`中的设置值一样，keystore中的内容无法自动的应用（apply）到正在运行的Elasticsearch节点，需要重启。然而有一些安全设置被标记为reloadable，这些设置可以被[reload](####Nodes-reload-secure-settings-API)。

&emsp;&emsp;对于安全设置的值，不管是否可以reloadable，集群中的所有节点都必须一致。在安全设置变更后，执行`bin/elasticsearch-keystore`调用下面的请求：

```text
POST _nodes/reload_secure_settings
{
  "secure_settings_password": "keystore-password" 
}
```

1. Elasticsearch keystore会对密码进行加密。

&emsp;&emsp;上述API会解密并且重新读取集群中所有节点的整个keystore，但是只有reloadable的安全设置可以被应用（apply）。其他的安全设置不会生效直到下一次重启。一旦API调用返回了，意味着reload完成了，即所以依赖这些设置的内部数据结构都已经发生了变更。Everything should look as if the settings had the new value from the start。

&emsp;&emsp;当更改了多个reloadable安全设置后，在集群中的每一个节点上修改后，随后发起[reload_secure_settings](####Nodes-reload-secure-settings-API)的调用，而不用在每一次变更后reload。

&emsp;&emsp;下面是reloadable安全设置

- [The Azure repository plugin](https://www.elastic.co/guide/en/elasticsearch/plugins/8.2/repository-azure-client-settings.html)
- [The EC2 discovery plugin](https://www.elastic.co/guide/en/elasticsearch/plugins/8.2/discovery-ec2-usage.html#_configuring_ec2_discovery)
- [The GCS repository plugin](https://www.elastic.co/guide/en/elasticsearch/plugins/8.2/repository-gcs-client.html)
- [The S3 repository plugin](https://www.elastic.co/guide/en/elasticsearch/plugins/8.2/repository-s3-client.html)
- [Monitoring settings](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/monitoring-settings.html)
- [Watcher settings](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/notification-settings.html)

#### Auditing security settings
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/auditing-settings.html#auditing-settings)

&emsp;&emsp;你可以使用[audit logging](###Enable-audit-logging)来记录安全相关的事件，比如认证失败，拒绝连接，以及数据访问事件。另外也会记录通过API访问安全配置的操作，例如创建、更新、移除[native](####Native-user-authenticationedit)、[built-in](####Built-in-users) users，[roles](####Create-or-update-roles-API])、[role mappings](####Create-or-update-role-mappings-API) 以及 [API keys](####Create-API-key-API)。

>TIP：日志审计只在某些订阅中提供，详细信息见 https://www.elastic.co/subscriptions

&emsp;&emsp;在配置后，集群上的所有节点都需要设置一遍。静态设置，比如说`xpack.security.audit.enabled`，在每个节点的`elasticsearch.yml`中都必须配置。对于动态的日志设置，使用[cluster update settings API](####Cluster-update-settings-API) 可以让所有节点的设置一致。

##### General Auditing Settings

###### xpack.security.audit.enabled

&emsp;&emsp;（[Static](######Static（settings） )）设置为true来开启。默认值为false。在每个节点上日志事件会被放到一个名为`<clustername>_audit.json`的专用的文件中。

&emsp;&emsp;如果开启了，集群中的所有节点的`elasticsearch.yml`都需要配置该设置。

##### Audited Event Settings

&emsp;&emsp;日志事件以及其他一些信息比如获取什么样的日志可以通过下面的设置来控制：

###### xpack.security.audit.logfile.events.include

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）在日志输出中指定事件类型（[kind of events](####Audit-events)），设置为`_all`会彻底的记录所有的日志事件，但通常不建议这么做因为会非常的verbose。默认值是一个list包含：`access_denied`, `access_granted`, `anonymous_access_denied`, `authentication_failed`, `connection_denied`, `tampered_request`, `run_as_denied`, `run_as_granted`, `security_config_change`。

###### xpack.security.audit.logfile.events.exclude

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）从包含的（[kind of events](####Audit-events)）列表中指定排除部分选项。当xpack.security.audit.logfile.events.include的值设置为`_all`时，然后用`xpack.security.audit.logfile.events.exclude`来进行指定选项的排除是不错的方式。默认值是空的list。

###### xpack.security.audit.logfile.events.emit_request_body

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）用于指定是否将REST请求的请求body作为日志事件的属性，这个设置可以用于[audit search queries](####Auditing-search-queries)。

&emsp;&emsp;默认是false，那么请求body不会被打印出来。

>IMPORTANT：注意的是，当日志事件中包含请求body时，一些敏感数据可能以明文形式被记录，即使是一些安全相关API，比如修改用户密码，认证信息在开启后有被泄露（filter out）的风险

##### Local Node Info Settings

###### xpack.security.audit.logfile.emit_node_name

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）指定在每一个日志事件中，节点名字[node name](####Important-Elasticsearch-configuration)是否作为其中的一个域field。默认值是false。

###### xpack.security.audit.logfile.emit_node_host_address

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）指定在每一个日志事件中，节点的地址是否作为其中的一个域field。默认值是false。

###### xpack.security.audit.logfile.emit_node_host_name

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）指定在每一个日志事件中，节点的主机名是否作为其中的一个域field。默认值是false。

###### xpack.security.audit.logfile.emit_node_id

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）指定在每一个日志事件中，节点的id是否作为其中的一个域field。不同于[node name](####Important-Elasticsearch-configuration)，管理员可以在配置文件中修改节点id，节点id在集群启动后将不可变更，默认值是true。

##### Audit Logfile Event Ignore Policies

&emsp;&emsp;下面的设置会影响[ignore policies](####Logfile-audit-events-ignore-policies)，它们能更精细的（fine-grained）控制日志事件打印到日志文件中。拥有相同police_name的设置将会组合到一个策略（police）中。如何一个事件匹配到任意一条策略的所有条件，该日志事件将被忽略并不会被打印。大多数的日志事件都遵照（subject to）忽略策略。唯一的例外（sole exception）是`security_config_change`类型的事件，无法被过滤掉（filter out），除非完全的通过`xpack.security.audit.logfile.events.exclude`进行exclude。

###### xpack.security.audit.logfile.events.ignore_filters.\<policy_name>.users

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）用户名字列表或者名字通配值（wildcards）。匹配该值的所有用户的日志事件不会打印出来。

###### xpack.security.audit.logfile.events.ignore_filters.\<policy_name>.realms

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）authentication realm的列表或者通配值，匹配该值的所有realm的日志事件不会打印出来。

###### xpack.security.audit.logfile.events.ignore_filters.\<policy_name>.actions

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）action名字的列表或者通配值，action的名字可以在日志事件的`action`域中找到， 匹配该值的所有action的日志事件不会打印出来。

###### xpack.security.audit.logfile.events.ignore_filters.\<policy_name>.roles

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）角色列表或者角色通配值（wildcards）。匹配该值并且拥有该角色的所有用户的日志事件不会打印出来。如果用户拥有多个角色，而一些角色没有被策略覆盖到，那么该策略不会覆盖其日志事件。

###### xpack.security.audit.logfile.events.ignore_filters.\<policy_name>.indices

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）索引名字列表或者通配值，日志事件中所有的索引名都匹配后才不会打印。如果事件中涉及（concern）了多条索引，并且一些索引没有被该策略覆盖到，那么该策略不会覆盖日志事件。

#### Circuit breaker settings
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/circuit-breaker.html)

&emsp;&emsp;Elasticsearch包含多种熔断器来防止导致OOM的操作。每一种熔断器指定了内存的使用限制。另外，父级别的熔断器指定了内存的总量，限制子级别的熔断器的内存使用总量。

&emsp;&emsp;除了特别的说明，这些设置能通过[cluster-update-settings](####Cluster-update-settings-API) API在运行中的集群上进行动态的更新。

&emsp;&emsp;关于熔断器提供的报错信息，见[Circuit breaker errors](###Fix-common-cluster-issues)。

##### Parent circuit breaker

&emsp;&emsp;父级别的熔断器可以通过下面的设置进行配置：

###### indices.breaker.total.use_real_memory

&emsp;&emsp;（[Static](######Static（settings） )）如果该值为true，那么父级别的熔断器会考虑真实的内存使用量，否则考虑的是子级别的熔断器预定（reserved）的内存量总和。默认值为true。

###### indices.breaker.total.limit

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）当`indices.breaker.total.use_real_memory`为false，并且虚拟机的内存使用量达到70%，或者当`indices.breaker.total.use_real_memory`为true时，并且虚拟机的内存使用量达到95%，所有的父熔断器开始限制内存使用。

##### Field data circuit breaker

&emsp;&emsp;Filed data熔断器会估算把域filed载入到[field data cache](####Field-data-cache-settings)所使用的堆内存量。如果载入后会导致缓存超过了先前定义的内存限制值，熔断器将会停止该操作并返回错误。

###### indices.breaker.fielddata.limit

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）fielddata breaker的限制值，默认是40%的JVM堆内存量。

###### indices.breaker.fielddata.overhead

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）该配置是一个常量，所有field data的估算值乘以这个常量来计算出最终的估算值。默认是值1.03。

##### Request circuit breakeredit

&emsp;&emsp;Request熔断器允许Elasticsearch防止每一个请求的数据结构（例如，在一次请求中，聚合计算占用的内存量）超过一定量的内存量。

###### indices.breaker.request.limit

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）Request熔断器的限制值，默认是60%的JVM堆内存量。

###### indices.breaker.request.overhead

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）该配置是一个常量，所有Request的估算值乘以这个常量来计算出最终的估算值。默认是值1。

##### In flight requests circuit breaker

&emsp;&emsp;in flight Request熔断器允许Elasticsearch限制所有在传输或者HTTP层的请求的内存使用量不超过在一个节点上的相对内存总量。内存使用量基于请求本身的content length。This circuit breaker also considers that memory is not only needed for representing the raw request but also as a structured object which is reflected by default overhead。

###### network.breaker.inflight_requests.limit

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）in flight Request熔断器的限制值，默认是60%的JVM堆内存量。

###### network.breaker.inflight_requests.overhead

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）该配置是一个常量，所有in flight Request的估算值乘以这个常量来计算出最终的估算值。默认是值2。

##### Accounting requests circuit breaker

&emsp;&emsp;accounting熔断器允许Elasticsearch限制某些请求已经结束，但是相关内存还未被释放的内存量。比如说Lucene的段占用的内存。

###### indices.breaker.accounting.limit

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）accounting熔断器的限制值，默认是100%的JVM堆内存量。这意味着受到父级熔断器的配置限制。

###### indices.breaker.accounting.overhead

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）该配置是一个常量，所有accounting Request的估算值乘以这个常量来计算出最终的估算值。默认是值1。

##### Script compilation circuit breaker

&emsp;&emsp;和上文中的熔断器稍微不同的是，Script compilation熔断器限制了在一个时间周期内inline script compilation的数量。

&emsp;&emsp;见[scripting](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-scripting-using.html) 文档中`prefer-parameters`章节的内容来了解更多。

###### script.max_compilations_rate

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）Limit for the number of unique dynamic scripts within a certain interval that are allowed to be compiled. Defaults to 150/5m, meaning 150 every 5 minutes。

##### Regex circuit breakeredit

&emsp;&emsp;写的不好的正则表达式会降低（degrade）集群稳定性以及性能。regex熔断器限制了[Painless scripts](https://www.elastic.co/guide/en/elasticsearch/painless/8.2/painless-regexes.html)中正则表达式的使用以及复杂性。

###### script.painless.regex.enabled

&emsp;&emsp;（[Static](######Static（settings） ))）允许painless脚本中使用正则表达式，该值可以是：

- limited（默认值）
  - 允许使用正则表达式但是使用集群设置中的[script.painless.regex.limit-factor](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/circuit-breaker.html#script-painless-regex-limit-factor)限制正则表达式的复杂性
- true
  - 允许使用正则表达式并且不限制正则表达式的复杂性。regex熔断器关闭
  
- false
  - 不允许使用正则表达式。包含任何正则表达式的painless脚本都会返回错误
  
###### script.painless.regex.limit-factor

&emsp;&emsp;（[Static](######Static（settings） ))）用来限制painless脚本中正则表达式的字符数量。Elasticsearch通过当前设置的值与脚本输入的值的长度的乘积值作为限制值。

&emsp;&emsp;比如说输入值`foobarbaz`的长度为9，如果`script.painless.regex.limit-factor`的值为6，那么基于`foobarbaz`的正则表达式的长度为54（6 \* 9）。如果超过这个限制值，将触发regex熔断器并返回错误。

&emsp;&emsp;只有`script.painless.regex.enabled`为`limited`时，Elasticsearch才会使用该限制值。

#### Cluster-level shard allocation and routing settings
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-cluster.html)

&emsp;&emsp;Shard allocation说的是分配分片到节点的过程。该过程会在initial recovery、副本（replica）分配、rebalancing或者增加或者移除节点时发生。

&emsp;&emsp;master node的其中一个作用是决定如何分配分片到节点，何时在节点之间移动分片来平衡集群。

&emsp;&emsp;以下的一些设置用来控制分配分片的过程：

- [Cluster-level shard allocation settings](#####Cluster-level-shard-allocation-settings)用来控制分片和平衡操作
- [Disk-based shard allocation settings](#####Disk-based-shard-allocation-settings)描述了Elasticsearch如何考虑（take into account）磁盘空间等等相关信息
- [Shard allocation awareness](#####Shard-allocation-awareness) 和[Forced awareness](#####Forced-awareness)用来控制在不同的rack和可见区域上发布分片
- [Cluster-level shard allocation filteringed](#####Cluster-level shard allocation filtering)允许一些节点或者组内的节点不会被分配分片，使得这些节点能够被关闭（decommissioned）

&emsp;&emsp;除此之外，还有一些其他的配置，见[Miscellaneous cluster settings](#####Miscellaneous-cluster-settings)。

##### Cluster-level shard allocation settings

&emsp;&emsp;你可以使用下面的设置来控制分片的分配以及恢复：

###### cluster.routing.allocation.enable

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）开启或者关闭某些分片类型的分配：

- `all`：（default）所有类型的分配都可以被分配
- `primaries`：只有主分片才能被分配
- `new_primaries`：只有主分片中新的索引才能被分配
- `none`：任何分片中的任何索引都不能被分配

&emsp;&emsp;在节点重启后，上述的配置不会影响本地主分片的恢复。重启后的节点上的未分配的主分片的allocation id如果匹配了集群状态中的active allocation ids，那么会立即恢复。

###### cluster.routing.allocation.node_concurrent_incoming_recoveries

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）在一个节点上允许同时进行恢复incoming recoveries的并发数量。incoming recoveries中的分片（大多数是副本分片或者就是分片正在重新分配位置relocating）将被分配到当前节点上。默认值是2。

###### cluster.routing.allocation.node_concurrent_outgoing_recoveries

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）在一个节点上允许同时进行恢复outgoing recoveries的并发数量。outgoing recoveries中的分片（大多数是当前节点上的主分片或者就是分片正在重新分配位置relocating）将被分配到当前节点上。默认值是2。

###### cluster.routing.allocation.node_concurrent_recoveries

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）一种快捷方法shortcut来设置`cluster.routing.allocation.node_concurrent_incoming_recoveries`和`cluster.routing.allocation.node_concurrent_outgoing_recoveries`。

###### cluster.routing.allocation.node_initial_primaries_recoveries

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）在一个节点重启后，副本分片replica的恢复是通过网络实现的，然而一个未分配的主分片unassigned primary则是通过读取本地磁盘数据恢复的。在同一个节点上通过这种本地恢复的方式是很快的，默认值是4。

###### cluster.routing.allocation.same_shard.host

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）该值允许执行一个检查，防止在单个主机上上分配多个相同的分片，通过主机名以及主机地址来描述一台主机。默认值是false。意味着默认不会进行检查。只有在同一台机器上的多个节点启动后，该值才会作用（apply）。

##### Shard rebalancing settings

&emsp;&emsp;当每一个节点上的分片数量是相等并且在任何节点的任何索引上没有集中分片（concentration of shards），那么集群是平衡的。Elasticsearch会运行一个自动程序（automatic process）称为rebalancing，它会在集群中的节点之间移动分片来提高平衡性。rebalancing遵守类似 [allocation filtering](#####Cluster-level-shard-allocation-filtering) 和 [forced awareness](#####Forced-awareness)的分片分配规则。这些规则会阻止完全的平衡集群，因此rebalancing会努力（strive to）在你配置的规则下尽可能的去平衡集群。如果你正在使用 [data tiers](###Data-tiers)，那么Elasticsearch会自动的应用分配过滤规则将每一个分配放到合适的数据层，这些规则意味着平衡器在每一层中独立工作。

&emsp;&emsp;你可以使用下面的设置在集群中控制分片的平衡：

###### cluster.routing.rebalance.enable

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）为指定的分片类型开启或关闭rebalancing：

- `all`：（default）所有类型的分片都允许rebalancing
- `primaries`：只有主分片才允许rebalancing
- `replicas`：只有副本分片才允许rebalancing
- `none`：任何类型分片的任何索引都不允许rebalancing

###### cluster.routing.allocation.allow_rebalance

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）用于指定什么时候允许分片rebalancing

- `always`：总是允许rebalancing
- `indices_primaries_active`：只有当集群中的所有主分片都被分配后
- `indices_all_active`：（default）只有当集群中所有的分片（primaries and replicas）都被分配后

###### cluster.routing.allocation.cluster_concurrent_rebalance

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）用于控制集群范围（cluster wide）内分片平衡并发数，默认值是2。注意的是这个设置仅仅控制因为集群不平衡导致的分片重定位（shard relocating）的并发数。这个设置不会限制因 [allocation filtering](#####Cluster-level-shard-allocation-filtering) 和 [forced awareness](#####Forced-awareness)导致的分片重定位。

##### Shard balancing heuristics settings

&emsp;&emsp;基于每个节点上分片的分配情况计算出一个weight来实现rebalancing，并且通过在节点之间移动分片来降低heavier节点的weight并且提高lighter节点的weight。当不存在可能使任何节点的权值与其他节点的权值更接近超过可配置阈值的分片移动时，集群是平衡的。下面的设置允许你控制计算的细节

###### cluster.routing.allocation.balance.shard

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）为节点上分配的分片总数定义一个weight因子。默认值是0.45f。提高这个值会使集群中所有节点的分片数量趋于均衡。

###### cluster.routing.allocation.balance.index

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）为每一个索引的分片数量定义一个weight因子。默认值是0.55f。提高这个值会使集群中所有节点上的每一个索引的的分片数量趋于均衡。

###### cluster.routing.allocation.balance.threshold

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）该值时候一个非负的浮点之后，Minimal optimization value of operations that should be performed。提高这个值将导致集群在优化分片平衡方面不那么积极（leess aggressive）。

>NOTE：无论balancing算法的结果如何，rebalancing可能因为forced awareness或者allocation filtering而无法执行

##### Disk-based shard allocation settings

&emsp;&emsp;基于磁盘的分片分配器能保证所有节点都有足够的磁盘空间而不用执行更多的分片移动。它基于一对阈值来分配分片：low wateremark 和 high watermark。主要的目的是保证在每一个节点不会超过high watermark，或者是暂时的超过（overage is temporary）。如果某个节点上超过了high  watermark，那么Elasticsearch会通过把分片移动到集群中的其他节点来的方式来解决。

>NOTE：节点上有时（from time to time）出现临时的超过high watermark是正常的

&emsp;&emsp;分配器总是尝试通过禁止往已经超过low watermark的节点分配更多分配的方式来让节点远离（clear of）high watermark。重要的是，如果所有的节点都已经超过了low watermark那么不会有新的分片会被分配并且Elasticsearch不会通过在节点间移动分片的方式来让磁盘使用率低于high watermark。你必须保证在你的集群中有足够的磁盘空间并且总存在一些低于low watermark的节点。

&emsp;&emsp;通过基于磁盘的分片分配器进行的分片移动必须满足（satisfy）其他的分片分配规则，例如 [allocation filtering](#####Cluster-level-shard-allocation-filtering) 和 [forced awareness](#####Forced-awareness)。如果这些规则过于严格，它们还可以防止碎片移动，以保持节点的磁盘使用在控制之下。如果你正在使用 [data tiers](###Data-tiers)，那么Elasticsearch会自动的应用分配过滤规则将每一个分配放到合适的数据层，这些规则意味着平衡器在每一层中独立工作。

&emsp;&emsp;如果某个节点填满磁盘的速度比Elasticsearch将分片移动到其他地方的速度还要快就会有磁盘被填满的风险。为了防止出现这个问题，万不得已的情况下（ as a last resort），一旦磁盘使用达到`flood-stage` watermark，Elasticsearch会阻塞受到影响的节点上的分片索引的写入。但仍然继续将分片移动到集群中的其他节点。当受到影响的节点上的磁盘使用降到high watermark，Elasticsearch会自动的移除write block。

> TIP：集群中的节点各自使用容量不相同的磁盘空间是正常的。集群的[balance](#####Shard rebalancing settings)取决与每一个节点上分片的数量以及分片中的索引。基于下面的两个理由，集群的balance既不会考虑分片的大小，也不会考虑每一个节点上磁盘可用的空间：
> - 磁盘的使用随着时间发生变化。平衡不同节点的磁盘使用将会有很多更多的分片移动，perhaps even wastefully undoing earlier movements。移动一个分片会消耗例如I/O资源以及网络带宽，并且可能从文件系统缓存中换出（evict）数据。这些资源最好用于你的查询和索引。
> - 集群中每一个节点有相同的磁盘使用在性能上通常没有有不同磁盘使用的性能高，只要所有的磁盘不是太满
> 

&emsp;&emsp;你可以使用下面的设置控制基于磁盘的分配：

###### cluster.routing.allocation.disk.threshold_enabled

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）默认值为`true`。设置为`false`则关闭基于磁盘的分配。

###### cluster.routing.allocation.disk.watermark.low

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）控制磁盘使用量的水位下限（low watermark）。默认值为`85%`。意味着Elasticsearch不会将分片分配到磁盘使用超过85%的节点上。该值也可以是一个字节单位的值（例如`500mb`），使得当磁盘空间小于指定的值时就不让Elasticsearch分配分片到这个节点。这个设置不会影响新创建的索引的主分片，但是会组织副本分配的创建。

###### cluster.routing.allocation.disk.watermark.high

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）控制磁盘使用量的水位上限。默认值为`90%`，意味着Elasticsearch将对磁盘使用量超过90%的节点上的分片进行relocate。该值也可以是一个字节单位的值（跟low watermark类似）。使得当磁盘空间小于指定的值时就把该节点上的分片进行relocate。这个设置会影响所有分片的分配，无论之前是否已经分配。

###### cluster.routing.allocation.disk.watermark.enable_for_single_data_node

&emsp;&emsp;（[Static](###### Static（settings） )) 在更早的发布中，当做出分配决策时，对于单个数据节点的集群是不会考虑disk watermark的。在7.14之后被值为deprecated并且在8.0移除。现在这个设置唯一合法的值为`true`。这个设置在未来的发布中移除。

###### cluster.routing.allocation.disk.watermark.flood_stage

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）控制flood stage watermark。只要至少有一个节点超过该值，分配到这个节点的一个或者分片对应的索引会被Elasticsearch强制置为read-only index block（`index.blocks.read_only_allow_delete`）。这个设置是防止节点发生磁盘空间不足最后的手段。当磁盘使用量降到high watermark后会自动释放index block。

> NOTE：你不能在设置中混合使用比例值（percentage）和字节值（byte value）。要么所有的值都是比例值，要么都是字节值。这种强制性的要求使得Elasticsearch可以进行一致性的处理。另外要确保low disk threshold要低于high disk threshold，并且high disk threshold要低于flood stage threshold。

&emsp;&emsp;下面的例子中在索引`my-index-000001`上重新设置read-only index block：

```text
PUT /my-index-000001/_settings
{
  "index.blocks.read_only_allow_delete": null
}
```

###### cluster.routing.allocation.disk.watermark.flood_stage.frozen

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）用于专用的frozen node，控制flood stage watermark，默认值为95%

###### cluster.routing.allocation.disk.watermark.flood_stage.frozen.max_headroom

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）用于专用的frozen node，控制flood stage watermark的head room。当`cluster.routing.allocation.disk.watermark.flood_stage.frozen`没有显示设置时默认值为20GB。该值限制（cap）了专用的frozen node上磁盘的空闲量。

###### cluster.info.update.interval

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）Elasticsearch定时检查集群中每一个节点上磁盘使用情况的时间间隔。默认值为`30s`。

> NOTE：比例值说的是（refer to）已使用的磁盘空间，而字节值说的是剩余磁盘空间。这可能会让人疑惑，因为它弄反了高和低的含义。比如，设置low watermark为10GB，high watermark为5GB是合理的，反过来设置的话就不行

&emsp;&emsp;下面的例子讲low watermark的值设置为至少100gb，high watermark的值设置为至少50gb，flood stage watermark的值为10gb，并且每一分钟进行检查：

```text
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.disk.watermark.low": "100gb",
    "cluster.routing.allocation.disk.watermark.high": "50gb",
    "cluster.routing.allocation.disk.watermark.flood_stage": "10gb",
    "cluster.info.update.interval": "1m"
  }
}
```

##### Shard allocation awareness

&emsp;&emsp;你可以自定义节点属性作为感知属性（awareness attributes）让Elasticsearch在分配分片时考虑你物理硬件的配置。如果Elasticsearch知道哪些节点在同一台物理机上、同一个机架（rack）、或者同一个区域，使得在发布主分片跟副本分片时能最低限度的降低丢失所有副本分片的风险。

&emsp;&emsp;当使用（[Dynamic](######Dynamic（settings）)）的`cluster.routing.allocation.awareness.attributes`的设置开启awareness attribute后，分片只会被分配到设置了awareness attributes的节点上。当你使用多个awareness attribute时，Elasticsearch在分配分片时会单独地（separately）考虑每一个attribute。

> NOTE: attribute values的数量决定了每一个位置上副本分配的分配数量。如果在每一个位置上的节点数量是unbalanced并且有许多的副本，副本分片可能不会被分配

###### Enabling shard allocation awareness

&emsp;&emsp;开启分片的分配awareness，需要：

1. 使用一个自定义的节点属性来指定每个节点的位置。如果你想要Elasticsearch在不同的机架间发布分片，你可能需要在配置文件`elasticsearch.yml`上设置一个名为`rack_id`的属性。

```text
node.attr.rack_id: rack_one
```

&emsp;&emsp;你也可以在启动的时候设置自定义的属性。

```text
./bin/elasticsearch -Enode.attr.rack_id=rack_one
```

2. 在每一个master-eligible node的配置文件`elasticsearch.yml`上设置`cluster.routing.allocation.awareness.attributes`，告诉Elasticsearch在分配分片的时候要考虑一个或者多个awareness attribute。

```text
cluster.routing.allocation.awareness.attributes: rack_id
```

&emsp;&emsp;用逗号分隔多个属性。

&emsp;&emsp;你也可以使用[cluster-update-settings](####Cluster-update-settings API) API来设置或者更新集群的awareness attributes。

&emsp;&emsp;基于上述的配置例子，如果你启动了两个节点并且都设置`node.attr.rack_id`为`rack_one`并且为每个index设置5个主分片，每个主分片设置一个副本分片，那么这两个节点都包含所有的主分片以及副本分片。

&emsp;&emsp;如果你增加了两个节点并且都设置`node.attr.rack_id`为`rack_two`，Elasticsearch会把分片移动到新的节点，ensuring（if possible）相同的副本分片不会在相同的机架上。

&emsp;&emsp;如果`rack_two`失败并关闭了它的两个节点，默认情况下，Elasticsearch会将丢失的副本分片分配给`rack_one`中的节点。为了防止特定分片的多个副本被分配到相同的位置，可以启用forced awareness。

###### Forced awareness

&emsp;&emsp;默认情况下，如果一个位置失败了（one location fails），Elasticsearch将缺失的分片分配到剩余的位置。可能所有的位置都有足够的资源来存放主分片跟副本分片，也有可能某个位置无法存储所有的分片。

&emsp;&emsp;为了防止某个位置因为失败事件（event of failure）而造成负载过高（overload），你可以设置`cluster.routing.allocation.awareness.force`使得副本分片不会被分配直到其他位置可以被分配。

&emsp;&emsp;比如说，如果你有一个awareness attribute 名为`zone`并且在两个节点分别配置`zone1`跟`zone2`，那么在只有一个zone可用的情况，你可以使用forced awareness来防止Elasticsearch分配副本分片。

```text
cluster.routing.allocation.awareness.attributes: zone
cluster.routing.allocation.awareness.force.zone.values: zone1,zone2
```

&emsp;&emsp;为awareness attribute指定所有可能的值。

&emsp;&emsp;基于上述的配置例子，如果启动了两个节点并且设置`node.attr.zone`的值为`zone1`并且为每个index设置5个分片，每个分片设置一个副本分片，那么Elasticsearch会创建索引并且分配5个主分片但是不会有副本分片。只有某个节点设置`node.attr.zone`的值为`zone2`才会分配副本分片。

##### Cluster-level shard allocation filtering

&emsp;&emsp;你可以使用cluster-level shard allocation filters来控制Elasticsearch从任何索引分配分片的位置。结合[per-index allocation filtering](####Index-level shard allocation filtering)和[allocation awareness](#####Shard allocation awareness)来应用集群级别（cluster wide）的filter。

&emsp;&emsp;Shard allocation filters可以基于自定义的节点属性或者内建的属性：`_name`, `_host_ip`, `_publish_ip`, `_ip`, `_host`,`_id` 和` _tier`。

&emsp;&emsp;`cluster.routing.allocation`设置是动态的[Dynamic](######Dynamic（settings）)，允许将live indices从一组节点上移动到其他组。只有在不破坏路由约定（routing constraint）下才有可能重新分配分片，比如说不会将主分片和它的副本分片分配到同一个节点上。

&emsp;&emsp;最常用的cluster-level shard allocation filtering用例是当你想要结束（decommission）一个节点。要在关闭节点之前将分片移出节点，您可以创建一个过滤器，通过其 IP 地址排除该节点：

```text
PUT _cluster/settings
{
  "persistent" : {
    "cluster.routing.allocation.exclude._ip" : "10.0.0.1"
  }
}
```

##### Cluster routing settings

###### cluster.routing.allocation.include.{attribute}

&emsp;&emsp;([Dynamic](######Dynamic（settings）))将分配分片到一个节点，这个节点的`{attribute}`至少是用逗号隔开的多个属性中的一个。

###### cluster.routing.allocation.require.{attribute}

&emsp;&emsp;([Dynamic](######Dynamic（settings）))只将分片分配到一个节点，这个节点的`{attribute}`包含所有用逗号隔开的多个属性。

###### cluster.routing.allocation.exclude.{attribute}

&emsp;&emsp;([Dynamic](######Dynamic（settings）))不将分片分配到一个节点，这个节点的`{attribute}`包含用逗号隔开的多个属性中的任意一个。

&emsp;&emsp;cluster allocation settings支持下面的内建属性：

|    \_name    | 根据node name匹配节点 |
| :---------: | ---- |
|  \_host\_ip   | 根据host IP 地址（hostname关联的IP）匹配节点 |
| \_publish\_ip | 根据发布的IP地址匹配节点 |
|     \_ip     | 根据\_host\_ip或者\_publish\_ip匹配节点 |
|    \_host    | 根据hostname匹配节点 |
|     \_id     | 根据node id匹配节点 |
|    \_tier    | 根据节点的[data tier](###Data tiers)角色匹配节点 |

> NOTE:  `_tier`的过滤基于[ node roles](####Node)，只有部分角色是[ data tier](###Data tiers) 角色，并且[generic data role](#####Data node)将会匹配任何的tier filtering。a subset of roles that are data tier roles, but the generic data role will match any tier filtering.

&emsp;&emsp;在指定attribute values时可以使用通配符，例如：

```text
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.exclude._ip": "192.168.2.*"
  }
}
```

##### Miscellaneous cluster settings

###### Metadata

&emsp;&emsp;通过下面的设置可以将整个集群设置为read-only：

###### cluster.blocks.read_only

&emsp;&emsp;([Dynamic](######Dynamic（settings）)) 让整个集群read only（索引不支持写操作），metadata不允许更改（创建或者删除索引）

###### cluster.blocks.read_only_allow_delete

&emsp;&emsp;([Dynamic](######Dynamic（settings）)) 与`cluster.blocks.read_only`一样，但是允许删除索引来释放资源。

> WARNING: 不要依赖这些设置来防止集群发生变更。任何有[cluster-update-settings API](####Cluster update settings API)  访问权限的用户都可以让集群再次read-write

##### Cluster shard limit

&emsp;&emsp;基于集群中的节点数量，集群中分片的数量有一个soft limit。这是为了防止可能无意中破坏集群稳定性的操作。

> IMPORTANT：这个限制作为一个安全网（safety net），不是一个推荐的大小。你集群中可以安全的支持准确的分片数量取决于你的硬件配置跟工作负载，但是在大部分case下，这个limit应该还不错，因为默认的limit是非常大的。

&emsp;&emsp;如果一个操作，比如创建新的索引，恢复snapshot index，或者打开一个关闭的索引会导致集群中的分片数量超过限制。那这个操作会失败并且给出shard limit的错误。

&emsp;&emsp;如果由于node membership的变化或者设置的变更使得集群已经超过了限制，所有创建或者打开索引的操作都会失败直到下文中将介绍的提高limit或者[关闭](####Open index API)[删除](####Delete index API)一些索引使得分片的数量降到了limit以下。

&emsp;&emsp;对于normal indices（non-frozen），每一个non-frozen data node上cluster shard limit的默认值是1000，对于frozen indices，每一个frozen data node上的默认值是3000。所有打开的索引的主分片跟副本分片的数量都会纳入到统计中（count toward the limit），包括未分配的分片。例如5个主分片和2个副本的数量为15个分片。关闭的索引不会参与分片的统计。

&emsp;&emsp;你可以使用下面的方式动态的调整cluster shard limit：

###### cluster.max_shards_per_node

&emsp;&emsp;([Dynamic](######Dynamic（settings）)) 限制集群中主分片跟副本分片的总数量。Elasticsearch使用下面的公式统计limit：

&emsp;&emsp;`cluster.max_shards_per_node * number of non-frozen data nodes`

&emsp;&emsp;关闭的索引不会纳入到统计中。默认是`1000`。没有data node的集群是没有限制的。

&emsp;&emsp;超过上限后，Elasticsearch会reject创建更多分片的请求。比如集群的`cluster.max_shards_per_node`的值是`100`，并且三个data node就有`300`的分片数量限制。如果集群中已经包含了296个分片，Elasticsearch会reject增加5个或者更多分片数量的请求。

&emsp;&emsp;注意的是frozen shard有他们自己独立的限制。

###### cluster.max_shards_per_node.frozen

&emsp;&emsp;([Dynamic](######Dynamic（settings）)) 限制集群中主分片跟replica frozen shard的总数量。Elasticsearch使用下面的公式统计limit：

&emsp;&emsp;`cluster.max_shards_per_node * number of frozen data nodes`

&emsp;&emsp;关闭的索引不会纳入到统计中。默认是`3000`。没有frozen data node的集群是没有限制的。

&emsp;&emsp;超过上限后，Elasticsearch会reject创建更多frozen shard的请求。比如集群的`cluster.max_shards_per_node.frozen`的值是`100`，并且三个frozen data node就有`300`的分片数量限制。如果集群中已经包含了296个分片，Elasticsearch会reject增加5个或者更多frozen shard数量的请求。

> NOTE: These setting do not limit shards for individual nodes. 可以使用设置[cluster.routing.allocation.total_shards_per_node](#####cluster.routing.allocation.total_shards_per_node)来限制每一个节点上的分片数量。

##### User-defined cluster metadata

&emsp;&emsp;可以通过Cluster Settings API来存储以及查询用户自定义的集群元信息（User-defined cluster metadata）。可以用来存储任意的（arbitrary），很少更改（infrequently-changing）的关于集群的信息，而不需要创建索引来存储这些信息。使用以`cluster.metadata`为前缀的任意的key来存储信息。例如，在`cluster.metadata.administrator`这个key下存储管理员的email地址，如下所示：

```text
PUT /_cluster/settings
{
  "persistent": {
    "cluster.metadata.administrator": "sysadmin@example.com"
  }
}
```

> IMPORTANT： User-defined cluster metadata不要存储敏感的或者机密信息。任何人都可以通过[Cluster Get Settings API](####Cluster get settings API)访问存储在User-defined cluster metadata的信息，并且会被记录在Elasticsearch日志中。

##### Index tombstones

&emsp;&emsp;集群状态（cluster state）维护者index tombstone来显示的告知（denote）被删除的索引。集群状态维护tombstone的数量，通过下面的设置来进行控制：

###### cluster.indices.tombstones.size

（[Static](######Static（settings） )）当发生删除时，Index tombstones会阻止不属于集群的节点加入集群并重新导入索引，就好像delete was never issued。为了防止增长的太大，我们只保留最新的`cluster.indices.tombstones.size`数量的删除，默认值是500。You can increase it if you expect nodes to be absent from the cluster and miss more than 500 deletes. We think that is rare, thus the default. Tombstones don’t take up much space, but we also think that a number like 50,000 is probably too big.

&emsp;&emsp;如果 Elasticsearch 遇到当前集群状态中不存在的索引数据，则这些索引被认为是dangling。 例如，如果你在 Elasticsearch 节点离线时删除超过 `cluster.indices.tombstones.size` 的索引，就会发生这种情况。

&emsp;&emsp;你可以使用[Dangling indices API](####Dangling indices:)来进行管理。

##### Logger

&emsp;&emsp;这个设置用来控制日志，使用`logger.`为前缀的key来[动态的](######Dynamic（settings）)更新。发布下面的请求（issue this request）来提高`indices.recovery`模块的日志等级为`DEBUG`：

```text
PUT /_cluster/settings
{
  "persistent": {
    "logger.org.elasticsearch.indices.recovery": "DEBUG"
  }
}
```

##### Persistent tasks allocation

&emsp;&emsp;Plugin可以创建一系列称为persistent task的任务。这些任务通常是long-lived并且存储在cluster state中，并且允许在一次full cluster restart后恢复（revive）这些任务。

&emsp;&emsp;每一次创建一个persistent task时，master node会负责将任务分配到集群中的一个节点。然后被分配的节点会pick up这个任务然后在本地执行。通过下面的设置来控制被分配的persistent task的运行：

###### cluster.persistent_tasks.allocation.enable

([Dynamic](######Dynamic（settings）))开启或者关闭persistent task的分配：

- all - (default) 允许persistent task被分配到节点
- none - 不允许分配任何类型的persistent task

&emsp;&emsp;这个设置不会影响已经执行的persistent task。只有最新创建的，或者必须要重新被分配的persistent task才会收到这个设置的影响。

###### cluster.persistent_tasks.allocation.recheck_interval

([Dynamic](######Dynamic（settings）))master node会自动的检查在cluster state发生重大变化（change significantly）后persistent tast是否需要被分配。然而，也会有其他的一些因素，比如说memory usage，这个会影响persistent task能否被分配但这不会引起cluster state的变更。这个设置控制了检查的间隔。默认是30秒。最小允许的时间为10秒。

#### Cross-cluster replication settings
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ccr-settings.html#ccr-recovery-settings)

&emsp;&emsp;可以使用[cluster update settings API](####Cluster update settings API)动态更新集群中的CCR（cross-cluster replication）设置。

##### Remote recovery settings

&emsp;&emsp;下面的设置可以用于在[remote recoveries](####Initializing followers using remote recovery)中限制数据传输速率：

###### ccr.indices.recovery.max_bytes_per_sec ([Dynamic](######Dynamic（settings）))

&emsp;&emsp;用来限制每一个节点上inbound和outbound remote recovery traffic。由于这个设置是作用到每一个节点上，但是可能存在许多的节点并发的执行remote recoveries，remote recovery占用的流量可能比这个限制高。如果这个值设置的太高，那么会存在这么一种风险，即正在进行中的recoveries会过度消费带宽 ，使得集群变得不稳定。这个设置应该同时被leader和follower clusters使用。例如，如果在leader上设置了`20mb`，leader将只能发送`20mb/s`到follower，即使follower可以达到`60mb/s`。默认值为`40mb`。

##### Advanced remote recovery settings

&emsp;&emsp;下面的专家级设置（expert settings）用于管理remote recoveries时的资源消费。

###### ccr.indices.recovery.max_concurrent_file_chunks ([Dynamic](######Dynamic（settings）))

&emsp;&emsp;控制每个recovery中并发请求文件块（file chunk）的数量。由于多个remote recoveries已经在并发运行，增加这个专家级设置可能只有助于没有达到总的inbound and outbound remote recovery traffic的单个分片的remote recovery，即上文中的`ccr.indices.recovery.max_bytes_per_sec`。`ccr.indices.recovery.max_concurrent_file_chunks`的默认值为`5`。允许最大值为`10`。

###### ccr.indices.recovery.chunk_size ([Dynamic](######Dynamic（settings）))

&emsp;&emsp;控制在文件传输时，follower请求的文件块的大小。默认值为`1mb`。

###### ccr.indices.recovery.recovery_activity_timeout ([Dynamic](######Dynamic（settings）))

&emsp;&emsp;控制recovery的活跃（activity）超时时间。这个超时时间主要是应用在leader集群上。在处理recovery期间，leader cluster必须打开内存资源提供数据给follower。如果leader在周期时间内没有接收到follower的接收请求，leader会关闭资源。默认值为`60s`。

###### ccr.indices.recovery.internal_action_timeout ([Dynamic](######Dynamic（settings）))

&emsp;&emsp;控制在remote recovery处理期间每一个网络请求的超时时间。某个单独的动作（individual action）超时会导致recovery失败。默认值为`60s`。

#### Discovery and cluster formation settings

&emsp;&emsp;[Discovery and cluster formation](###Discovery and cluster formation)受到下面设置的影响：

##### discovery.seed_hosts

&emsp;&emsp;（[Static](######Static（settings） ))）提供集群中master-eligible node的地址列表。可以是单个包含地址，用逗号隔开的字符串。每一个地址有`host:port`或者`host`的格式。`host`可以是hostname（可以用DNS解析）、IPv4地址、IPv6地址。IPv6地址必须用{}包裹。如果hostname用DNS解析出多个地址，Elasticsearch会使用所有的地址。DNS基于[JVM DNS caching](####DNS cache settings)进行查找（lookup）。如果没有指定`port`，会有序检查下面的设置来找到`port`：

1. `transport.profiles.default.port`
2. `transport.port`

&emsp;&emsp;如果没有设置上面的配置，则`port`的默认值为`9300`。`discovery.seed_hosts`的默认值为`["127.0.0.1", "[::1]"]`。见[discovery.seed_hosts](######discovery.seed_hosts)。

###### discovery.seed_providers

&emsp;&emsp;（[Static](######Static（settings） ))）指定[seed hosts provider](#####Seed hosts providers)的类型，用于获取启动发现程序（discovery process）时需要用到的seed nodes地址。默认情况为[settings-based seed hosts provider](######Settings-based seed hosts provider)，它包含了`discovery.seed_hosts`中的seed node 地址。

###### discovery.type

&emsp;&emsp;（[Static](######Static（settings） ))）指定Elasticsearch是否形成一个多节点的（multiple-node）的集群，默认值为`multi-node`，意味着Elasticsearch在形成一个集群时会发现其他节点并且允许其他节点随后加入到这个集群。如果设置为`single-node`，Elasticsearch会形成一个单节点（single-node）集群并且suppresses the timeout set by `cluster.publish.timeout`。当你想要使用这个设置的时候，见[Single-node discovery](####Single-node discovery)了解更多的信息

###### cluster.initial_master_nodes

&emsp;&emsp;（[Static](######Static（settings） ))）在全新的集群中初始化master-eligible node集合。默认情况下是空值，意味着这个节点想要加入到一个引导后（bootstrapped）的集群。集群一旦形成后需要移除这个设置。当重启一个节点或者添加一个新的节点到已有的集群中时不要使用这个设置。见[cluster.initial_master_nodes](######cluster.initial_master_nodes)。

##### Expert settings

&emsp;&emsp;[Discovery and cluster formation](###Discovery and cluster formation)受到下面专家级设置（expert settings）的影响，当然我们不推荐修改这些设置的默认值。

> WARNING：如果你调整了集群中的这些设置，可能会无法正确的形成一个集群或者可能集群变得不稳定，或者无法处理（intolerant）对于一些故障。

###### discovery.cluster_formation_warning_timeout

&emsp;&emsp;（[Static](######Static（settings） ))）尝试形成一个集群时，多长时间后仍然没有形成集群，开始记录未形成集群的警告日志的设置。默认值为`10s`。如果在`discovery.cluster_formation_warning_timeout`的时间内没有形成一个集群，那节点会记录一个包含`master not discovered`的warnning message，它描述了当前discovery process当前的状态。

###### discovery.find_peers_interval

&emsp;&emsp;（[Static](######Static（settings） ))）一个节点尝试新一轮discovery的时间间隔。默认值为`1s`。

###### discovery.probe.connect_timeout

&emsp;&emsp;（[Static](######Static（settings） ))）尝试连接每一个地址的连接等待时间。默认值为`30s`。

###### discovery.probe.handshake_timeout

&emsp;&emsp;（[Static](######Static（settings） ))）尝试通过handshake识别remote node的等待时间。默认值为`30s`

###### discovery.request_peers_timeout

&emsp;&emsp;（[Static](######Static（settings） ))）在认为请求失败时，询问其peer后的等待时间。默认值为`3s`。

###### discovery.find_peers_warning_timeout

&emsp;&emsp;（[Static](######Static（settings） ))）节点尝试discover它的peers并开始记录为什么尝试连接失败的verbose message的时间。默认值为`3m`。

###### discovery.seed_resolver.max_concurrent_resolvers

&emsp;&emsp;（[Static](######Static（settings） ))）DNS并发解析seed node的地址时允许的并发量。默认值为`10`。

###### discovery.seed_resolver.timeout

&emsp;&emsp;（[Static](######Static（settings） ))）DNS解析seed note的地址的超时时间。默认值为`5s`。

###### cluster.auto_shrink_voting_configuration

&emsp;&emsp;([Dynamic](######Dynamic（settings）))控制[voting configuration](####Voting configurations)是否自动的去除（shed）不能担任voting的节点（departed node），只要它至少还包含3个节点。默认值为`true`。如果设置为`false`。voting configuration不会自动的进行收缩，你必须通过[voting configuration exclusions API](####Voting configuration exclusions API)手动的移除不能担任voting的节点（departed node）。

###### cluster.election.back_off_time

&emsp;&emsp;（[Static](######Static（settings） ))）
（未完成）

###### cluster.election.duration

###### cluster.election.initial_timeout

#### Field data cache settings
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-fielddata.html)

&emsp;&emsp;field data cache中包含了[field data](#####fielddata mapping parameter)和[global ordinals](####eager_global_ordinals)，它们都是用来让某些域能够用于聚合。由于是on-heap的数据结构，所以很有必要监控缓存的使用情况。

##### Cache size

&emsp;&emsp;构建cache中实体（entities）的开销是很昂贵的，所以默认情况下会保留载入到内存的entities。默认的cache大小是没有限制的，cache大小会不断增大直到达到[field data circuit breaker](#####Field data circuit breaker)设定的上限。这种行为可以配置。

&emsp;&emsp;如果设置了cache大小的上限，cache将会清除cache中最近更新的实体（If the cache size limit is set, the cache will begin clearing the least-recently-updated entries in the cache）。这个设置会自动的避免circuit breaker limit，代价就是需要重新构建cache。

&emsp;&emsp;如果达到了circuit breaker limit，后续会增加cache大小的请求将被阻止。在这种情况下你必须要手动[clear the cache](####Clear cache API)。

###### indices.fielddata.cache.size

&emsp;&emsp;（[Static](######Static（settings） ))） field data cache的最大值。比如节点堆空间大小的`38%`，或者是一个absolute value，`12GB`。默认是没有限制。如果选择设置这个值，这个值应该小于[Field data circuit breaker limit](#####Field data circuit breaker)。

##### Monitoring field data

&emsp;&emsp;你可以使用[nodes stats API](####Nodes stats API)和[cat fielddata API](####cat fielddata API)来监控filed data的内存使用情况以及 field data circuit breaker。


#### Index lifecycle management settings in Elasticsearch
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-settings.html#index-lifecycle-step-wait-time-threshold)

##### Index level settings

###### index.lifecycle.origination_date

###### index.lifecycle.parse_origination_date

###### index.lifecycle.step.wait_time_threshold

#### Index management settings
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-management-settings.html#stack-templates-enabled)

##### stack.templates.enabled

#### Index recovery settings
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/recovery.html)

#### Indexing buffer settings
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indexing-buffer.html)

&emsp;&emsp;indexing buffer用来缓存新添加的文档，当缓冲区满后，缓存中的文档会被写入到一个段中并且存放到磁盘上。Indexing buffer的大小被节点上的所有分片共享。

&emsp;&emsp;下面的设置都是静态的必须在集群中的每一个节点上配置：

##### indices.memory.index_buffer_size

&emsp;&emsp;（[Static](######Static（settings） )）可以是百分比或者是字节单位的数值。默认是`10%`意味着堆内存的10%会被分配给节点用于Indexing buffer，并且被节点上所有的分片共享。

##### indices.memory.min_index_buffer_size

&emsp;&emsp;（[Static](######Static（settings） )）如果`index_buffer_size`指定为百分比，那这个设置用来指定为一个绝对最小值（absolute minimum）。默认是是`48m`。

##### indices.memory.max_index_buffer_size

&emsp;&emsp;（[Static](######Static（settings） )）如果`index_buffer_size`指定为百分比，那这个设置用来指定为一个绝对最大值（absolute maximum）。默认是无限制的。

#### Local gateway settings
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-gateway.html)

##### Dangling indices

#### Logging
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/logging.html)

#### Machine learning settings in Elasticsearch
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ml-settings.html)

#### Node
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-node.html)

&emsp;&emsp;任何时候在你启动了一个Elasticsearch的实例时，即你正在启动一个节点（Node）。相互连接的节点集合成为一个集群（[cluster](####Cluster-level shard allocation and routing settings)）。如果你运行了 一个单个节点的Elasticsearch，那么你拥有一个只有一个节点的集群。

&emsp;&emsp;默认情况下，集群中的每个节点都可以处理[ HTTP and transport](####Networking )的传输。transport layer只用来处理节点之间的交互，HTTP layer用于REST clients 。

&emsp;&emsp;所有的节点都知道集群中的其他节点并且可以转发客户端的请求到合适的节点。

##### Node roles

&emsp;&emsp;你可以在`elasticsearch.yml`中设置`node.roles`来定义一个节点的角色。如果你设置了`node.roles`，这个节点只会被分配为你指定的角色。如果你不指定`node.roles`，这个节点会被分配为下面的角色：

- master
- data
- data_content
- data_hot
- data_warm
- data_cold
- data_frozen
- ingest
- ml
- remote_cluster_client
- transform

> IMPORTANT：如果你设置了`node.roles`，你要保证你的集群需要你指定的这些角色。每一个集群需要有下面的角色：
>
> - master
> - (data_content和data_hot) 或者 data
>
> 一些Elastic Stack 功能同样要求指定下面的节点角色：
>
> - 跨集群搜索（Cross-cluster search）和跨集群副本（Cross-cluster replication）需要`remote_cluster_client`角色
> - Stack Monitoring和ingest pipeline需要`ingest`角色
> - Fleet，Elastic Security应用，和transforms需要`transform`角色。`remote_cluster_client`角色同样需要用于这些功能中的cross-cluster search
> - Machine learning功能，比如异常检测（anomaly detection）需要`ml`角色

&emsp;&emsp;随着集群的增长，特别是集群中有大规模的machine learning任务、continuous transforms，我们建议从专用的data node、machine learning node、transform node中分离出master-eligible节点。

|  [Master-eligible node](#####Master-eligible node)  | 拥有`master`角色的节点，使得节点有资格被[选举](###Discovery and cluster formation)为master node来控制整个集群 |
| :-------------------------------------------------: | :----------------------------------------------------------: |
|             [Data node](#####Data node)             | 拥有`data`角色的节点，data node保留数据并且执行相关操作例如CRUD、查询、聚合。一个拥有`data`角色的节点可以添加任何其他特定的数据节点角色，例如hot 、warm等 |
|           [Ingest node](#####Ingest node)           | 拥有`ingest`角色的节点，ingest node可以对文档进行[ingest pipeline](##Ingest pipelines)使得可以在索引前transform或者丰富文档。对于繁重的ingest，使用专用的ingest node并且让拥有`master`或者`data`角色的节点不要有`ingest`角色 |
|  [Remote-eligible node](#####Remote-eligible node)  | 拥有`remote_cluster_client`角色的节点，使得这个节点有资格成为一个remote client |
| [Machine learning node](#####Machine learning node) | 拥有`ml`角色的节点，如果你想要使用machine learning功能，你的集群中至少要有一个machine learning node。见[Machine learning settings](####Machine learning settings in Elasticsearch)和[Machine learning in the Elastic Stack](https://www.elastic.co/guide/en/machine-learning/8.2/index.html)了解更多信息 |
|        [Transform node](#####Transform node)        | 拥有`transform`角色的节点。如果想要使用transform，你的集群中至少要有一个transform node。见[Transforms settings](#####Transforms settings in Elasticsearch)和[Transforming data](###Transforming data)了解更多信息 |

> NOTE：Coordinating node
> 一些像查询请求或者块索引（bulk-indeing）的请求可能涉及不同data node上的数据。一次查询请求，例如，搜索请求分两个阶段执行，由接收客户端请求的节点进行协调 - coordinating node。
> 
> 在scatter阶段，coordinating node将请求转发给持有数据的数据节点。 每个数据节点在本地执行请求并将其结果返回给coordinating node。 在收集（gather）阶段，协调节点将每个数据节点的结果减少为单个全局结果集。
> 
> 每个节点都隐含的是一个coordinating node。 这意味着通过设置 `node.roles` 为空的角色列表的节点将作为coordinating node，并且不能被关闭这种设定。 因此，这样的节点需要有足够的内存和 CPU 才能处理收集阶段。

##### Master-eligible node

&emsp;&emsp;master node负责轻量级（lightweight）的集群范围（cluster-wide）的一些工作，比如创建、删除一个索引，跟踪哪些节点是集群的一部分以及决定给哪里节点分配分片。稳定的master node对于集群的健康是十分重要的。

&emsp;&emsp;一个不是[voting-only](######Voting-only master-eligible node)的master-eligible node会在[master election process](###Discovery and cluster formation)中被选为master node。

> IMPORTANT: master node必须有一个`path.data`目录，其内容在重启后仍然存在，跟data node一样，这个目录中存放了集群的元数据（cluster metadata）。集群元数据描述了如何读取存储在data node上的数据，所以如果丢失了集群元数据就无法读取data node上的数据。


###### Dedicated master-eligible node

&emsp;&emsp;满足被选举为master的节点为了能完成它的职责所需要的资源对于集群的健康是十分重要的。如果被选举为master的节点因为其他的工作导致超负荷了，那么集群将无法较好的运行。避免master node工作超负荷最可靠的方法就是将所有master-eligible node只配置为`master`角色，让它们作为专用的master-eligible node（dedicated master-eligible node），让它们集中做好管理集群的工作。Master-eligible node可以跟[coordinating nodes](#####Coordinating only node)一样将客户端的请求路由到集群中的其他节点，但是你不应该让专用的master-eligible node做这些事情。

&emsp;&emsp;在一些小规模或者轻负载（lightly-loaded）的集群中，如果master-eligible node有其他的角色和职责也可以正常的运行，但是，一旦集群中包含多个节点，通常使用专用的master-eligible node是有意义的。

&emsp;&emsp;通过设置下面的配置来创建一个专用的master-eligible节点：

```text
node.roles: [ master ]
```

###### Voting-only master-eligible node

&emsp;&emsp;voting-only master-eligible node是会参与[master elections](###Discovery and cluster formation)的节点，但是不会成为集群的master节点。特别在于一个voting-only node可以成为选举过程中的tiebreaker。

&emsp;&emsp;使用"master-eligible"这个词来描述一个voting-only node看起来有点让人困惑，因为这种节点是完全没资格成为master节点的。使用这个术语是因为历史的原因（This terminology is an unfortunate consequence of history）：master-eligible node是那些参与选举、集群状态发布（cluster state publication）期间执行一些任务的节点，而voting-only node有着相同的工作职责（responsibility）只是他们不会被选举为master节点。

&emsp;&emsp;通过添加`master`和`voting_only`角色来配置一个master-eligible node为voting-only node。下面的例子创建了一个voting-onyl data node：

```text
node.roles: [ data, master, voting_only ]
```

> IMPORTANT：只有拥有`master`角色的节点才可以标记为拥有`voting_only`角色。

&emsp;&emsp;高可用（HA：high availability）的集群要求至少有三个master-eligible node，他们中的至少两个节点不是voting-only node。这样的集群才能够在其中一个节点发生故障后选出一个master节点。

&emsp;&emsp;由于voting-only node不会被选举为master节点，所以相比较真正的master node，它们需要较少的堆以及较低性能的CPU。然而所有的master-eligible node，包括voting-only node，相比较集群中的其他节点，它们需要相当快速的持久存储以及一个可靠的低延迟的网络连接，因为它们处于[publishing cluster state updates](####Publishing the cluster state)的关键路径（critical path）上。

&emsp;&emsp;Voting-only master-eligible node在集群中可能被分配其他的角色。比如说一个节点同时是data node或者Voting-only master-eligible node。一个专用的voting-only master-eligible node 在集群中是不会有其他的角色的。通过下面的方式来创建一个专用的voting-only master-eligible nodes：

```text
node.roles: [ master, voting_only ]
```

##### Data node

&emsp;&emsp;data note保留了你索引的文档的分片。data note处理像CRUD、查询、聚合的操作。这些操作有I/O-, memory-, and CPU-intensive。监控这些资源以及工作负载大后增加更多的data node是非常重要的。

&emsp;&emsp;拥有专用的data node最主要的好处是将master和data角色分开出来。

&emsp;&emsp;通过下面的方式创建一个专用的data node：

```text
node.roles: [ data ]
```

&emsp;&emsp;在[多层部署架构](###Data tiers)（multi-tier）中，你可以根据为data node分配不同数据层对应的数据角色（data role）：`data_content`,`data_hot`, `data_warm`, `data_cold`, 或者`data_frozen`。一个节点可以属于多个层，但是已经拥有一个指定数据角色的节点不能有通用的`data`角色（generic data role）。

##### Content data node

&emsp;&emsp;Content data node属于[content tier](####Content tier)的一部分。存储在content tier上的数据通常是iterm的集合比如说产品目录（product catalog）或者文章归档（article archive）。跟时序数据不同的是，这些内容的价值随着时间的流逝相对保持不变的，所以根据这些数据的寿命（age）将它们移到性能不同的数据层是不合理的。Content data通常有长时间保留（retention）的要求，并且也希望无论这些数据的寿命的长短，总是能很快的检索到。

&emsp;&emsp;Content tier node会为查询性能做优化-优先优化IO吞吐使得可以处理复杂的查询，聚合并且能很快的返回结果。同时这类节点也用来索引，content data通常没有例如log和metric这种时序数据一样高的ingest rate。从弹性的角度（resiliency perspective）看，当前层的索引应该配置为使用一个或者多个副本（replica）。

&emsp;&emsp;Content tier是必须要有的（required）。系统索引以及其他不是[data stream](##Data streams)的索引都会被自动分配到content tier。

&emsp;&emsp;通过下面的方式创建一个专用的content  node：

```text
node.roles: [ data_content ]
```

##### Hot data node

&emsp;&emsp;hot data node是 [hot tier](####Hot tier)的一部分。hot tier是Elasticsearch中时序数据的入口点（entry point），并且保留最近，最频繁搜索的时序数据。hot tier节点上的读写速度都需要很快，要求更多的硬件资源和更快的存储（SSDs）。出于弹性目的，hot tier上的索引应该配置一个或多个副本分片。

&emsp;&emsp;hot tier是必须要有的。[data stream](##Data streams)中新的索引会被自动分配到hot tier。

&emsp;&emsp;通过下面的方式创建一个专用的hot node:

```text
node.roles: [ data_hot ]
```

##### Warm data node

&emsp;&emsp;warm data node是[warm tie](####Warm tier)的一部分。一旦时序数据的访问频率比最近索引的数据（recently-indexed data）低了，这些数据就可以移到warm tier。warm tier通常保留最近几周的数据。允许更新数据，但是infrequent。warm tier节点不需要像hot tier一样的快。出于弹性目的，warm tier上的索引应该配置一个或多个副本分片。

&emsp;&emsp;通过下面的方式创建一个专用的warm node:

```text
node.roles: [ data_warm ]
```

##### Cold data node

&emsp;&emsp;cold data node是[cold tier](####Cold tier)的一部分。当你不再经常（regular）搜索时序数据了，那可以将它们从warm tier移到cold tier。数据仍然可以被搜索到，在这一层的通常会被优化成较低的存储开销而不是查询速度。

&emsp;&emsp;为了更好的节省存储（storage saveing），你可以在cold tier保留[fully mounted indices](######Fully mounted index)的[searchable snapshots](###Searchable snapshots)。跟普通索引（regular index）不同的是，这些fully mounted indices不需要副本分片来满足可靠性（reliability），一旦出现失败事件，可以从底层（underlying）snapshot中恢复。这样可以潜在的减少一般的本地数据存储开销。snapshot仓库要求在cold tier使用fully mounted indices。Fully mounted indices只允许读取，不能修改。

&emsp;&emsp;另外你可以使用cold tier存储普通索引并且使用副本分片的方式，而不是使用searchable snapshot，这样会帮你在较低成本的硬件上存储较老的索引，但是相比较warm tier不会降低磁盘空间。

&emsp;&emsp;通过下面的方式创建一个专用的cold node:

```text
node.roles: [ data_cold ]
```

##### Frozen data node

&emsp;&emsp;frozen data node是[frozen tier](####Frozen tier)的一部分。一旦数据不需要或者很少（rare）被查询，也许就可以将数据从cold tier移到frozen tier，where it stays for the rest of its life。

&emsp;&emsp;frozen tier需要用到snapshot repository。frozen tier使用[partially mounted indices](######Partially mounted index)的方式存储以及从snapshot repository中载入数据。这样仍然可以让你搜索frozen数据并且可以减少本地储存（local storage）和操作开销（operation cost）。因为Elasticsearch必须有时从snapshot repository中提取（fetch）数据，在frozen tier的查询速度通常比cold  tier慢。

##### Ingest node

&emsp;&emsp;Ingest node可以执行pre-processing pipelines。由一个或者多个ingest processor组成。基于ingest processor执行的操作类型以及需要的资源，拥有一个专用的ingest node还是有意义的，使得这个节点只运行指定的任务。

&emsp;&emsp;通过下面的方式创建一个专用的Ingest node:

```text
node.roles: [ ingest ]
```

##### Coordinating only node

&emsp;&emsp;如果你的节点不需要（take away）处理master职责（duty）、保留数据、pre-process文档的能力，那么你还剩下一个coordinating only node，只能处理路由请求、查询的reduce phase、以及分布式的块索引（bulk Indexing）。本质上coordinating only node表现为像一个智能的负载均衡节点。

&emsp;&emsp;在大规模的集群中将coordinating node角色从data node和master-eligible node中剥离（offload）出来能体现coordinating only nodes的益处。这些节点加入到集群中并且接收完整的[cluster state](####Cluster state API)，就像每一个其他的节点一样，他们使用cluster state将请求路由到合适的地方。

> WARNING：集群中添加太多的coordinating only nodes会增加整个集群的负担（burden），因为被选举为master的节点必须等待从每一个节点更新后的cluster state的回应。不能过度夸大（overstate）coordinating only node的益处-data node也可以实现跟coordinating only nodes相同的目的（same purpose）

&emsp;&emsp;通过下面的方式创建一个专用的coordinating node:

```text
node.roles: [ ]
```

##### Remote-eligible node

&emsp;&emsp;一个remote-eligible node扮演一个cross-cluster client的角色以及连接[remote clusters](###Remote clusters)。一旦连接后，你可以使用[cross-cluster search](###Search across clusters)搜索远端的集群。你可以使用[cross-cluster replication](###Cross-cluster replication)在集群间同步数据。

```test
node.roles: [ remote_cluster_client ]
```

##### Machine learning node

&emsp;&emsp;Machine learning node运行任务并且处理machine learning API的请求。见[Machine learning settings](####Machine learning settings in Elasticsearch)查看更多的信息

&emsp;&emsp;通过下面的方式创建一个专用的machine learning node:

```text
node.roles: [ ml, remote_cluster_client]
```

&emsp;&emsp;`remote_cluster_client`是可选的，但是强烈建议配置进去。否则当使用machine learning jobs 或者 datafeed时，cross-cluster search会失败。如果在你的异常检测任务中使用corss-cluster search，在所有的master-eligible node上同样需要`remote_cluster_client`这个角色。否则 datafeed无法开始，见[Remote-eligible node](#####Remote-eligible node)。

##### Transform node

&emsp;&emsp;Transform node运行transforms并且处理transform API的请求。见[Transforms settings](####Transforms settings in Elasticsearch)。

&emsp;&emsp;通过下面的方式创建一个专用的transform node:

```text
node.roles: [ transform, remote_cluster_client ]
```

&emsp;&emsp;`remote_cluster_client`是可选的，但是强烈建议配置进去。否则当使用transforms时，cross-cluster search会失败。见[Remote-eligible node](#####Remote-eligible node)。

##### Changing the role of a node

&emsp;&emsp;每一个data node在磁盘上维护下面的数据：

- 分配给节点的每一个分片的分片数据
- 分配在这个节点的每一个分片的索引元数据（index metadata），以及
- 集群范围（cluster-wide）的元数据，例如settings和index templates

&emsp;&emsp;同样的，每一个master-eligible node在磁盘上维护下面的数据：

- 集群中每一个索引的索引元数据，以及
- 集群范围（cluster-wide）的元数据，例如settings和index templates

&emsp;&emsp;每一个节点在启动时会检查[数据路径](######path.data)的内容。如果发现了非预期（unexpected）的数据，节点将拒绝启动。这样就可以避免导入会导致红色的集群健康的[unwanted dangling indices](#####Dangling indices)。更准确的说，如果在启动时发现磁盘上有分片数据但是这个节点没有`data`角色，那么这个节点不会启动。如果在启动时发现磁盘上有索引元数据但是这个节点没有`data`和`master`角色，那么这个节点不会启动。

&emsp;&emsp;可以通过更改`elasticsearch.yml`然后重启节点来修改节点的角色。这就是众所周知的 `repurposing`一个节点。为了能满足上文中描述的对非预期的检查，在启动没有`data`或者`master`角色的节点前，你必须执行一些额外的步骤来为repurposing做准备。

- 如果你想要通过repurposing来移除一个data note的`data`角色，你应该先通过[allocation filter](#####Cluster-level shard allocation settings)安全的将节点上的所有分片数据迁移到集群中的其他节点上。
- 如果你想要通过repurposing来移除`data`和`master`节点，最简单的办法是启动一个全新的节点，这个节点设置一个空的数据路径以及想要的节点角色。你会发现使用[allocation filter](#####Cluster-level shard allocation settings)可以安全的先将分片数据迁移到集群中的其他地方

&emsp;&emsp;如果没有办法按照这些额外的步骤实现你的目的，你可以使用[elasticsearch-node repurpose](####Changing the role of a node)工具来删除阻止节点启动的多余的数据（excess data）。

##### Node data path settings

###### path.data

&emsp;&emsp;每个data node、master-eligible node都要访问一个数据目录，这个目录中存放了分片、索引和集群元数据。`path.data`的默认值是`$ES_HOME/data`但是可以在`elasticsearch.yml`中配置一个全路径或者一个相对于`$ES_HOME`的相对路径：

```text
path.data:  /var/elasticsearch/data
```

&emsp;&emsp;跟其他的节点设置一样，可以在命令行上指定：

```text
./bin/elasticsearch -Epath.data=/var/elasticsearch/data
```

&emsp;&emsp;`path.data`的内容在启动时必须存在，因为这是你数据存储的位置。Elasticsearch 要求文件系统像由本地磁盘支持一样运行，但这意味着只要远程存储正常运行，它就可以在配置正确的远程块设备（例如 SAN）和远程文件系统（例如 NFS）上正常工作，与本地存储没有什么不同。你可以在同一个文件系统上运行多个Elasticsearch节点，但是每个节点要有自己的数据路径。

&emsp;&emsp;Elasticsearch集群的性能常常受制于底层存储的性能，所以你必须保证你的存储能有可以接受的性能范围内。一些远程存储的性能很差，尤其是在 Elasticsearch 施加（impose）的那种负载下，所以在使用特定的存储架构之前，请确保仔细地对你的系统进行基准测试。

> TIP：如果使用了`.zip`或者`.tar.gz`的发行版，`path.data`的值应该设置到Elasticsearch home目录以外的路径上，这样删除home目录时不会删除你的数据！RPM和Debian发行版已经为你做了这些工作。

> IMPORTANT：不要修改数据目录中的任何东西或者运行可能会影响这个目录中内容的程序。如果一些不是Elasticsearch的操作修改了数据目录的内容，那么Elasticsearch可能会发生故障，报告出corruption或者其他data inconsistencies，或者工作正常但是轻微的丢失了一些你的数据。不要尝试做文件系统级别的数据目录的备份。因为没有可用的方法来恢复这类备份。而是应该用 [Snapshot and restore](##Snapshot and restore)做安全的备份。不要在数据目录做病毒扫描，病毒扫描会使得Elasticsearch不能正确的工作并且可能会修改数据目录的内容。数据目录不包含可执行文件，因此病毒扫描只会发现误报

##### Other node settings

&emsp;&emsp;更多的跟节点相关的设置可以在[Configuring Elasticsearch](###Configuring Elasticsearch) 和 [Important Elasticsearch configuration](####Important Elasticsearch configuration)找到，包括：

- [cluster.name](#####Cluster name setting)
- [node.name](#####Node name setting)
- [network settings](####Networking)

#### Networking 
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-network.html)

##### Commonly used network settings

##### Binding and publishing

#### Node query cache settings
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-cache.html)

##### Advanced HTTP settings

###### http.max_content_length

##### Advanced transport settings

###### Long-lived idle connections

#### License settings
[link](https://www.elastic.co/guide/en/elasticsearch/reference/current/license-settings.html)

#### Search settings
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-settings.html)

&emsp;&emsp;下面的expert setting用来管理全局的查询和聚合限制。

##### indices.query.bool.max_clause_count

&emsp;&emsp;（[Static](######Static（settings） ) integer）一个查询能包含的clause的最大数量。默认值为`4096`。

&emsp;&emsp;这个设置限制了一颗query tree能包含的clause的最大数量。4096这个默认值已经是非常高了，通常来说该值是够用的。这个限制会作用于重写query（rewrite query），所以不仅仅是`bool` query，所有会重写为bool query的query，例如`fuzzy` query，都会有较高数量的clause。这个限制的目的是防止查询的范围变得很大，占用过多的CPU和内存。如果你一定要提高这个值，保证你已经竭尽全力尝试了所有其他的选项，否则就不要这么做。较高的值会导致性能降低以及内存问题，特别是在负载高或者资源较少的集群上。

&emsp;&emsp;Elasticsearch提供了一些工具来防止遇到这个clause数量上限的问题，例如在[terms](####Terms query) query中，它允许查询许多不一样的值，但是它只作为一个clause。或者是带有[index_prefixes](####index_prefixes)选项的[text](####Text type family)域，它会执行prefix query，这个query会膨胀为较高数量的term，但是只作为一个term query。

##### search.max_buckets

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)，integer）在一次响应中，[aggregation buckets ](###Bucket aggregations)的最大分桶数量。默认值为65535。

&emsp;&emsp;尝试返回超过这个限制的请求会返回一个错误。

##### indices.query.bool.max_nested_depth

&emsp;&emsp;（[Static](######Static（settings） ) integer）bool query的最大深度。默认值为`20`。

&emsp;&emsp;这个设置限制了bool query的嵌套深度。深度大的bool query可能会导致stack overflow。

#### Security settings in Elasticsearch

##### Realm settings

###### Kerberos realm settings

###### OpenID Connect realm settings

#### Shard request cache settings
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/shard-request-cache.html)

&emsp;&emsp;当一个查询请求运行在一个或者多个索引上时，每一个参与的分片会在本地执行查询然后将结果返回到coordinating note，节点会结合（combine）分片级别（shard-level）的结果到一个"全局的"的结果集中。

&emsp;&emsp;分片层的请求缓存模块会在每一个分片上缓存本地的结果。这允许经常使用（并且可能很重）的搜索请求几乎立即返回结果。请求缓存（request cache）非常适用于日志用例，因为这类数据只有最近的索引会持续更新，从旧的索引中获取的结果可以直接从缓存中返回。

> IMPORTANT：默认情况下，请求缓存在`size=0`时仅仅缓存查询请求的结果。所以不会缓存`hits`，但是它会缓存`hits.total`, [aggregations](##Aggregations)以及[suggestions](####Suggesters)。
>
> 大多数使用`now`（见 [Date Math](####Date Math)）的请求不能被缓存。
>
> 使用脚本的查询使用了一些不确定的API调用不会被缓存，比如说`Math.random()` 或者 `new Date()` 。

##### Cache invalidation

&emsp;&emsp;The cache is smart。它可以跟非缓存的查询一样的近实时查询。

&emsp;&emsp;当分片[refresh](###Near real-time search)后文档发生了变更或者当你更新了mapping时，缓存的结果会自动的被废止。换句话说，你总是可以从缓存中获得与未缓存搜索请求相同的结果。

&emsp;&emsp;refresh的间隔时间越长意味着缓存的结果保留时间越长，即使文档已经发生了变化。如果缓存满了，基于LRU（least recently used）机制来丢弃缓存。

&emsp;&emsp;可以通过[clear-cache API](####Clear cache API)手动将缓存置为过期的（expired）：

```text
POST /my-index-000001,my-index-000002/_cache/clear?request=true
```

##### Enabling and disabling caching

&emsp;&emsp;缓存默认开启，可以在创建一个新的索引时来关闭缓存：

```text
PUT /my-index-000001
{
  "settings": {
    "index.requests.cache.enable": false
  }
}
```

&emsp;&emsp;可以通过[update-settings](####Update index settings API) API来手动的开启或关闭缓存：

```text
PUT /my-index-000001/_settings
{ "index.requests.cache.enable": true }
```

##### Enabling and disabling caching per request

&emsp;&emsp;`request_cache` 这个query-string参数可以用于在每一次请求中来开启或关闭缓存。如果设置了这个参数，会覆盖索引层（index-levele）的设置：

```text
GET /my-index-000001/_search?request_cache=true
{
  "size": 0,
  "aggs": {
    "popular_colors": {
      "terms": {
        "field": "colors"
      }
    }
  }
}
```

&emsp;&emsp;请求中的`size`如果大于0的话则不会缓存即使在索引设置中开启了缓存。要缓存这些请求，你需要使用的query-string参数。

##### Cache key

&emsp;&emsp;使用完整的JSON计算出的hash值作为cache key。这意味着如果JSON发生了变更，比如说key的先后顺序发生了变化，那么对应的cache key就无法被识别。

> TIP：大多数 JSON 库都支持规范模式（canonical mode），可确保 JSON的key始终以相同的顺序发出。这种规范模式可以在应用程序中使用，以确保始终以相同的方式序列化请求。

##### Cache settings

&emsp;&emsp;缓存在节点层进行管理，缓存大小的最大值默认是堆内存的1%。可以通过`config/elasticsearch.yml`修改：

```text
indices.requests.cache.size: 2%
```

&emsp;&emsp;同样的你可以使用`indices.requests.cache.expire`为缓存结果设置一个TTL，但是并没有理由要这么做。因为过时的结果（stale result）会在refresh后自动的废止。这个设置只是为了completeness（This setting is provided for completeness' sake only）。

##### Monitoring cache usage

&emsp;&emsp;缓存的大小（字节）和evictions的数量可以通过[indices-stats](####Index stats API)接口来查看：

```text
GET /_stats/request_cache?human
```

&emsp;&emsp;或者通过[nodes-stats](####Nodes stats API)接口查看：

```text
GET /_nodes/stats/indices/request_cache?human
```

#### Snapshot and restore settings

##### SLM settings

###### slm.history_index_enabled

###### slm.retention_schedule

###### slm.retention_duration

###### repositories.url.allowed_urls

#### Transforms settings in Elasticsearch
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/transform-settings.html)

#### Thread pools
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-threadpool.html)

&emsp;&emsp;一个节点会使用多个线程池来管理内存消费（memory consumption）。许多的线程池使用队列hold住pending中的请求而不是丢弃。

&emsp;&emsp;节点中有很多的线程池，下面介绍主要的几个线程池：

###### generic

&emsp;&emsp;用于一般的操作（例如background node discovery）。线程池类型是[scaling](######scaling)。

###### search

&emsp;&emsp;用于count/search/suggest操作。线程池类型是[fixed](######fixed)。线程数量为**int((# of [allocated processors](#####Allocated processors setting) * 3) / 2) + 1**。队列大小为`1000`。

###### search_throttled

&emsp;&emsp;用于在`search_throttled indices`上的count/search/suggest/get操作。线程池类型为`fixed`，线程数量为`1`，队列大小为`100`。

###### search_coordination

&emsp;&emsp;用于轻量级的查询相关的协同操作（search-related coordination）。线程池类型为`fixed`，线程数量为**a max of min(5, (# of [allocated processors](#####Allocated processors setting)) / 2)**，队列大小为`1000`。

###### get

&emsp;&emsp;用于get操作，线程池类型为`fixed`，线程数量为[# of allocated processors](#####Allocated processors setting)，队列大小为`1000`。

###### analyze

&emsp;&emsp;用于analyzer请求，线程池类型为`fixed`，线程数量为`1`，队列大小为`16`。

###### write

&emsp;&emsp;用于单篇文档的 index/delete/update以及bulk的请求，线程池类型为`fixed`，线程数量为[# of allocated processors](#####Allocated processors setting)，队列大小为`10000`。这个线程池的最大值是**1 + [# of allocated processors](#####Allocated processors setting)**。

###### snapshot

&emsp;&emsp;用于snapshot/restore请求，线程池类型为[scaling](######scaling)，keep-alived的值为`5m`以及**a max of min(5, ([# of allocated processors](#####Allocated processors setting)) / 2)**。

###### snapshot_meta

&emsp;&emsp;用于读取snapshot repository metadata的请求，线程池类型为[scaling](######scaling)，keep-alived的值为`5m`以及**a max of min(50, ([# of allocated processors](#####Allocated processors setting)) / 3)**。

###### warmer

&emsp;&emsp;用于segment warm-up的操作，线程池类型为[scaling](######scaling)，keep-alived的值为`5m`以及**a max of min(5, ([# of allocated processors](#####Allocated processors setting)) / 2)**。

###### refresh

&emsp;&emsp;用于refresh的操作，线程池类型为[scaling](######scaling)，keep-alived的值为`5m`以及**a max of min(10, ([# of allocated processors](#####Allocated processors setting)) / 2)**。

###### fetch_shard_started

&emsp;&emsp;用于列出分片状态（list shard state）的操作，线程池类型为[scaling](######scaling)，keep-alived的值为`5m`以及**a default maximum size of 2 * [# of allocated processors](#####Allocated processors setting)**。

###### fetch_shard_store

&emsp;&emsp;用于列出分片存储（list shard store）的操作，线程池类型为[scaling](######scaling)，keep-alived的值为`5m`以及**a default maximum size of 2 * [# of allocated processors](#####Allocated processors setting)**。

###### flush

&emsp;&emsp;用于[flush](####Flush API)和[translog](###Translog) `fsync`的操作，线程池类型为[scaling](######scaling)，keep-alived的值为`5m`以及**a max of min(10, ([# of allocated processors](#####Allocated processors setting)) / 2)**。

###### force_merge

&emsp;&emsp;用于[force merge](####Force merge API)操作，线程池类型为[fixed](######fixed)，线程数量为`1`，队列大小为`unbounded`。

###### management

&emsp;&emsp;用于集群管理，线程池类型为[scaling](######scaling)，keep-alived的值为`5m`以及**a default maximum size of 5**。

###### system_read

&emsp;&emsp;用于读取系统索引的操作，线程池类型为[fixed](######fixed)，keep-alived的值为`5m`以及**a default max of min(5, ([# of allocated processors](#####Allocated processors setting)) / 2)**。

###### system_write

&emsp;&emsp;用于写入系统索引的操作，线程池类型为[fixed](######fixed)，keep-alived的值为`5m`以及**a default max of min(5, ([# of allocated processors](#####Allocated processors setting)) / 2)**。

###### system_critical_read

&emsp;&emsp;用于读取关键系统索引（critical system indices）的操作，线程池类型为[fixed](######fixed)，keep-alived的值为`5m`以及**a default max of min(5, ([# of allocated processors](#####Allocated processors setting)) / 2)**。

###### system_critical_write

&emsp;&emsp;用于写入关键系统索引（critical system indices）的操作，线程池类型为[fixed](######fixed)，keep-alived的值为`5m`以及**a default max of min(5, ([# of allocated processors](#####Allocated processors setting)) / 2)**。

###### watcher

&emsp;&emsp;用于[watch executions](##Watcher)。线程池类型为[fixed](######fixed)，以及**a default max of min(5 * ([# of allocated processors](#####Allocated processors setting)) , 50)**，队列大小为`50`。

&emsp;&emsp;线程池的设置是[static](######Static（settings） )，通过编辑`elasticsearch.yml`更改。下面的例子中更改了`write`线程池中的线程数量：

```text
thread_pool:
    write:
        size: 30
```

##### Thread pool types

&emsp;&emsp;下文中介绍的是线程池的类型以及对应的参数：

###### fixed

&emsp;&emsp;`fixed`类型的线程池中有固定数量的线程，以及一个队列（通常是有界的），当线程池中没有可用线程时，队列会hold住请求。

&emsp;&emsp;`size`参数控制了线程的数量。

&emsp;&emsp;`queue_size`控制了队列中的请求数量，当没有可用线程时，队列会hold住请求。默认情况下，该值为`-1`意味着队列是无界的。但一个新的请求到来时，如果队列已满则abort这个请求。

```text
thread_pool:
    write:
        size: 30
        queue_size: 1000
```

###### scaling

&emsp;&emsp;`scaling`类型的线程池中拥有动态数量的线程。线程数量跟工作负载成正比（proportional）。线程数量在参数`core`和`max`之间变化。

&emsp;&emsp;`keep_alive`参数用于决定当线程池中的线程没有任何工作时，呆在线程池中的最大时间。

```text
thread_pool:
    warmer:
        core: 1
        max: 8
        keep_alive: 2m
```

##### Allocated processors setting

&emsp;&emsp;处理器（processor）的数量会被自动的检测。线程池的会基于处理器的数量自动的设置。在有些情况下，覆盖（override）处理器的数量是很有用的。可以显示的（explicit）设置`node.processors`。

```text
node.processors: 2
```

&emsp;&emsp;以下的情况要显示的覆盖设置`node.processors`：

1. If you are running multiple instances of Elasticsearch on the same host but want Elasticsearch to size its thread pools as if it only has a fraction of the CPU, you should override the node.processors setting to the desired fraction, for example, if you’re running two instances of Elasticsearch on a 16-core machine, set node.processors to 8. Note that this is an expert-level use case and there’s a lot more involved than just setting the node.processors setting as there are other considerations like changing the number of garbage collector threads, pinning processes to cores, and so on.
2. Sometimes the number of processors is wrongly detected and in such cases explicitly setting the node.processors setting will workaround such issues.

&emsp;&emsp;若要检查检查到的处理器的数量，使用nodes info AP带上`os`这个flag。 

#### Advanced configuration
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/advanced-configuration.html#advanced-configuration)

### Important system configuration
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-discovery.html)

#### Configuring system settings
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/setting-system-settings.html)

&emsp;&emsp;根据你安装Elasticsearch时使用的安装包，以及你正在使用的操作系统，系统设置的配置位置略有不同。

&emsp;&emsp;当使用`.zip`或者`.tar.gz`的安装包时，可以通过下面的方式配置系统设置：

- 在[ulimit](#####ulimit)中临时设置
- 在[/etc/security/limits.conf](#####/etc/security/limits.conf)中永久设置

&emsp;&emsp;当使用RPM或者Debian发行版时，大多数的系统设置在[system configuration file](#####Sysconfig file)中设置，然而在使用systemd的系统中，要求在[systemd configuration file](#####Systemd configuration)中指定system limit。

##### ulimit

&emsp;&emsp;在linux系统中，`ulimit`用于临时更改资源限制（resource limit）。Limits通常需要使用root用户进行设置，然后切换到普通用户启动Elasticsearch。例如，将文件句柄（file handler）的最大数量设置为65,536（ulimit -n），你可以按照下面的方式进行设置：

```text
sudo su  
ulimit -n 65535 
su elasticsearch 
```

&emsp;&emsp;第1行，切换到root用户

&emsp;&emsp;第2行，设置打开的文件最大数量（max number of open files）

&emsp;&emsp;第3行，切换到普通用户来启动Elasticsearch

&emsp;&emsp;这个新的限制（new limit）只适用于当前会话（current session）

&emsp;&emsp;你可以通过`ulimit -a`查询（consult）当前目前所有的limit

##### /etc/security/limits.conf

&emsp;&emsp;在linux系统中，可以通过编辑文件`/etc/security/limits.conf`为linux的某个系统用户设置persistent limits。如果要为`elasticsearch`这个系统用户设置可以打开的文件最大数量为65,535，在文件中添加一行下面的内容：

```text
elasticsearch  -  nofile  65535
```

&emsp;&emsp;这个变更只有在`elasticsearch`这个用户下次打开一个新的session时才会生效。

> NOTE: Ubuntu and `limits.conf`
> 在Ubuntu中，由`init.d`启动的进程会忽略`limits.conf`文件，如果需要让这个文件生效，可以编辑`/etc/pam.d/su`然后注释下面这一行的内容：`# session    required   pam_limits.so`

##### Sysconfig file

&emsp;&emsp;当使用RPM或者Debian安装包时，可以在系统配置文件中指定系统变量：

|  RPM   | /etc/sysconfig/elasticsearch |
| :----: | :--------------------------: |
| Debian |  /etc/default/elasticsearch  |

&emsp;&emsp;然而system limits需要通过[systemd](#####Systemd configuration)来指定。

##### Systemd configuration

&emsp;&emsp;当在使用systemd的系统上使用RPM或者Debian安装包时，system limits需要通过[systemd](https://en.wikipedia.org/wiki/Systemd)来指定。

&emsp;&emsp;systemd service file（`/usr/lib/systemd/system/elasticsearch.service`）中包含了默认的limit。

&emsp;&emsp;你可以新增一个文件名为`/etc/systemd/system/elasticsearch.service.d/override.conf`（或者你可以执行`sudo systemctl edit elasticsearch`，它会在默认编辑器中自动打开文件），在这个文件中做如下的变更：

```text
[Service]
LimitMEMLOCK=infinity
```

&emsp;&emsp;完成更改后，运行下面的命令来reload units：

```text
sudo systemctl daemon-reload
```

#### Disable swapping
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/setup-configuration-memory.html)

&emsp;&emsp;大多数的操作系统会为文件系统缓存（file system cache）尽可能多的使用内存并且急切的（eagerly）将unused application memory交换出去（swap out）。这会导致部分JVM堆甚至executable page被交换到磁盘上。

&emsp;&emsp;Swapping对性能、节点稳定影响是非常大的，无论如何（at all cost）都要避免。这会导致垃圾回收的时间达到分钟级别而不是毫秒级别，使得节点的响应时间变慢甚至与节点的断开连接。在弹性分布式系统中，让操作系统杀死节点反而更有效。

&emsp;&emsp;有三种方法关闭swapping。较好的方法（prefer option）是完全的关闭swapping。如果不能用这个方法，那么基于你的环境选择最小化swappiness还是内存锁定。

##### Disable all swap files

&emsp;&emsp;通常Elasticsearch是一个box中唯一运行的服务，并且通过JVM参数来控制内存的使用，就没有必要开启swapping。

&emsp;&emsp;在linux系统中，你可以运行下面的命令临时关闭swapping：

```text
sudo swapoff -a
```

&emsp;&emsp;这个操作不需要重启Elasticsearch。

&emsp;&emsp;若要永久关闭，你需要编辑文件`/etc/fstab`，然后将所有包含`swap`的内容都注释。

&emsp;&emsp;在Windows上，可以通过`System Properties → Advanced → Performance → Advanced → Virtual memory`达到相同的目的。

##### Configure swappiness

&emsp;&emsp;要确保Linux系统上另一个可用的选项：sysctl的`vm.swappiness`的值是`1`。这个选项会降低kernel进行swap的趋势，在正常情况下（normal circumstances）不会进行swapping，并且允许在紧急情况下运行将整个系统进行swapping。

###### Enable bootstrap.memory_lock

&emsp;&emsp;另一种方式是通过在Linux/Unix系统上使用[mlockall](https://pubs.opengroup.org/onlinepubs/007908799/xsh/mlockall.html)或者是Windows系统上的[VirtualLock](https://learn.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-virtuallock?redirectedfrom=MSDN)，将进程地址空间锁定到RAM，防止Elasticsearch堆内存被swapping。

> NOTE: 一些平台在使用了memory lock后仍然会swap off-heap memory。为了防止swap off-heap memory，转而尝试[disable all swap files](#####Disable all swap files)。

&emsp;&emsp;在`elasticsearch.yml`中设置`bootstrap.memory_lock`为`true`来开启memory lock：

```text
bootstrap.memory_lock: true
```

> WARNING：如果尝试分配比可用内存更多的内存量，`mlockall`可能会导致JVM或者shell session退出。

&emsp;&emsp;在启动Elasticsearch之后，你可以通过下面这个请求输出的`mlockall`的值检查是否应用了设置：

```text
GET _nodes?filter_path=**.mlockall
```

&emsp;&emsp;如果你看到`mlockall`的值为`false`，那么意味着`mlockall`请求失败了。你可以在日志中看到包含`Unable to lock JVM Memory`的更详细的信息。

&emsp;&emsp;在Linux/Unix系统上最有可能导致失败的原因是运行中的Elasticsearch没有权限来lock memory。可以通过下面的方式授权：

- `.zip` and `.tar.gz`
  - 在启动Elasticsearch前切换到root用户并执行[ulimit -l unlimited](#####ulimit)，或者在`/etc/security/limits.conf`中设置`memlock`为`unlimited`。

```text
# allow user 'elasticsearch' mlockall
elasticsearch soft memlock unlimited
elasticsearch hard memlock unlimited
```

- RPM and Debian
  - 在[systemd configuration](#####Systemd configuration)中将`LimitMEMLOCK`设置为`infinity`

&emsp;&emsp;另一个导致`mlockall`可能的原因是[the JNA temporary directory (usually a sub-directory of /tmp) is mounted with the noexec option](####Ensure JNA temporary directory permits executables)，可以使用`ES_JAVA_OPTS`环境变量为JNA指定一个临时的目录来解决：

```text
export ES_JAVA_OPTS="$ES_JAVA_OPTS -Djna.tmpdir=<path>"
./bin/elasticsearch
```

&emsp;&emsp;或者在配置文件jvm.options中设置这个JVM标志。

#### File Descriptors
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/file-descriptors.html)

>NOTE：File Descriptors仅仅适用于Linux以及MacOs，在Windows平台上运行Elasticsearch可以忽略。在 Windows 上，JVM 使用仅受可用资源限制的 [API](https://learn.microsoft.com/zh-cn/windows/win32/api/fileapi/nf-fileapi-createfilea?redirectedfrom=MSDN)

&emsp;&emsp;Elasticsearch会使用很多file descriptors 或者 file handles。文件描述符用完可能是灾难性的，并且很可能会导致数据丢失。要确保将open files descriptors的数量提高到65,536或者更高用于Elasticsearch的运行。

&emsp;&emsp;对于`.zip`和`.tar.gz`的安装包，在Elasticsearch启动前使用root用户执行`ulimit -n 65535`，或者在`/etc/security/limits.conf`设置`nofile`的值为`65535`。

&emsp;&emsp;在MacOs平台上，你必须要使用JVM参数`-XX:-MaxFDLimit`让Elasticsearch使用更高的file descriptors限制。

&emsp;&emsp;RPM和Debian已经默认将file descriptors的最大值设置为65535并且不需要进一步的配置。

&emsp;&emsp;你可以使用[Nodes stats](####Nodes stats API) API检查每一个节点上的`max_file_descriptors`：

```text
GET _nodes/stats/process?filter_path=**.max_file_descriptors
```

#### Virtual memory
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/vm-max-map-count.html)

&emsp;&emsp;Elasticsearch默认使用[mmapfs](######mmapfs)存储索引文件。默认情况下操作系统会将mmap count限制的太低导致OOM的异常。

&emsp;&emsp;在Linux下，你可以使用root通过下面的命令来提高上限：

```text
sysctl -w vm.max_map_count=262144
```

&emsp;&emsp;可以在`/etc/sysctl.conf`中更新`vm.max_map_count`来永久的设置该值。在重启后使用`sysctl vm.max_map_count`命名来验证刚才的变更。

&emsp;&emsp;RPM跟Debian安装包会自动进行配置。不需要进行额外的配置。

#### Number of threads
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/max-number-of-threads.html)

&emsp;&emsp;Elasticsearch会使用线程池用于不同类型的操作。重要的是当有需要时就会创建新的线程。保证Elasticsearch能创建的线程数量至少可以有4096个。

&emsp;&emsp;在启动Elasticsearch前切换到root设置[ulimit -u 4096](#####ulimit)，或者在[/etc/security/limits.conf](#####/etc/security/limits.conf)中将`nproc`设置为4096。

&emsp;&emsp;在`systemd`下运行的service会自动的为Elasticsearch进程设置线程数量。不需要额外的配置。 

#### DNS cache settings
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/networkaddress-cache-ttl.html)

#### Ensure JNA temporary directory permits executables
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/executable-jna-tmpdir.html)

#### TCP retransmission timeout
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/system-config-tcpretries.html)

### Bootstrap Checks
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/bootstrap-checks.html)

#### Development vs. production mode

### Discovery and cluster formation
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-discovery.html)

&emsp;&emsp;Discovery and cluster formation负责节点发现，选举master，形成集群，以及每一次发生变更时集群状态的发布。

&emsp;&emsp;下面的processes和settings是discovery and cluster formation的 一部分：

- [Discovery](####Discovery)
  - Discovery是当master未知时，节点之间用来相互发现的步骤，例如一个节点刚刚启动或者当上一个master node发生故障时

- [Quorum-based decision making](####Quorum-based decision making)
  - Elasticsearch如何基于quorum-based voting mechanism来做出决策，即使一些节点发生了故障

- [Voting configurations](####Voting configurations)
  - Elasticsearch在节点加入以及离开集群时如何自动的更新voting configuration

- [Bootstrapping a cluster](####Bootstrapping a cluster)
  - 当Elasticsearch集群第一次启动时Bootstrapping a cluster是必须。在[development node](####Development vs. production mode)中，如果没有配置discovery settings，这将由节点本身自动执行。由于auto-bootstrapping是[inherently unsafe](####Quorum-based decision making)，在[production mode](####Development vs. production mode)中要求节点显示配置（[explicitly configure](####Bootstrapping a cluster)）bootstrappping。

- [Adding and removing master-eligible nodes](####Adding and removing nodes)
  - 建议在集群中设置一个小规模的并且固定数量的master-eligible node，只通过添加或者移除master-ineligible node来扩大或者缩小集群。然而也有一些情况需要将一些master-eligible node 添加到集群，或者从集群中移除。 这块内容描述的是添加或者移除master-eligible node的过程，包括在同一时间移除超过一半数量的master-eligible node时额外必须要执行的步骤。

- [Publishing the cluster state](####Publishing the cluster state)
  - Cluster state publishing描述的是被选举为master的node更新集群中所有节点的集群状态的过程

- [Cluster fault detection](####Cluster fault detection)
  - Elasticsearch执行健康检查来检测以及移除有故障的节点

- [Settings](####Discovery and cluster formation settings)
  - 让用户通过设置来影响（influence）discovery，cluster formation，master election和fault detection processes

#### Discovery
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-discovery-hosts-providers.html)

&emsp;&emsp;Discovery是cluster formation module发现其他节点并形成一个集群的过程。这个过程发生在当你启动一个Elasticsearch节点或者当一个节点认为master node发生故障并且等到发现master node或者选出新的master node后继续运行时。

&emsp;&emsp;这个过程和上一次已知的集群中的master-eligible node的地址一起，开始于一个或者多个[seed hosts providers](#####Seed hosts providers)中提供的seed address列表。这个过程有两个阶段：首先，通过连接每一个地址来尝试明确节点是否连接并且验证是否为master-eligible node。然后如果成功了，它会共享它已知的master-eligible node的remote node并且等待响应。然后该节点探测（prove）它刚刚发现的所有新节点，请求它们的对等节点（peer），等等。

&emsp;&emsp;如果当前节点不是master-eligible node，那它会继续处理直到它发现了被选举为master的node。如果没有发现master node那么这个节点将在`discovery.find_peers_interval`后重试，默认值是`1s`。

&emsp;&emsp;如果当前节点是master-eligible node，那它会继续处理直到它要么发现了一个master node，要么已经发现了足够多masterless master-eligible node并完成master的选举。如果这两个都没有发生，则在`discovery.find_peers_interval`后重试，默认值是`1s`。

&emsp;&emsp;一旦一个master被选举出来，通常情况下它会一直是master node，直到它被故意（deliberate）停止。如果[falult detection](####Cluster fault detection)发现集群发生了故障，也有可能作为master并停止。如果一个节点不是master node，它则继续discovery的过程。

##### Troubleshooting discovery

&emsp;&emsp;大多数情况下，discovery和选举的过程会很快完成。并且master node在很长的一段时间内都是会master。

&emsp;&emsp;如果你的集群没有一个稳定（stable）的master，许多的功能不能正确工作。那么Elasticsearch会报出错误给客户端并记录日志。你必须在解决其它问题前先处理master node的不稳定问题。master node选举出来之前或者master node稳定前将无法解决其他任何的问题。

&emsp;&emsp;如果你有一个稳定的master node但是一些节点不能发现它或者加入，这些节点会报出错误给客户端并记录日志。在解决其它问题前你必须先解决这些节点无法加入到集群的障碍（obstacle）。节点不能加入集群前将无法解决其他任何问题。

&emsp;&emsp;如果集群在超过好几秒后都没有选举出master，说明master不稳定或者其他节点无法发现或者加入join a stable master。那Elasticsearch会在日志中记录其原因。如果这个问题超过好几分钟还存在，Elasticsearch会在日志中记录额外的日志。若要正确的处理discovery和选举问题，可以收集并分析所有节点上最新的5分钟内的日志。

&emsp;&emsp;下面的内容介绍了一些常见的discovery和选举问题。

###### No master is elected

&emsp;&emsp;当一个节点赢的选举，日志中会包含`elected-as-master`的信息并且所有的节点会记录一条包含`master node changed`的信息来标识最新赢的选举的master node。

&emsp;&emsp;如果没有选出master node并且没有节点赢得选举，所有的节点会重复的在日志中记录问题：`org.elasticsearch.cluster.coordination.ClusterFormationFailureHelper`。默认情况下，每10秒发生一次。

&emsp;&emsp;master的选举只会涉及master-eligible node，所以这种情况下集中查看master-eligible node的日志。这些节点的日志会说明master选举的要求，比如用于discovery的相关节点。

&emsp;&emsp;如果日志显示Elasticsearch不能从quorum中发现足够多的节点。你必须解决阻止Elasticsearch发现缺失节点的原因。缺失的节点需要用于重新构造（reconstruct）cluster metadata。没有cluster metadata，你集群中的数据是没有意义的（meaningless）。cluster metadata存储在集群中master-eligible node的子集中。如果不能发现quorum ，那么缺失的节点是拥有cluster metadata的节点。

&emsp;&emsp;保证有足够多的节点来形成quorum并且每个节点能相互通过网络联系到对方。如果选举问题持续了好几分钟，Elasticsearch会额外的报出网络连接的细节。如果你不能启动足够多的节点来形成quorum，那么启动一个新的集群并且从最新的snapshot中恢复数据。参考[Quorum-based decision making ](####Quorum-based decision making)了解更多信息。

&emsp;&emsp;如果日志显示Elasticsearch已经发现了 a possible quorum of nodes。那么集群不能选出master的原因通常是其中的节点不能发现quorum。检查其他master-eligible node上的日志并且保证他们已经发现了足够多的节点来形成一个quorum。

###### Master is elected but unstable

&emsp;&emsp;当一个节点赢得了选举，它的日志中会包含`elected-as-master`的信息。如果重复出现这个日志，说明选举出的节点不稳定。在这种情况下，集中在master-eligible node中的日志中查看为什么选举出的胜利者后来又停止成为master并且又触发另一个选举的原因。

###### Node cannot discover or join stable master

&emsp;&emsp;如果选举出的master node是一个稳定的节点，但是某个节点不能发现并加入到集群，它会在日志中使用`ClusterFormationFailureHelper`重复的报出日志。其他受到影响的节点和选举为master的节点上可能会有一些关于这个问题的额外的日志。

###### Node joins cluster and leaves again

&emsp;&emsp;如果某个节点加入到了集群但是Elasticsearch认为它出了故障并随后将再次从集群中移除。见[Troubleshooting an unstable cluster](#####Troubleshooting an unstable cluster)了解更多信息。

##### Seed hosts providers

&emsp;&emsp;默认情况下，cluster formation module提供了两个seed hosts provider来配置seed node列表：[settings-based](######Settings-based seed hosts provider)和[file-based](######File-based seed hosts provider)。可以扩展为支持云环境以及通过[discovery plugins](https://www.elastic.co/guide/en/elasticsearch/plugins/8.2/discovery.html)的其他形式的seed hosts provider。seed hosts provider使用`discovery.seed_providers`进行配置，这个是settings-base 这种provider的默认方式。这个设置接受一个不同的provider的列表，允许你能用多种方式来为你的集群找到seed hosts。

&emsp;&emsp;每一个seed hosts provider提供（yield）seed node的IP地址或者hostname。如果返回的是hostname，那么需要使用DNS来查找对应的IP地址。如果一个hostname对应了对个IP地址，那么Elasticsearch会尝试找到所有的地址。如果hosts provider没有显示的（explicit）给出节点的TCP port，则会隐式的（implicit）使用port range中的第一个：`transport.profiles.default.port`或者`transport.port`（如果没有设置`transport.profiles.default.port`）。通过DSN查找IP地址的并发数量由`discovery.seed_resolver.max_concurrent_resolvers`控制，默认值是`10`。超时时间由`discovery.seed_resolver.timeout`控制，默认值是`5s`。注意的是DNS查找IP地址受限于[JVM DNS caching](####DNS cache settings)。

###### Settings-based seed hosts provider

&emsp;&emsp;Settings-based seed hosts provider使用了节点设置来配置一个静态的seed nodes的地址列表。这些地址可以是hostname或者是IP地址。使用hostname时会在每一次discovery时去查找对应的IP地址。

&emsp;&emsp;下面使用了[discovery.seed_hosts](######discovery.seed_hosts)静态设置：

```text
discovery.seed_hosts:
   - 192.168.1.10:9300
   - 192.168.1.11 
   - seeds.mydomain.com 
```

&emsp;&emsp;第3行，将默认使用`transport.profiles.default.port`，如果没有配置`transport.profiles.default.port`则使用`transport.port`

&emsp;&emsp;第4行，如果这个hostname对应了多个IP地址，Elasticsearch会尝试连接每一个IP地址

###### File-based seed hosts provider

&emsp;&emsp;File-based seed hosts provider通过外部的文件来配置hosts列表。这个文件发生变更后，Elasticsearch就会重新加载，所以不需要重启节点就可以使用变更后的seed nodes的地址。例如运行在Docker容器中的Elasticsearch就可以很方便的在节点启动时，如果给定的IP地址无法连接时就可以动态的提供。

&emsp;&emsp;若要开启file-based的discovery，那么在`elasticsearch.yml`中配置`file`即可：

```text
discovery.seed_providers: file
```

&emsp;&emsp;然后按照下面的格式在`$ES_PATH_CONF/unicast_hosts.txt`中创建一个文件。任何时刻在`unicast_hosts.txt`文件中发生的变更都会被Elasticsearch捕获并使用新的hosts列表。

&emsp;&emsp;注意的是file-based discovery 插件增强了`elasticsearch.yml`中的unicast hosts list：如果在`discovery.seed_hosts`中有合法的seed address，那么Elasticsearch会使用这些地址并额外使用`unicast_hosts.txt`中的地址。

&emsp;&emsp;`unicast_hosts.txt`文件中每一行是一个node entry。每一个node entry由host（hostname或者IP地址）和一个可选的transport port 组成。如果指定了port，必须是写在host后面并且用`:`分隔。如果没有指定port，默认使用`transport.profiles.default.port`，如果没有配置`transport.profiles.default.port`则使用`transport.port`。

&emsp;&emsp;例如，下面的`unicast_hosts.txt` 中有四个node参与discovery，其中一些使用了默认的端口号：

```text
10.10.10.5
10.10.10.6:9305
10.10.10.5:10005
# an IPv6 address
[2001:0db8:85a3:0000:0000:8a2e:0370:7334]:9301
```

&emsp;&emsp;可以使用hostname来代替IP地址，但是需要上文描述的DNS。IPv6的地址必须要给定端口号。

&emsp;&emsp;你可以在文件中添加注释， 所有的注释必须以`#`开头

###### EC2 hosts provider

&emsp;&emsp;[EC2 discovery plugin](https://www.elastic.co/guide/en/elasticsearch/plugins/8.2/discovery-ec2.html)使用[AWSAPI](https://github.com/aws/aws-sdk-java)来 查找seed node。

###### Azure Classic hosts provider

&emsp;&emsp;[Azure Classic discovery plugin](https://www.elastic.co/guide/en/elasticsearch/plugins/8.2/discovery-azure-classic.html)使用Azure Classic API来查找seed node。

###### Google Compute Engine hosts provider

&emsp;&emsp;[GCE discovery plugin ](https://www.elastic.co/guide/en/elasticsearch/plugins/8.2/discovery-gce.html)使用GCE API来查找seed node。

#### Quorum-based decision making
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-discovery-quorums.html#modules-discovery-quorums)

&emsp;&emsp;选举出master node和变更集群状态是两个基本的任务，需要所有的master-eligible node一起工作执行。重要的是即使一些节点发生了故障也要让这些工作具有鲁棒性。Elasticsearch中达到这个鲁棒性的方式是：考虑每一个从`quorum`中接受到成功的响应。`quorum`即集群中的master-eligible node集合。只需要一部分节点响应的好处在于在一些节点发生故障后也不会影响集群继续工作。quorum是经过谨慎选择的，这样集群中不会出现裂脑（split brain）的场景：集群被划分为两块，每一块会作出与另一块不一样的决策。

&emsp;&emsp;Elasticsearch允许你向正在运行中的集群添加或者移除master-eligible node。在很多场景中只要按需简单的启动或者节点即可。见[Adding and removing nodes](###Adding and removing nodes)。

&emsp;&emsp;通过更新集群中的[voting configuration](####Voting configurations)使得在添加/移除节点时，Elasticsearch能维护一个最佳的容错能力（Elasticsearch maintains an optimal level of fault tolerance）。voting configuration是master-eligible的集合，它们的响应会被计数用来对选举出新的master和提交（commit）新的集群状态做出决策。只有voting configuration中的超过半数才能做出决策。通常情况下，voting configuration跟当前集群中所有的master-eligible集合是相同的，然而在有些情况下会有不同。

&emsp;&emsp;为了保证集群仍然可用（remain available），你必须不能在同一时间（at the same time）停止一半或者更多的voting configuration中的节点。只要超过一半的voting node是可用的，集群就能正常的工作。例如，如果有3个或者4个master-eligible node，集群可以容忍一个不可用的节点。如果2个或者更少的master-eligible node，他们必须都是可用的。

&emsp;&emsp;一个节点加入/离开集群时，选举为master的节点必须发布（issue）集群状态的更新，调整（adjustment）voting configuration，这个过程可能会花费一点时间。在移除更多节点之前等待调整结束是非常重要的。

##### Master elections

&emsp;&emsp;Elasticsearch在启动阶段以及当master node发生故障时使用选举的处理方式来达成一致选出master node。任何的master-eligible都可以开始一个选举，通常来说第一次选举都会成功（normally the first election that takes place will succeed）。只有当两个节点碰巧同时开始选举时，选举才会失败，所以每个节点上的选举都是随机安排的，以降低这种情况发生的概率。节点会重试选举，直到选举出master node。backing off on failure，使得选举会最终的成功。The scheduling of master elections are controlled by the [master election settings](######cluster.election.back_off_time)。

##### Cluster maintenance, rolling restarts and migrations

&emsp;&emsp;许多的集群维护任务包括临时的关闭一个或者多个节点然后重新启动它们。默认情况下，如果其中一个master-eligible node下线了，Elasticsearch仍然可用。比如在[rolling upgrade](####Rolling restart)期间。如果多个节点停止并且再次启动，Elasticsearch会自动的进行恢复，例如在[full cluster restart](####Full-cluster restart)期间。上述说到的场景中，不需要使用APIs来做出额外的措施，because the set of master nodes is not changing permanently。

#### Voting configurations
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-discovery-voting.html)

&emsp;&emsp;每一个Elasticsearch集群中都有一个voting configuration，通过[master-eligible nodes](#####Master-eligible node)集合的投票计数来作出一些决策，例如选举出新的master或者提交一个新的集群状态。决策只有在voting configuration 中节点大多数都响应后（超过半数）可以通过。	

&emsp;&emsp;通常情况下voting configuration和当前集群中所有的master-eligible node集合相同。然而在某些情况下可能会不同。

> IMPORTANT：为了保证集群仍然可用（remain available），你必须不能在同一时间（at the same time）停止一半或者更多的voting configuration中的节点。只要超过一半的voting node是可用的，集群就能正常的工作。例如，如果有3个或者4个master-eligible node，集群可以容忍一个不可用的节点。如果2个或者更少的master-eligible node，他们必须都是可用的。

&emsp;&emsp;在某个节点加入或者离开集群时，Elasticsearch会自动的对voting configuration作出对应的变更来保证集群尽可能的具有弹性（resilient）。非常重要的一点是：你从集群中移除更多节点之前，要等待调整（adjustment）完成，见[Adding and removing nodes.](###Adding and removing nodes)。

&emsp;&emsp;当前的voting configuration存储在集群状态中，你可以按照以下的方式来查看当前的内容：

```text
GET /_cluster/state?filter_path=metadata.cluster_coordination.last_committed_config
```

> NOTE：当前的voting configuration不需要跟集群中的所有的可用的master-eligible node集合相同。改变（alert）voting configuration会涉及进行投票，所以节点加入或者离开集群时会花一点时间来调整configuration。同样的，也会存在most resilient configuration包含不可用的节点或者不包含可用的节点。在这种情况下，voting configuration就会跟集群中的master-eligible node不相同。

&emsp;&emsp;规模大（large）的voting configuration 通常更具有弹性。所以Elasticsearch更喜欢（prefer to）在master-eligible node加入到集群后把他们添加到voting configuration中。同样的，如果一个在voting configuration中的节点离开了集群并且集群中有另一个不在voting configuration中的master-eligible node，那么会去交换这两个节点。voting configuration的大小没有发生变化但是提高了弹性。

&emsp;&emsp;节点离开集群后，从voting configuration中移除节点不是一件简单的事情。不同的策略有不同的好处跟缺点，所以正确的选择取决于集群如何被使用。你可以通过[cluster.auto_shrink_voting_configuration setting](######cluster.auto_shrink_voting_configuration)来控制voting configuration是否要自动的收缩（shrink）。

> NOTE：如果`cluster.auto_shrink_voting_configuration`设置为`true`（默认值，推荐值）。那么集群中会至少有3个master-eligible node。Elasticsearch仍然可以处理集群状态的更新，只要除了其中一个maste-eligible node，其他的所有节点都是健康的。

&emsp;&emsp;在某些情况下，Elasticsearch能够容忍多个节点的丢失， but this is not guaranteed under all sequences of failures。如果`cluster.auto_shrink_voting_configuration`设置为`false`。你必须手动的去voting configuration中移除离开集群的节点（departed node）。使用[voting exclusions API ](####Voting configuration exclusions API)来达到想要的弹性等级。

&emsp;&emsp;无论怎么配置，Elasticsearch都不会受到裂脑导致不一致的困扰。`cluster.auto_shrink_voting_configuration`这个设置只会只会影响在一些节点发生故障后的可用性问题，以及影响节点离开/加入集群时 administrative task的执行。

##### Even numbers of master-eligible nodes

&emsp;&emsp;正常来说集群中应该有奇数个master-eligible node。如果是偶数个，Elasticsearch会将其中一个排除在voting configuration以外来保证奇数个。这种omission不会降低集群的容错能力。事实上还有略微提升：如果集群受到网络分区的影响，将其划分为大小相等的两部分，那么其中一部分将包含大多数voting configuration，并能够继续运行。如果所有的master-eligible node的投票都进行了统计，那么任何一方都不会包含严格多数的节点，因此集群将无法做任何处理（make any progress）。

&emsp;&emsp;例如，如果在集群中有4个master-eligible node并且voting configuration包含这四个节点。任何quorum-based的决策都要求至少三个投票。这意味着这个集群可以容忍单个master-eligible node的丢失。如果集群被两等分，没有一半可以获得三个master-eligible node，集群就不会做任何处理。如果voting configuration只有三个master-eligible node，集群仍然可以容忍丢失一个节点，但是quorum-based的决策要求3个投票节点中的2个。即使发生了分裂，其中一半会包含3个投票节点的2个节点，所以这一半仍然是可用的。

##### Setting the initial voting configuration

&emsp;&emsp;当一个全新的集群第一次启动，必须选出第一个master node。要完成这个选举，需要知道用于投票计数的master-eligible集合。这个初始化的voting configuration即`bootstrap configuration`并且在[cluster bootstrapping process](####Bootstrapping a cluster)中设置。

&emsp;&emsp;重要的一点是`bootstrap configuration`明确指定了在第一次选举中参与的节点。 It is not sufficient to configure each node with an expectation of how many nodes there should be in the cluster. It is also important to note that the bootstrap configuration must come from outside the cluster: there is no safe way for the cluster to determine the bootstrap configuration correctly on its own。

&emsp;&emsp;如果boostrap configuration没有正确设置，当你启动一个全新的集群时，会有形成两个不同集群的风险。这种情况会导致数据丢失：很有可能在你注意到这个问题前已经启动了2个集群并且不可能在随后合并这两个集群。

> NOTE：To illustrate the problem with configuring each node to expect a certain cluster size，想象一下启动一个三个节点的集群，每一个节点知道自己将成为三个节点集群中的一员。三个节点的大多数是2，所以正常情况下前两个启动的节点能发现对方并且形成一个集群，并且第三个节点会在随后很短的时间内加入它们。然而想象下如果错误当启动了四个节点而不是三个。在这种情况下，就会有足够的节点来形成两个集群。当然，如果每一个节点是手动启动的，那不大可能启动太多的节点。如果使用了自动化编排（orchestrator），这当然有可能会发生这种情况。特别是编排器不具备弹性（resilient）而导致网络分区的错误。

&emsp;&emsp;最初的quorum只有在整个集群最开始启动的时候需要。加入到一个已经建立好的集群的节点可以安全的从被选举为master节点上获取所有的信息。注意的是之前作为集群一部分的节点将在重新启动时会将所有需要的信息存储到磁盘中。

#### Bootstrapping a cluster
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-discovery-bootstrap-cluster.html)

&emsp;&emsp;最开始启动Elasticsearch时要求一个集群中的一个或者多个初始化的[master-eligible node](#####Master-eligible node)集合，需要显示的定义，即`cluster bootstrapping`。仅要求在集群第一次启动时：已经加入到一个集群的节点会将信息存储在它们的数据目录中用于[full cluster restart](###Full cluster restart upgrade)并且新加入到集群的节点会从集群中被选举master的节点上获取信息。

&emsp;&emsp;最初的master-eligible node集合定义在[cluster.initial_master_nodes setting](######cluster.initial_master_nodes)中。配置中要包含每一个master-eligible node的下面其中一个条目：

- 节点的[node name](#####Node name setting)
- 如果没有设置`node.name`则要求有节点的hostname。因为`node.name`默认就是节点的hostname。你可以使用fully-qualified hostname或者bare hostname，取决于[depending on your system configuration](#####Node name formats must match)。
- 如果无法使用`node.name`，则使用节点的[transport publish address](#####Binding and publishing)的IP地址。通常是[network.host](#####Commonly used network settings)对应的IP地址，但是[this can be overridden](#####Advanced network settings)
- 节点的publish address的IP地址跟端口号。如果无法使用`node.name`并且多个节点共享同一个IP地址，可以使用的格式为：`IP:PORT`。

&emsp;&emsp;在你启动一个master-eligible node时，你可以通过命令行或者`elasticsearch.yml`的方式提供这个设置。集群形成之后，将这个设置从每一个节点中移除。不能对master-**ineligible** node，已经加入集群的master-eligible node以及重启一个或者多个节点配置这个设置。

&emsp;&emsp;技术上来讲只在集群中的单个master-eligible node上设置`cluster.initial_master_nodes`就足够了并且只在设置中提及这个单个节点。但是会在集群完全形成前缺乏容错能力。因此最好使用三个master-eligible node进行引导，每一个节点中都配置了包含三个节点的`cluster.initial_master_nodes`。

> WARNING：你必须在每一个节点上设置完全相同的`cluster.initial_master_nodes`，使得在引导期间只会形成一个集群，避免数据丢失的风险。

&emsp;&emsp;对于拥有三个master-eligible node（[node name](#####Node name setting)是 master-a, master-b and master-c）的集群，配置如下：

```text
cluster.initial_master_nodes:
  - master-a
  - master-b
  - master-c
```

&emsp;&emsp;跟其他的节点设置一样，也可以通过命令行的方式指定the initial set of master nodes 来启动Elasticsearch：

```text
bin/elasticsearch -E cluster.initial_master_nodes=master-a,master-b,master-c
```

##### Node name formats must match

&emsp;&emsp;`cluster.initial_master_nodes`列表中使用的node name必须和节点的属性`node.name`完全一样。默认情况下node name被设置为机器的hostname，这个值在你的系统配置中可能不是fully-qualified。如果每一个node name是fully-qualified domain name，例如`master-a.example.com`，那你在`cluster.initial_master_nodes`列表中也必须使用fully-qualified domain name。如果你的node name是bare hostname (without the .example.com suffix) ，那你在`cluster.initial_master_nodes`列表中也必须使用bare hostname。如果你同时使用了fully-qualified 和 bare hostnames，或者其他无法匹配node.name 和 cluster.initial_master_nodes的值，那集群无法成功形成并会看到下面的日志信息：

```text
[master-a.example.com] master not discovered yet, this node has
not previously joined a bootstrapped (v7+) cluster, and this
node must discover master-eligible nodes [master-a, master-b] to
bootstrap a cluster: have discovered [{master-b.example.com}{...
```

&emsp;&emsp;这个消息显示了node name：`master-a.example.com` 和 `master-b.example.com`以及`cluster.initial_master_nodes`中的值：`master-a`和`master-b`。日志中明显的指出它们无法完全匹配。

##### Choosing a cluster name

&emsp;&emsp;[cluster.name](#####Cluster name setting)这个设置能让你创建多个彼此相互隔离的集群。节点之间在第一次相互连接时会验证集群名。Elasticsearch只会形成一个所有节点的集群名都相同的集群。默认值是`elasticsearch`，不过建议修改这个值来体现出集群的逻辑名称。

##### Auto-bootstrapping in development mode

&emsp;&emsp;默认情况下，每一个节点在第一次启动时会自动的引导（bootstrap）自己进入一个单节点的集群。如果配置了下面任意的一个设置，自动引导会被替代：

- `discovery.seed_providers`
- `discovery.seed_hosts`
- `cluster.initial_master_nodes`

&emsp;&emsp;若要添加一个节点到现有的一个集群中，那么配置`discovery.seed_hosts`或者其他相关的设置，这样新的节点就可以发现集群中现有的master-eligible节点。若要引导一个多节点的集群，配置上文中[section on cluster bootstrapping](####Bootstrapping a cluster)描述的`cluster.initial_master_nodes`。

###### Forming a single cluster

&emsp;&emsp;一旦Elasticsearch的节点加入到一个现有的集群，或者引导出一个集群后，他不会加入到其他的集群。Elasticsearch不会将两个不同的已经形成的集群进行合并，即使你随后尝试配置所有的节点到一个节点中。这是因为没法在不丢失数据的情况下合并不同的集群。你可以通过`GET /`在每一个节点上获取集群的UUID来检查形成的不同集群。

&emsp;&emsp;如果你想要添加一个节点到现有的集群中而不是引导出不同的单节点集群，那你必须：

1. 关闭节点
2. 通过删除[data folder](######path.data)的方式来完全擦除（wipe）节点
3. 配置`discovery.seed_hosts`或者 `discovery.seed_providers`以及其他相关的discover 设置
4. 重启节点并且验证下节点是否加入到集群而不是形成自己的单节点集群

&emsp;&emsp;如果你是要形成一个新的多节点集群而不是引导出多个单节点集群，那你必须

1. 关闭所有的节点
2. 通过删除每一个节点的[data folder](######path.data)的方式来完全擦除（wipe）所有的节点
3. 按照上文描述的方式来配置`cluster.initial_master_nodes`
4. 配置`discovery.seed_hosts`或者 `discovery.seed_providers`以及其他相关的discover 设置
5. 重启所有的节点并验证它们形成了单个集群

#### Publishing the cluster state
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/cluster-state-publishing.html)

&emsp;&emsp;master node是集群中唯一可以对集群状态（cluster states）做出变更的节点。master node一次处理一批集群状态的更新。计算变更请求并更新集群状态到集群中的所有节点。每一次的发布开始于master向集群中所有的节点广播更新后的集群状态。每一个节点都响应确认，但未应用最新接收的状态。一旦master收集到足够的来自master-eligible node的确认，这个新的集群状态被认为是提交（commit）了，然后master node广播另一个消息让节点应用提交的集群状态。每一个节点收到这条消息后，应用更新后的状态，然后发送一个确认给master。

&emsp;&emsp;master允许在有限的时间内完成集群状态更改到发布给所有的节点。定义在`cluster.publish.timeout`设置中，默认值`30s`。从publication开始算时间。如果在新的集群状态提交前达到了这个时间，集群状态的变更会被reject然后master会认为自己出现了故障。然后开始新的选举。

&emsp;&emsp;如果在达到`cluster.publish.timeout`之前新的集群状态提交了，master会认为这次变更成功了。master会等待所有节点应用更新状态后的确认或者等待超时，然后开始处理并发布下一次的集群状态更新。如果master没有收到一些确认（比如一些节点还没有确认他们已经应用了当前的更新）这些节点被认为是它们的集群状态落后于master最新的状态。默认值是`90s`。如果仍然没有成功的应用集群状态更新，那么它会被认为发生了故障并且从集群中移除。

&emsp;&emsp;发布的集群状态的更新内容通常是跟上一次状态的差异，这样能减低时间以及网络带宽。例如为集群状态中部分索引的更新mappings时，只要节点上有上一次的集群状态，那么就可以只发布这些索引更新对应的差异到集群中的节点上。如果一个节点缺失了上一个集群状态，比如说重新加入到了集群，master会发送完整的集群状态到这个节点，使得这个节点后续能接收差异更新。

> NOTE：Elasticsearch是基于点对点的系统，节点之间相互直连。high-throughput的APIs（index，delete，Search）通常不会跟master node交互。master node负责全局的集群状态并且当节点加入或者离开时重新分配分片。每次集群状态发生变化后，最新的状态会被发布到集群中的节点上

#### Cluster fault detection
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/cluster-fault-detection.html)

&emsp;&emsp;被选举为master的节点会周期性的检查集群中的每个节点来保证它们仍是连接的并且健康的。每一个节点同样周期性的检查master node的健康。这些检查分别被称为` follower checks`和`leader checks`。

&emsp;&emsp;Elasticsearch允许这些检查偶尔的出现失败或者超时的问题并且不采取任何行动。只有在连续多次检查（consecutive check）失败后才会认为这个节点发生了故障。你可以通过[cluster.fault_detection.\* settings](####Discovery and cluster formation settings)来控制故障检测的行为。

&emsp;&emsp;如果master node检测到某个节点失去了连接，这种情况会被视为立即失败（immediate failure）。master会绕过（bypass）超时和重试这些设置，并将这个节点从集群中移除。同样的，如果某个节点检测到master node失去了连接，这种情况也会被视为立即失败。这个节点会绕过（bypass）超时和重试这些设置，尝试discovery阶段来尝试找到/选举一个新的master。

&emsp;&emsp;另外，每一个节点会周期性的往data path中写入一个小文件然后删除它来验证data path是否健康。如果节点发现它的data path不健康，随后会从集群中移除直到data path恢复。你可以通过[monitor.fs.health settings](####Discovery and cluster formation settings)来控制这个行为。

&emsp;&emsp;如果某个节点在一个合理的时间内无法应用更新后的集群状态，master node也会将其从集群中移除。超时时间默认是2分钟，这个时间从更新集群状态开始。参考[Publishing the cluster state](####Publishing the cluster state)了解更多信息。

##### Troubleshooting an unstable cluster

&emsp;&emsp;一般情况下，节点只有在故意关闭（deliberately shutdown）时才离开集群。如果某个节点意外的（unexpectedly）离开集群，解决这个问题是非常重要的。集群中的节点意外的离开集群会造成集群不稳定并且会产生一些问题。比如说：

- 集群的健康可能是黄色或者红色
- 一些分片可能会初始化并且其他的分片可能会失败
- 查询，索引以及监控可能会失败并且在日志中抛出异常
- 索引`.security`可能会不可用，阻塞集群的访问
- master node可能会由于频繁的集群状态更新变的忙碌

&emsp;&emsp;若要解决集群中的问题，首先要保证集群中有一个[stable master](#####Troubleshooting discovery)，优先于其他的问题，集中精力关注意外退出的节点。直到集群中有一个稳定的master node以及稳定的节点成员才有可能解决其他的问题。

&emsp;&emsp;诊断数据（Diagnostics）和统计数据在一个不稳定的集群中通常是没有参考价值的。这些工具只是实时的在某个时间给出集群状态的视图。应该查看一段时间内集群的行为模式（pattern of behavior）。特别要关注master node上的日志，当某个节点离开集群时，master node的日志中会有以下类似的信息（为了可读性添加了换行符）

```text
[2022-03-21T11:02:35,513][INFO ][o.e.c.s.MasterService    ]
    [instance-0000000000] node-left[
        {instance-0000000004}{bfcMDTiDRkietFb9v_di7w}{aNlyORLASam1ammv2DzYXA}{172.27.47.21}{172.27.47.21:19054}{m}
            reason: disconnected,
        {tiebreaker-0000000003}{UNw_RuazQCSBskWZV8ID_w}{bltyVOQ-RNu20OQfTHSLtA}{172.27.161.154}{172.27.161.154:19251}{mv}
            reason: disconnected
        ], term: 14, version: 1653415, ...
```

&emsp;&emsp;这个消息说的是master node(instance-0000000000)上的`MasterService`正在处理一个`node-left`的任务。并列出了被移除的节点以及移除的原因。其他的节点可能有类似的日志，但是细节较少：

```text
[2020-01-29T11:02:36,985][INFO ][o.e.c.s.ClusterApplierService]
    [instance-0000000001] removed {
        {instance-0000000004}{bfcMDTiDRkietFb9v_di7w}{aNlyORLASam1ammv2DzYXA}{172.27.47.21}{172.27.47.21:19054}{m}
        {tiebreaker-0000000003}{UNw_RuazQCSBskWZV8ID_w}{bltyVOQ-RNu20OQfTHSLtA}{172.27.161.154}{172.27.161.154:19251}{mv}
    }, term: 14, version: 1653415, reason: Publication{term=14, version=1653415}
```

&emsp;&emsp;集中关注master node上`MasterService`中抛出的日志，它包含更多的细节。如果你无法查看到来自`MasterService`的日志，检测下面的内容：

- 你正在查看的是被选举为master的节点上的日志
- 日志记录的时间段是正确的
- 日志等级需要`INFO`

&emsp;&emsp;节点在开始/结束 follow一个master node时会记录包含`master node changed`的日志，你可以使用这些信息来观察一段时间内的view of the state of the master 。

&emsp;&emsp;节点重启的会先离开集群然后再次加入到集群中。重新加入后，`MasterService`会记录它正在处理`node-join`的任务。你可以从master的日志中得知节点重启的信息，因为`node-join`相关日志描述的是节点的`joining after restart`。在较老的Elasticsearch版本中，你可以通过查看`node-left`和`node-join`中的"ephemeral" ID来判断节点的重启。每次节点的启动，这个 ephemeral ID各不相同。如果某个节点意外的重启，你需要查看这个节点的日志了解它为什么关闭了。

&emsp;&emsp;如果节点没有重启，你应该在`node-left`日志中查看节点离开集群（departure）的原因。有下面三个可能的原因：

- `disconnected`：master node到被移出集群的节点之间的连接关闭了
- `lagging`：master发布了集群状态的更新，但是被移出集群的节点没有在允许的时间内应用这次更新。默认情况下是2分钟。参考[Discovery and cluster formation settings](####Discovery and cluster formation settings)了解更多信息
- `followers check retry count exceeded`：master node往节点连续多次发送了健康检查，这些检查被reject或者超时了。默认情况下，每次的健康检查会在10秒后超时，Elasticsearch会在连续3次的健康检查失败后移除这个节点。参考[Discovery and cluster formation settings](####Discovery and cluster formation settings)了解更多信息

###### Diagnosing disconnected nodes

&emsp;&emsp;Elasticsearch被设计为运行在一个相对可靠的网络上。它在节点间打开一定数量的TCP端口并且期望这些连接一直保持打开。如果连接关闭，Elasticsearch会尝试重新连接。因此要限制偶尔的小故障（occasional blip）对集群的影响，即使是受到影响的节点暂时离开了集群。相反，重复丢失连接会严重影响运行。

&emsp;&emsp;集群中master node和其他节点之间的连接是特别重要的。被选举为master的节点从来不会自发地（spontaneous）关闭跟其他节点的outbound连接。同样的，一旦建立了连接，节点也不会自发地关闭outbound连接，除非节点关闭。

&emsp;&emsp;如果你看到因为`disconnected`的原因意外的（unexpected）关闭了。可能是Elasticsearch以外的原因导致了连接关闭。一个常见的问题是防火墙配置错误：错误的超时时间（improper timeout），或者策略跟Elasticsearch[不兼容](######Long-lived idle connections)。也有可能是由于其他的连接问题，比如由于硬件故障网络阻塞（network congestion）导致包的丢失。如果你是高级用户（advanced user），你可以配置下面的内容来获取更多关于网络异常的信息：

```text
logger.org.elasticsearch.transport.TcpTransport: DEBUG
logger.org.elasticsearch.xpack.core.security.transport.netty4.SecurityNetty4Transport: DEBUG
```

&emsp;&emsp;极端的情况下，你可能需要进行抓包并使用`tcpdump`对其进行分析，查看节点之间的是否出现丢包或者被网络上的其他设备reject。

###### Diagnosing lagging nodes

&emsp;&emsp;Elasticsearch需要每一个节点能合理的快速处理集群状态的更新。节点花费太长的时间来处理集群状态的更新对集群是不利的。master会以`lagging`的原因将节点移除。参考[Discovery and cluster formation settings](####Discovery and cluster formation settings)了解更多的信息。

&emsp;&emsp;通常导致lagging的原因是节点的性能问题。然而也有可能因为严重的网络延迟。若要排除（rule out）网络延迟，确保[合理配置了](####TCP retransmission timeout)`net.ipv4.tcp_retries2`。包含`warn threshold`的日志信息可能会提供更多的根因。

&emsp;&emsp;如果你是高级用户（advance user），你可以通过配置下面的内容获取更多节点被移除时的正在处理的信息：

```text
logger.org.elasticsearch.cluster.coordination.LagDetector: DEBUG
```

&emsp;&emsp;当开启这个日志后，Elasticsearch会尝试在发生故障的节点上运行[Nodes hot threads ](####Nodes hot threads API)并在master节点的日志里面记录运行结果。

###### Diagnosing follower check retry count exceeded nodes

&emsp;&emsp;Elasticsearch需要每一个节点要成功响应网络消息（network message）并且合理的快速响应。如果某个节点reject请求或者完全不响应请求，可能会对集群造成损坏。如果多个连续的检查都失败了，那么Elasticsearch会以`follower check retry count exceeded`的原因将节点移除。并且在`node-left`消息中指出多少个连续的不成功的检查以及有多少个请求超时了。参考[Discovery and cluster formation settings](####Discovery and cluster formation settings)了解更多的信息。

&emsp;&emsp;超时和失败（timeout and failure）可能是因为网络延迟或者节点的性能问题。确保[合理的配置](####TCP retransmission timeout)`net.ipv4.tcp_retries2`来消除可能导致这种不稳定的网络延迟。日志消息中包含`warn threshold`的日志可能会有导致这种不稳定的原因。

&emsp;&emsp;如果上一次检查失败并抛出了异常，那这个异常会被报出，通常会报出需要解决的问题。如果发生了超时，也许需要理解下成功的检查中涉及到的步骤顺序：

1. master上运行在线程`elasticsearch[master][scheduler][T#1]`的`FollowerChecker`告知`TransportService`往follower节点发送检查请求的消息。
2. master上运行在线程`elasticsearch[master][transport_worker][T#2]`的`TransportService`将检查请求消息传给操作系统。
3. master上的操作系统将消息转化成一个或者多个包通过网络发送出去。
4. 在master与follower之间的各种的路由，防火墙和设备转发这些包，这个过程中可能有fragmenting或者defragmenting
5. 在follower节点上的操作系统接收到包并通知Elasticsearch
6. follower上运行在线程`elasticsearch[follower][transport_worker][T#3]`的`TransportService`读取了包，然后重新构造并处理了检查请求。通常来说，检查很快就成功。如果是这样，线程马上构造一个响应并传递给操作系统
7. 如果检查没有马上成功（例如，最近一次选举开始了）
   a. follower上运行在线程`elasticsearch[follower][cluster_coordination][T#4]`的`FollowerChecker`处理这次请求。它开始构造响应并告诉`TransportService`向master发送响应。
   b. follower上运行在线程` elasticsearch[follower][transport_worker][T#3]`的`TransportService`将响应传给操作系统。
8.  follower上的操作系统将响应转化成一个或者多个包通过网络发送出去。
9.  在master与follower之间的各种的路由，防火墙和设备转发这些包，这个过程中可能有fragmenting或者defragmenting
10.  在master节点上的操作系统接受到包并通知Elasticsearch
11.  master上运行在线程`elasticsearch[master][transport_worker][T#2]`的`TransportService`读取了包，重新构造了检查响应，然后进行处理，只要这次检查没有超时。

&emsp;&emsp;存在很多的事情会延缓检查的完成并且导致它超时：

1. 将检查请求传递给`TransportService`后可能有long GC或者virtual machine pause。
2. 可能会长时间的等待`transport_worker`线程变成可用，或者在将检查请求传递给操作系统前可能有long GC或者virtual machine pause。
3. master上的系统错误（例如，a broken network card）可能会延缓通过网络发送消息，possibly indefinitely。
4. 一路上交互的设备可能会延缓，丢弃，或者破坏包。master上的操作系统会等待并根据`net.ipv4.tcp_retries2`重新发送（retransmit）没有确认的或者被破坏的包。我们建议[reducing this value](####TCP retransmission timeout)，因为默认值会导致很长的延迟。
5. follower上的系统错误（例如，a broken network card）可能会延缓通过网络发送的消息，possibly indefinitely。
6. 可能会长时间的等待`transport_worker`变成可用，或者在follower上处理请求的过程中可能有long GC或者virtual machine pause。
7. 可能会长时间的等待`cluster_coordination`变成可用，可能又要等待`transport_worker`变成可用。处理请求的过程中可能有long GC或者virtual machine pause。
8. follower上的系统错误（例如，a broken network card）可能会延缓通过网络发送的响应
9. 一路上交互的设备可能会延缓，丢弃，或者破坏包导致重发（retransmissions）、
10. master上的系统错误（例如，a broken network card）可能会延缓通过网络接收响应
11. 可能会长时间的等待`transport_worker`变成可用来处理响应，可能有long GC或者virtual machine pause。

&emsp;&emsp;若要确定follower check超时的原因。我们可以根据下面的内容来缩小下（narrow down）原因：

- GC pause会在GC日志中记录，Elasticsearch默认会emit这些信息。通常也可以根据main node日志中的`JvmMonitorService`。使用这些日志来确定是否是GC导致的延迟
- VM pause同样会影响在同一个host上的其他process。VM pause还会导致系统时钟中断，Elasticsearch将在其日志中报告这一点
- Packet capture可以展示系统层和网络层的错误。特别是当你同时捕获master和故障节点上的网络流量时。用于follower check的连接不用于任何其他流量，因此仅从流模式就可以很容易地识别出来，即使使用了TLS：几乎每秒钟都会有几百个字节发送到每个方向，首先是master的请求，然后是follower的响应。你应该能够观察到这种连接上的任何重传、数据包丢失或其他延迟。
- 长时间等待特定线程成为可用的这种问题可以通过跟踪stack dump（例如使用`jstack`）或者profiling trace（例如使用Java Flight Recorder）来观察节点离开集群前（departure）几秒的信息。[Nodes hot threads](####Nodes hot threads AP) API有时候也能产生一些有用的信息。但需要记住的是，这个API还需要许多`transport_worker`和`cluster_coordination`线程用于across集群中的节点。API可能会受到你正在诊断的问题的影响。`jstack`更可靠点因为他不要求任何JVM线程。涉及到follower check的线程是`transport_worker`和`cluster_coordination`。因此不应该长时间等待这两个线程。在Elasticsearch的日志中可能也有长时间等待线程的信息。参考[Networking threading model](####Networking)了解更多的信息。

### Add and remove nodes in your cluster
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/add-elasticsearch-nodes.html)

&emsp;&emsp;当你启动了一个Elasticsearch的实例就是启动了一个节点。Elasticsearch集群就是一组节点的集合，这些节点有相同的`cluster.name`的属性。节点加入或者离开节点，集群都会自动的识别并且最终将数据分布到可用的节点上。

&emsp;&emsp;如果你正在运行单个实例的Elasticsearch，你的集群中只有一个节点。所有的主分片在单个节点上。副本分片无法被分配，因此你的集群颜色是黄色的。集群有完整的功能但是在出现故障时有丢失数据的风险。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/elas_0202.png">

&emsp;&emsp;向集群中添加节点能提高承载力和可靠性（capacity and reliability）。默认情况下，一个节点既是data node并且又是一个有资格成为master的节点来控制集群。你也可以配置一个新的节点用于特定的目的，例如处理[ingest request](##Ingest pipelines)，见[Nodes](####Node)了解更多信息。

&emsp;&emsp;向集群中添加更多节点后，它会自动的分配副本分片。当所有的主分片和副本分片都可用后（active），集群状态会变更为绿色。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/elas_0204.png">

&emsp;&emsp;你可以在你的本地机器上运行多个节点来体验多个节点的集群的行为。若要添加一个节点到一个运行在你本地机器上的集群，你需要：

1. 启动一个新的Elasticsearch实例
2. 在`elasticsearch.yml`文件中的`cluster.name`配置中指定集群名字。例如若要将一个节点添加到名为`logging-prod`的集群中，那么在`elasticsearch.yml`中添加一行： cluster.name: "logging-prod" 

&emsp;&emsp;若要添加一个节点到运行在多个机器上的集群，你必须要[set discovery.seed_hosts](######discovery.seed_hosts)，使得新的节点可以发现集群中的其他节点。

&emsp;&emsp;更多discover和shard allocation的信息见[Discovery and cluster formation](###Discovery and cluster formation)和[Cluster-level shard allocation and routing settings](####Cluster-level shard allocation and routing settings)。

#### Master-eligible nodes

&emsp;&emsp;随着节点的添加和移除，Elasticsearch通过自动的更新集群的投票配置（cluster's voting configuration）来保持最佳的容错水平。通过[master-eligible nodes](#####Master-eligible node)的投票计数来作出一些决策，例如选举出新的master或者提交一个新的集群状态。

&emsp;&emsp; 建议在集群中设置一个小规模的并且固定数量的master-eligible node，只通过添加或者移除master-ineligible node来扩大或者缩小集群。然而也有一些情况需要将一些master-eligible node 添加到集群，或者从集群中移除。

##### Adding master-eligible nodes

&emsp;&emsp;如果你想要在你的集群中添加一些节点，对新节点进行简单配置来找到现有的集群然后启动它们。如果这样做是合适的话（ if it is appropriate to do so），Elasticsearch会将新的节点添加到voting configuration中。

&emsp;&emsp;在master选举或者加入到一个现有的已经形成的集群中时，节点会发送一个join request到master，使得正式的添加到集群中。

##### Removing master-eligible nodes

&emsp;&emsp;在移除master-eligible node时，非常重要的一点是不要在同一时间移除太多的节点。例如如果当前有七个master-eligiable node，然后你想要降到三个。不是简单的马上将其中四个节点停止就行：这么做将只剩下三个节点，少于[voting configuration](####Voting configurations)中的一半，意味着集群不能再执行任何的动作。

&emsp;&emsp;更准确的说，如果在同一时间关闭一半或者更多的master-eligible node，集群通常会变成不可用。可以通过再次启动被移除的节点就可以让集群重新上线（back online）。

&emsp;&emsp;只要集群中至少有3个master-eligible node，作为一般的规则要一个接着一个的去移除节点，给于集群足够的时间自动的调整（[adjust](####Quorum-based decision making)）voting configuration，适应新的节点集合对应的容错级别。

&emsp;&emsp;如果只剩下两个master-eligible node，它们都不能被安全的移除因为它们都依靠对方才能进行可靠的运行。若要移除这些节点中的一员，需要通知Elasticsearch让这个节点不再是voting configuration中的一部分。投票权利应该给于其他的节点。这样你就可以让这个节点下线并且不会阻止其他节点的正常运行。排除在voting configuration之外的节点仍然可以正常工作，但是Elasticsearch会尝试将这个节点从voting configuration中移除并不再需要它的投票。重要的是，Elasticsearch不会自动的将排除在voting configuration之外的节点重新回到voting configuration中。一旦节点成功的自动配置为voting configuration之外的节点，它就可以安全的关闭并且不会影响集群master-level的可用性。可以通过[Voting configuration exclusions ](####Voting configuration exclusions API)将节点排除在voting configuration之外：

```text
# Add node to voting configuration exclusions list and wait for the system
# to auto-reconfigure the node out of the voting configuration up to the
# default timeout of 30 seconds
POST /_cluster/voting_config_exclusions?node_names=node_name

# Add node to voting configuration exclusions list and wait for
# auto-reconfiguration up to one minute
POST /_cluster/voting_config_exclusions?node_names=node_name&timeout=1m
```


### Full-cluster restart and rolling restart
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/restart-cluster.html)

&emsp;&emsp;可能会存在一些情况[where you want to perform a full-cluster restart ](####Encrypt internode communication-1)或者rolling restart。对于[full-cluster restart](####Full-cluster restart)，需要关闭并重启集群中所有的节点，而对于[rolling restart](####Rolling restart)，则是需要在某一时间只关闭一个节点，这样提供的服务就不会停止。

#### Full-cluster restart

1. **Disable shard allocation**

&emsp;&emsp;当你关闭了一个data node，allocation process将位于该节点上的主分片复制到其他节点前会等待`index.unassigned.node_left.delayed_timeout`（默认一分钟），因为这会占用很多I/O。由于节点很快就要重启，那这个I/O是没有必要的。你可以在关闭[data node](#####Data node)前[disabling allocation of replica](####Cluster-level shard allocation and routing settings)，免得在超时前没有重启好节点。

```text
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.enable": "primaries"
  }
}
```

2. **Stop indexing and perform a flush**

&emsp;&emsp;执行[flush](####Flush API)加快分片恢复。

```text
POST /_flush
```

&emsp;&emsp;2.1 **Temporarily stop the tasks associated with active machine learning jobs and datafeeds** (Optional)

&emsp;&emsp;Machine leanring功能需要[subscription](https://www.elastic.co/cn/subscriptions)。

&emsp;&emsp;当你关闭一个集群时，你有两个选项来处理machine learning jobs和datafeeds。

- 临时暂停跟你的machine learning jobs和datafeeds相关的任务，使用[set upgrade mode API](####Set upgrade mode API)防止打开新的任务:

```text
POST _ml/set_upgrade_mode?enabled=true
```

- 当你关闭了upgrade mode，会使用上一次自动保存的模型状态（model state）来恢复任务。这个选项可以避免在关机期间管理活跃的任务（active job）的开销，并且快于显示的停止datafeeds和关闭任务jobs。

- [Stop all datafeeds and close all jobs](https://www.elastic.co/guide/en/machine-learning/8.2/stopping-ml.html)。这个选项会在关闭时保存模型状态。当集群重启后，你重新打开jobs时，他们能使用完全相同的模型。然而相比较使用upgrade mode，保存最新的模型状态会需要更长的时间，特别是你拥有很多的jobs或者说jobs有很大的模型状态。

&emsp;&emsp;2.2 **Shut down all nodes**

- 如果你使用`systemd`运行Elasticsearch：

```text
sudo systemctl stop elasticsearch.service
```

- 如果你使用SysV `init`允许Elasticsearch：

```text
sudo -i service elasticsearch stop
```

- 如果将Elasticsearch作为一个后台应用运行：

```text
kill $(cat pid)
```

3. **Perform any needed changes**
4. **Restart nodes**

&emsp;&emsp;如果你有专用的master node，那可以在处理你的data node前，先启动它们，然后等待他们形成一个集群并选举出一个master。你可以通过日志查看处理进程。

&emsp;&emsp;一旦有足够的master-eligible node相互的发现了对方，他们就会形成一个集群并且选举出master。在这个时候，你可以使用[cat health](####cat health API)和[cat nodes](####cat nodes API)来监控加入到集群的节点。

```text
GET _cat/health

GET _cat/nodes
```

&emsp;&emsp;`_cat/health`返回的`status`列显示了集群中每一个节点的状态: `red`，`yellow`或者`green`。

5. **Wait for all nodes to join the cluster and report a status of yellow**

&emsp;&emsp;当一个节点加入到集群，它开始恢复存储在本地的所有的主分片。[\_cat/health](####cat health API) API最开始会报出值为`red`的`status`，意思是还有主分片没有被分配。

&emsp;&emsp;某个节点一旦恢复了本地的分片，`status`会切换到`yellow`，意思是所有的主分片已经恢复，但不意味着所有的副本分片已经分配好。这是意料之中的事，因为你还没有重新开启副本分片的分配。延迟副本分片的分片直到所有的节点的`status`为`yellow`，使得master能将副本分片分配到那些已经有本地分片拷贝的节点上。

6. **Re-enable allocation**

&emsp;&emsp;当所有的节点加入到集群后，并且恢复好了它们的主分片后，将`cluster.routing.allocation.enable`恢复到它的默认值来重新开启分配：

```text
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.enable": null
  }
}
```

&emsp;&emsp;一旦重新开启了分配，集群开始将副本分片分配到data node。在这个时间点恢复索引和查询是安全的，但如果你能等待所有的分片和副本分片成功的分配结束并且所有节点的状态都是`green`，那你的集群能更快的恢复。

&emsp;&emsp;你可以使用[\_cat/health ](####cat health API)和[\_cat/recovery](####cat recovery API) APIs来监控处理进程：

```text
GET _cat/health

GET _cat/recovery
```

7. **Restart machine learning jobs** (Optional)

&emsp;&emsp;如果你临时暂停了machine learning jobs相关的任务，使用[set upgrade mode API](####Set upgrade mode API)将它们返回到活跃状态：

```text
POST _ml/set_upgrade_mode?enabled=false
```

&emsp;&emsp;如果你在停止节点前关闭了所有的machine learning jobs，可以在Kibana中打开jobs并开始datafeed或者使用[open jobs](####Open anomaly detection jobs API)和[start datafeed ](####Start datafeeds API) APIs。

#### Rolling restart

1. **Disable shard allocation**

&emsp;&emsp;当你关闭了一个data node，allocation process将该节点上的主分片复制到其他节点前会等待`index.unassigned.node_left.delayed_timeout`（默认一分钟），因为这会占用很多I/O。由于节点很快就要重启，那这个I/O是没有必要的。你可以在关闭[data node](#####Data node)前[disabling allocation of replica](####Cluster-level shard allocation and routing settings)，免得在超时前没有重启好节点。

```text
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.enable": "primaries"
  }
}
```

2. **Stop non-essential indexing and perform a flush** (Optional)

&emsp;&emsp;尽管你可以在rolling restart期间继续执行索引操作，但如果你临时的停止没有必要的索引操作并执行[flush](####Flush API)可以让分片恢复的更快

```text
POST /_flush
```

3. **Temporarily stop the tasks associated with active machine learning jobs and datafeeds**  (Optional)

&emsp;&emsp;Machine leanring功能需要[subscription](https://www.elastic.co/cn/subscriptions)。

&emsp;&emsp;当你关闭一个集群时，你有两个选项来处理machine learning jobs和datafeeds。

- 临时暂停跟你的machine learning jobs和datafeeds相关的任务，使用[set upgrade mode API](####Set upgrade mode API)防止打开新的任务:

```text
POST _ml/set_upgrade_mode?enabled=true
```

- 当你关闭了upgrade mode，会使用上一次自动保存的模型状态（model state）来恢复任务。这个选项可以避免在关机期间管理活跃的任务（active job）的开销，并且快于显示的停止datafeeds和关闭任务jobs。

- [Stop all datafeeds and close all jobs](https://www.elastic.co/guide/en/machine-learning/8.2/stopping-ml.html)。这个选项会在关闭时保存模型状态。当集群重启后，在你重新打开jobs时，他们能使用完全相同的模型。然而相比较使用upgrade mode，保存最新的模型状态会需要更长的时间，特别是你拥有很多的jobs或者说jobs有很大的模型状态。

- 如果你执行了一个rolling restart，你也可以让machining learning jobs继续运行。当你关闭一个machine leaning node时，它的jobs会自动的移动到其他节点并恢复模型状态（model state）。这个选项可以让你的jobs继续运行但是会增加集群中的负载。

4. **Shut down a single node in case of rolling restart**

- 如果你使用`systemd`运行Elasticsearch：

```text
sudo systemctl stop elasticsearch.service
```

- 如果你使用SysV `init`允许Elasticsearch：

```text
sudo -i service elasticsearch stop
```

- 如果将Elasticsearch作为一个后台应用运行：

```text
kill $(cat pid)
```

5. **Perform any needed changes**
6. **Restart the node you changed**

&emsp;&emsp;启动节点后并通过查看日志文件或者提交一个`_cat/nodes`请求来确认该节点加入到了集群：

```text
GET _cat/nodes
```

7. **Reenable shard allocation**

&emsp;&emsp;对于data node，一旦它加入到集群后，就可以移除`cluster.routing.allocation.enable`这个设置来开启分片分配并开始使用这个节点：

```text
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.enable": null
  }
}
```

8. **Repeat in case of rolling restart**

&emsp;&emsp;当节点恢复后并且集群已经稳定，对每一个需要变更的节点重复这些步骤。

9. **Restart machine learning jobs** (Optional)

&emsp;&emsp;如果你临时暂停了machine learning jobs相关的任务，使用[set upgrade mode API](#### Set upgrade mode API)将它们返回到活跃状态：

```text
POST _ml/set_upgrade_mode?enabled=false
```

&emsp;&emsp;如果你在停止节点前关闭了所有的machine learning jobs，可以在Kibana中打开jobs并开始datafeed或者使用[open jobs](####Open anomaly detection jobs API)和[start datafeed ](####Start datafeeds API) APIs。

### Remote clusters
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/remote-clusters.html)

&emsp;&emsp;你可以将本地集群（local cluster）与其他Elasticsearch集群进行连接，即remote cluster。remote cluster可以位于不同的数据中心或者地理区域，其包含的index和data stream可以被CCR（cross-cluster replication）用于复制或者在一个本地集群使用CCS（cross-cluster search）。

&emsp;&emsp;在[CCR](###Cross-cluster replication)中，提取（ingest）的是remote cluster上的索引数据。`leader index`可以被复制（replicate）到你本地集群的一个或者多个只读的follower index上。使用CCR创建一个多集群可以提供灾备（discovery recovery），让数据的地理位置靠近你的用户，或者建立一个中心化的集群用于出报表。

&emsp;&emsp;[Cross-cluster search](###Search across clusters)能让你对一个或者多个remote cluster运行一个查询请求。这个能力可以让每一个区域都有一个全局视图的集群。允许你从本地集群执行单个查询请求，并从所有连接的remote cluster中返回数据。

&emsp;&emsp;开启并配置security对本地集群和remote cluster都是很重要的。当本地集群连接到一个remote cluster，本地集群上Elasticsearch的superuser能获取remote cluster上完整的读取权限。为了能安全的使用CCR和CCS，在所有连接的节点上开启[enable security](####Configure remote clusters with security)并且至少在每一个节点的transport level配置 Transport Layer Security (TLS) 。

&emsp;&emsp;此外，操作系统级别的本地管理员如果能够充分访问Elasticsearch配置文件和私钥，就有可能接管remote cluster。确保你的安全策略中包括在操作系统级别保护本地和remote cluster。

&emsp;&emsp;若要注册一个remote cluster，使用[sniff mode](#####Sniff mode) 或[proxy mode ](#####Proxy mode)[connect the local cluster](####Connect to remote clusters)和remote cluster中的节点。注册完remote cluster后，为CCR和CCS [configure privileges](####Configure roles and users for remote clusters)。

##### Sniff mode

&emsp;&emsp;在sniff mode中，使用名字和seed node list来创建集群。当注册了一个remote cluster后，它的cluster state从seed node中获取并且最多三个`gateway nodes`作为remote cluster requests的一部分。这个模式要求gateway node 的publish address能被本地集群访问到。

&emsp;&emsp;Sniff模式是默认的连接模式。

&emsp;&emsp;`gateway node`的选择取决于下面的标准：

- Version：remote node必须和注册的集群兼容：
  - 相同主版本（major version）的节点可以相互连接。例如7.0可以跟任何7.x节点通信
  - 只有主版本的最后一个小版本可以跟下一个主版本通信。在6.x系列，6.8可以跟任意的7.x的节点通信。同时6.7只可以跟7.0通信
  - 版本兼容性是对称的，意味着如果6.7可以跟7.0通信，那么7.0也可以跟6.7通信。下面的表描述（depict）了本地集群跟remote cluster之间的兼容性

| **Remote cluster** | 5.0–5.5 | 5.6  | 6.0–6.6 | 6.7  | 6.8  | 7.0  | 7.1–7.16 | 7.17 | 8.0–8.2 |
| :----------------: | :-----: | :--: | :-----: | :--: | :--: | :--: | :------: | :--: | :-----: |
|      5.0–5.5       |    √    |  √   |    ×    |  ×   |  ×   |  ×   |    ×     |  ×   |    ×    |
|        5.6         |    √    |  √   |    √    |  √   |  √   |  ×   |    ×     |  ×   |    ×    |
|      6.0–6.6       |    ×    |  √   |    √    |  √   |  √   |  ×   |    ×     |  ×   |    ×    |
|        6.7         |    ×    |  √   |    √    |  √   |  √   |  √   |    ×     |  ×   |    ×    |
|        6.8         |    ×    |  √   |    √    |  √   |  √   |  √   |    √     |  √   |    ×    |
|        7.0         |    ×    |  ×   |    ×    |  √   |  √   |  √   |    √     |  √   |    ×    |
|      7.1–7.16      |    ×    |  ×   |    ×    |  ×   |  √   |  √   |    √     |  √   |    ×    |
|        7.17        |    ×    |  ×   |    ×    |  ×   |  √   |  √   |    √     |  √   |    √    |
|      8.0–8.2       |    ×    |  ×   |    ×    |  ×   |  ×   |  ×   |    ×     |  √   |    √    |


> IMPORTANT：Elastic仅支持在这些配置的一个子集上进行跨集群搜索，见[Supported cross-cluster search configurations](####Supported cross-cluster search configurations)。

- Role：默认情况下，任何non-[master-eligible](#####Master-eligible node) node 都可以作为一个gateway node。专用的master node永远不会被选为gateway node
- attributes：你可以通过设置[cluster.remote.node.attr.gateway](#####Sniff mode remote cluster settings)设置为`true`来定义gateway node。然而，这样的节点仍然需要上面的要求

##### Proxy mode

&emsp;&emsp;在proxy模式中，使用一个名字和单个代理地址来创建一个集群。当你注册了一个remote cluster，数量可配置的socket 连接对代理地址开放。这个proxy要求将这些连接路由到remote cluster。Proxy mode不要求remote cluster node‘有用于访问的publish address。

&emsp;&emsp;proxy模式不是默认的连接模式并且必须要配置。Proxy mode跟sniff mode有相同的版本兼容要求。

> IMPORTANT：Elastic仅支持在这些配置的一个子集上进行跨集群搜索，见[Supported cross-cluster search configurations](####Supported cross-cluster search configurations)。

#### Configure remote clusters with security
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/remote-clusters-security.html)

#### Connect to remote clusters
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/remote-clusters-connect.html)

&emsp;&emsp;本地集群使用[ transport interface](####Networking)建立于remote cluster的连接。本地集群中的coordinating node会跟remote cluster中指定的节点建立[long-lived](#####Long-lived idle connections) TCP连接。Elasticsearch要求这些连接保持打开，即使连接空闲（idle）了很长一段时间。

> NOTE：你必须要有`manage` cluster privilege用于跟remote cluster进行连接

&emsp;&emsp;若要在Kibana的Stack Management 中添加一个remote cluster：

1. 从侧边导航栏选择**Remote Clusters**
2. 指定remote cluster（Cluster A）的Elasticsearch endpoint URL，或者IP地址，hostname以及transport端口（默认值`9300`）。例如`cluster.es.eastus2.staging.azure.foundit.no:9400`或者`192.168.1.1:9300`

&emsp;&emsp;或者使用[cluster update settings API ](####Cluster update settings API)添加一个remote cluster。你也可以为本地集群中的每一个node [dynamically configure](########## Dynamically configure remote clusters) remote cluster。若要对本地集群中不同的节点配置remote cluster，那么在每一个节点的`elasticsearch.yml`中定义[static settings](#####Statically configure remote clusters)。

&emsp;&emsp;连接remote cluster之后，[configure roles and users for remote clusters](####Configure roles and users for remote clusters)。

&emsp;&emsp;下面的请求添加了一个名为`cluster_one`的remote cluster，`cluster alias`是一个唯一标识符代表连接的remote cluster用于区分本地和远程的索引。

```text
PUT /_cluster/settings
{
  "persistent" : {
    "cluster" : {
      "remote" : {
        "cluster_one" : {    
          "seeds" : [
            "127.0.0.1:9300" 
          ]
        }
      }
    }
  }
}
```

&emsp;&emsp;第6行，这个remote cluster的`cluster alias`是`cluster_one`·

&emsp;&emsp;第8行，指定remote cluster中seed node 的hostname跟transport port

&emsp;&emsp;你可以使用[remote cluster info API ](####Remote cluster info API)来验证本地集群是否于remote cluster连接成功。

```text
GET /_remote/info
```

&emsp;&emsp;API的响应指出本地集群跟`cluster alias`为`cluster_one`的集群连接成功：

```text
{
  "cluster_one" : {
    "seeds" : [
      "127.0.0.1:9300"
    ],
    "connected" : true,
    "num_nodes_connected" : 1,  
    "max_connections_per_cluster" : 3,
    "initial_connect_timeout" : "30s",
    "skip_unavailable" : false, 
    "mode" : "sniff"
  }
}
```

&emsp;&emsp;第7行，在remote cluster中，跟本地集群连接的节点数量

&emsp;&emsp;第10行，如果通过CCS进行查询时节点不可用，是否要跳过这个集群

##### Dynamically configure remote clusters

&emsp;&emsp;使用[cluster update settings API](####Cluster update settings API)动态的配置集群中每一个节点上的remote 设置。下面的请求增加了三个remote cluster：`cluster_one`，`cluster_two`，`cluster_three`。

&emsp;&emsp;参数`seed`指定了remote cluster中seed node的hostname和[transport port](#####Advanced transport settings)（默认值`9300`）

&emsp;&emsp;参数`mode`决定了连接模式，默认值是[sniff](#####Sniff mode)。因为`cluster_one`没有指定`mode`，所以使用默认值。`cluster_two`和`cluster_three`都显示的使用了不同的模式：

```text
PUT _cluster/settings
{
  "persistent": {
    "cluster": {
      "remote": {
        "cluster_one": {
          "seeds": [
            "127.0.0.1:9300"
          ]
        },
        "cluster_two": {
          "mode": "sniff",
          "seeds": [
            "127.0.0.1:9301"
          ],
          "transport.compress": true,
          "skip_unavailable": true
        },
        "cluster_three": {
          "mode": "proxy",
          "proxy_address": "127.0.0.1:9302"
        }
      }
    }
  }
}
```

&emsp;&emsp;你可以在初始化配置后动态的为一个remote cluster更新。下面的请求为`cluster_two`更新了compress settings，为`cluster_three`更新 了compress settings 以及ping schedule settings

> NOTE：当compression和ping schedule settings更改后，所有现有的节点连接必须关闭并重新打开，可能会导致正在运行的请求失败。

```text
PUT _cluster/settings
{
  "persistent": {
    "cluster": {
      "remote": {
        "cluster_two": {
          "transport.compress": false
        },
        "cluster_three": {
          "transport.compress": true,
          "transport.ping_schedule": "60s"
        }
      }
    }
  }
}
```

&emsp;&emsp;你可以通过将每一个remote cluster settings的值赋值为`null`的方式将一个remote cluster从一个cluster settings中删除。下面的请求将`cluster_two`从cluster settings中移除，留下了`cluster_one`和`cluster_three`：

```text
PUT _cluster/settings
{
  "persistent": {
    "cluster": {
      "remote": {
        "cluster_two": {
          "mode": null,
          "seeds": null,
          "skip_unavailable": null,
          "transport.compress": null
        }
      }
    }
  }
}
```

##### Statically configure remote clusters

&emsp;&emsp;如果你在`elasticsearch.yml`中指定了设置，带有这些设置的节点才能连接remote cluster并且服务remote cluster的请求。

> NOTE：对于每一个节点，使用[cluster update settings API](####Cluster update settings API)指定的cluster settings优先在`elasticsearch.yml`中的设置

&emsp;&emsp;在下面的例子中，`cluster_one`, `cluster_two`, 和`cluster_three`是随意的（arbitrary）取的名字（aliases）用来代表连接的集群。这些名字随后用于区分本地和远端的索引。

```text
cluster:
    remote:
        cluster_one:
            seeds: 127.0.0.1:9300
        cluster_two:
            mode: sniff
            seeds: 127.0.0.1:9301
            transport.compress: true      
            skip_unavailable: true        
        cluster_three:
            mode: proxy
            proxy_address: 127.0.0.1:9302 
```

&emsp;&emsp;第8行，`cluster_two`中显示的开启了Compression

&emsp;&emsp;第9行，`cluster_two`中失去连接的remote cluster是可选的

&emsp;&emsp;第12行，`cluster_three`中用于连接的代理地址

#### Configure roles and users for remote clusters
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/remote-clusters-privileges.html#remote-clusters-privileges-ccs)

&emsp;&emsp;在[connecting remote clusters](####Connect to remote clusters)之后，你需要同时在本地和远端集群中创建一个用户角色（user role）并且赋予必要的privilege。这些角色要求可以使用CCR（cross-cluster replication）和CCS（cross-cluster search）。

> IMPORTANT：你必须在本地和远端集群使用相同的角色。例如，下面用于CCR的配置在本地和远端集群上使用了`remote-replication`角色名。然而，你可以在每一个集群上指定不同的角色定义。

&emsp;&emsp;你可以用Kibana的Stack Management，从侧边导航栏中选择**Security > Roles**来管理用户和角色。你也可以使用[role management APIs](####Role mappings)动态的添加，更新，移除并且检索角色。当你使用APIs在`native` realm中管理角色时，角色存储在内部的Elasticsearch index中。

&emsp;&emsp;下面的请求使用了[create or update roles API](####Create or update roles API)。你必须至少有`manage_security` 的cluster privilege来使用这个API。

##### Configure privileges for cross-cluster replication

&emsp;&emsp;CCR用户在本地集群和remote cluster上要求有不同的 index和cluster privilege。使用下面的请求分别在本地集群和remote cluster上创建不同的角色，然后创建一个用户拥有这些角色。

###### Remote cluster

&emsp;&emsp;在包含leader index的remote cluster上，CCR角色需要`read_ccr`的cluster privilege，以及在leader index上的`monitor`和`read` privilege

> NOTE：如果请求导致了[on behalf of other users](####Submitting requests on behalf of other users)，那么用户在remote cluster上必须要有`run_as`的privilege。

&emsp;&emsp;下面的请求在remote cluster上创建了一个`remote-replication`的角色：

```text
POST /_security/role/remote-replication
{
  "cluster": [
    "read_ccr"
  ],
  "indices": [
    {
      "names": [
        "leader-index-name"
      ],
      "privileges": [
        "monitor",
        "read"
      ]
    }
  ]
}
```

###### Local cluster

&emsp;&emsp;在包含follower index的本地集群上，`remote-replication` 角色需要`manage_ccr`的cluster privilege，follower index的`monitor`，`read`，`write`，和`manage_follow_index` privilege。

&emsp;&emsp;下面的请求在本地集群上创建了一个`remote-replication`的角色：

```text
POST /_security/role/remote-replication
{
  "cluster": [
    "manage_ccr"
  ],
  "indices": [
    {
      "names": [
        "follower-index-name"
      ],
      "privileges": [
        "monitor",
        "read",
        "write",
        "manage_follow_index"
      ]
    }
  ]
}
```

&emsp;&emsp;在每一个集群上创建完`remote-replication`的角色后，使用[create or update users API](####Create or update users API)在本地集群上创建一个用户，并赋予`remote-replication`的角色。例如，下面的请求赋予一个名为`cross-cluster-user`的用户`remote-replication`的角色：

```text
POST /_security/user/cross-cluster-user
{
  "password" : "l0ng-r4nd0m-p@ssw0rd",
  "roles" : [ "remote-replication" ]
}
```

> NOTE：在本地集群上你只需要创建这个用户。

&emsp;&emsp;你可以随后[configure cross-cluster replication](####Tutorial: Set up cross-cluster replication)来跨中心复制你的数据。

##### Configure privileges for cross-cluster search

&emsp;&emsp;CCS用户在本地集群和remote cluster上要求有不同的 index和cluster privilege。使用下面的请求分别在本地集群和remote cluster上创建不同的角色，然后创建一个用户拥有这些角色。

###### Remote cluster

&emsp;&emsp;在remote cluster上，CCS角色要求为目标索引有`read`和`read_cross_cluster` privilege。

> NOTE：如果请求导致了[on behalf of other users](####Submitting requests on behalf of other users)，那么用户在remote cluster上必须要有`run_as`的privilege。

&emsp;&emsp;下面的请求在remote cluster上创建了一个`remote-search`的角色：

```text
POST /_security/role/remote-search
{
  "indices": [
    {
      "names": [
        "target-indices"
      ],
      "privileges": [
        "read",
        "read_cross_cluster"
      ]
    }
  ]
}
```

###### Local cluster

&emsp;&emsp;在本地集群上，集群用于初始化CCS，用户只需要`remote-search`角色。role privilege可以是空。

&emsp;&emsp;下面的请求在本地集群上创建了一个`remote-search`角色：

```text
POST /_security/user/cross-search-user
{
  "password" : "l0ng-r4nd0m-p@ssw0rd",
  "roles" : [ "remote-search" ]
}
```

> NOTE：在本地集群上你只需要创建这个用户。

&emsp;&emsp;有`remote-search`角色的用户随后可以[search across clusters](###Search across clusters)。

##### Configure privileges for cross-cluster search and Kibana

&emsp;&emsp;当使用Kibana跨多个集群搜索时，一个两步骤的赋权过程决定了用户是否可以访问remote cluster上的data streams和index：

- 首先，本地集群确定了用户是否被授权访问remote cluster。本地集群就是Kibana连接的那个集群
- 如果用户有权限，remote cluster随后确定用户是否能访问指定的data stream和index

&emsp;&emsp;为了保证Kibana用户能访问remote cluster，赋予它们一个在remote cluster上指定索引的 read privilege。你可以在remote cluster中指定data streams和index：`<remote_cluster_name>:<target>`。

&emsp;&emsp;例如，你可能将Logstash data索引到本地集群并且周期性的将older time-based的索引归档到你的remote cluster上。你想要在这两个集群上进行搜索，那你必须在两个集群上同时enable Kibana users。

###### Local cluster

&emsp;&emsp;在本地集群上，创建一个`logstash-reader`的角色，然后在`logstash-*`上授予（grant）`read`和`view_index_metadata`的privilege。

> NOTE：如果你将本地集群配置为其他集群的remote cluster，你本地集群上的`logstash-reader`角色同样需要赋予`read_cross_cluster` privilege。

```text
POST /_security/role/logstash-reader
{
  "indices": [
    {
      "names": [
        "logstash-*"
        ],
        "privileges": [
          "read",
          "view_index_metadata"
          ]
    }
  ]
}
```

&emsp;&emsp;给你的Kibana用户赋予一个角色能[access to Kibana](https://www.elastic.co/guide/en/kibana/8.2/xpack-security-authorization.html)以及`logstash_reader`角色。例如，下面的请求创建了`cross-cluster-kibana`用户并赋予`kibana-access`和`logstash-reader`的角色。

```text
PUT /_security/user/cross-cluster-kibana
{
  "password" : "l0ng-r4nd0m-p@ssw0rd",
  "roles" : [
    "logstash-reader",
    "kibana-access"
    ]
}
```

###### Remote cluster

&emsp;&emsp;在remote cluster上，创建一个`logstash-reader`并赋予`read_cross_cluster`的privilege以及`logstash-*`索引的`read`和`view_index_metadata`的privilege。

```text
POST /_security/role/logstash-reader
{
  "indices": [
    {
      "names": [
        "logstash-*"
        ],
        "privileges": [
          "read_cross_cluster",
          "read",
          "view_index_metadata"
          ]
    }
  ]
}
```

#### Remote cluster settings

##### Sniff mode remote cluster settings


## Index Modules
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-modules.html)

&emsp;&emsp;Index Modules是用于索引创建的模块，控制与索引相关的所有方面。

#### Index Settings

&emsp;&emsp;索引层的设置（index level settings）可以根据每一个索引进行配置，索引配置可以划分为：

##### static

&emsp;&emsp;只能在索引创建期间或者对一个[closed index](####Open-index API)进行设置。

##### dynamic（index modules）

&emsp;&emsp;使用[update-index-settings](####Update-index-settings-API)对live index的索引配置进行变更。

```text
在一个关闭的索引上更改静态或者动态的索引设置可能会导致错误的设置，这些设置可能会更改（rectify）或者重建（recreating）索引（不包含被删除的数据）
```

#### Static index settings

&emsp;&emsp;下面是所有的静态索引设置，这些设置跟任何特定的索引模块都没有关联（associate）：

##### index.number_of_shards

&emsp;&emsp;一个索引应该拥有的主分片数量。默认值是1。这个设置只能在索引创建期间设置。不能对关闭的索引进行设置。

```text
每个索引的分片的数量上限是1024。这个限制值是一个安全限制，它基于资源分配防止索引的创建导致集群的不稳定（destabilize）。这个限制可以通过对集群中所有节点的系统属性（system property）export ES_JAVA_OPTS="-Des.index.max_number_of_shards=128"进行变更
```

##### index.number_of_routing_shards

&emsp;&emsp;整数值，跟`index.number_of_shards`一起用来将文档路由到主分片中，见[\_routing field](####_routing-field)。

&emsp;&emsp;Elasticsearch在对索引进行[split](####Split an index)时会使用这个值。例如，一个拥有5个分片，并且`index.number_of_routing_shards`的值设置为30时，那么每个分片可以按照因子2或者3进行划分，换句话说，可以按照下面的方式进行划分：

- 5 -> 10 -> 30 （5个分片中的每个分片先分为2份，然后10个分片的每个分片再分为3份）
- 5 -> 15 -> 30（5个分片中的每个分片先分为3份，然后15个分片中的每个分片再分为2份）
- 5 -> 30（5个分片中的每个分片分为6份）

&emsp;&emsp;默认值取决于索引的主分片数量，默认情况下根据切分因子2进行切分，并且最多切分出1024个分片。

```text
在Elasticsearch 7.0及以后的版本中，该值会影响文档分配到分片中。当使用自定义的路由规则来重新写入索引reindexing时，你必须显示指定index.number_of_routing_shards来维护文档的分配。见related breaking change（https://www.elastic.co/guide/en/elasticsearch/reference/7.0/breaking-changes-7.0.html#_document_distribution_changes）。
```

##### index.codec

&emsp;&emsp;默认使用[LZ4](https://www.amazingkoala.com.cn/Lucene/yasuocunchu/2019/0226/37.html)对store data进行压缩，但是可以设置为`best_compression`，其使用[DEFLATE](https://en.wikipedia.org/wiki/Deflate)有更高压缩率，代价是降低了存储域[stored filed](https://www.amazingkoala.com.cn/Lucene/suoyinwenjian/2020/1013/169.html)的性能。如果你更新了压缩类型compression type，新的数据在段合并后会被应用（apply）。可以使用[force merge](####Force merge API)对段进行强制合并。

##### index.routing_partition_size

&emsp;&emsp;使用自定义的[路由值](#####Routing to an index partition)，路由到一定数量的分片中（区别于路由到某一个分片，这里指的是可以被路由到某个分片集合中，集合的大小即index.routing_partition_size）。默认值是1并且只能在创建索引时设置，该值必须比`index.number_of_shards`小，除非`index.number_of_shards`的值就是1。见[Routing to an index partition](#####Routing to an index partition)查看如何使用该值。

##### index.soft_deletes.enabled

&emsp;&emsp;该值描述了当前索引是否开启soft deletes。Soft deletes只能在创建索引时配置并且只能在Elasticsearch6.5及以后的版本的索引上配置。默认值为true。

- 从7.6.0开始后，创建索引时设置该值为false的功能已被弃用。

##### index.soft_deletes.retention_lease.period

&emsp;&emsp;在分片的history retention lease过期之前，保留的最长时间。Shard history retention leases能保证在Lucene层的段合并后soft deletes仍然能被保留（retain）。如果soft deletes在follower节点上生成副本期间被合并了merge away，那么在随后的处理中会因为在leader节点上不完整的history而导致失败。默认值为12h。

##### index.load_fixed_bitset_filters_eagerly

&emsp;&emsp;该值描述了[cached filters](###Query and filter context)是否为nested queries提前载入了pre-loaded。可选值是true(默认值)和false。

##### index.shard.check_on_startup

```text
WARNING：该配置仅针对专家用户。因为该配置允许在分片启动时进行一些开销较大的操作（processsing）并且只有在诊断集群中的问题时才有用。如果你确定要使用，你应该只是临时使用并且在不需要后移除这个配置
```

&emsp;&emsp;Elasticsearch在分片生命周期的不同时刻自动对其内容执行完整性检查。比如当恢复一个副本replica时，会对传输的每一个文件进行校验和的验证或者拍摄快照时（take a snapshot）。当启动一个节点或者完成一个分片的恢复跟重分配时，同样的会在打开一个分片时对重要的文件进行完整性的验证。你可以在对其进行拍摄快照并放到新的仓库中（fresh repository）或者恢复到一个新的节点后，手动的对整个分片进行完整性的校验。

&emsp;&emsp;当前配置用来描述Elasticsearch在打开一个分片时是否要对其进行额外的完整性检查。如果检查出corruption，那么阻止这个分片被打开，该配置接受下面的值：

- false
  - 在打开分片时不进行额外的corruption检查，该值是默认值并且是推荐值

- checksum
  - 通过分片中的每一个文件的校验和来校验分片的内容，这将检测从磁盘读取的数据与Elasticsearch最初写入的数据不同的情况，例如由于未检测到的磁盘损坏或其他硬件故障。这些检查需要从磁盘读取整个分片，这需要大量的时间和IO带宽，并可能通过从文件系统缓存中清除重要数据来影响集群性能

- true
  - 执行跟`checksum`一样的检查，同样要检查分片的logical inconsistencies，比如RAM问题或者硬件故障导致正在写入的索引被corrupted。这些检查要求从磁盘中读取所有的分片，所以它会带来大量的时间跟IO带宽并且对分片内容进行各种检查时又会带来大量的时间，CPU以及内存开销。

#### Dynamic index settings

&emsp;&emsp;下面列出的是所有的动态索引设置，这些设置与任何特定的index module没有关联。

##### index.number_of_replicas

&emsp;&emsp;每个主分片的副本分片（replica）的数量，默认值是1。

```text
WARNING：：该配置如果设置为0，可能导致在节点重启时临时可用性损失或者导致数据永久丢失
```

##### index.auto_expand_replicas

&emsp;&emsp;基于集群中节点的数量自动增加（auto-expand）副本分片的数量。该值可以设置为一个用破折号来区分上下界的值（例如: 0-5），或者使用`all`作为上界（例如：0-all）。默认值是false。注意的是这种自动增加副本分片数量只会考虑[allocation filtering](####Index-level shard allocation filtering)规则，会忽视其他的分配规则例如[total shards per node](####Total shards per node)，如果适用的规则（applicable rules）阻止分配所有的副本分片，会导致集群变成`yellow`。

&emsp;&emsp;如果上界是`all`，那么[shard allocation awareness](####Cluster-level shard allocation and routing settings)跟[cluster.routing.allocation.same_shard.host](####Cluster-level shard allocation and routing settings)这两个配置在这个索引上会被忽略。

##### index.search.idle.after
&emsp;&emsp;某个分片在多久未收到搜索或者请求后被认定为空闲的，默认值是30秒。

##### index.refresh_interval

&emsp;&emsp;多久执行一次refresh操作。该操作使得最近的修改能够被搜索到。默认值是1秒。可以被设置为`-1`来关闭refresh。如果没有显示的（explicit）设置这个值，那么分片在至少`index.search.idle.after`时间内没有收到搜索请求则不会收到后台刷新，直到分片收到查询请求。搜索时如果遇到某个分片正在进行refresh，那么会等待下一次后台refresh，这种行为的目的是在没有搜索请求时能优化bulk Indexing。为了避免这种行为，应该显式设置1s的值作为刷新间隔。

##### index.max_result_window

&emsp;&emsp;当前索引返回的结果最大值，基于`from + size`，默认值是10000。搜索请求占用的堆内存和时间与`from + size`成比例。见[scroll](###Paginate search results)或[Search After](####Search after) ，替换成这些方式来获得更多的搜索结果。

##### index.max_inner_result_window

&emsp;&emsp;`from + size`的最大值，用于索引的inner hits definition以及top hits aggregation。默认值为`100`。inner hits和top hits aggregation占用堆内存并且跟`from + size` 的大小成比例，该值用于限制内存的使用。

##### index.max_rescore_window

&emsp;&emsp;`rescore`请求中`window_size`的最大值。默认值为`index.max_result_window`，即`10000`。查询请求会占用堆内存并且跟`max(window_size, from + size)`的大小成比例，该值用于限制内存的使用。

##### index.max_docvalue_fields_search

&emsp;&emsp;某次查询中允许`docvalue_fileds`数量最大值。默认值为`100`。查询Doc-value域有一定的开销因为可能需要查看对每一个域中的每一个文档。

##### index.max_script_fields

&emsp;&emsp;某次查询中允许`script_fields`数量最大值。默认值为`32`。

##### index.max_ngram_diff

&emsp;&emsp;The maximum allowed difference between min_gram and max_gram for NGramTokenizer and NGramTokenFilter。默认值为`1`。

##### index.max_shingle_diff

&emsp;&emsp;[shingle token filter](####Shingle token filter)中max_shingle_size和min_shingle_size之间允许的最大不同。默认值为`3`。

##### index.max_refresh_listeners

&emsp;&emsp;索引中每一个分片上的可用的refresh listeners数量最大值。这些listeners用于实现[refresh=wait_for](####?refresh(api))。

##### index.analyze.max_token_count

&emsp;&emsp;使用\_analyze API允许生成token数量最大值。默认值为`10000`。

##### index.highlight.max_analyzed_offset

&emsp;&emsp;在高亮请求中，允许处理（analyze）的字符数量最大值。这个设置只有在text上请求高亮，并且没有设置offset或者term vectors才会应用。默认值为`1000000`。

##### index.max_terms_count

&emsp;&emsp;Terms Query中可以使用的term数量最大值。默认值为`65536`。

##### index.max_regex_length

&emsp;&emsp;Regexp Query中可以使用的regex的长度最大值。默认值为`1000`。

##### index.query.default_field

&emsp;&emsp;（string or array of strings）通配模版（Wildcard(`*`) patterns）会匹配到一个或者多个域。下面的请求类型默认查询这些匹配到的域：

- [More like this](####More like this query)
- [Multi-match](####Multi-match query)
- [Query string](####Query string query)
- [Simple query string](####Simple query string query)

&emsp;&emsp;默认值为`*`，意味着匹配所有eligible域用于[term-level queries](###Term-level queries)，除了metadata域。

##### index.routing.allocation.enable

&emsp;&emsp;用于控制索引的分片分配。该参数可以设置为：

- `all`（默认值）- 允许对所有的分片使用shard rebalancing
- `primaries` - 只允许对主分片使用shard rebalancing
- `replicas` - 只允许对副本分配（replica）使用shard rebalancing
- `none` - 不允许对分片使用shard rebalancing

##### index.gc_deletes

&emsp;&emsp;[a deleted document’s version number](####Delete API)仍然可用的时长，它用于[further versioned operations](####Index API)。默认值为`60s`。

##### index.default_pipeline

&emsp;&emsp;用于索引的默认的[ingest pipeline](##Ingest pipelines)。如果设置了默认的pipeline但pipeline不存在，索引请求则会失败。可以使用`pipeline`参数覆盖默认的pipeline。特定的pipeline名字`none`意味着不允许任何pipeline 。

##### index.final_pipeline

&emsp;&emsp;索引的final [ingest pipeline]()。如果设置了final pipeline并且该pipeline不存在，索引请求则会失败。 final pipeline总是在请求中指定的pipeline以及默认的pipeline之后运行。特定的pipeline名字`none`意味着不允许任何pipeline 。

> NOTE：你不能使用一个final pipeline修改`_index`域，如果pipeline尝试进行修改，索引请求则会失败。

##### index.hidden

&emsp;&emsp;该值描述的是索引是否默认隐藏。隐藏的索引不会在匹配到通配表达式到后返回。使用了`expand_wildcards`参数的请求会受到该参数的控制。可选值为`true`或者`false`。

#### Settings in other index modules

&emsp;&emsp;index module中其他可用的index setting：

- [Analysis](##Text analysis)

&emsp;&emsp;用于定义analyzers, tokenizers, token filters and character filters。

- [Index shard allocation](###Index Shard Allocation)

&emsp;&emsp;控制分片如何，何时分配到哪一个节点。

- [Mapping](##Mapper)

&emsp;&emsp;为索引开启/关闭dynamic mapping。

- [Merging](###Merge)

&emsp;&emsp;控制后台合并处理程序如何对分片进行和并。

- [Similarities](###Similarity module)

&emsp;&emsp;配置自定义的Similarity设置自定义查询结果的打分方式。

- [Slowlog](###Slow Log)

&emsp;&emsp;控制如何通过日志记录slow Query和slow fetch。

- [Store](###Store)

&emsp;&emsp;配置用于访问分片数据的文件系统。

- [Translog](###Translog)

&emsp;&emsp;控制transaction log以及后台flush操作。

- [History retention](###History retention)

&emsp;&emsp;控制索引的历史操作。

- [Indexing pressure](###Indexing pressure)

&emsp;&emsp;Configure indexing back pressure limits

#### X-Pack index settings

- [Index lifecycle management](####Index lifecycle management settings in Elasticsearch)

&emsp;&emsp;为索引指定生命周期策略以及rollover alias。

### Analysis
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-modules-analysis.html)

&emsp;&emsp;The index analysis module acts as a configurable registry of analyzers用于将一个string filed转化为不同的term：

- 添加到倒排索引（inverted index）中使得文档可以被搜索到
- 用于high level query，比如[match query](####Match query)，生成用于搜索的term

&emsp;&emsp;见[Text analysis](##Text analysis)了解配置细节。

### Index Shard Allocation
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-modules-allocation.html)

&emsp;&emsp;这个组件提供了per-index settings来控制分片到节点的分配：

- [Shard allocation filtering](####Index-level shard allocation filtering)：控制哪些分片分配到哪些节点中。
- [Delayed allocation](####Delaying allocation when a node leaves)：节点离开集群后，未分配的（unassigned）分片延缓分配到节点。
- [Total shards per node:](####Total shards per node)：每一个节点上同一个索引的分片的数量限制
- [Data tier allocation](####Index-level data tier allocation filtering)：控制[data tiers](###Data tiers)上的索引分配

#### Index-level shard allocation filtering
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/shard-allocation-filtering.html)

&emsp;&emsp;你可以使用shard allocation filtering来控制Elasticsearch如何为一个指定的索引进行分片的分配。索引层（index-level）的filtering跟 [cluster-wide allocation filtering](#####Cluster-level shard allocation filtering) 和 [allocation awareness](#####Shard allocation awareness)结合应用。

&emsp;&emsp;Shard allocation filtering可以基于节点上自定义的属性或者内置属性例如`_name`, `_host_ip`, `_publish_ip`, `_ip`, `_host`, `_id`, `_tier` 以及`_tier_preference`。 [Index lifecycle management](###ILM: Manage the index lifecycle)使用基于自定义的节点属性的filtering来决定在进行阶段转变时如何重新分配分片。

&emsp;&emsp;设置（setting）`cluster.routing.allocation`是动态的，使得可以让live index从一些节点移到其他节点。只有在不会破坏其他路由限制（routing constraint）时才会重新分配分片，比如说不会在一台机器上同时分配主分片和副本。

&emsp;&emsp;例如，你可以使用自定义的节点属性来指示（indicate）一个节点的性能属性并且使用shard allocation filtering将指定的索引路由到级别最合适的硬件中（the most appropriate class of hardware）。

##### Enabling index-level shard allocation filtering

&emsp;&emsp;基于自定义的节点属性进行过滤（筛选）：

1. 在每一个节点的配置文件`elasticsearch.yml`中指定过滤节点属性。例如，如果你有`small`, `medium`, 和 `big`三类节点，你可以添加一个`size`属性，基于节点的大小进行过滤。

```text
node.attr.size: medium
```

&emsp;&emsp;你也可以在节点启动时指定自定义的属性：

```text
./bin/elasticsearch -Enode.attr.size=medium
```

2. 在索引中添加shard allocation filtering信息。`index.routing.allocation`这个设置支持三种类型的过滤：`include`, `exclude`和`require`。例如，告诉Elasticsearch使用`index.routing.allocation.include`将索引`text`的分片在值为`big`或者`medium`的节点上进行分配：

```text
PUT test/_settings
{
  "index.routing.allocation.include.size": "big,medium"
}
```

&emsp;&emsp;如果你指定多个条件并要求节点同时满足才能在其节点上进行分片的分配：

- 如果指定了任意`require`类型的条件，所有的条件都需要满足
- 如果指定了任意`exclude`类型的条件，所有的条件都不能满足
- 如果指定了任意`include`类型的条件，至少一个条件需要满足

&emsp;&emsp;例如，将索引`text`的分片移到机架值为`ranck1`并且大小为`big`的节点，你可以这么指定：

```text
PUT test/_settings
{
  "index.routing.allocation.require.size": "big",
  "index.routing.allocation.require.rack": "rack1"
}
```

##### Index allocation filter settings

###### index.routing.allocation.include.{attribute}

&emsp;&emsp;将索引分配给一个节点，这个节点的`{attribute}`属性中至少包含用逗号隔开的值。

###### index.routing.allocation.require.{attribute}

&emsp;&emsp;将索引分配给一个节点，这个节点的`{attribute}`属性中包含所有用逗号隔开的值。

###### index.routing.allocation.exclude.{attribute}

&emsp;&emsp;将索引分配给一个节点，这个节点的`{attribute}`属性中不包含用逗号隔开的值。

&emsp;&emsp;索引的分配设置支持下列的内置属性：

|    `_name`    |                     根据节点名字进行匹配                     |
| :-----------: | :----------------------------------------------------------: |
|  `_host_ip`   |                     根据host ip进行匹配                      |
| `_publish_ip` |            根据发布的IP（publish IP）地址进行匹配            |
|     `_ip`     |           根据`_host_ip`或者`_publish_ip`进行匹配            |
|    `_host`    |                     根据hostname进行匹配                     |
|     `_id`     |                      根据节点id进行匹配                      |
|    `_tier`    | 根据[data tier](###Data tiers)角色匹配，见[data tier allocation filtering](####Index-level data tier allocation filtering)了解更多细节 |

&emsp;&emsp;`_tier`这种过滤属性基于[node](####Node)角色，只有部分角色是 [data tier](####Index-level data tier allocation filtering)角色并且一般的（generic）[data role](#####Data node)会匹配到任意的tier filtering。

&emsp;&emsp;你可以使用通配符来指定属性值，例如：

```text
PUT test/_settings
{
  "index.routing.allocation.include._ip": "192.168.2.*"
}
```

#### Delaying allocation when a node leaves
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/delayed-allocation.html)

&emsp;&emsp;无论什么原因，有意为之，或者其他原因导致节点离开集群后，master的反应是：

- 将副本分片（replica shard）提升为主分片（primary）来代替在那个节点上的主分片
- 分配副本分片来代替缺失的副本分片（假设有足够的节点）
- 在剩余的节点上均匀地平衡分片

&emsp;&emsp;这些动作通过尽快的保证每一个分片拥有副本分片的方式来保护集群不会丢失数据。

&emsp;&emsp;尽管我们在[node level](####Index recovery settings)和[cluster level](###Cluster-level shard allocation)层限制（throttle）了并发恢复（concurrent recovery），但是这个"shard-shuffle"仍然会对集群造成一些额外的负载，如果节点很有可能很快重新加入到节点，那就没有必要做上面的动作了。想象下下面的场景：

- 节点5失去了网络连接
- master将副本分片提升为主分片，原来的主分片在节点5上
- master将新的副本分片分配到集群中的其他节点
- 每一个新的副本分片都要通过网络从主分片中复制获得
- 更多分片移动到其他节点来平衡集群
- 节点5在几分钟后回到了集群
- master将分片分配给节点5来平衡集群

&emsp;&emsp;如果master可以等待几分钟，那么缺失的分片就可以以最小的网络代价将分片分配给Node5。已经被自动[flush](####Flush API)的空闲分片（没有收到索引请求的分片为idle shard）的处理过程会更快。

&emsp;&emsp;因为动态设置`index.unassigned.node_left.delayed_timeout`，默认值为`1m`，节点离开集群后，未分配（unassigned）的分配（allocate）时间可能会被延后。

&emsp;&emsp;可以在一个live index（或者所有的索引）上更新这个设置：

```text
PUT _all/_settings
{
  "settings": {
    "index.unassigned.node_left.delayed_timeout": "5m"
  }
}
```

&emsp;&emsp;开启延迟分配后，上面的场景就会变成：

- 节点5失去了网络连接
- master将副本分片提升为主分片，原来的主分片在节点5上
- master记录日志：unassigned shard的分片被延后了，以及延后时间
- 由于存在unassigned replica shards，集群的颜色为黄色
- `timeout`过期前，节点5在几分钟后回到了集群
- 缺失的副本分片重新分配到节点5（and sync-flushed shards recover almost immediately）

> NOTE：This setting will not affect the promotion of replicas to primaries, nor will it affect the assignment of replicas that have not been assigned previously. In particular, delayed allocation does not come into effect after a full cluster restart. Also, in case of a master failover situation, elapsed delay time is forgotten (i.e. reset to the full initial delay).

##### Cancellation of shard relocation

&emsp;&emsp;如果延后分配超时，master会将缺失的分片分配给其他的节点并开始恢复。如果缺失的节点重新回到集群，并且它的分片跟主分片有相同的sync-id，shard recovery会被取消并synced shard会用于恢复。

&emsp;&emsp;因为这个理由，默认的`timeout`只要设置为一分钟：即使shard relocation已经开始，取消恢复而是使用synced shard有更小的代价。

##### Monitoring delayed unassigned shards

&emsp;&emsp;因为这个timeout设置导致延后分配的分片数量可以通过[cluster health API](####Cluster health API)查看：

```text
GET _cluster/health 
```

&emsp;&emsp;第1行，这个请求会返回一个`delayed_unassigned_shards`的值。

##### Removing a node permanently

&emsp;&emsp;如果某个节点不再回到集群，你可能想让Elasticsearch马上分配缺失的分片，只要将timeout的值更新为0：

```text
PUT _all/_settings
{
  "settings": {
    "index.unassigned.node_left.delayed_timeout": "0"
  }
}
```

&emsp;&emsp;缺失的分片开始恢复后，你就可以重新设置这个值。

#### Index recovery prioritization
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/recovery-prioritization.html)

&emsp;&emsp;whenever possible，未分配的索引会根据优先级依次进行恢复。根据下面的条件对索引进行排序：

- 可选的`index.priority`的值（higher before lower）
- 索引的创建时间（higher before lower）
- 索引的名字（higher before lower）

&emsp;&emsp;这意味着默认情况下，新的索引会先于旧的索引进行恢复。

&emsp;&emsp;使用每一个索引中可自动更新的`index.priority`来自定义索引的优先顺序。例如：

```text
PUT index_1

PUT index_2

PUT index_3
{
  "settings": {
    "index.priority": 10
  }
}

PUT index_4
{
  "settings": {
    "index.priority": 5
  }
}
```

&emsp;&emsp;在上面的例子中：

- 首先恢复`index_3`，因为它有最高的`index.priority`
- 接着恢复`index_4`，因为它有第二高的`index.priority`
- 然后恢复`index_2`，因为相比较`index_1`它是最近创建的
- 最后恢复`index_1`

&emsp;&emsp;这个设置可以是一个整数，可以通过[update indices settings](####Update index settings API)更新。

```text
PUT index_4/_settings
{
  "index.priority": 1
}
```

#### Total shards per node
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/allocation-total-shards.html)

&emsp;&emsp;cluster-level的分片分配器（shard allocator）会尝试将一个索引的多个分片尽可能的分布在多个节点上。然而由于你拥有的分片和索引的数量，以及它们的大小，可能没法总是平均的进行分配。

&emsp;&emsp;下面的动态设置允许你在每一个节点上，硬性限制（hard limit）单个索引允许的分片数量：

##### index.routing.allocation.total_shards_per_node

- 单个节点上允许分配的分片（副本分片和主分片）总数。默认没有限制。

&emsp;&emsp;你也可以限制一个节点上的分片总数并且regardless of index：

##### cluster.routing.allocation.total_shards_per_node

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）每一个节点上允许分配的主分片和副本分片的最大数量。默认值`-1`（没有限制）。

&emsp;&emsp;Elasticsearch会在分片分配时检查这个值，例如，某个集群的`cluster.routing.allocation.total_shards_per_node`的值为`100`，并且三个节点使用下面的分片分配：

- Node A： 100个分片
- Node B：98个分片
- Node C：1个分片

&emsp;&emsp;如果Node C发生故障，Elasticsearch会将分片分配到Node B。分配到Node A会超过node A的分片限制。

> WARNING：这些设置强行（impose）设置了限制会导致一些分片无法被分配。使用时需要注意这个问题。

#### Index-level data tier allocation filtering
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/data-tier-shard-filtering.html)

&emsp;&emsp;你可以通过index-level的设置`_tier_preference`来控制将索引分配到哪个[data tier ](###Data tiers)中。

&emsp;&emsp;这个设置对应的data node roles：

- [data_content](#####Content data node)
- [data_hot](#####Hot data node)
- [data_warm](#####Warm data node)
- [data_cold](#####Cold data node)
- [data_frozen](#####Frozen data node)

> NOTE：[date](#####Data node) role不是合法的data tier因此不能用于`_tier_preference`。The frozen tier stores [partially mounted indices](######Partially mounted index) exclusively


##### Data tier allocation settings

###### index.routing.allocation.include.\_tier\_preference

&emsp;&emsp;该参数的值是一个list。首先将索引分配到list中第一个data tier中，该节点必须是可用的。防止无法在期望的（prefer）data tier分配索引时导致无法分配这个索引。例如，如果你设置`index.routing.allocation.include._tier_preference`为`data_warm,data_hot`，索引会分配到拥有`data_warm`角色的节点上，如果在warm tier中没有节点，但是有拥有`data_hot`角色的节点，那么就分配到这个节点中。Used in conjuction with [data tiers](####Data tier index allocation)。

### Index blocks 

[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-modules-blocks.html#index-blocks-read-only)

#### Index block settings

##### index.blocks.read_only

### Merge
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-modules-merge.html)

&emsp;&emsp;Elasticsearch中的一个分片即Lucene中的索引，Lucene中的索引由一个或多个段（segment）组成。Segment是内部存储单元（internal storage element），用于存储数据，并且是不可改变的（immutable）。小段会周期性的合并到大段中并expunge deletes。

&emsp;&emsp;合并过程使用auto-throttle来平衡合并跟其他行为，例如查询之间的硬件资源的使用。

#### Merge scheduling

&emsp;&emsp;merge scheduler（ConcurrentMergeScheduler）控制了在需要进行合并时合并的执行过程。合并在不同的线程中运行，当使用的线程数量达到最大值后，后续的合并需要等待merge thread可用后才继续执行。

&emsp;&emsp;merge scheduler支持下面的动态参数：

##### index.merge.scheduler.max_thread_count

&emsp;&emsp;单个分片上可以同时合并的线程数量。默认值为`Math.max(1, Math.min(4 <<node.processors, node.processors>> / 2))`。这个值在固态硬盘（solid-state-disk）上 work well。如果你的索引工作在spinning platter drives，将这个值降为`1`。

### Similarity module
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-modules-similarity.html)

### Slow log
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-modules-translog.html)

#### Search Slow Log

&emsp;&emsp;分片层的慢查询日志（slow search log）允许将慢查询（query and fetch phases）记录到一个指定的日志文件中。

&emsp;&emsp;可以同时为query跟fetch设置阈值，见下面的例子：

```text
index.search.slowlog.threshold.query.warn: 10s
index.search.slowlog.threshold.query.info: 5s
index.search.slowlog.threshold.query.debug: 2s
index.search.slowlog.threshold.query.trace: 500ms

index.search.slowlog.threshold.fetch.warn: 1s
index.search.slowlog.threshold.fetch.info: 800ms
index.search.slowlog.threshold.fetch.debug: 500ms
index.search.slowlog.threshold.fetch.trace: 200ms
```

&emsp;&emsp;上述的设置都是动态的，可以通过[update indices settings](####Update index settings API)设置，例如：

```text
PUT /my-index-000001/_settings
{
  "index.search.slowlog.threshold.query.warn": "10s",
  "index.search.slowlog.threshold.query.info": "5s",
  "index.search.slowlog.threshold.query.debug": "2s",
  "index.search.slowlog.threshold.query.trace": "500ms",
  "index.search.slowlog.threshold.fetch.warn": "1s",
  "index.search.slowlog.threshold.fetch.info": "800ms",
  "index.search.slowlog.threshold.fetch.debug": "500ms",
  "index.search.slowlog.threshold.fetch.trace": "200ms"
}
```

&emsp;&emsp;默认情况下阈值是关闭的（默认值为-1）。

&emsp;&emsp;由于是分片层级别范围，意味着记录的是对于特定分片的查询。日志中不包含全部的查询请求，而是通过广播到每一个分片去执行日志的记录。跟请求级别（request level）相比，这种分片层级别的日志记录的好处是能关联到某个机器上的情况。

&emsp;&emsp;在`log4j2.properties`文件中配置查询慢日志。

#### Identifying search slow log origin

&emsp;&emsp;明确是什么导致了慢查询通常是非常有用的。如果在请求的header中带有`X-Opaque-ID`，那么在查询慢日志（Search Slow log）中会增加一个额外的`id`域来描述user ID。

```text
{
  "type": "index_search_slowlog",
  "timestamp": "2030-08-30T11:59:37,786+02:00",
  "level": "WARN",
  "component": "i.s.s.query",
  "cluster.name": "distribution_run",
  "node.name": "node-0",
  "message": "[index6][0]",
  "took": "78.4micros",
  "took_millis": "0",
  "total_hits": "0 hits",
  "stats": "[]",
  "search_type": "QUERY_THEN_FETCH",
  "total_shards": "1",
  "source": "{\"query\":{\"match_all\":{\"boost\":1.0}}}",
  "id": "MY_USER_ID",
  "cluster.uuid": "Aq-c-PAeQiK3tfBYtig9Bw",
  "node.id": "D7fUYfnfTLa2D7y-xw6tZg"
}
```

#### Index Slow log

&emsp;&emsp;索引慢日志跟查询慢日志是类似的。以`_index_indexing_slowlog.json.`为后缀的日志以及阈值使用跟查询慢日志相同的配置方式：

```text
index.indexing.slowlog.threshold.index.warn: 10s
index.indexing.slowlog.threshold.index.info: 5s
index.indexing.slowlog.threshold.index.debug: 2s
index.indexing.slowlog.threshold.index.trace: 500ms
index.indexing.slowlog.source: 1000
```

&emsp;&emsp;上述的设置都是动态的，可以通过[update indices settings](####Update index settings API)设置，例如：

```text
PUT /my-index-000001/_settings
{
  "index.indexing.slowlog.threshold.index.warn": "10s",
  "index.indexing.slowlog.threshold.index.info": "5s",
  "index.indexing.slowlog.threshold.index.debug": "2s",
  "index.indexing.slowlog.threshold.index.trace": "500ms",
  "index.indexing.slowlog.source": "1000"
}
```

&emsp;&emsp;默认情况下，Elasticsearch会在慢日志（slowlog）中记录`_source`中前1000个字符。你可以通过`index.indexing.slowlog.source`更改这个限制。设置为`0`或者`false`将不会记录`source`字段。如果设置为`true`则会记录完整的`_source`并且不关心其内容的大小。`_source`中的内容被格式化（format）过使得可以作为日志中的单独一行。如果不格式化`_source`的内容是件重要的事情，那么可以通过将`index.indexing.slowlog.reformat`设置为`false`来关闭formatting，这样会使得`_source`中的内容以多行的形式出现在日志中。

&emsp;&emsp;在`log4j2.properties`文件中配置索引慢日志。

#### Slow log levels

&emsp;&emsp;可以通过设置查询\索引慢日志日志级别来设置合适的域值，来关闭（switch off）冗长的（more verbose）设置。例如可以设置`index.indexing.slowlog.level: INFO`那么我们就可以设置`index.indexing.slowlog.threshold.index.debug`跟`index.indexing.slowlog.threshold.index.trace`为`-1`。

### Store
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-modules-store.html)

&emsp;&emsp;Store模块允许你控制索引数据在磁盘上的存储和访问方式。

> NOTE：这是一个low-level的设置。一些store的实现有较差的并发性或者没有对堆内存的使用进行优化。我们建议使用默认值（sticking to the defaults）

##### File system storage types

&emsp;&emsp;现在有不同的文件系统实现或者存储类型（file system implementations or storage types）。默认情况下，Elasticsearch会基于操作系统环境来选择最佳实现。

&emsp;&emsp;Storage type可以通过在`config/elasticsearch.yml`文件中配置来显示的为所有的索引进行设置：

```text
index.store.type: hybridfs
```

这是一个静态设置，可以在创建索引时基于每个索引进行设置：

```text
PUT /my-index-000001
{
  "settings": {
    "index.store.type": "hybridfs"
  }
}
```

> WARNING：这是一个专家（expert-only）设置并且可能在未来的版本中移除。

&emsp;&emsp;下面的内容列出了所有支持的不同类型的存储：

###### fs

&emsp;&emsp;默认的文件系统实现。会根据操作系统环境选择最佳的实现方式。目前在所有支持的系统上都是`hybridfs`，但可能会发生变化。

###### simplefs

&emsp;&emsp;deprecated::[7.15, "simplefs已经被弃用并将在8.0中移除"，转而用niofs或者其他文件系统。Elasticsearch 7.15 及以后的版本中使用niofs作为simplefs这个存储类型会提供相对于simplefs更好或者相同的性能]

&emsp;&emsp;Simple FS 类型是使用随机访问文件的文件系统存储的简单实现（straightforward implementation）（对应Lucene中的[SimpleFsDirectory](https://www.amazingkoala.com.cn/Lucene/Store/2019/0613/66.html)）。这种实现有较差的并发性能（使用多线程会有瓶颈）并且没有对堆内存的使用进行优化。

###### niofs

&emsp;&emsp;NIO FS类型使用NIO将分片索引存储在文件系统上（对应Lucene中的[NIOFSDirectory](https://www.amazingkoala.com.cn/Lucene/Store/2019/0613/66.html)）。他允许多线程并发的读取相同的文件。由于在SUN Java实现中存在一个bug，所以不建议在Windows上使用这个类型并且没有对堆内存的使用进行优化。

###### mmapfs

&emsp;&emsp;MMap FS 类型通过将文件映射到内存（mmap）将分片索引存储在文件系统上（对应Lucene中的[MMapDirectory](https://www.amazingkoala.com.cn/Lucene/Store/2019/0613/66.html)）。内存映射占用了进程中的一部分虚拟内存地址空间，等于被映射文件的大小。在使用这个之前确保你拥有大量的[virtual address space](####Virtual memory)。

###### hybridfs

&emsp;&emsp;`hybridfs`类型是`niofs`和`mmapfs`的混合类型，基于不同类型的文件的读取访问模式选择最佳的文件系统类型。目前只有Lucene term dictionary，norms和doc values文件是内存映射的。其他的文件使用Lucene的[NIOFSDirectory](https://www.amazingkoala.com.cn/Lucene/Store/2019/0613/66.html)打开。跟`mmapfs`一样，在使用这个之前确保你拥有大量的[virtual address space](####Virtual memory)。

&emsp;&emsp;你可以通过`node.store.allow_mmap`来限制（restrict）`mmapfs`以及`hybridfs`的使用。这是一个布尔类型的设置描述是否允许内存映射（memory-mapping）。默认值是允许的。这个设置是很有用的，比如说，如果你处于无法控制创建大量内存映射的环境中，那么你需要禁用使用内存映射的能力。

#### Preloading data into the file system cache
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/preload-data-to-file-system-cache.html)


### Translog
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-modules-translog.html)

&emsp;&emsp;Lucene中的[commit](https://www.amazingkoala.com.cn/Lucene/Index/2019/0906/91.html)会将更改（changes）持久化到磁盘，这是一种开销很大的操作，所以不能在每次索引或者删除操作后执行commit。如果在索引过程中退出或者硬件错误，那么在一次commit后，下一次commit前这个期间发生的变更会被移除（丢失）。

&emsp;&emsp;Lucene中，每一次单独的变更都执行commit的话其开销是很大的，所以每一个分片都会把写操作（writes operation）写入到事务日志（transaction log）中，即所谓的translog。所有的索引和删除操作在Lucene中建立索引后（未持久化到磁盘）会被添加到translog中，在发生crash时间后，最近被确认的（acknowledge）（已经写入Lucene的内存意味着确认，但未持久化到磁盘）但是不在Lucene commit中的操作，在分片恢复时会使用translog进行恢复。

&emsp;&emsp;Elasticsearch中执行[flush](####Flush API)即执行Lucene中的commit操作，并且开始一个新的translog generation。flush会在后台自动执行使得不会让translog记录太多操作，这样在恢复期间不会因为重新（replay）执行translog中的操作使得花费很多的时间。也可以通过API来执行flush，但这几乎是没必要的。



#### Translog settings

&emsp;&emsp;translog中的数据只有在它是`fsync`并且commit后会持久化到磁盘上。硬件故障的事件或者是操作系统崩溃或者JVm崩溃或者分片失败，上一次commit之后的translog数据都会丢失。

&emsp;&emsp;`index.translog.durability`默认设置为`request`意味着在translog在成功`fsynced`并且commit到主分片以及每一个分配的副本后，Elasticsearch将只报告成功的索引、删除、更新、bulk request给client。如果`index.translog.durability`设置为`async`,那么Elasticsearch只会每隔`index.translog.sync_interval`对translog进行`fsynced`并且commit，意味着当节点恢复时，任何crash之前的操作可能会丢失。

&emsp;&emsp;下面每个索引的[dynamically updatable](###Index APIs)设置控制translog的行为：

##### index.translog.sync_interval

&emsp;&emsp;translog fsynced 到磁盘并且commit的间隔，不考虑写操作，默认时间是5s。不允许设置100ms以下的值。

##### index.translog.durability

&emsp;&emsp;translog是否在每一次的index、delete、update或者bulk request都进行`fsynced`并且commit。可以设置为下面的值：

######  request

&emsp;&emsp;（默认值）每一个请求都进行`fsynced`并且commit。硬件错误事件时，所有确认的写操作已经commit到磁盘。

###### async

&emsp;&emsp;每隔`index.translog.sync_interval`进行`fsynced`并且commit。在出现失败事件时，上一次自动commit后的所有确认的写操作都会被丢弃

##### index.translog.flush_threshold_size

&emsp;&emsp;所有在Lucene中没有安全持久化的操作都会存储在translog中，这个操作对读操作是可见的，在分片被停后并且必须要恢复时，它们用于重新索引操作。这个设置控制了这些操作占用总量的最大值，防止恢复时占用太长的时间。当达到最大值时会触发flush，生成一个新的Lucene commit。默认值是`512mb`。


### History retention
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-modules-history-retention.html)

&emsp;&emsp;Elasticsearch有时候需要replay在分片上执行的一些操作。例如，如果一个副本分片简单的offline了，相比较从头开始（from scratch）构造这个副本分片，只replay它在offline期间丢失的操作有着更高的效率。同样的，[cross-cluster replication](###Cross-cluster replication)的工作方式为：在leader cluster执行操作，然后在follower cluster上replay这些操作。

&emsp;&emsp;Elasticsearch对索引的写操作在Lucene层来说只有[两个操作](https://www.amazingkoala.com.cn/Lucene/Index/2019/0626/68.html)：索引（添加）一篇文档或者删除现有的文档。更新操作的实现方式是先删除旧的文档然后索引一篇新的文档。索引一篇文档到Lucene的操作包含了所有用于replay的信息，但是对于文档的删除不是这样的。为了解决这个问题， Elasticsearch使用了名为`软删除`[soft deletes](https://www.amazingkoala.com.cn/Lucene/Index/2020/0616/148.html)的功能来保留Lucene索引上最近的删除信息，使得可以用于replay。

&emsp;&emsp;Elasticsearch只保留索引中一些最近删除的文档，是因为软删除的文档仍然占用一些空间。Elasticsearch最终会完全的丢弃这些软删除的文档来释放空间使得索引不会随着时间一直增长。Elasticsearch不需要replay在分片上执行的每一个操作，因为总是有可能在remote cluster执行完整的分片拷贝。然而，复制整个分片可能比replay一些丢失的操作要花费更多的时间，所以Elasticsearch会保留期望在未来用于replay的所有操作。

&emsp;&emsp;Elasticsearch使用了`shard history retention leases`的机制来追踪期望在未来将用于replay的操作。每一个可能需要用于replay的操作的分片拷贝（shard copy）必须首先为自己创建一个shard history retention leases。例如，this shard copy might be a replica of a shard或者在使用cross-cluster replication时的follower index。每一个retention lease会追踪对应的分片拷贝第一个没有收到的操作的序号，随着分片拷贝收到新的操作，增加retention lease中的序号值，表示不会在未来replay这些操作。一旦soft-deleted operations没有被任何的retention lease占用（hold），Elasticsearch就会丢弃它们。

&emsp;&emsp;如果分片拷贝发生了故障，那它会停止更新它的shard history retention lease。意味着Elasticsearch会保留所有的新的操作，使得在分片拷贝恢复后执行replay操作。然而，retention lease只持续一个有限的时间。如果分片拷贝没有及时的恢复，那retention lease会到期。这可以保护Elasticsearch，防止出现某个分片拷贝永久性的故障后，retention history会永久保留的问题，一旦某个retention lease到期后，Elasticsearch可以开始丢失这些历史信息。如果分片拷贝在retention lease到期后才恢复，那Elasticsearch会回退到拷贝整个索引，因为这时候再也没法简单的replay丢失的操作了。retention lease的过期时间默认是`12h`，这个时间已经足够长了，适用于大多数的场景。

#### History retention settings

##### index.soft_deletes.enabled

&emsp;&emsp;索引是否开启soft deletes（Deprecated in 7.6.0）。Soft deletes只能在创建索引时配置并且是Elasticsearch6.5及以后的版本中的索引创建。默认值为`true`。

##### index.soft_deletes.retention_lease.period

&emsp;&emsp;在retention lease过期前保留shard history retention lease的最大时间。Shard history retention leases 能保证Lucene在执行段的合并时仍然会[保留](https://www.amazingkoala.com.cn/Lucene/Index/2020/0616/148.html)Soft delete对应的信息。如果soft delete在复制（replicate）到follower之前，在段的合并期间被合并了，那么follower中接下来的操作会因为leader上不完整的历史操作而失败。默认值`12h`。


### Index Sorting
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-modules-index-sorting.html)

&emsp;&emsp;在Elasticsearch中创建一个新的索引时，可以配置每一个分片中的段内文档的排序方式。默认情况下，Lucene不会使用任何的排序。`index.sort.*`这些设置定义了使用哪些域用于段中文档的排序。

> WARNING：nested fields are not compatible with index sorting。because they rely on the assumption that nested documents are stored in contiguous doc ids, which can be broken by index sorting。An error will be thrown if index sorting is activated on an index that contains nested fields.

&emsp;&emsp;下面的例子定义如何在单个域上定义一个排序

```text
PUT my-index-000001
{
  "settings": {
    "index": {
      "sort.field": "date", 
      "sort.order": "desc"  
    }
  },
  "mappings": {
    "properties": {
      "date": {
        "type": "date"
      }
    }
  }
}
```

&emsp;&emsp;第5行，根据`date`域排序

&emsp;&emsp;第6行，降序

&emsp;&emsp;也可以使用多个域进行排序：

```text
PUT my-index-000001
{
  "settings": {
    "index": {
      "sort.field": [ "username", "date" ], 
      "sort.order": [ "asc", "desc" ]       
    }
  },
  "mappings": {
    "properties": {
      "username": {
        "type": "keyword",
        "doc_values": true
      },
      "date": {
        "type": "date"
      }
    }
  }
}
```

&emsp;&emsp;第5行，先根据`username`域排序，再根据`date`域排序 

&emsp;&emsp;第6行，根据`username`域使用升序，根据`date`使用降序


&emsp;&emsp;Index Sorting支持下面的设置：

`index.sort.field`

&emsp;&emsp;指定用于排序的一个或者多个域。只允许开启`doc_values`的`boolean` 、`numeric`、 `date`、 `keyword`类型的域。

`index.sort.order`

&emsp;&emsp;每一个排序域的排序规则：

  - `asc`: 升序
  - `desc`：降序

`index.sort.mode`

&emsp;&emsp;Elasticsearch支持使用mutil-values的域用于排序。这个参数描述的是使用哪个值用于文档排序。两个参数值可选：

  - `min`：选择最小的值
  - `max`：选择最大的值

`index.sort.missing`

&emsp;&emsp;这个参数描述的是，如果文档中缺失用于排序的域该如何对这篇文档进行排序。两个参数值可选：

  - `_last`：没有用于排序的域的文档排在最后面
  - `_first`：没有用于排序的域的文档排在最前面

> WARNING：Index Sorting只有定义在创建索引时。不能对现有的索引添加或者更新排序。Index Sorting在索引期间有一定的开销，因为文档必须在flush和merge时进行排序。你应该在启动这个功能前测试下对你的应用的影响

##### Early termination of search request

&emsp;&emsp;默认情况下，Elasticsearch中的一个查询请求需要访问匹配查询的所有文档才能获取到某个排序条件下的TopN的文档。然而当Index sort和查询中指定的排序（Search sort）一致时，每个段中的查询TopN的操作就有可能限制需要访问的文档数量。例如，如果索引中的文档根据`timestamp`域排序：

```text
PUT events
{
  "settings": {
    "index": {
      "sort.field": "timestamp",
      "sort.order": "desc" 
    }
  },
  "mappings": {
    "properties": {
      "timestamp": {
        "type": "date"
      }
    }
  }
}
```

&emsp;&emsp;第6行，索引按照`timestamp`域降序排序（most recent first）

&emsp;&emsp;你可以搜索最新的10条事件：

```text
GET /events/_search
{
  "size": 10,
  "sort": [
    { "timestamp": "desc" }
  ]
}
```

&emsp;&emsp;Elasticsearch会检测到每一个的top doc都已经排序过了，并且将只会对每个段中的TopN的文档进行比较。剩余的满足查询条件的文档用于统计结果的数量以及构造aggregation。

&emsp;&emsp;如果你只是想要查找最新的10条事件并且对于满足查询条件的文档的数量不感兴趣，那么你可以将`track_total_hits`设置为`false`：

```text
GET /events/_search
{
  "size": 10,
  "sort": [ 
      { "timestamp": "desc" }
  ],
  "track_total_hits": false
}
```

&emsp;&emsp;第4行，Index sort将用于top documents并且每个段都会在收集了前10篇文档后就提前退出。

&emsp;&emsp;这次Elasticsearch不会去统计满足查询条件的文档的数量，并且能在每一个段收集了N篇文档后就提前退出。

```text
{
  "_shards": ...
   "hits" : {  
      "max_score" : null,
      "hits" : []
  },
  "took": 20,
  "timed_out": false
}
```

&emsp;&emsp;第3行，由于提前退出了，所以不知道满足查询条件的文档数量。

> NOTE：聚合操作会收集所有满足查询条件的文档，无视`track_total_hits`的值。

#### Use index sorting to speed up conjunctions
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-modules-index-sorting-conjunctions.html)

&emsp;&emsp;Index sorting对organize Lucene doc id（不要跟`_id`混淆（conflate））非常有用，使得conjunction更加高效（a AND b AND ...）。如果任意一个clause没有匹配到，那么整个conjunction都不会匹配，conjunction可以变的非常高效。通过使用Index sorting，我们可以把没有匹配的文档放一起，有助于在范围很大的ID区间内进行跳跃，跳过那些不匹配的文档。

&emsp;&emsp;这个trick只能在low-cardinality的域上工作。A rule of thumb，你首先应该将拥有low-cardinality以及经常用于过滤的域作为排序字段。The sort order (asc or desc) does not matter as we only care about putting values that would match the same clauses close to each other。

&emsp;&emsp;例如，如果你正在索引汽车用于销售，你应该根据fuel type, body type, make, year of registration and finally mileage进行排序。

### Indexing pressure
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-modules-indexing-pressure.html)

&emsp;&emsp;索引文档到Elasticsearch会引入系统的内存和CPU的负载。每一个索引操作，包含了coordinating, primary和replica这几个阶段。这些阶段在集群中的多个节点上运行。

&emsp;&emsp;Indexing pressure可以由外部的索引请求或者内部的，例如恢复，跨集群副本分片（cross-cluster replication）引起。如果系统中有过多的索引工作，集群会达到饱和（saturated）。这会对其他操作造成不好的（adversely）影响，例如查询，cluster coordination以及background processing。

&emsp;&emsp;为了防止出现这个问题，Elasticsearch内部会监控索引负载。当负载超过一定的限制，新的索引操作会被reject。

#### Indexing stages

&emsp;&emsp;外部的索引操作（external indexing operation）会经历三个阶段：coordinating，primary以及replica。见[Basic write model](#####Basic write model)。

#### Memory limits

&emsp;&emsp;节点设置`indexing_pressure.memory.limit`严格限制了未完成（outstanding）索引请求的可用的字节数量。这个设置默认值是堆内存的10%。

&emsp;&emsp;每一个索引阶段（indexing stage）的开头，Elasticsearch会统计一个索引请求占用的字节数。在索引阶段的结尾才会释放这个统计。意味着上游的阶段会统计请求开销直到所有的下游都完成。例如，coordinating请求会保留统计直到primary和replica阶段完成，primary请求会保留统计直到每一个同步的replica返回响应，如果有需要的话开启replica重试。

&emsp;&emsp;当未完成的coordinating, primary和 replica的索引占用字节超过配置的值后，节点会在coordinating或者primary阶段reject这个新的索引请求。

&emsp;&emsp;当未完成的replica阶段的索引字节超过配置限制的1.5倍后，节点会在replicate阶段reject这个索引请求。这种设计意味着由于indexing pressure作用于节点，它会天然的在发生未完成的replica工作时会停止coordinating和primary工作。

&emsp;&emsp;`indexing_pressure.memory.limit`的默认为`10%`。你应该深思熟虑后再去修改它。这个设置只作用于索引请求。这意味着其他的索引开销（buffers，listener，等等）同样需要堆内存。Elasticsearch的其他组件同样需要内存，这个值设置的太高会影响其他有内存操作（memory operation）的操作和组件。

#### Monitoring

&emsp;&emsp;你可以使用[node stats API](####Nodes stats API)查询indexing pressure metrics。

#### Indexing pressure settings

##### indexing_pressure.memory.limit

&emsp;&emsp;索引请求可能需要消费的字节数。当达到或者超过这个限制后，节点会reject新的coordinating和primary操作。当replica操作消费超过1.5倍，节点会reject新的replicate请求。默认值是堆内存的10%。

## Mapping
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping.html)

&emsp;&emsp;Mapping是定义一篇文档中的域（field）如何存储以及索引的过程。

&emsp;&emsp;每篇文档都是域的集合，每个域都有自己的[date type](###Field data types)。在mapping你的数据时，你要创建一个mapping定义，定义中包含的域的集合跟文档相关（pertinent）。mapping的定义同样包括了[metadata fields](###Metadata fields)。比如`_source`域，它自定义了文档相关metadata的处理方式。

&emsp;&emsp;使用`dynamic mapping`和`explicit mapping`来定义你的数据。每一种方式在你使用数据过程中有不同的好处。例如，显示的对域进行映射使得不按照默认方式进行处理，或者你想要获得对域的创建有更多的控制，随后你可以允许Elasticsearch对其他的域进行动态映射。

> NOTE：在7.0.0之前，mapping的定义包含一个类型名（type name）。Elasticsearch 7.0.0以及之后的版本再也不接受一个默认的mapping。见[Removal of mapping types](###Removal of mapping types)。
> 

**Experiment with mapping options**

&emsp;&emsp;使用[Define runtime fields in a search request](####Define runtime fields in a search request)来体验不同的mapping options，同时还可以在查询请求中通过覆盖mapping中字段值的方式来解决你的Index mapping中的错误。

#### Dynamic mapping(overview)

&emsp;&emsp;[Dynamic mapping](####Dynamic field mapping)适合你刚开始接触Elasticsearch/Mapping时体验数据的探索。Elasticsearch会自动的增加新的域，你只需要索引文档就可以了。你可以添加域到top-level mapping中，以及inner [object](####Object field type)和[nested](####Nested field type)域中。

&emsp;&emsp;使用[dynamic templates](####Dynamic templates)自定义mappings，根据满足的条件动态的添加域。

#### Explicit mapping(overview)

&emsp;&emsp;[Explicit mapping](###Explicit mapping)允许你精确的选择并定义mapping definition，例如：

- 哪些string域应该作为full text field
- 哪些域包含数值，日期或者地理位置
- 日期的[format](####format)
- 为[dynamically added fields](###Dynamic mapping)自定义规则来控制mapping

&emsp;&emsp;在不需要reindex的前提下，使用[runtime fields](####Map a runtime field)作出策略变更。runtime field和indexed field配合使用来平衡资源使用和性能。索引文件体积会更小，但是会降低查询性能。

#### Settings to prevent mapping explosion

&emsp;&emsp;在一个索引中定义太多的域会导致mapping explosion，引起OOM的错误以及难以恢复的情况。

&emsp;&emsp;想象这么一种场景，每一篇新的文档都会引入新的域，比如使用了[dynamic mapping](###Dynamic mapping)。每一个域添加到Index mapping中，随着mapping中域的数量的增长便成长为了问题。

&emsp;&emsp;使用[mapping limit settings](###Mapping limit settings)限制mapping（手动或者动态创建）中域的数量，防止因为文档导致mapping explosion。

### Dynamic mapping
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/dynamic-mapping.html)

&emsp;&emsp;Elasticsearch最重要的一个功能就是能够让你快速的去探索数据。索引一篇文档，你不需要首先创建一个索引，然后定义好mapping，接着定义每一个域，你只需要直接对一篇文档进行索引，索引名、类型、域都会自动的配置好。

```text
PUT data/_doc/1 
{ "count": 5 }
```

&emsp;&emsp;第1行，创建一个名为data的索引，以及`_doc`类型的mapping，和一个域名为`count`，数据类型为`long`的域。


&emsp;&emsp;这种自动检测并且添加（addition of）一个新域的机制称为动态索引dynamic mapping。可以基于下列方法来自定义你的动态索引规则：

- [Dynamic field mappings](####Dynamic field mapping)：用于管理域的检测的规则
- [Dynamic templates](####Dynamic templates)：配置自定义的规则实现自动的添加域

> TIP：[Index templates](##Index templates)允许你为新的索引配置默认的mapping，settings 以及[aliases](##Aliases) ，不管是自动还是显示创建索引。

#### Dynamic field mapping
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/dynamic-field-mapping.html)

&emsp;&emsp;当Elasticsearch检测到文档中有一个新域，会默认将它添加到mapping中。参数[dynamic](####dynamic(mapping parameter))控制了其行为方式。

&emsp;&emsp;你可以设置参数`dynamic`的值为`true`或者`runtime`来显示的指引Elasticsearch对于即将到来的文档动态的创建域。当开启了dynamic field mapping ，Elasticsearch会根据下表中的规则决定如何映射为不同类型的域。

> NOTE：Elasticsearch仅支持下表中域的数据类型（[field data type](###Field data types)）的自动检测，你必须显式的指定其他数据类型。

|                      **JSON data type**                      |                **"dynamic":"true"**                |              **"dynamic":"runtime"**               |
| :----------------------------------------------------------: | :------------------------------------------------: | :------------------------------------------------: |
|                             null                             |                   No field added                   |                   No field added                   |
|                      `true` or `false`                       |                     `boolean`                      |                     `boolean`                      |
|                           `double`                           |                      `float`                       |                      `double`                      |
|                            `long`                            |                       `long`                       |                       `long`                       |
|                           `object`                           |                      `object`                      |                   No field added                   |
|                           `array`                            | Depends on the first non-`null` value in the array | Depends on the first non-`null` value in the array |
|  `string` that passes [date detection](#####Date detection)  |                       `date`                       |                       `date`                       |
| `string` that passes [numeric detection](#####Numeric detection) |                 `float` or `long`                  |                 `double` or `long`                 |
| `string` that doesn’t pass `date`detection or `numeric` detection |         `text` with a `.keyword`sub-field          |                     `keyword`                      |

&emsp;&emsp;你可以同时在文档层或者[object](####Object field type)层来关闭动态mapping。将参数`dynamic`设置为`false`来忽略新的域，设置为`strict`来当Elasticsearch遇到一个未知的域时就reject这个文档。

> TIP：可以通过[update mapping API](####Update mapping API)来对现有的域的参数`dynamic`的值进行更新。

&emsp;&emsp;你可以为[Date detection](#####Date detection)和[Numeric detection](#####Numeric detection)自定义dynamic field mapping的规则。你可以使用[dynamic_templates](####Dynamic templates)来自定义个性化的mapping规则并应用到额外的dynamic fields。

##### Date detection

&emsp;&emsp;如果开启了`date_detection`（默认开启），新来的string类型的域会被检测是否其域值能够被`dynamic_date_formats`中指定的日期模板匹配到。如果匹配到了，就添加对应的新的[date](####Date field type)域。

&emsp;&emsp;`dynamic_date_formats`的默认值是：
- [ "strict_date_optional_time","yyyy/MM/dd HH:mm:ss Z||yyyy/MM/dd Z"]

&emsp;&emsp;例如：

```text
PUT my-index-000001/_doc/1
{
  "create_date": "2015/09/02"
}

GET my-index-000001/_mapping 
```

&emsp;&emsp;第6行，`create_date`域以`"yyyy/MM/dd HH:mm:ss Z||yyyy/MM/dd Z"`这种[format](####format)作为[date](####Date field type)被添加。

##### Disabling date detection

&emsp;&emsp;可以将`date_detection`的值设置为`false`来关闭date detection。

```text
PUT my-index-000001
{
  "mappings": {
    "date_detection": false
  }
}

PUT my-index-000001/_doc/1 
{
  "create_date": "2015/09/02"
}
```

&emsp;&emsp;第8行，`create_date`被添加为[text](####Text type family)域。

##### Customizing detected date formats

&emsp;&emsp;另外，`dynamic_date_formats`也能定制为你自己的[formats](####format))：

```text
PUT my-index-000001
{
  "mappings": {
    "dynamic_date_formats": ["MM/dd/yyyy"]
  }
}

PUT my-index-000001/_doc/1
{
  "create_date": "09/25/2015"
}
```

##### Numeric detection

&emsp;&emsp;JSON原生支持浮点型以及整数的数据类型，但是有些应用或者语言有时会将数值类型作为（render）字符串类型。通常正确的解决办法是显示的映射这些域。但是numeric detection能使得自动实现（默认关闭）：

```text
PUT my-index-000001
{
  "mappings": {
    "numeric_detection": true
  }
}

PUT my-index-000001/_doc/1
{
  "my_float":   "1.0", 
  "my_integer": "1" 
}
```

&emsp;&emsp;第10行，`my_float`被添加为[float](####Numeric field types)域

&emsp;&emsp;第11行，`my_integer`被添加为[integer](####Numeric field types)域

#### Dynamic templates
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/dynamic-templates.html)

&emsp;&emsp;Dynamic template能让你在默认的[dynamic field mapping rules](####Dynamic field mapping)之外，更好的控制Elasticsearch如何映射你的数据。通过设置[dynamic](####dynamic(mapping parameter))参数的值为`true`或者`runtime`来开启dynamic mapping。然后你就可以基于下面的匹配条件使用dynamic template来自定义mapping，动态的作用（apply）到域：

- [match_mapping_type](#####match_mapping_type)对Elasticsearch检测到的数据类型进行操作
- [match and unmatch ](#####match and unmatch)使用一个pattern对域名进行匹配
- [path_match and path_unmatch](#####path_match and path_unmatch)对full dotted path的域名进行操作
- 如果dynamic template没有‘定义`match_mapping_type`，`match`或者`path_match`，那它不会匹配任何域。 it won’t match any field. You can still refer to the template by name in dynamic_templates section of a [bulk request](#####Bulk indexing usage)。

&emsp;&emsp;使用`{name}`和`{dynamic_type}`这些[template variables](#####Template variables)作为mapping中的占位符。

> IMPORTANT：只有当某个域包含一个具体的值才能添加Dynamic field mappings。当域中包含`null`或者空的数组时，Elasticsearch不会添加一个dynamic field mapping。如果在`dynamic_template`中使用了`null_value`选项，只有在一篇有具体的值的文档索引后才能作用这个选项。 

&emsp;&emsp;Dynamic template由一组带名字的object数组组成：

```text
  "dynamic_templates": [
    {
      "my_template_name": { 
        ... match conditions ... 
        "mapping": { ... } 
      }
    },
    ...
  ]
```

&emsp;&emsp;第3行，模板的名字可以是任意的string value

&emsp;&emsp;第4行，匹配条件可以包含: `match_mapping_type`, `match`, `match_pattern`, `unmatch`, `path_match`, `path_unmatch`

&emsp;&emsp;第5行，匹配到的域应该使用的mapping

##### Validating dynamic templates

&emsp;&emsp;如果提供的mapping中包含了一个非法的mapping 片段（snippet），则返回一个validation error。Validation会在索引期间应用dynamic template时发生，并且在大多数情况下发生在dynamic template更新后。提供一个非法的mapping片段可能会因为下面的一些条件导致更新或者dynamic template的校验失败：

- If no `match_mapping_type` has been specified but the template is valid for at least one predefined mapping type, mapping片段式合法的。然而，如果匹配了模板的某个域索引为不同的类型，那么会返回validation error。例如，配置一个没有`match_mapping_type`的dynamic template在索引期间认为string类型是合法的，但是匹配了模板的某个域索引为了long类型，那么会返回一个validation error。建议在mapping片段中配置`mathc_mapping_type`为期望的JSON类型或者想要的`type`
- 如果在mapping片段中使用`{name}`占位符，那当更新dynamic template时会跳过validation。这是因为在那个时间点域名是未知的。在索引期间应用模版时才进行validation。

&emsp;&emsp;模版是有序处理的，使用匹配到的第一个模板。当通过[update mapping](####Update mapping API) API更新新的dynamic template后，所有现有的模板会被覆盖。这使得最开始添加的模板可以被重新排序或者被删除。

##### Mapping runtime fields in a dynamic template

&emsp;&emsp;如果你想要Elasticsearch的动态的映射某些类型的新域作为runtime field，那么在索引mapping中设置`"dynamic":"runtime"`。这些域不会被索引，在查询期间从`_source`中获取。

&emsp;&emsp;或者你可以使用默认的动态mapping规则，然后创建dynamic模版将指定的域映射为runtime field。你可以在索引mapping中设置`"dynamic":"true"`然后创建一个dynamic template将某些类型的新域映射为runtime field。

&emsp;&emsp;比如说你的数据中的每一个域的域名都是以`ip_`开始的。基于[dynamic mapping rules](#####match_mapping_type)，Elasticsearch将通过了`numeric`检测的域映射为`float`或者`long`，然而你可以创建一个dynamic template将string类型的值映射为runtime filed并且域的类型为`ip`>

&emsp;&emsp;下面的请求中定义了一个名为`strings_as_ip`的dynamic template。当Elasticsearch检测到新的域值为`string`域匹配到了`ip*`这个pattern，它就会将这些域映射为runtime field并且域的类型为[ip](####ip)。因为`ip`域没有动态映射，你可以使用带`"dynamic":"true"`或者`"dynamic":"runtime"`的模版。

```text
PUT my-index-000001/
{
  "mappings": {
    "dynamic_templates": [
      {
        "strings_as_ip": {
          "match_mapping_type": "string",
          "match": "ip*",
          "runtime": {
            "type": "ip"
          }
        }
      }
    ]
  }
}
```

&emsp;&emsp;见[this template](#####text-only mappings for strings)来了解如何使用dynamic template将`string`域映射为indexed filed或者runtime field。

##### match_mapping_type

&emsp;&emsp;`match_mapping_type`是数据类型，用于JSON parser。因为JSON不能区分`integer`和`long`，`double`和`float`。浮点型数值都被认为是`double`的JSON数据类型，`integer`数值都被认为是`long`。

> NOTE：在dynamic mappings中，Elasticsearch总是选择wider data type。唯一特例是`float`，它需要比`double`更少的存储空间并且对于大多数应用来说精度是足够的。Runtime field不支持`float`，这就是为什么`"dynamic":"runtime"`使用的是`double`。

&emsp;&emsp;Elasticsearch会自动检测下面的数据类型：

| JSON data type | "dynamic":"true" | "dynamic":"runtime" |
| :------------: | :--------------: | :-----------------: |
| null           | 不增加域           |      不增加域               |
| true/false | boolean | Boolean |
| double | float | double |
| long | long | long |
| object | object | 不增加域 |
| array | 取决于数组中第一个不是null的值 | 取决于数组中第一个不是null的值 |
| string（通过了[date detection](#####Date detection)的解析） | float或者long | double或者long |
| string（通过了[date detection](#####Date detection)的解析） | date | date |
| string没有通过date或者numeric的解析 | text以及.keyword的子域 | keyword |


&emsp;&emsp;使用通配符(`*`)匹配所有的数据类型。

&emsp;&emsp;例如，如果想要将integer field都映射为`integer`而不是`long`，并且所有的`string` field映射为`text`和`keyword`，我们可以使用下面的模板：

```text
PUT my-index-000001
{
  "mappings": {
    "dynamic_templates": [
      {
        "integers": {
          "match_mapping_type": "long",
          "mapping": {
            "type": "integer"
          }
        }
      },
      {
        "strings": {
          "match_mapping_type": "string",
          "mapping": {
            "type": "text",
            "fields": {
              "raw": {
                "type":  "keyword",
                "ignore_above": 256
              }
            }
          }
        }
      }
    ]
  }
}

PUT my-index-000001/_doc/1
{
  "my_integer": 5, 
  "my_string": "Some string" 
}
```

&emsp;&emsp;第33行，`my_integer`域映射为一个`integer`

&emsp;&emsp;第34行，`my_string`域映射为`text`以及一个`keyword`（见[multi-field](####fields)）

##### match and unmatch

&emsp;&emsp;`match`参数使用pattern来匹配域名（field name），同时`unmatch`使用pattern排除`match`参数匹配到的域。

&emsp;&emsp;`match_pattern`参数调整了`match`参数的行为，支持Java regular expression来匹配域名而不是使用简单的通配符，例如：

```text
  "match_pattern": "regex",
  "match": "^profit_\d+$"
```

&emsp;&emsp;下面的例子匹配了所有名字以`long_`开头的域，但是排除了以`_text`结尾的域，并将他们的域值映射为`long`：

```text
PUT my-index-000001
{
  "mappings": {
    "dynamic_templates": [
      {
        "longs_as_strings": {
          "match_mapping_type": "string",
          "match":   "long_*",
          "unmatch": "*_text",
          "mapping": {
            "type": "long"
          }
        }
      }
    ]
  }
}

PUT my-index-000001/_doc/1
{
  "long_num": "5", 
  "long_text": "foo" 
}
```

&emsp;&emsp;第21行，`long_num`域映射为`long`

&emsp;&emsp;第22行，`long_text`使用了默认的`string`映射


##### path_match and path_unmatch

&emsp;&emsp;`path_match`和`path_unmatch`参数的工作方式跟`match`和`unmatch`是一样的，不同的是，它们是作用在full dotted path上，而不是final name，比如：`some_object.*.some_filed`。

&emsp;&emsp;下面的例子将`object`类型的域`name`中的除了`middle`的所有字段都`copy to`顶层的full_name中：

```text
PUT my-index-000001
{
  "mappings": {
    "dynamic_templates": [
      {
        "full_name": {
          "path_match":   "name.*",
          "path_unmatch": "*.middle",
          "mapping": {
            "type":       "text",
            "copy_to":    "full_name"
          }
        }
      }
    ]
  }
}

PUT my-index-000001/_doc/1
{
  "name": {
    "first":  "John",
    "middle": "Winston",
    "last":   "Lennon"
  }
}
```

&emsp;&emsp;注意的是`path_match`和`pathc_match`除了匹配leaf filed（上图中的`first`、`middle`、`last`就是leaf field）还会匹配object path（下图中的`name.tittle`就是object path）。例如，索引下面的文档会报错因为`path_match`这个设置同样会匹配到`name.tille`，这个字段是个object不能映射为text：

```text
PUT my-index-000001/_doc/2
{
  "name": {
    "first":  "Paul",
    "last":   "McCartney",
    "title": {
      "value": "Sir",
      "category": "order of chivalry"
    }
  }
}
```

##### Template variables

&emsp;&emsp;`{name}`和`{dynamic_type}`是mapping中的占位符，它们分别用域名和检测到的动态类型（detected dynamic type）进行值的替换。下面的例子中将所有域值为string的域名作为[analyzer](####analyzer)的值，并且关闭所有不是string类型的域的[doc_values](####doc_values)：

```text
PUT my-index-000001
{
  "mappings": {
    "dynamic_templates": [
      {
        "named_analyzers": {
          "match_mapping_type": "string",
          "match": "*",
          "mapping": {
            "type": "text",
            "analyzer": "{name}"
          }
        }
      },
      {
        "no_doc_values": {
          "match_mapping_type":"*",
          "mapping": {
            "type": "{dynamic_type}",
            "doc_values": false
          }
        }
      }
    ]
  }
}

PUT my-index-000001/_doc/1
{
  "english": "Some English text", 
  "count":   5 
}
```

&emsp;&emsp;第30行，因为域名为`english`的域值是`string`类型，那么它的域名将作为anlyzer参数的值

&emsp;&emsp;第31行，因为`count`的域值是个数值，所以它会被动态映射为`long`类型并且关闭`doc_values`

##### Dynamic template examples

&emsp;&emsp;下面是一些可能比较有用的dynamic template：

###### Structured search

&emsp;&emsp;当你设置了`"dynamic":"true"`，Elasticsearch会将string filed映射为`text`域以及`keyword`的子域。如果你只要索引结构化的内容并且对不需要全文检索，你可以让Elasticsearch只映射为`keyword`域。然而，你在查询那些被索引的域时，你必须提供精确的关键字。

```text
PUT my-index-000001
{
  "mappings": {
    "dynamic_templates": [
      {
        "strings_as_keywords": {
          "match_mapping_type": "string",
          "mapping": {
            "type": "keyword"
          }
        }
      }
    ]
  }
}
```

###### text-only mappings for strings

&emsp;&emsp;与上一个例子相反的（contrary）是，如果你只关心string filed上的全文检索并且不计划使用聚合，排序或者精确（exact ）查询，你可以让Elasticsearch映射为`text`了：

```text
PUT my-index-000001
{
  "mappings": {
    "dynamic_templates": [
      {
        "strings_as_text": {
          "match_mapping_type": "string",
          "mapping": {
            "type": "text"
          }
        }
      }
    ]
  }
}
```

&emsp;&emsp;或者你可以创建一个dynamic template将你的string field在mapping的runtime块映射为`keyword`域。当Elasticsearch检测到`string`类型的域，会将这些域创建为runtime field并且域的类型为`keyword`。

&emsp;&emsp;尽管你的`string`域不会被索引，但是它们的值会存储在`_source`中并且可以用于查询请求，聚合，过滤和排序。

&emsp;&emsp;例如下面的请求创建了一个dynamic template将`string`域映射为runtime field并且域的类型为`keyword`，尽管`runtime`的定义是空白的。但是基于Elasticsearch用于添加域的类型到mapping的[dynamic mapping rules](####Dynamic field mapping)，新的`string`域会被映射为runtime field并且域的类型为`keyword`。所有没有通过date detection和numeric detection的`string`都会被自动的映射为`keyword`：

```text
PUT my-index-000001
{
  "mappings": {
    "dynamic_templates": [
      {
        "strings_as_keywords": {
          "match_mapping_type": "string",
          "runtime": {}
        }
      }
    ]
  }
}
```

&emsp;&emsp;你可以索引一篇简单的文档：

```text
PUT my-index-000001/_doc/1
{
  "english": "Some English text",
  "count":   5
}
```

&emsp;&emsp;当你查看mapping时，你会看到`english`域是一个runtime field并且域的类型为`keyword`。

```text
GET my-index-000001/_mapping
```

```text
{
  "my-index-000001" : {
    "mappings" : {
      "dynamic_templates" : [
        {
          "strings_as_keywords" : {
            "match_mapping_type" : "string",
            "runtime" : { }
          }
        }
      ],
      "runtime" : {
        "english" : {
          "type" : "keyword"
        }
      },
      "properties" : {
        "count" : {
          "type" : "long"
        }
      }
    }
  }
}
```

###### Disabled norms

&emsp;&emsp;Norms是索引期间（index-time）的打分因子。如果你不关心打分，那你就不能根据文档分数对文档进行排序，你可以禁用索引中的打分因子的存储来节省一些空间。

```text
PUT my-index-000001
{
  "mappings": {
    "dynamic_templates": [
      {
        "strings_as_keywords": {
          "match_mapping_type": "string",
          "mapping": {
            "type": "text",
            "norms": false,
            "fields": {
              "keyword": {
                "type": "keyword",
                "ignore_above": 256
              }
            }
          }
        }
      }
    ]
  }
}
```

&emsp;&emsp;模版中出现的`keyword`域是dynamic mappings中默认存在的。当然如果你不需要在这个域上执行精确查询或者聚合，你可以不需要它们，你可以正如上文中描述的那样来移除他们。

###### Time series

&emsp;&emsp;当在Elasticsearch中处理时序数据时，通常有很多的数值类型的域，你会经常使用它们用于聚合但是从来不用于过滤。在这种场景下，你可以不索引这些域来节省磁盘空间并且能获得一些索引速度的提升：

```text
PUT my-index-000001
{
  "mappings": {
    "dynamic_templates": [
      {
        "unindexed_longs": {
          "match_mapping_type": "long",
          "mapping": {
            "type": "long",
            "index": false
          }
        }
      },
      {
        "unindexed_doubles": {
          "match_mapping_type": "double",
          "mapping": {
            "type": "float", 
            "index": false
          }
        }
      }
    ]
  }
}
```

&emsp;&emsp;第18行，正如默认的dynamic mapping rules，double会被映射为`float`，因为通常来说精确是足够的，并且只要一半的磁盘空间。

### Explicit mapping
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/explicit-mapping.html)

&emsp;&emsp;相比较Elasticsearch猜测你的数据类型，如果你更了解数据类型，你可能想要指定自己的显示的mapping（explicit mapping）。

&emsp;&emsp;你可以在[create an index ](####Create an index with an explicit mapping)或者[add fields to an existing index](####Add a field to an existing mapping)时创建域的mapping（field mapping）。

#### Create an index with an explicit mapping

&emsp;&emsp;你可以使用[create index](####Create index API) API创建一个explicit mapping的新的索引。

```text
PUT /my-index-000001
{
  "mappings": {
    "properties": {
      "age":    { "type": "integer" },  
      "email":  { "type": "keyword"  }, 
      "name":   { "type": "text"  }     
    }
  }
}
```

&emsp;&emsp;第5行，创建一个名为`age`，类型为[integer](####Numeric field types)的域

&emsp;&emsp;第6行，创建一个名为`email`，类型为[keyword](####Keyword type family)的域

&emsp;&emsp;第7行，创建一个名为`name`，类型为[text](####Text type family)的域

#### Add a field to an existing mapping

&emsp;&emsp;你可以使用[update mapping](####Update mapping API) API在一个现有的（existing）索引中增加一个或者多个域。

&emsp;&emsp;下面的例子中创建了一个名为`employee-id`，类型为`keyword`，mapping参数为[index](####index(mapping parameter))的域。这意味着`employee-id`域的域值会被存储但是不会被索引，即无法用于查询。

```text
PUT /my-index-000001/_mapping
{
  "properties": {
    "employee-id": {
      "type": "keyword",
      "index": false
    }
  }
}
```

#### Update the mapping of a field

&emsp;&emsp;除了支持更新[mapping parameters](###Mapping parameters)，你不能对现有的域更改mapping或者域的类型。更改一个现有的域会invalidate已经写入到索引文件中的数据。

&emsp;&emsp;如果你更改data stream的backing index中域的类型，见[Change mappings and settings for a data stream](###Change mappings and settings for a data stream)。

&emsp;&emsp;如果你需要在其他索引中更改域的mapping，那么使用正确的mapping创建一个新的索引，然后[reindex](####Reindex API)你的数据到那个索引中。

&emsp;&emsp;重命名一个域会invalidate使用旧的域名索引的数据，你可以增加一个[alias](####Alias field type)域创建一个替换的域名。

#### View the mapping of an index

&emsp;&emsp;你可以使用[get mapping](####Get mapping API) API查看现有的索引的mapping。

```text
GET /my-index-000001/_mapping
```

&emsp;&emsp;下面的API返回下面的响应：

```text
{
  "my-index-000001" : {
    "mappings" : {
      "properties" : {
        "age" : {
          "type" : "integer"
        },
        "email" : {
          "type" : "keyword"
        },
        "employee-id" : {
          "type" : "keyword",
          "index" : false
        },
        "name" : {
          "type" : "text"
        }
      }
    }
  }
}
```

#### View the mapping of specific fields

&emsp;&emsp;如果你只想指定一个或者多个域并查看它们的mapping，你可以使用[get field mapping](####Get field mapping API) API。

&emsp;&emsp;如果你的索引包含很多的域或者你不需要完整的索引的mapping，这个接口是很有用的。

&emsp;&emsp;下面的请求展示了`employee-id`域的mapping。

```text
GET /my-index-000001/_mapping/field/employee-id
```

&emsp;&emsp;这个API返回下面的响应：

```text
{
  "my-index-000001" : {
    "mappings" : {
      "employee-id" : {
        "full_name" : "employee-id",
        "mapping" : {
          "employee-id" : {
            "type" : "keyword",
            "index" : false
          }
        }
      }
    }
  }
}
```

### Runtime fields
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/runtime.html)

&emsp;&emsp;`runtime field`是在查询阶段计算出来的域，可以让你用于：

- 添加新的域到现有的文档并且不需要重新索引你的数据
- 不需要你理解是如何结构化（ how it’s structured）的就可以开始处理你的数据
- 在查询期间覆盖索引中域的值
- 在不用修改现有策略情况下，定义用于特殊用途的域

&emsp;&emsp;你可以通过search API访问`runtime field`，就像访问其他域一样，Elasticsearch不会对`runtime field`区别对待。你可以在[index mapping](####Map a runtime field)或者[search request](####Define runtime fields in a search request)中定义`runtime field`，提供不同的方式体现了`runtime field`内部的灵活性。

&emsp;&emsp;在`_search` API上使用[fields](###Retrieve selected fields from a search)参数来 [retrieve the values of runtime fields](####Retrieve a runtime field)，runtime filed不会在`_source`中展示，但是`fields` API可以在所有域上使用，即使这些域不在`_source`中。

&emsp;&emsp;Runtime fields用于处理日志数据（log data）时候特别好用（见[例子](####Explore your data with runtime fields)），特别是当你不确定数据结构时。尽管查询性能会降低，但是你能更快的索引日志数据，并且索引体积较小。

##### Benefits

&emsp;&emsp;由于runtime fiels没有被索引，所以新增的runtime field不会增加索引大小。你直接在索引mapping中定义runtime fields来节省存储开销以及提高提取（ingestion）速度。你能够更快的把数据提取到Elastic Stack并可以正确的访问。当你定义了一个runtime field，你可以在在查询请求中使用它用于聚合、过滤、和排序。

&emsp;&emsp;如果你让一个runtime field成为一个索引域（indexed field），你不需要修改任何请求来指定runtime filed。甚至你可以指定某个域在某些索引中是runtime field，同时在某个索引中是一个索引域。你可以灵活的选择哪些域作为索引域还是runtime field。

&emsp;&emsp;Runtime fileds最核心、最重要的好处就是它提供了在提取（ingest）文档后可以在这篇文档中添加域的能力。这种能力简化了mapping的设计，因为你不需要提前决定用哪种数据类型进行解析，可以在任何时候修改。使用runtime field使得有更小的索引和更快的提取时间，这将使用更少的资源并降低你的操作成本。

##### Incentives

&emsp;&emsp;使用脚本的`_search` API中的很多方法可以用runtime field替代。runtime field的使用会受到runtime field中定义的脚本需要处理的文档数量的影响。例如，如果你在`_search` API上使用`fields`参数来[retrieve the values of a runtime field](####Retrieve a runtime field)，脚本跟[script fields ](#####Script fields)一样，都只会对top hits进行处理。

&emsp;&emsp;你可以使用script fields访问`_source`中的值并且返回基于脚本计算出的值。runtime fields有相同的能力，但是能提供更好的灵活性，因为在查询请求中可以对runtime fields进行查询以及聚合。Script field只能获取数据。

&emsp;&emsp;同样的，你可以在查询请求中基于脚本写一个[script query](####Script query)来过滤文档。runtime field提供一个类似的功能并且更加灵活。你编写一个脚本来创建域值，它们能在任何地方都可见，比如[fields](####Retrieve selected fields from a search)，[all queries](##Query DSL)以及[aggregation](##Aggregations)。

&emsp;&emsp;你可以使用脚本来[sort search results](###Sort search results)，但使用runtime field可以完成完全相同的工作。

&emsp;&emsp;如果你将查询请求中所有的脚本替换为runtime field，性能是差不多的，因为它们计算的文档数量是一样的。这个功能的性能很大程度上取决于脚本需要计算的文档数量。

##### Compromises

&emsp;&emsp;Runtime fields使用更少的磁盘空间并且在查询你的数据上提供灵活性，但是会因为runtime脚本中定义的计算而影响查询性能。

&emsp;&emsp;若要平衡性能和灵活性，将那些你常用于查询，过滤，例如timestamp这类域，对这些域进行索引。在执行查询时，Elasticsearch会自动的使用这些索引域（indexed field）在很快的响应时间内获得结果。随后你可以使用runtime fields来限制Elasticsearch需要用于计算的域的数量。索引域和runtime field的配合使用，可以提供数据弹性以及如何为其他的域定义查询。

&emsp;&emsp;使用[asynchronous search API](####Async search)用于执行包含runtime field的查询。这种查询方法可以抵偿计算每一篇包含runtime field的文档的性能影响。如果query不能同步返回数据集，你将在这些结果变得可见后异步获取到。

> IMPORTANT：对runtime field的查询是昂贵的，如果[search.allow_expensive_queries](##Query DSL)设置为`false`，Elasticsearch不允许昂贵的查询并且reject所有对runtime field的查询。

#### Map a runtime field
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/runtime-mapping-fields.html)

&emsp;&emsp;通过在mapping中定义一个`runtime` section以及一个[Painless script](###How to write scripts)就可以映射到runtime fileds。这个脚本可以访问所在文档中所有的内容，包括可以通过`params._source`来读取到`_source`的内容以及任何域加上域值。在查询时，查询条件中包含了这个scripted field（下文中的day_of_week），脚本会运行并且生成结果给scripted field。

>当定义了一个脚本用于runtime fields时，脚本中必须使用 [emit method](https://www.elastic.co/guide/en/elasticsearch/painless/8.2/painless-runtime-fields-context.html)来输出计算的值。

&emsp;&emsp;下面例子的脚本中，将根据`@timestamp`的域值计算出的结果赋值给`day of the week`，`day of the week`是一个`data`类型：

```text
PUT my-index-000001/
{
  "mappings": {
    "runtime": {
      "day_of_week": {
        "type": "keyword",
        "script": {
          "source": "emit(doc['@timestamp'].value.dayOfWeekEnum.getDisplayName(TextStyle.FULL, Locale.ROOT))"
        }
      }
    },
    "properties": {
      "@timestamp": {"type": "date"}
    }
  }
}
```

&emsp;&emsp;`runtime`中可以是以下的类型：

- boolean
- composite
- date
- double
- geo_point
- ip
- keyword
- long
- [lookup](####Retrieve a runtime field)

&emsp;&emsp;`date`类型的runtime fileds的域值格式跟`date`域接受的[format](####format(mapping parameter))是一致的。

&emsp;&emsp;`lookup`类型的runtime fileds允许从关联的索引中检索域的信息，见[retrieve fields from related indices](####Retrieve a runtime field)。

&emsp;&emsp;如果开启了[dynamic field mapping](####Dynamic field mapping)，并且`dynamic`参数设置了参数值`runtime`，新的域会作为runtime自动添加到索引mapping中。

```text
PUT my-index-000001
{
  "mappings": {
    "dynamic": "runtime",
    "properties": {
      "@timestamp": {
        "type": "date"
      }
    }
  }
}
```

##### Define runtime fields without a script

&emsp;&emsp;Runtime fileds通常使用脚本来管理数据，然后有些情况下可以不使用脚本来使用runtime fileds。比如你想从`_source`中检索出某个域的域值并不作任何的改变，那你不需要提供脚本，你只需要创建一个不带脚本的runtime fileds：

```text
PUT my-index-000001/
{
  "mappings": {
    "runtime": {
      "day_of_week": {
        "type": "keyword"
      }
    }
  }
}
```

&emsp;&emsp;当没有提供脚本时，在查询期间，Elasticsearch会从`_source`中查找到跟runtime fields域名一样的域，如果存在的话就返回该域值。如果不存在，那么在response中不会包含runtime fields的任何值。

&emsp;&emsp;在大多数情况下，会优先从[doc_values](####doc_values)中读取。由于在Lucene中不同的存储方式，相比较从`_source`中检索，通过[doc_values](####doc_values)的方式读取速度更快。

&emsp;&emsp;但是有些场景下需要从`_source`中检索域的信息。比如说由于`text`类型默认情况下不会有`doc_values`，所以不得不从`_source`中读取，在其他情况下，可以选择禁用特定域上的doc_values。

>NOTE：你可以在`params._source`中添加前缀来检索想要的数据（比如`params._source.day_of_week`）,为了简单起见，定义runtime fields时，建议尽可能在mapping定义中不使用脚本。

##### Updating and removing runtime fields

&emsp;&emsp;你可以在任何时候更新或者移除runtime fileds。添加一个相同名字的runtime files就可以覆盖现在有的runtime files，通过设置为null来移除现在有的runtime files：

```text
PUT my-index-000001/_mapping
{
 "runtime": {
   "day_of_week": null
 }
}
```

###### Downstream impacts

&emsp;&emsp;更新或者移除runtime files可能会使得正在查询的请求返回的数据不一致。由于mapping的变更可能会影响到每一个分片会访问到不同版本的脚本。

>WARNING：如果你对runtime fileds进行变更或者移除，依赖runtime fileds的Existing queries or visualizations in Kibana可能会出现失败的情况，比如可视化的图表使用runtime field类型的`ip`被改为`boolean`，或者被移除后会导致出错。

#### Define runtime fields in a search request
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/runtime-search-request.html)

&emsp;&emsp;你可以在查询请求中指定一个`runtime_mappings`区域/块（section）来创建runtime fields，它只属于这次的请求。你指定了一个脚本作为`runtime_mappings`的一部分跟[adding a runtime field to the mappings](####Map a runtime field)是一样的。

&emsp;&emsp;在查询请求中定义一个runtime field跟在index mapping中使用的格式是一样的，所以只需要把查询请求中`runtime_mappings`区域中的内容直接拷贝到index mapping的`runtime`区域中。

&emsp;&emsp;下面的查询请求添加了`day_of_week`域到`runtime_mappings`区域中，这个域的域值将被自动的计算出来并且只处于这次查询请求的上下文中（only within the context of this search request）。

```text
GET my-index-000001/_search
{
  "runtime_mappings": {
    "day_of_week": {
      "type": "keyword",
      "script": {
        "source": "emit(doc['@timestamp'].value.dayOfWeekEnum.getDisplayName(TextStyle.FULL, Locale.ROOT))"
      }
    }
  },
  "aggs": {
    "day_of_week": {
      "terms": {
        "field": "day_of_week"
      }
    }
  }
}
```

##### Create runtime fields that use other runtime fields

&emsp;&emsp;你甚至可以在查询请求中定义runtime field，并且它的域值可以从其他的runtime field中计算出来。比如你批量索引了传感器的数据：

```text
POST my-index-000001/_bulk?refresh=true
{"index":{}}
{"@timestamp":1516729294000,"model_number":"QVKC92Q","measures":{"voltage":"5.2","start": "300","end":"8675309"}}
{"index":{}}
{"@timestamp":1516642894000,"model_number":"QVKC92Q","measures":{"voltage":"5.8","start": "300","end":"8675309"}}
{"index":{}}
{"@timestamp":1516556494000,"model_number":"QVKC92Q","measures":{"voltage":"5.1","start": "300","end":"8675309"}}
{"index":{}}
{"@timestamp":1516470094000,"model_number":"QVKC92Q","measures":{"voltage":"5.6","start": "300","end":"8675309"}}
{"index":{}}
{"@timestamp":1516383694000,"model_number":"HG537PU","measures":{"voltage":"4.2","start": "400","end":"8625309"}}
{"index":{}}
{"@timestamp":1516297294000,"model_number":"HG537PU","measures":{"voltage":"4.0","start": "400","end":"8625309"}}
```

&emsp;&emsp;你意识到你将数值类型numeric type索引成了`text`类型，你想要在`measures.start`以及`measures.end`域上进行聚合操作却无法实现，因为你不能在`text`类型上进行聚合。Runtime fields to the rescue!。你可以添加runtime fileds，跟索引域index field（即measures.start和measures.end）的域名保持一致并且修改域的类型：

```text
PUT my-index-000001/_mapping
{
  "runtime": {
    "measures.start": {
      "type": "long"
    },
    "measures.end": {
      "type": "long"
    }
  }
}
```

&emsp;&emsp;Runtime field的优先级（take precedence over）比相同域名的索引域高。这种灵活性使得你可以投影（shadow）现有的域（existing fields）并且计算出新值而不用修改域本身的信息。如果你在索引数据的时候犯错了，那么你可以使用runtime field在查询阶段重新计算，将获得的值进行[override values](####Override field values at query time)。

&emsp;&emsp;现在你可以很容易的在`measures.start`和`measures.end`域上执行[average aggregation](####Avg aggregation)：

```text
GET my-index-000001/_search
{
  "aggs": {
    "avg_start": {
      "avg": {
        "field": "measures.start"
      }
    },
    "avg_end": {
      "avg": {
        "field": "measures.end"
      }
    }
  }
}
```

&emsp;&emsp;响应中返回了聚合结果并且没有改变 the underlying data（指的是measures.start跟measures.end）：

```text
{
  "aggregations" : {
    "avg_start" : {
      "value" : 333.3333333333333
    },
    "avg_end" : {
      "value" : 8658642.333333334
    }
  }
}
```

&emsp;&emsp;另外你还可以定义一个runtime field作为查询请求中的一部分来计算出一个值，在相同的query（基于上文中介绍的query）中对这个runtime field进行[stats aggregation](###Metrics aggregations)。

&emsp;&emsp;`duration`这个runtime field不在 index mapping中，当我们仍然可以使用这个域进行查询和聚合计算。下面的请求将返回经过计算的`duration`的值并且从用于聚合的文档中基于数值类型的数据执行stats aggregation来计算统计值（即count、min、max、avg、sum）：

```text
GET my-index-000001/_search
{
  "runtime_mappings": {
    "duration": {
      "type": "long",
      "script": {
        "source": """
          emit(doc['measures.end'].value - doc['measures.start'].value);
          """
      }
    }
  },
  "aggs": {
    "duration_stats": {
      "stats": {
        "field": "duration"
      }
    }
  }
}
```

&emsp;&emsp;尽管 `duration`这个runtime field只在这次查询请求的上下文中存在，你也可以对该字段进行搜索和聚合。灵活性十分的强大，它可以弥补（rectify）你在index mapping中犯的错误并且在单个查询请求中动态的完成计算。

```text
{
  "aggregations" : {
    "duration_stats" : {
      "count" : 6,
      "min" : 8624909.0,
      "max" : 8675009.0,
      "avg" : 8658309.0,
      "sum" : 5.1949854E7
    }
  }
}
```



#### Override field values at query time
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/runtime-override-values.html)

&emsp;&emsp;如果您创建的runtime field与mapping已经存在的字段具有相同的名称，则runtime field会将对mapping filed进行投影（shadow）。在查询时，Elasticsearch计算runtime field，根据脚本计算一个值，并将该值作为查询的一部分返回。因为runtime field会对mapping field进行投影（shadow），所以你可以覆盖搜索中返回的值，而不需要修改mapping field。

&emsp;&emsp;例如你在索引`my-index-000001`中添加了以下的文档：

```text
POST my-index-000001/_bulk?refresh=true
{"index":{}}
{"@timestamp":1516729294000,"model_number":"QVKC92Q","measures":{"voltage":5.2}}
{"index":{}}
{"@timestamp":1516642894000,"model_number":"QVKC92Q","measures":{"voltage":5.8}}
{"index":{}}
{"@timestamp":1516556494000,"model_number":"QVKC92Q","measures":{"voltage":5.1}}
{"index":{}}
{"@timestamp":1516470094000,"model_number":"QVKC92Q","measures":{"voltage":5.6}}
{"index":{}}
{"@timestamp":1516383694000,"model_number":"HG537PU","measures":{"voltage":4.2}}
{"index":{}}
{"@timestamp":1516297294000,"model_number":"HG537PU","measures":{"voltage":4.0}}
```

&emsp;&emsp;你后来意识到`HG537PU`传感器并没有报告它们的真实电压。索引值应该是报告值的1.7倍。你可以在`_search`请求的`runtime_mappings`部分定义一个脚本，来投影（shadow）电压场，并在查询时计算一个新值，而不是重新索引数据。

&emsp;&emsp;如果你想要查询 model number是`HG537PU`的文档：

```text
GET my-index-000001/_search
{
  "query": {
    "match": {
      "model_number": "HG537PU"
    }
  }
}
```

&emsp;&emsp;相应中包含了model number是`HG537PU`的索引值：

```text
{
  ...
  "hits" : {
    "total" : {
      "value" : 2,
      "relation" : "eq"
    },
    "max_score" : 1.0296195,
    "hits" : [
      {
        "_index" : "my-index-000001",
        "_id" : "F1BeSXYBg_szTodcYCmk",
        "_score" : 1.0296195,
        "_source" : {
          "@timestamp" : 1516383694000,
          "model_number" : "HG537PU",
          "measures" : {
            "voltage" : 4.2
          }
        }
      },
      {
        "_index" : "my-index-000001",
        "_id" : "l02aSXYBkpNf6QRDO62Q",
        "_score" : 1.0296195,
        "_source" : {
          "@timestamp" : 1516297294000,
          "model_number" : "HG537PU",
          "measures" : {
            "voltage" : 4.0
          }
        }
      }
    ]
  }
}
```

&emsp;&emsp;下面的请求中定义了一个runtime field，它的脚本中将估算域值为`HG537PU`的`model_number`域，会对于每一个满足条件的`voltage`的域值乘以1.7。

&emsp;&emsp;通过在`_serach` API中使用[field](###Retrieve selected fields from a search)参数，你可以检索`measures.voltage field`，满足查询请求中的文档会根据脚本对该域值进行计算。

```text
POST my-index-000001/_search
{
  "runtime_mappings": {
    "measures.voltage": {
      "type": "double",
      "script": {
        "source":
        """if (doc['model_number.keyword'].value.equals('HG537PU'))
        {emit(1.7 * params._source['measures']['voltage']);}
        else{emit(params._source['measures']['voltage']);}"""
      }
    }
  },
  "query": {
    "match": {
      "model_number": "HG537PU"
    }
  },
  "fields": ["measures.voltage"]
}
```

&emsp;&emsp;我们从结果中可以看到，经过计算的`measures.voltage`的域值分别是`7.14`和`6.8`。That’s more like it！。runtime field作为查询请求的一部分进行域值计算，而不修改索引值仍，索引值仍然在响应中返回：

```text
{
  ...
  "hits" : {
    "total" : {
      "value" : 2,
      "relation" : "eq"
    },
    "max_score" : 1.0296195,
    "hits" : [
      {
        "_index" : "my-index-000001",
        "_id" : "F1BeSXYBg_szTodcYCmk",
        "_score" : 1.0296195,
        "_source" : {
          "@timestamp" : 1516383694000,
          "model_number" : "HG537PU",
          "measures" : {
            "voltage" : 4.2
          }
        },
        "fields" : {
          "measures.voltage" : [
            7.14
          ]
        }
      },
      {
        "_index" : "my-index-000001",
        "_id" : "l02aSXYBkpNf6QRDO62Q",
        "_score" : 1.0296195,
        "_source" : {
          "@timestamp" : 1516297294000,
          "model_number" : "HG537PU",
          "measures" : {
            "voltage" : 4.0
          }
        },
        "fields" : {
          "measures.voltage" : [
            6.8
          ]
        }
      }
    ]
  }
}
```

#### Retrieve a runtime field
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/runtime-retrieving-fields.html)

&emsp;&emsp;在`_search` API中使用参数[fields](###Retrieve selected fields from a search)来检索runtime fields的域值。`_source`中不会展示runtime fields的信息，但是`fields`可以展示所有域的信息，即使有些域不是`_source`中的一部分。

##### Define a runtime field to calculate the day of week

&emsp;&emsp;例如下面的请求中增加了一个名为`day_of_week`的runtime filed。这个runtime filed中包含了一个根据`@timestamp`域计算星期的脚本。请求中定义了**"[dynamic](####dynamic(mapping parameter))":"runtime"**使得新添加的域在mapping中将作为runtime fileds。

```text
PUT my-index-000001/
{
  "mappings": {
    "dynamic": "runtime",
    "runtime": {
      "day_of_week": {
        "type": "keyword",
        "script": {
          "source": "emit(doc['@timestamp'].value.dayOfWeekEnum.getDisplayName(TextStyle.FULL, Locale.ROOT))"
        }
      }
    },
    "properties": {
      "@timestamp": {"type": "date"}
    }
  }
}
```

##### Ingest some data

&emsp;&emsp;我们ingest一些样本数据，有两个索引字段`@timestamp`和`message`：

```text
POST /my-index-000001/_bulk?refresh
{ "index": {}}
{ "@timestamp": "2020-06-21T15:00:01-05:00", "message" : "211.11.9.0 - - [2020-06-21T15:00:01-05:00] \"GET /english/index.html HTTP/1.0\" 304 0"}
{ "index": {}}
{ "@timestamp": "2020-06-21T15:00:01-05:00", "message" : "211.11.9.0 - - [2020-06-21T15:00:01-05:00] \"GET /english/index.html HTTP/1.0\" 304 0"}
{ "index": {}}
{ "@timestamp": "2020-04-30T14:30:17-05:00", "message" : "40.135.0.0 - - [2020-04-30T14:30:17-05:00] \"GET /images/hm_bg.jpg HTTP/1.0\" 200 24736"}
{ "index": {}}
{ "@timestamp": "2020-04-30T14:30:53-05:00", "message" : "232.0.0.0 - - [2020-04-30T14:30:53-05:00] \"GET /images/hm_bg.jpg HTTP/1.0\" 200 24736"}
{ "index": {}}
{ "@timestamp": "2020-04-30T14:31:12-05:00", "message" : "26.1.0.0 - - [2020-04-30T14:31:12-05:00] \"GET /images/hm_bg.jpg HTTP/1.0\" 200 24736"}
{ "index": {}}
{ "@timestamp": "2020-04-30T14:31:19-05:00", "message" : "247.37.0.0 - - [2020-04-30T14:31:19-05:00] \"GET /french/splash_inet.html HTTP/1.0\" 200 3781"}
{ "index": {}}
{ "@timestamp": "2020-04-30T14:31:27-05:00", "message" : "252.0.0.0 - - [2020-04-30T14:31:27-05:00] \"GET /images/hm_bg.jpg HTTP/1.0\" 200 24736"}
{ "index": {}}
{ "@timestamp": "2020-04-30T14:31:29-05:00", "message" : "247.37.0.0 - - [2020-04-30T14:31:29-05:00] \"GET /images/hm_brdl.gif HTTP/1.0\" 304 0"}
{ "index": {}}
{ "@timestamp": "2020-04-30T14:31:29-05:00", "message" : "247.37.0.0 - - [2020-04-30T14:31:29-05:00] \"GET /images/hm_arw.gif HTTP/1.0\" 304 0"}
{ "index": {}}
{ "@timestamp": "2020-04-30T14:31:32-05:00", "message" : "247.37.0.0 - - [2020-04-30T14:31:32-05:00] \"GET /images/nav_bg_top.gif HTTP/1.0\" 200 929"}
{ "index": {}}
{ "@timestamp": "2020-04-30T14:31:43-05:00", "message" : "247.37.0.0 - - [2020-04-30T14:31:43-05:00] \"GET /french/images/nav_venue_off.gif HTTP/1.0\" 304 0"}
```

##### Search for the calculated day of week

&emsp;&emsp;下面的请求使用 search API检索`day_of_week`域，这个域在mapping定义为runtime field。这个域的域值在查询期间动态的生成，并不需要重新对数据索引，也不需要索引这个域。这种灵活性使得不需要更改索引域的情况下改变映射：

```text
GET my-index-000001/_search
{
  "fields": [
    "@timestamp",
    "day_of_week"
  ],
  "_source": false
}
```

&emsp;&emsp;上述请求会为每一个匹配的文档返回一个`day_of_week`域。我们可以定义另一个名为`client_ip`的runtime filed，对`message`域进行操作来获取它的域值来完善接下来的查询。

```text
PUT /my-index-000001/_mapping
{
  "runtime": {
    "client_ip": {
      "type": "ip",
      "script" : {
      "source" : "String m = doc[\"message\"].value; int end = m.indexOf(\" \"); emit(m.substring(0, end));"
      }
    }
  }
}
```

&emsp;&emsp;使用域名为`client_ip`的runtime filed，指定一个域值来进行查询：

```text
GET my-index-000001/_search
{
  "size": 1,
  "query": {
    "match": {
      "client_ip": "211.11.9.0"
    }
  },
  "fields" : ["*"]
}
```

&emsp;&emsp;这次查询的响应中命中2条，`day_of_week`（`sunday`）的值会在查询中使用mapping中定义的脚本计算获得。结果中包含了IP address的值为`211.11.9.0`的文档：

```text
{
  ...
  "hits" : {
    "total" : {
      "value" : 2,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "my-index-000001",
        "_id" : "oWs5KXYB-XyJbifr9mrz",
        "_score" : 1.0,
        "_source" : {
          "@timestamp" : "2020-06-21T15:00:01-05:00",
          "message" : "211.11.9.0 - - [2020-06-21T15:00:01-05:00] \"GET /english/index.html HTTP/1.0\" 304 0"
        },
        "fields" : {
          "@timestamp" : [
            "2020-06-21T20:00:01.000Z"
          ],
          "client_ip" : [
            "211.11.9.0"
          ],
          "message" : [
            "211.11.9.0 - - [2020-06-21T15:00:01-05:00] \"GET /english/index.html HTTP/1.0\" 304 0"
          ],
          "day_of_week" : [
            "Sunday"
          ]
        }
      }
    ]
  }
}
```

##### Retrieve fields from related indices

>WARNING：这个功能属于技术预览（technical preview ），可能在未来的发行版中变更或者移除。

&emsp;&emsp;`_serach` API中的[fields](###Retrieve selected fields from a search)参数可以用来检索类型为`lookup`的runtime fileds，它的域值可以从其他索引中获取。

```text
POST ip_location/_doc?refresh
{
  "ip": "192.168.1.1",
  "country": "Canada",
  "city": "Montreal"
}

PUT logs/_doc/1?refresh
{
  "host": "192.168.1.1",
  "message": "the first message"
}

PUT logs/_doc/2?refresh
{
  "host": "192.168.1.2",
  "message": "the second message"
}

POST logs/_search
{
  "runtime_mappings": {
    "location": {
        "type": "lookup", 
        "target_index": "ip_location", 
        "input_field": "host", 
        "target_field": "ip", 
        "fetch_fields": ["country", "city"] 
    }
  },
  "fields": [
    "host",
    "message",
    "location"
  ],
  "_source": false
}
```

&emsp;&emsp;第24行，定义了类型为`lookup`的runtime field，从目标索引中使用[term](####Term query)查询获得的结果作为runtime field的域值

&emsp;&emsp;第25行，ip_location即目标索引

&emsp;&emsp;第26行，host域的域值将作为term查询条件中的域值

&emsp;&emsp;第27行，ip域的域名将作为term查询条件中的域名

&emsp;&emsp;第28行，要求从目标索引ip_location中返回的域，见[fields](###Retrieve selected fields from a search)

&emsp;&emsp;上述的查询将从ip_location索引中为返回的搜索命中的每个ip地址返回country和city。

```text
{
  "took": 3,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 2,
      "relation": "eq"
    },
    "max_score": 1.0,
    "hits": [
      {
        "_index": "logs",
        "_id": "1",
        "_score": 1.0,
        "fields": {
          "host": [ "192.168.1.1" ],
          "location": [
            {
              "city": [ "Montreal" ],
              "country": [ "Canada" ]
            }
          ],
          "message": [ "the first message" ]
        }
      },
      {
        "_index": "logs",
        "_id": "2",
        "_score": 1.0,
        "fields": {
          "host": [ "192.168.1.2" ],
          "message": [ "the second message" ]
        }
      }
    ]
  }
}
```

&emsp;&emsp;在返回的结果中，每一个结果中单独的包含了一个从目标索引中获取的数据，这些数据分组展示（JSON中的array）。每一个结果中最多展示一条从目标索引中获取的数据，如果上文中的term查询获取了多个结果，那么就随机返回一条。

#### Index a runtime field
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/runtime-indexed.html)

&emsp;&emsp;Runtime fields在运行时的上下文中定义，你可以在[search query](####Define runtime fields in a search request) 的上下文定义runtime field或者在index mapping中定义一个[runtime](####Map a runtime field)区域（section）。如果你决定将runtime field写入到索引来获得较好的性能，你只要把完整的runtime filed的定义（包括脚本）都移动到 index mapping的上下文中。Elasticsearch会自动的使用这些索引域来驱动查询，获得更快的响应时间。这种能力使得只需要你写一次脚本并应用到任何支持runtime field的上文中。

>NOTE：目前不支持索引composite runtime field。

&emsp;&emsp;你可以使用runtime filed来限制Elasticsearch需要计算的字段的数量。将索引字段与runtime filed结合使用可以为你索引的数据以及如何为其他域定义查询方式提供灵活性。

>IMPORTANT：当你把runtime field写入到索引中，你不能更新其包含的脚本。如果你需要更新脚本，那么使用这个更新后的脚本并且创建一个新的域。

&emsp;&emsp;比如你的公司想要替换一些旧的压力值。已经连接的传感器只能报告真实读数的一小部分。相比较使用新的传感器来得到新的压力值，你决定基于现有的读数进行计算。基于现有的报告值，你可以为在索引`my-index-000001`中定义下列的mapping：

```text
PUT my-index-000001/
{
  "mappings": {
    "properties": {
      "timestamp": {
        "type": "date"
      },
      "temperature": {
        "type": "long"
      },
      "voltage": {
        "type": "double"
      },
      "node": {
        "type": "keyword"
      }
    }
  }
}
```

&emsp;&emsp;你批量写入了传感器的一些样本数据，包括了从每一个传感器中的voltage的读数：

```text
POST my-index-000001/_bulk?refresh=true
{"index":{}}
{"timestamp": 1516729294000, "temperature": 200, "voltage": 5.2, "node": "a"}
{"index":{}}
{"timestamp": 1516642894000, "temperature": 201, "voltage": 5.8, "node": "b"}
{"index":{}}
{"timestamp": 1516556494000, "temperature": 202, "voltage": 5.1, "node": "a"}
{"index":{}}
{"timestamp": 1516470094000, "temperature": 198, "voltage": 5.6, "node": "b"}
{"index":{}}
{"timestamp": 1516383694000, "temperature": 200, "voltage": 4.2, "node": "c"}
{"index":{}}
{"timestamp": 1516297294000, "temperature": 202, "voltage": 4.0, "node": "c"}
```

&emsp;&emsp;在跟一些网页工程师沟通后，你们意识到传感器应该报告2倍于现在的压力值，并且有可能更高。你创建了一个名为`voltage_corrected`的runtime filed，然后检索当前的`voltage`的值并且乘以2：

```text
PUT my-index-000001/_mapping
{
  "runtime": {
    "voltage_corrected": {
      "type": "double",
      "script": {
        "source": """
        emit(doc['voltage'].value * params['multiplier'])
        """,
        "params": {
          "multiplier": 2
        }
      }
    }
  }
}
```

&emsp;&emsp;在`_search` API上使用[fields](###Retrieve selected fields from a search)参数来检索计算后的值：

```text
GET my-index-000001/_search
{
  "fields": [
    "voltage_corrected",
    "node"
  ],
  "size": 2
}
```

&emsp;&emsp;在审查了传感器数据以及运行了一些测试后，对于报告的传感器数据，`multiplier`的值应该是`4`。为了获得更高的性能，你决定把`voltage_corrected`域写入到索引中，并且使用新的`multiplier`参数：

&emsp;&emsp;只要在一个新的名为`my-index-000001`索引中，把名为`voltage_corrected`的runtime field中的所有定义直接复制到新索引的mappings中，就是这么简单。你可以额外定义一个参数`on_script_error`来控制在索引期间当脚本抛出异常时是否要reject整个文档。

```text
PUT my-index-000001/
{
  "mappings": {
    "properties": {
      "timestamp": {
        "type": "date"
      },
      "temperature": {
        "type": "long"
      },
      "voltage": {
        "type": "double"
      },
      "node": {
        "type": "keyword"
      },
      "voltage_corrected": {
        "type": "double",
        "on_script_error": "fail", 
        "script": {
          "source": """
        emit(doc['voltage'].value * params['multiplier'])
        """,
          "params": {
            "multiplier": 4
          }
        }
      }
    }
  }
}
```

&emsp;&emsp;第19行，在索引期间当脚本抛出异常时文档会被reject，将`on_script_error`的值置为`ignore`使得将`voltage_corrected`注册到[\_ignored](####\_ignored field)的metadata filed中然后继续索引（索引当前文档的其他域）。

&emsp;&emsp;批量写入传感器的数据到`my-index-000001`中：

```text
POST my-index-000001/_bulk?refresh=true
{ "index": {}}
{ "timestamp": 1516729294000, "temperature": 200, "voltage": 5.2, "node": "a"}
{ "index": {}}
{ "timestamp": 1516642894000, "temperature": 201, "voltage": 5.8, "node": "b"}
{ "index": {}}
{ "timestamp": 1516556494000, "temperature": 202, "voltage": 5.1, "node": "a"}
{ "index": {}}
{ "timestamp": 1516470094000, "temperature": 198, "voltage": 5.6, "node": "b"}
{ "index": {}}
{ "timestamp": 1516383694000, "temperature": 200, "voltage": 4.2, "node": "c"}
{ "index": {}}
{ "timestamp": 1516297294000, "temperature": 202, "voltage": 4.0, "node": "c"}
```

&emsp;&emsp;你现在可以搜索查询中检索计算后的值，并且找到精确数值的文档。下面的范围查询返回`voltage_corrected`的值大于等于`16`并且小于等于`20`的所有文档。这里重申一遍，在`_search` API上使用[fields](###Retrieve selected fields from a search)参数来检索你想要查询的域：

```text
POST my-index-000001/_search
{
  "query": {
    "range": {
      "voltage_corrected": {
        "gte": 16,
        "lte": 20,
        "boost": 1.0
      }
    }
  },
  "fields": ["voltage_corrected", "node"]
}
```

&emsp;&emsp;响应中返回了满足范围查询的包含`voltage_corrected`域的文档，`voltage_corrected`的值基于脚本中计算出的值：

```text
{
  "hits" : {
    "total" : {
      "value" : 2,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "my-index-000001",
        "_id" : "yoSLrHgBdg9xpPrUZz_P",
        "_score" : 1.0,
        "_source" : {
          "timestamp" : 1516383694000,
          "temperature" : 200,
          "voltage" : 4.2,
          "node" : "c"
        },
        "fields" : {
          "voltage_corrected" : [
            16.8
          ],
          "node" : [
            "c"
          ]
        }
      },
      {
        "_index" : "my-index-000001",
        "_id" : "y4SLrHgBdg9xpPrUZz_P",
        "_score" : 1.0,
        "_source" : {
          "timestamp" : 1516297294000,
          "temperature" : 202,
          "voltage" : 4.0,
          "node" : "c"
        },
        "fields" : {
          "voltage_corrected" : [
            16.0
          ],
          "node" : [
            "c"
          ]
        }
      }
    ]
  }
}
```

#### Explore your data with runtime fields
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/runtime-examples.html)

&emsp;&emsp;假设你从一个数据量很大的日志数据中提取字段。数据的索引非常的耗时，并且使用大量的磁盘空间，并且你想要在不提前给出一个策略（mapping）前就能探索其数据结构（Date structure）。

&emsp;&emsp;已知日志数据中包含了你想要提取的字段（field）。在这个例子中，我们集中关注`@timestamp`和`message`域。通过使用running fields，你可以定义脚本，并在查询期间计算这些域的值。

##### Define indexed fields as a starting point

&emsp;&emsp;你可以先从一个简单的例子开始，将`@timestamp`和`message`添加到名为`my-index-000001`的索引的mapping中，作为indexed filed。To remain flexible，使用[wildcard](#####Wildcard field type)作为`message`的域类型：

```text
PUT /my-index-000001/
{
  "mappings": {
    "properties": {
      "@timestamp": {
        "format": "strict_date_optional_time||epoch_second",
        "type": "date"
      },
      "message": {
        "type": "wildcard"
      }
    }
  }
}
```

##### Ingest some data

&emsp;&emsp;在添加了用于检索的域的mapping后，开始将你的日志数据索引到Elasticsearch中。下面的请求使用了[bulk API](####Bulk API)将原始日志数据索引到`my-index-000001`中。你可以先用一小部分样例数据来体验runtime field而不是使用所有的日志数据。

&emsp;&emsp;The final document is not a valid Apache log format, but we can account for that scenario in our script。

```text
POST /my-index-000001/_bulk?refresh
{"index":{}}
{"timestamp":"2020-04-30T14:30:17-05:00","message":"40.135.0.0 - - [30/Apr/2020:14:30:17 -0500] \"GET /images/hm_bg.jpg HTTP/1.0\" 200 24736"}
{"index":{}}
{"timestamp":"2020-04-30T14:30:53-05:00","message":"232.0.0.0 - - [30/Apr/2020:14:30:53 -0500] \"GET /images/hm_bg.jpg HTTP/1.0\" 200 24736"}
{"index":{}}
{"timestamp":"2020-04-30T14:31:12-05:00","message":"26.1.0.0 - - [30/Apr/2020:14:31:12 -0500] \"GET /images/hm_bg.jpg HTTP/1.0\" 200 24736"}
{"index":{}}
{"timestamp":"2020-04-30T14:31:19-05:00","message":"247.37.0.0 - - [30/Apr/2020:14:31:19 -0500] \"GET /french/splash_inet.html HTTP/1.0\" 200 3781"}
{"index":{}}
{"timestamp":"2020-04-30T14:31:22-05:00","message":"247.37.0.0 - - [30/Apr/2020:14:31:22 -0500] \"GET /images/hm_nbg.jpg HTTP/1.0\" 304 0"}
{"index":{}}
{"timestamp":"2020-04-30T14:31:27-05:00","message":"252.0.0.0 - - [30/Apr/2020:14:31:27 -0500] \"GET /images/hm_bg.jpg HTTP/1.0\" 200 24736"}
{"index":{}}
{"timestamp":"2020-04-30T14:31:28-05:00","message":"not a valid apache log"}
```

&emsp;&emsp;现在你可以查看Elasticsearch是如何存储你的原始数据的。

```text
GET /my-index-000001
```

&emsp;&emsp;下面的mapping包含了两个域：`@timestamp`和`message`。

```text
{
  "my-index-000001" : {
    "aliases" : { },
    "mappings" : {
      "properties" : {
        "@timestamp" : {
          "type" : "date",
          "format" : "strict_date_optional_time||epoch_second"
        },
        "message" : {
          "type" : "wildcard"
        },
        "timestamp" : {
          "type" : "date"
        }
      }
    },
    ...
  }
}
```

##### Define a runtime field with a grok pattern

&emsp;&emsp;如果你想要检索结果中包含`clientip`，你可以在mapping中添加这个域并且作为runtime field。下面的runtime script定义了一个[grok pattern]()，它从单个文本（single text）的文档中提取出结构化的域。grok pattern类似与正则表达式支持aliased expressions。

&emsp;&emsp;脚本会匹配`%{COMMONAPACHELOG}` log pattern，它能处理Apache日志结构。如果pattern匹配到(`clientip != null`)，脚本会emit匹配到的IP地址。如果没有匹配到，脚本直接退出而不是报错。

```text
PUT my-index-000001/_mappings
{
  "runtime": {
    "http.client_ip": {
      "type": "ip",
      "script": """
        String clientip=grok('%{COMMONAPACHELOG}').extract(doc["message"].value)?.clientip;
        if (clientip != null) emit(clientip); 
      """
    }
  }
}
```

&emsp;&emsp;第8行，这个条件保证pattern没有匹配到也不会报错。

&emsp;&emsp;或者你可以在查询请求时定义相同的runtime field。runtime的定义以及脚本和之前在index mapping中的定义是完全一样。只要将定义拷贝到查询请求中的`runtime_mappings`块中以及一个匹配runtime field的query。这个query返回的结果跟你在index mapping中定义`http.clientip` runtime field返回的结果是一样的。前提是你在查询中指定了这个runtime field：

```text
GET my-index-000001/_search
{
  "runtime_mappings": {
    "http.clientip": {
      "type": "ip",
      "script": """
        String clientip=grok('%{COMMONAPACHELOG}').extract(doc["message"].value)?.clientip;
        if (clientip != null) emit(clientip);
      """
    }
  },
  "query": {
    "match": {
      "http.clientip": "40.135.0.0"
    }
  },
  "fields" : ["http.clientip"]
}
```

##### Define a composite runtime field

&emsp;&emsp;你可以定义一个`composite` runtime filed从单个脚本中emit多个域。你可以定义类型化的子域（typed subfields）集合然后emit多个值。在查询期间，每一个子域会检索出跟他们名字相关的值。意味着你只需要指定一次grok pattern然后就能返回多个值：

```text
PUT my-index-000001/_mappings
{
  "runtime": {
    "http": {
      "type": "composite",
      "script": "emit(grok(\"%{COMMONAPACHELOG}\").extract(doc[\"message\"].value))",
      "fields": {
        "clientip": {
          "type": "ip"
        },
        "verb": {
          "type": "keyword"
        },
        "response": {
          "type": "long"
        }
      }
    }
  }
}
```

###### Search for a specific IP address

&emsp;&emsp;使用`http.clientip` runtime field，你可以定义单个query用于查询指定的IP地址并返回所有相关的域。

```text
GET my-index-000001/_search
{
  "query": {
    "match": {
      "http.clientip": "40.135.0.0"
    }
  },
  "fields" : ["*"]
}
```

&emsp;&emsp;上面的API会返回下面的结果。由于`http`是一个`composite` runtime field，响应中的`fields`会包括每一个子域以及匹配到这个query相关的结果。不用提前构建你的数据结构，你就可以查询并以某种方式展示你的数据来体验以及决定索引哪些域。

```text
{
  ...
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "my-index-000001",
        "_id" : "sRVHBnwBB-qjgFni7h_O",
        "_score" : 1.0,
        "_source" : {
          "timestamp" : "2020-04-30T14:30:17-05:00",
          "message" : "40.135.0.0 - - [30/Apr/2020:14:30:17 -0500] \"GET /images/hm_bg.jpg HTTP/1.0\" 200 24736"
        },
        "fields" : {
          "http.verb" : [
            "GET"
          ],
          "http.clientip" : [
            "40.135.0.0"
          ],
          "http.response" : [
            200
          ],
          "message" : [
            "40.135.0.0 - - [30/Apr/2020:14:30:17 -0500] \"GET /images/hm_bg.jpg HTTP/1.0\" 200 24736"
          ],
          "http.client_ip" : [
            "40.135.0.0"
          ],
          "timestamp" : [
            "2020-04-30T19:30:17.000Z"
          ]
        }
      }
    ]
  }
}
```

&emsp;&emsp;还记得脚本中的`if`声明吗？

```text
if (clientip != null) emit(clientip);
```

&emsp;&emsp;如果脚本没有包含这个条件，当某个分片上没有匹配到pattern，query会失败。在包含这个条件后，query会跳过没有匹配到grok pattern的数据。

###### Search for documents in a specific range

&emsp;&emsp;你也可以在`timestamp`域上执行一个[range query](####Range query)。下面的请求会返回大于等于`2020-04-30T14:31:27-05:00`的文档。

```text
GET my-index-000001/_search
{
  "query": {
    "range": {
      "timestamp": {
        "gte": "2020-04-30T14:31:27-05:00"
      }
    }
  }
}
```

&emsp;&emsp;响应中会包含日志格式不匹配的文档，但是timestamp在定义的范围中。

```text
{
  ...
  "hits" : {
    "total" : {
      "value" : 2,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "my-index-000001",
        "_id" : "hdEhyncBRSB6iD-PoBqe",
        "_score" : 1.0,
        "_source" : {
          "timestamp" : "2020-04-30T14:31:27-05:00",
          "message" : "252.0.0.0 - - [30/Apr/2020:14:31:27 -0500] \"GET /images/hm_bg.jpg HTTP/1.0\" 200 24736"
        }
      },
      {
        "_index" : "my-index-000001",
        "_id" : "htEhyncBRSB6iD-PoBqe",
        "_score" : 1.0,
        "_source" : {
          "timestamp" : "2020-04-30T14:31:28-05:00",
          "message" : "not a valid apache log"
        }
      }
    ]
  }
}
```

##### Define a runtime field with a dissect pattern

&emsp;&emsp;如果你不需要正则表达式的强大功能，你也可以使用[dissect patterns](####Dissect processor)而不是grok patterns。Dissect patterns匹配固定的分隔符，但通常来说比grok快。

&emsp;&emsp;你可以使用dissect解析Apache log并且达到跟grok pattern一样的结果。相比较log pattern，它会包含你想要丢弃的string。特别的注意下你想要丢弃的string，能帮助你成功构建dissect patterns。

```text
PUT my-index-000001/_mappings
{
  "runtime": {
    "http.client.ip": {
      "type": "ip",
      "script": """
        String clientip=dissect('%{clientip} %{ident} %{auth} [%{@timestamp}] "%{verb} %{request} HTTP/%{httpversion}" %{status} %{size}').extract(doc["message"].value)?.clientip;
        if (clientip != null) emit(clientip);
      """
    }
  }
}
```

&emsp;&emsp;同样的，你可以定义一个dissect pattern来提取[HTTP response code](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)。

```text
PUT my-index-000001/_mappings
{
  "runtime": {
    "http.responses": {
      "type": "long",
      "script": """
        String response=dissect('%{clientip} %{ident} %{auth} [%{@timestamp}] "%{verb} %{request} HTTP/%{httpversion}" %{response} %{size}').extract(doc["message"].value)?.response;
        if (response != null) emit(Integer.parseInt(response));
      """
    }
  }
}
```

&emsp;&emsp;随后你可以使用`http.responses` runtime field执行一个query来检索指定的HTTP response。在`_search`请求中使用`fields`参数指明想要检索的域：

```text
GET my-index-000001/_search
{
  "query": {
    "match": {
      "http.responses": "304"
    }
  },
  "fields" : ["http.client_ip","timestamp","http.verb"]
}
```

&emsp;&emsp;下面的响应包含了HTTP response为`304`的文档。

```text
{
  ...
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "my-index-000001",
        "_id" : "A2qDy3cBWRMvVAuI7F8M",
        "_score" : 1.0,
        "_source" : {
          "timestamp" : "2020-04-30T14:31:22-05:00",
          "message" : "247.37.0.0 - - [30/Apr/2020:14:31:22 -0500] \"GET /images/hm_nbg.jpg HTTP/1.0\" 304 0"
        },
        "fields" : {
          "http.verb" : [
            "GET"
          ],
          "http.client_ip" : [
            "247.37.0.0"
          ],
          "timestamp" : [
            "2020-04-30T19:31:22.000Z"
          ]
        }
      }
    ]
  }
}
```

### Field data types
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping-types.html)

#### Alias field type
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/field-alias.html)

&emsp;&emsp;`alias`可以给索引中某个域定义一个别名（alternate）。在[search](###Search APIs)请求以及像 [field capabilities API](####Field capabilities API)中可以用来替代为目标域（target filed）。

```text
PUT trips
{
  "mappings": {
    "properties": {
      "distance": {
        "type": "long"
      },
      "route_length_miles": {
        "type": "alias",
        "path": "distance" 
      },
      "transit_mode": {
        "type": "keyword"
      }
    }
  }
}

GET _search
{
  "query": {
    "range" : {
      "route_length_miles" : {
        "gte" : 39
      }
    }
  }
}
```

&emsp;&emsp;第10行，目标域的路径。注意的是必须是全路径，包括所有的parent objects（比如：object1.object2.field）。

&emsp;&emsp;几乎所有的查询请求都可以使用alias field。特别是可以用于查询、聚合、以及排序字段，同时还有`docvalue_fields`, `stored_fields`, suggestions, and highlights。脚本同样支持alias来访问它的域值。见[unsupported APIs](#####Unsupported APIs)了解一些不支持的情况。

&emsp;&emsp;有些[search](###Search APIs)请求以及像 [field capabilities API](####Field capabilities API)中可以使用field wildcard patterns，这个wildcard patterns可以匹配到具体的filed alias：

```text
GET trips/_field_caps?fields=route_*,transit_mode
```

##### Alias targets

&emsp;&emsp;对于alias的目标域有一些要求限制：

- 目标域必须是一个具体的域（concrete field），不能是一个对象或者另一个alias
- 在alias创建时目标域必须已经存在
- 如果定义了nested objects，field alias必须要跟目标域有相同的nested scope

&emsp;&emsp;另外，alias只能对应一个目标域。意味着不可能使用一个alias在single clause（一个query属于一个clause）中查询多个目标域。

&emsp;&emsp;可以通过更新mapping来更改alias指向（refer to）的目标域。已知的一个限制是如果任何已经存储的percolator query包含了alias，他们仍然指向原先的目标。更多信息见[percolator documentation](####Percolator field type)。

##### Unsupported APIs

&emsp;&emsp;不支持往alias中写入域值：尝试在索引或者更新alias域会产生一个错误。同样的，alias不能作为多个域的[copy_to](####copy_to)的目标。

&emsp;&emsp;因为alias的名字没有在输入文档中呈现，所以不能通过用于source filtering。例如下面的请求会返回一个空结果：

```text
GET /_search
{
  "query" : {
    "match_all": {}
  },
  "_source": "route_length_miles"
}
```

&emsp;&emsp;目前只有[search](###Search APIs)请求以及像 [field capabilities API](####Field capabilities API)中可以接受alias。像[term vectors](####Term vectors API)就不能使用alias。

&emsp;&emsp;Finally, some queries, such as terms, geo_shape, and more_like_this, allow for fetching query information from an indexed document. Because field aliases aren’t supported when fetching documents, the part of the query that specifies the lookup path cannot refer to a field by its alias。

#### Boolean field type
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/boolean.html)

#### Date field type
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/date.html)

&emsp;&emsp;JSON没有日期（dates）类型，所以在Elasticsearch中日期字段可以是：

- 格式化后的字符串，比如`2015-01-01` 或者 `2015/01/01 12:10:30`
- 数值类型：milliseconds-since-the-epoch
- 数值类型：seconds-since-the-epoch（[配置](#####Epoch seconds)）

>NOTE：milliseconds-since-the-epoch的值必须是非负的，使用格式化后的值来表示1970前的日期

&emsp;&emsp;源码中，日期会被转化为UTC（如果指定了时区），存储为一个long类型的数值来表示milliseconds-since-the-epoch。

&emsp;&emsp;日期类型的查询会被表示为一个long类型的范围查询，聚合的结果和stored field会根据该域对应的date format被转回到一个字符串。

>NOTE：日期总是会被表示为字符串，即使在JSON中是long类型

&emsp;&emsp;时间格式data format可以自定义，如果`format`没有指定，会使用下面默认值：

```text
"strict_date_optional_time||epoch_millis"
```

&emsp;&emsp;上述默认值意味着接受的可选时间戳有milliseconds-since-the-epoch或者[strict_date_optional_time](####format)支持的格式。

&emsp;&emsp;例如

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "date": {
        "type": "date" 
      }
    }
  }
}

PUT my-index-000001/_doc/1
{ "date": "2015-01-01" } 

PUT my-index-000001/_doc/2
{ "date": "2015-01-01T12:10:30Z" } 

PUT my-index-000001/_doc/3
{ "date": 1420070400001 } 

GET my-index-000001/_search
{
  "sort": { "date": "asc"} 
}
```

&emsp;&emsp;第6行将会使用默认的`format`

&emsp;&emsp;第13行使用纯日期

&emsp;&emsp;第16行包含时间

&emsp;&emsp;第19行使用milliseconds-since-the-epoch

&emsp;&emsp;第23行，返回的`sort`的值将是milliseconds-since-the-epoch

>WARNING：日期将会接受类似`{"date": 1618249875.123456}`的带小数点的数值，但目前我们会丢失一定的精度，见这个[issue](https://github.com/elastic/elasticsearch/issues/70085) 。

##### Multiple date formats

&emsp;&emsp;可以使用`||`作为分隔符来指定multiple formats，这些`format`都被依次进行匹配直到找到对应的format。下面第一个format会将`milliseconds-since-the-epoch`的值转化为一个字符串。

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "date": {
        "type":   "date",
        "format": "yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
      }
    }
  }
}
```

##### Parameters for date fields

&emsp;&emsp;下面列出的参数可以用于`date`域

|       [doc_values](####doc_values)       | 是否以列式存储在磁盘上，使得可以用于排序、聚合、脚本处理，该值可以是true（默认值）或者false |
| :--------------------------------------: | ------------------------------------------------------------ |
|          [format](####format )           | 用于对日期进行解析，默认值`strict_date_optional_time||epoch_millis` |
|                  locale                  | 解析日期时使用的区域设置，因为在所有语言中，月份没有相同的名称和/或缩写。默认是 [ROOT locale](https://docs.oracle.com/javase/8/docs/api/java/util/Locale.html#ROOT) |
| [ignore_malformed](####ignore_malformed) | 如果是true，格式错误的数字会被忽略。如果是false，格式错误的数字会抛出异常并且整个文档会被丢弃。注意的是如果使用了参数`script`，当前参数不会被设置。 |
|            [index](####index)            | 是否需要被快速的搜索到？该值可以是true（默认值）或者false。日期域date field只有开启[doc_values](####doc_values)才能进行查询，尽管查询速度较慢 |
|       [null_value](####null_value)       | 可以设置一个满足[format](####format )的值，用来替代空值。默认值是null，意味这是一个缺失值。注意的是如果使用了参数`script`，当前参数不会被设置。 |
|             on_script_error              | 该值描述了通过参数`script`定义的脚本在索引期间发生错误后的行为。可以设置为false（默认值），那么整个文档会被reject。或者设置为`continue`，会通过[ignored field](#####\_ignored field)来进行索引并继续进行索引。这个参数只有参数`script`被设置后才能被设置 |
|                  script                  | 如果设置了该值，将会索引这个脚本生成的值，而不是直接读取source（输入文档中这个域的域值）。如果输入的文档中已经设置了这个域的值，那么这个脚本会被reject并报错。脚本的格式跟[runtime equivalent](####Map a runtime field)一致，并且应该输出long类型的时间戳。 |
|            [store](####store)            | 是否要额外存储域值并且可以被检索，不仅仅存储在[_source](####\_source field) 域中，该值可以是true或者false（默认值） |
|             [meta](####meta)             | 这个域的Metadata                                             |



##### Epoch seconds

&emsp;&emsp;如果你需要传输`seconds-since-the-epoch`，那么保证在`format`中列出`epoch_second`：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "date": {
        "type":   "date",
        "format": "strict_date_optional_time||epoch_second"
      }
    }
  }
}

PUT my-index-000001/_doc/example?refresh
{ "date": 1618321898 }

POST my-index-000001/_search
{
  "fields": [ {"field": "date"}],
  "_source": false
}
```

&emsp;&emsp;会收到下面的日期数据：

```text
{
  "hits": {
    "hits": [
      {
        "_id": "example",
        "_index": "my-index-000001",
        "_score": 1.0,
        "fields": {
          "date": ["2021-04-13T13:51:38.000Z"]
        }
      }
    ]
  }
}
```

#### Date nanoseconds field type
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/date_nanos.html)

#### Geopoint field type
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/geo-point.html)

#### Geoshape field type
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/geo-shape.html)

#### Histogram field type
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/histogram.html)



#### IP field type
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ip.html)

&emsp;&emsp;`ip`域可以用来索引/存储[IPV4](https://en.wikipedia.org/wiki/IPv4)或者[IPV6](https://en.wikipedia.org/wiki/IPv6)地址：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "ip_addr": {
        "type": "ip"
      }
    }
  }
}

PUT my-index-000001/_doc/1
{
  "ip_addr": "192.168.1.1"
}

GET my-index-000001/_search
{
  "query": {
    "term": {
      "ip_addr": "192.168.0.0/16"
    }
  }
}
```

>NOTE：你可以使用[ip_range data type](####Range field types)在单个域中存储ip ranges。

##### Parameters for ip fields

&emsp;&emsp;`ip`域接受下列的参数：

- [doc_values](####doc_values)
  - 该域值是否在磁盘上使用列式存储，使得可以用来进行聚合、排序或者脚本。可选值`true`或者`false`

- [ignore_malformed](####ignore_malformed)
  - 如果设置为`true`，数据格式错误的IP地址被忽略。如果设置为`false`（默认值），数据格式错误的IP地址会导致抛出异常并reject整个文档。注意的是如果使用了下面的参数`script`，那么不能设置当前参数

- [index](####index)
  - 是否该域需要被快速的搜索到？可选值`true`或者`false`。只有[doc_values](####doc_values)参数的域仍然可以使用term or range-based的查询，尽管慢一些（albeit slower）

- [null_value](####null_value)
  - `ip`域接受一个IPV4或者IPV6的值，同时也接受显示（explicit）的`null`值，意味着当前域被认为是一个缺失值。注意的是如果使用了下面的参数`script`，那么不能设置当前参数

- on_script_error
  - 定义了当使用参数`script`并且在索引期间脚本抛出异常后的行为。默认值为`reject`会导致整个文档会被reject，设置为`ignore`后，会被注册到文档的 [\_ignored](####\_ignored field)这个[metadata field](###Metadata fields)中并且继续索引。注意的是如果使用了下面的参数`script`，那么不能设置当前参数

- script
  - 如果设置了这个参数，域值会使用脚本生成的值而不是文档中的原始数据。如果使用输入文档（input document）中的数据作为域值，那么这篇文档会被reject并且返回错误。脚本跟[runtime equivalent](####Map a runtime field)采用一样的格式，并且应该返回IPV4或者IPV6格式的值

- [store](####store)
  - 当前域是否要存储使得可以从`_source`检索该域。可选值`true`或者`false`

- time_series_dimension
  - 预览功能， 可能会被更改或者移除，目前只在Elasticsearch代码内部使用，暂不介绍。


##### Querying ip fields

&emsp;&emsp;最常用查询IP的方式就是使用 [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#CIDR_notation)：

```text
GET my-index-000001/_search
{
  "query": {
    "term": {
      "ip_addr": "192.168.0.0/16"
    }
  }
}
```

&emsp;&emsp;或者

```text
GET my-index-000001/_search
{
  "query": {
    "term": {
      "ip_addr": "2001:db8::/48"
    }
  }
}
```

&emsp;&emsp;注意的是，冒号是query_string查询的特殊字符，因此ipv6地址需要转义。最简单的方法是在搜索值两边加双引号:

```text
GET my-index-000001/_search
{
  "query": {
    "query_string" : {
      "query": "ip_addr:\"2001:db8::/48\""
    }
  }
}
```

#### Keyword type family
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/keyword.html)

##### Wildcard field type

#### Nested field type
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/nested.html)

#### Numeric field types
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/number.html)

#### Join field type
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/parent-join.html)

#### Object field type
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/object.html)

&emsp;&emsp;事实上JSON documents是有层次的。文档中可能包含inner objects，并且它们自身还包含inner objects：

```text
PUT my-index-000001/_doc/1
{ 
  "region": "US",
  "manager": { 
    "age":     30,
    "name": { 
      "first": "John",
      "last":  "Smith"
    }
  }
}
```

&emsp;&emsp;第2行，文档外部同样是JSON object

&emsp;&emsp;第4行，包含一个名为`manager`的inner object

&emsp;&emsp;第6行，`manager`接着还包含一个名为`name`的inner object

&emsp;&emsp;内部的实现是一个简单的，扁平的key-value链，如下所示：

```text
{
  "region":             "US",
  "manager.age":        30,
  "manager.name.first": "John",
  "manager.name.last":  "Smith"
}
```

&emsp;&emsp;上述文档对应的显示的mapping如下所示：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": { 
      "region": {
        "type": "keyword"
      },
      "manager": { 
        "properties": {
          "age":  { "type": "integer" },
          "name": { 
            "properties": {
              "first": { "type": "text" },
              "last":  { "type": "text" }
            }
          }
        }
      }
    }
  }
}
```

&emsp;&emsp;第4行，mapping中最顶层的properties的定义

&emsp;&emsp;第8行，`manager`域是一个inner object

&emsp;&emsp;第11行，`manager.name`域是`manager`域中的inner object

&emsp;&emsp;你不需要显示的指定`object`指定`type`

##### Parameters for object fields

&emsp;&emsp;下面的参数可以作用于`object`域

- [dynamic](####dynamic(mapping parameter))：是否将新的域写入到已存在的object域中，可选值为`true` (default)， `runtime`， `false` and `strict`
- [enabled](####enabled)：object域的域值是否要进行解析并且写入到索引中（`true`），或者完全忽略(`false`)
- [properties](####properties)：object中的所有域（这些域被称为properties），这些域可以是任何数据类型[data type](###Field data types)，新的域可能被添加现在有的object域中（比如dynamic参数置为true的情况下）

> IMPORTANT：如果不是要索引一个object，而是object数组，请先阅读[Nested](####Nested field type)。

#### Percolator field type
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/percolator.html)

#### Range field types
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/range.html)

#### Rank features field type
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rank-features.html)

#### Rank feature field type
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rank-feature.html)

#### Text type family
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/text.html)

&emsp;&emsp;text family包含的域类型（field type）如下所示：

- [text](#####Text field type)，用于全文内容 的传统的域类型，比如email的body或者产品的描述
- [match_only_text](#####Match-only text field type)，经过space-optimized优化的`text`域的变种，它关闭了打分并且对于需要position的查询执行较慢。这个域最适合索引log messages。

##### Text field type

&emsp;&emsp;索引full-text values的域，例如email的body或者一个产品的描述。域值会被分词，域值会被[analyzer](##Text analysis)分词，将一个string值转化为一个一个独立的term，并将这些term进行索引。analysis process允许Elasticsearch在每一个full text filed中查询独立的词（individual Word）。Text域不用于排序或者很少用于聚合（尽管[significant text aggregation](####Significant text aggregation)是一个明显的例外）。

&emsp;&emsp;`text`域最适合用于无结构的并且是human-readable的内容。如果你需要索引无结构的machine-generated的内容，见[Mapping unstructured content](#####Wildcard field type)。

&emsp;&emsp;如果你需要索引例如email地址，hostnames，状态码，或者tags，你应该选择[keyword](####Keyword type family)域。

&emsp;&emsp;下面是text域的mapping例子：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "full_name": {
        "type":  "text"
      }
    }
  }
}
```

##### Use a field as both text and keyword

&emsp;&emsp;有时候同一个域需要同时有full text(`text`)和keyword(`keyword`)。一个用来全文检索，另一个用于聚合和排序。可以通过 [multi-fields](####fields)实现。

##### Parameters for text fields

&emsp;&emsp;`text`域接受下面的参数

| [analyzer](####analyzer) | 分词器[analyzer](##Text analysis)y应该同时用于index-time和search-time（除非被[search_analyzer](####search_analyzer)重写（override）），默认是index analyzer或者[standard analyzer](####Standard analyzer). |
| :----------------------------------------------------------: | :--: |
| [eager_global_ordinals](####eager_global_ordinals) | 是否在refresh尽快的载入global ordinals？默认是`false`。如果经常用于terms aggregation，开启这个参数是很有必要的 |
| [fielddata](####fielddata mapping parameter) | 这个域是否用于排序、聚合、或在script中使用？默认是`false` |
| [fielddata_frequency_filter](#####fielddata_frequency_filter mapping parameter) | （Expert Setting）在开启`fielddata`后，将哪些值载入到内存中，默认值是`false` |
| [fields](####fields) | Multi-fields允许同一个将string value基于不同的目的索引用多种方式进行索引。比如用于查询或者用于排序聚合，或者使用不同的分词器对string value进行分词 |
| [index](####index) | 这个域是否可以被搜索？默认是`true` |
| [index_options](####index_options) | 哪些信息需要被存储到索引中用于查询和高亮，默认是`positions` |
| [index_prefixes](####index_prefixes) | 开启这个参数后，term的2到5个前缀字符会被索引到额外的域（separate filed）。这使得前缀搜索更高效，代价就是索引大小会增加 |
| [index_phrases](####index_phrases) | 开启这个参数后，2个term组成的域值会被索引到额外的域（separate filed）中。这使得exact phrase（no slop） 查询可以更高效，代价就是索引大小会增加。注意的是只有移除了停用词后才能达到最佳性能，因为包含了停用词的短语将不会使用subsidiary field并且会回滚到标准的短语查询。默认是`false` |
| [norms](####norms) | 执行打分的查询时是否要考虑域值的长度 |
| [position_increment_gap](####position_increment_gap) | The number of fake term position which should be inserted between each element of an array of strings. Defaults to the `position_increment_gap` configured on the analyzer which defaults to `100`. `100` was chosen because it prevents phrase queries with reasonably large slops (less than 100) from matching terms across field values. |
| [store](####store) | 是否存储域值并且可以通过`_source`域查询。默认是`false` |
| [search_analyzer](####search_analyzer) | 是否在search-time时使用上文中第一个参数中指定的分词器（默认值）？ |
| [search_quote_analyzer](#####search_quote_analyzer) | 当遇到短语时，是否在search-time使用上文中第一个参数中指定的分词。默认值是上文中`search_analyzer`中指定的分词器 |
| [similarity](####similarity) | 使用哪种打分算法？默认值是`BM25` |
| [term_vector](####term_vector) | 是否为这个域存储term vectors？默认值是`false` |
| [meta](####meta) | 这个域的Metadata |


##### fielddata mapping parameter

&emsp;&emsp;`text`域默认是可以用于搜索的，但是默认不能用于聚合、排序、或者scripting。如果你想要排序，聚合，或者从script中访问`text`域，你将会遇到这个异常：`Fielddata is disabled on text fields by default`。 设置`your_field_name`为`fielddata=true`，通过uninverting倒排索引（inverted index）将fielddata导入到内存中。注意的是这将会使用大量的内存。

&emsp;&emsp;Field data是在排序、聚合、或者scripting中访问fulll text field的分词后的token的唯一方法。例如，比如说`New York`这个full text field将会分词为`new`和`york`两个token，对这些token进行聚合需要field data。

##### Before enabling fielddata

&emsp;&emsp;在text field上开启fielddata通常是没有意义的。Field data使用[field data cache](####Field data cache settings)并存储在堆上，因为计算开销很大。计算field data会导致latency spikes并且增加堆内存的使用，这也是导致集群性能问题的原因之一。

&emsp;&emsp;大多数的用户会使用[multi-field mappings](####fields)来同时获得`text`域用于全文检索以及不分词的`keyword`域用于聚合，如下所示：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "my_field": { 
        "type": "text",
        "fields": {
          "keyword": { 
            "type": "keyword"
          }
        }
      }
    }
  }
}
```

&emsp;&emsp;第5行，`my_field`域用于查询

&emsp;&emsp;第8行，`my_field.keyword`用于聚合、排序或者在script中使用

##### Enabling fielddata on text fields

&emsp;&emsp;你可以使用[update mapping API](####Update mapping API)对现有的`text`域开启fielddata，如下所示：

```text
PUT my-index-000001/_mapping
{
  "properties": {
    "my_field": { 
      "type":     "text",
      "fielddata": true
    }
  }
}
```

&emsp;&emsp;第4行，`my_field`指定的mapping应该由这个域现有的mapping加上`fielddata`参数组成。

##### fielddata_frequency_filter mapping parameter

&emsp;&emsp;FieldData filtering可以用于减少载入到内存的term的数量，因此能降低内存使用量。可以通过frequency来过滤term：

&emsp;&emsp;

##### Match-only text field type

### Metadata fields
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping-fields.html)

&emsp;&emsp;每一篇文档都有关联的metadata，例如`_index`和`_id` metadata field。这些metadata fields的行为可以在创建mapping时自定义。

##### Identity metadata fields

&emsp;&emsp;[\_index](####_index field)：文档所属索引。

&emsp;&emsp;[\_id](####_id field)：文档的ID。

##### Document source metadata fields

&emsp;&emsp;[\_source](####_source field)：代表文档内容的原始JSON。

&emsp;&emsp;[\_size](https://www.elastic.co/guide/en/elasticsearch/plugins/8.2/mapper-size.html)：`_srouce`域的大小（单位：字节），由[mapper-size](https://www.elastic.co/guide/en/elasticsearch/plugins/8.2/mapper-size.html)插件提供。

##### Doc count metadata field

&emsp;&emsp;[\_doc_count](####_doc_count field)：当文档作为pre-aggregation 数据时的一个自定义的用于存储文档计数的域。

##### Indexing metadata fields

&emsp;&emsp;[\_field_names](####_field_names field)：文档中所有域值不为null的域。

&emsp;&emsp;[\_ignored](####_ignored field)：因为[ignore_malformed](####ignore_malformed)，在索引期间文档中所有被ignore的域、

##### Routing metadata field

&emsp;&emsp;[\_routing](####_routing field)：自定义的routing value用于将一篇文档路由到指定的分片上。

##### Other metadata field

&emsp;&emsp;[\_meta](####_meta field)：应用指定的metadata。

&emsp;&emsp;[\_tier](####_tier field)：文档所属的索引所在的[data tier](###Data tiers)。

#### \_doc_count field
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping-doc-count-field.html)

&emsp;&emsp;Bucket aggregation总是返回一个名为`doc_count`的域，它表示每一个bucket中参与集合的文档数量。计算`doc_count`的值是很简单的。在每一个bucket中，每收集到一篇文档，`doc_count`的值就加一。

&emsp;&emsp;在独立的文档中进行聚合计算时这种简单的方式是很高效的。但是统计那些存储pre-aggregation的文档数量就会丢失精度（比如说[histogram](####Histogram aggregation)或者`aggregate_metric_double`），因为一个summary field中代表了多篇文档（见[Histogram fields](#####Histogram fields)）。

&emsp;&emsp;为了允许纠正统计pre-aggregation数据时的文档数量。可以使用一种名为`_doc_count`的metadata field类型。`_doc_count`必须总是用一个正整数来表示单个summary field对应的文档数量。

&emsp;&emsp;当文档中添加了`_doc_count`，所有的[bucket aggregation](###Bucket aggregations)必须遵照这个`_doc_count`的值，按照这个值来累加bucket中`doc_count`的值。如果一篇文档中不包含任何`_doc_count`，默认存在一个`_doc_count = 1`的信息。

>IMPORTANT：
>- `_doc_count`域的域值在一篇文档中只能是一个正整数，Nested arrays是不允许的如果一篇文档中不包含`_doc_count`，
>- 按照`_doc_count = 1`这种默认行为进行聚合统计
##### Example

&emsp;&emsp;下面的[create index API](####Create index API)使用下面的mapping创建了一个新的索引：

- 域名`my_histogram`是一个[histogram](####Histogram field type)类型的域存储了百分比数据（percentile data）
- 域名`my_text`是一个[keyword](####Keyword type family)类型的域为这个Histogram存储了标题信息

```text
PUT my_index
{
  "mappings" : {
    "properties" : {
      "my_histogram" : {
        "type" : "histogram"
      },
      "my_text" : {
        "type" : "keyword"
      }
    }
  }
}
```

&emsp;&emsp;下面的[index API](####Index API)请求存储2个histogram的pre-aggregated data：`histogram_1`and `histogram_2`

```text
PUT my_index/_doc/1
{
  "my_text" : "histogram_1",
  "my_histogram" : {
      "values" : [0.1, 0.2, 0.3, 0.4, 0.5],
      "counts" : [3, 7, 23, 12, 6]
   },
  "_doc_count": 45 
}

PUT my_index/_doc/2
{
  "my_text" : "histogram_2",
  "my_histogram" : {
      "values" : [0.1, 0.25, 0.35, 0.4, 0.45, 0.5],
      "counts" : [8, 17, 8, 7, 6, 2]
   },
  "_doc_count": 62 
}
```

&emsp;&emsp;第18行，`_doc_count`必须是一个正整数表示每一个histogram中的文档数量。

&emsp;&emsp;如果我们在`my_index`上运行下面的[terms aggregation](####Terms aggregation)：

```text
GET /_search
{
    "aggs" : {
        "histogram_titles" : {
            "terms" : { "field" : "my_text" }
        }
    }
}
```

&emsp;&emsp;我们会得到下面的response：

```text
{
    ...
    "aggregations" : {
        "histogram_titles" : {
            "doc_count_error_upper_bound": 0,
            "sum_other_doc_count": 0,
            "buckets" : [
                {
                    "key" : "histogram_2",
                    "doc_count" : 62
                },
                {
                    "key" : "histogram_1",
                    "doc_count" : 45
                }
            ]
        }
    }
}
```

#### \_field_names field
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping-field-names-field.html)

&emsp;&emsp;` _field_names`域用来对一篇文档中每一个域值不是null的域的域名进行索引。[exists query](####Term query)使用这个字段来查找具有或不具有特定字段的任何非空值的文档。

&emsp;&emsp;现在` _field_names`只会对有`doc_values`以及关闭`norms`的域的域名进行索引。对于有些只开启`doc_values`或者只开启`norms`的域也可以使用[exists query](####Term query)，但是不是通过` _field_names`域实现的。

##### Disabling \_field_names

&emsp;&emsp;不允许关闭`_field_names`。现在默认开启`_field_names`是因为它不再有曾经的索引开销。

> NOTE：关闭`_field_names`的功能已经被移除了。在新的索引上使用它会抛出一个错误。在8.0前的索引上关闭`_field_names`仍然是允许的，但是会报告出一个deprecation的警告。

#### \_ignored field
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping-ignored-field.html)

&emsp;&emsp;`_ignored` field 用来索引并存储在索引期间被忽略的文档的所有域的名字。例如域值是格式错误的并且开启了[ignore_malformed](####ignore_malformed)或者keyword域值超过了[ignore_above](####ignore_above)中的设置。

&emsp;&emsp;这个域可以使用 [term](####Term query), [terms](####Terms query), [exists](####Exists query)进行查询。

&emsp;&emsp;下面的例子描述了匹配的文档中包含一个或者多个被忽略的域。

```text
GET _search
{
  "query": {
    "exists": {
      "field": "_ignored"
    }
  }
}
```

&emsp;&emsp;下面的例子描述了将匹配在索引期间，`@timestamp`字段被忽略的文档。

```text
GET _search
{
  "query": {
    "term": {
      "_ignored": "@timestamp"
    }
  }
}
```

#### \_id field
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping-id-field.html)

&emsp;&emsp;每个文档都有一个`_id`用于唯一识别（unique identification）。该值会被索引使得可以通过[GET API](####Get API)或者[ids query](####IDs)用于查找。这个`_id`要么在索引期间被赋值（assigned）、要么通过Elasticsearch生成一个唯一的值。这个域在mappings中是不可以配置的。

&emsp;&emsp;`_id`域的值可以在`term`、`terms`、`match`、`query_string`查询中可用（accessible）。

```text
# Example documents
PUT my-index-000001/_doc/1
{
  "text": "Document with ID 1"
}

PUT my-index-000001/_doc/2?refresh=true
{
  "text": "Document with ID 2"
}

GET my-index-000001/_search
{
  "query": {
    "terms": {
      "_id": [ "1", "2" ] 
    }
  }
}

```

&emsp;&emsp;第16行即根据`_id`进行查询，见[ids query](####IDs)。

&emsp;&emsp;`_id`域在聚合（aggregation）、排序、脚本中是被限制使用的。万一（in case）需要使用`_id`域来进行排序或者聚合，建议将`_id`域的值复制（duplicate）到其他字段并且这个字段开启`doc_valies`。

>\_id字段的值的大小被限制为512个字节并且larger values会被reject。

#### \_index field
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping-routing-field.html)

#### \_meta field
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping-meta-field.html)

&emsp;&emsp;一种可以使用自定义元数据（meta data）跟mapping进行关联的类型。Elasticsearch完全不会去使用它，可以用来存储指定应用（application-specific）的元数据，比如说这篇文档所属类别：

```text
PUT my-index-000001
{
  "mappings": {
    "_meta": { 
      "class": "MyApp::User",
      "version": {
        "min": "1.0",
        "max": "1.3"
      }
    }
  }
}
```

&emsp;&emsp;第4行，可以通过 [GET mapping API](####Get mapping API)检索`_meta`的信息

&emsp;&emsp;`_meta`域可以通过[update mapping API](####Update mapping API)对已存在的类型进行变更。

```text
PUT my-index-000001/_mapping
{
  "_meta": {
    "class": "MyApp2::User3",
    "version": {
      "min": "1.3",
      "max": "1.5"
    }
  }
}
```

#### \_routing field
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping-routing-field.html)

&emsp;&emsp;使用下面的公式让索引中的一篇文档被路由到指定的分片中：

```text
routing_factor = num_routing_shards / num_primary_shards
shard_num = (hash(_routing) % num_routing_shards) / routing_factor
```

&emsp;&emsp;num_routing_shards即索引设置中的[index.number_of_routing_shards](####index.number_of_routing_shards)。

&emsp;&emsp;num_primary_shards即索引设置中的[index.number_of_shards](####index.number_of_shards)。

&emsp;&emsp;\_routing的默认值是文档的[\_id](####_id field)。自定义的路由模板（routing pattern）可以通过对某个文档指定一个自定义的`routing`值实现。例如：

```text
PUT my-index-000001/_doc/1?routing=user1&refresh=true 
{
  "title": "This is a document"
}

GET my-index-000001/_doc/1?routing=user1 
```

&emsp;&emsp;第1行中，这篇文档使用`user1`作为路由值替代ID。

&emsp;&emsp;第6行中，当[getting](####Get API)、[deleting](####Delete API)、[updating](####Update API)这篇文档时需要提供路由值。

&emsp;&emsp;`_routing`的值可以在查询中使用（accessible）:

```text
GET my-index-000001/_search
{
  "query": {
    "terms": {
      "_routing": [ "user1" ] 
    }
  }
}
```

&emsp;&emsp;第5行中，在`_routing`域上进行查询，见[ids query](####IDs)。

>Data streams不支持自定义routing除非创建时，在模板中开启allow_custom_routing。

#####  Searching with custom routing

&emsp;&emsp;自定义的路由能减少（reduce）查询的压力。不需要将搜索请求分散（fan out）到索引的所有分片，而是只发送到匹配特定路由值的分片。

```text
GET my-index-000001/_search?routing=user1,user2 
{
  "query": {
    "match": {
      "title": "document"
    }
  }
}
```

&emsp;&emsp;第1行中，这次查询只会在路由值user1、user2相关的分片中执行。

#####  Making a routing value required

&emsp;&emsp;当使用自定义的路由时，无论是[Indexing](####Index)、[getting](####Get API)、[deleting](####Delete API)、[updating](####Update API)一篇文档时都是需要提供路由值的。

&emsp;&emsp;忘掉路由值能将一篇文档索引到一个或多个分片上。作为一个安全措施（as a safeguard），`_routing`域可以被配置使得在所有的CRUD操作时都需要`routing`值。

```text
PUT my-index-000002
{
  "mappings": {
    "_routing": {
      "required": true 
    }
  }
}

PUT my-index-000002/_doc/1 
{
  "text": "No routing value provided"
}
```

&emsp;&emsp;第5行中，所有文档的操作都需要提供路由值。

&emsp;&emsp;第10行中，这个请求会抛出`routing_missing_exception`的异常。

#####  Routing to an index partition

&emsp;&emsp;使用自定义的路由值来配置索引时，可以路由到一个分片集合中而不是单个分片上。这可以降低集群不平衡导致关闭的风险，同时能降低查询压力。

&emsp;&emsp;在索引创建期间可以通过索引层的设置[index.routing_partition_size](####index.routing_partition_size)来实现。随着分区（partition）的大小的增长，数据将分布的越均匀（evenly），那么每次查询都需要搜索更多的分片。

&emsp;&emsp;当提供了这个设置后，计算分片的公式将变成：

```text
routing_value = hash(_routing) + hash(_id) % routing_partition_size
shard_num = (routing_value % num_routing_shards) / routing_factor
```

&emsp;&emsp;也就是说（that is）`routing`域将用来计算出分片集合，然后`_id`用来从分片集合中选出一个分片。

&emsp;&emsp;要开启这个功能，`index.routing_partition_size`的值要大于1并且小于`index.number_of_shards`。

&emsp;&emsp;一旦开启这个功能，被分区（partitioned）的索引将有下列的限制：

- 不能创建Mapping中的[join filed](####Join field type)的关系
- 这个索引的所有mappings信息都需要指定`_routing`的require

#### \_source field
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping-source-field.html)

&emsp;&emsp;`_source`域包含了在索引期间传给Elasticsearch的原始的JSON格式的文档内容。`_source`自身不会被索引（因此这个域没法用于搜索），又因为它被存储了，所以可以通过`fetch requests`例如[get](####Get API)或者[search](####Search API)方式来获得。

##### Disabling the \_source field

&emsp;&emsp;`_source`域会带来索引的存储成本，基于这个原因可以通过下面的方式关闭：

```text
PUT my-index-000001
{
  "mappings": {
    "_source": {
      "enabled": false
    }
  }
}
```

>  WARNING：Think before disabling the \_source field
>  &emsp;&emsp;用户经常不考虑关闭`_source`域的后果然后会后悔这么做。如果`_source`域不可见那么下面的功能就没法支持：
>
>  - [update](####Update API)、[update_by_query](####Update By Query API)以及[reindex](####Reindex API)这些API
>  - [On the fly highlighting](###Highlighting)
>  - 从一个索引重新索引到另一个的能力，或者是修改mapping、分词器或者升级一个索引到一个新的主要版本
>  - 索引期间通过观察原始文档调试查询或者聚合的能力
>  - 在未来的版本中可能可以自动修复受损（corruption）索引的能力

> TIP：如果需要关心磁盘空间，可以提高 [compression level](####index.codec)而不是关闭`_source`。

##### Including / Excluding fields from \_source

&emsp;&emsp;这是expert-only的功能使得在`_source`的内容在索引之后、存储之前进行域的剪枝。

> WARNING：从`_source`中移除一些域跟关闭`_source`有着类似的负面问题（downside），特别是你不能从一个Elasticsearch索引重新索引到另一个。可以考虑使用[source filtering](###Retrieve selected fields from a search) 来替代这种方式。

&emsp;&emsp;参数`includes/excludes`（支持wildcards）可以按照以下方式使用：

```text
PUT logs
{
  "mappings": {
    "_source": {
      "includes": [
        "*.count",
        "meta.*"
      ],
      "excludes": [
        "meta.description",
        "meta.other.*"
      ]
    }
  }
}

PUT logs/_doc/1
{
  "requests": {
    "count": 10,
    "foo": "bar" 
  },
  "meta": {
    "name": "Some metric",
    "description": "Some metric description", 
    "other": {
      "foo": "one", 
      "baz": "two" 
    }
  }
}

GET logs/_search
{
  "query": {
    "match": {
      "meta.other.foo": "one" 
    }
  }
}
```

&emsp;&emsp;第21、25、27、28行，`_source` 域存储之前会移除这几个域

&emsp;&emsp;第37行，我们仍然可以通过这个域进行检索，尽管它没有被存储

#### \_tier field
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping-tier-field.html)

### Mapping parameters
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping-params.html)

&emsp;&emsp;这一章节提供了不同的mapping 参数（mapping parameters）的详细的介绍，mapping参数用于[filed mappings](###Field data types)：

&emsp;&emsp;下面的mapping参数对于某些或者所有的域的数据类型是通用的：

- [`analyzer`](####analyzer(mapping parameter))
- [`coerce`](####coerce)
- [`copy_to`](####copy_to)
- [`doc_values`](####doc_values)
- [`dynamic`](####dynamic(mapping parameter))
- [`eager_global_ordinals`](####eager_global_ordinals)
- [`enabled`](####enabled(mapping parameter))
- [`fielddata`](###fielddata mapping parameter(1))
- [`fields`](####fields)
- [`format`](####format(mapping parameter))
- [`ignore_above`](####ignore_above)
- [`ignore_malformed`](####ignore_malformed)
- [`index_options`](####index_options)
- [`index_phrases`](####index_phrases)
- [`index_prefixes`](####index_prefixes)
- [`index`](####index(mapping parameter))
- [`meta`](####meta(mapping parameter))
- [`normalizer`](####normalizer(mapping parameter))
- [`norms`](####norms(mapping parameter))
- [`null_value`](####null_value)
- [`position_increment_gap`](####position_increment_gap)
- [`properties`](####properties)
- [`search_analyzer`](####search_analyzer)
- [`similarity`](####similarity)
- [`store`](####store(mapping parameter))
- [`term_vector`](####term_vector)

#### analyzer(mapping parameter)
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analyzer.html#analyzer)

&emsp;&emsp;只有[text](####Text type family)类型支持`analyzer`参数。

&emsp;&emsp;`analyzer`参数用来指定分词器[analyzer](####Anatomy of an analyzer) 并且用于在索引和查询[text](####Text type family)域时进行文本分词[text analysis](##Text analysis)。

&emsp;&emsp;除非用mapping参数[search_analyzer](####search_analyzer)进行覆盖，分词器将同时用于[index and search analysis](####Index and search analysis)，见[Specify an analyzer](####Specify an analyzer)

&emsp;&emsp;**TIPS: **建议在生产中使用前先试用下分词器，见[Test an analyzer](####Test an analyzer)。

&emsp;&emsp;**TIPS: **`analyzer`参数不能通过[update mapping API](####Update mapping API)对已存在的域进行变更。

##### search_quote_analyzer

&emsp;&emsp;（未完成）



#### coerce
（8,2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/coerce.html#coerce)

&emsp;&emsp;Data is not always clean，在JSON中，5这个数字可以用字符串"5"表示，也可以用数值表示，或者应该是整数5，但是在JSON中是一个浮点数，比如5.0，甚至是字符串"5.0"。

&emsp;&emsp;`coerce`会尝试将dirty value转化为域的数据类型。比如：

- 字符串会被强制转化为数值
- 浮点型会被截断为整数

&emsp;&emsp;例如：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "number_one": {
        "type": "integer"
      },
      "number_two": {
        "type": "integer",
        "coerce": false
      }
    }
  }
}

PUT my-index-000001/_doc/1
{
  "number_one": "10" 
}

PUT my-index-000001/_doc/2
{
  "number_two": "10" 
}
```

&emsp;&emsp;第18行，number_one这个域将包含数值类型的10

&emsp;&emsp;第23行，由于`coerce`被禁用了，所以这篇文档会被reject

> TIP：`coerce`参数可以通过[update mapping API](####Update mapping API)更新。

##### Index-level default

&emsp;&emsp;`index.mapping.coerce`这个设置能够在索引层全局的关闭所有域的`coerce`的设置

``` text
PUT my-index-000001
{
  "settings": {
    "index.mapping.coerce": false
  },
  "mappings": {
    "properties": {
      "number_one": {
        "type": "integer",
        "coerce": true
      },
      "number_two": {
        "type": "integer"
      }
    }
  }
}

PUT my-index-000001/_doc/1
{ "number_one": "10" } 

PUT my-index-000001/_doc/2
{ "number_two": "10" } 
```

&emsp;&emsp;第20行，`number_one`这个域中的设置覆盖了索引层的设置，即开启了`coerce`

&emsp;&emsp;第23行，这个文档会被reject，因为`number_two`继承了索引层的设置，即关闭了`coerce`

#### copy_to
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/copy-to.html)

&emsp;&emsp;`copy_to`参数允许你将多个域的域值写入到group field中，这个group filed可以被当成一个域进行查询。

> TIP：如果你经常使用多个域进行查询，你可以使用copy_to写入到较少的域来提高查询速度。

&emsp;&emsp;见[Tune for search speed](###Tune for search speed)。

&emsp;&emsp;下图中，`first_name`和`last_name`的域值能被拷贝到`full_name`中：

``` text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "first_name": {
        "type": "text",
        "copy_to": "full_name" 
      },
      "last_name": {
        "type": "text",
        "copy_to": "full_name" 
      },
      "full_name": {
        "type": "text"
      }
    }
  }
}

PUT my-index-000001/_doc/1
{
  "first_name": "John",
  "last_name": "Smith"
}

GET my-index-000001/_search
{
  "query": {
    "match": {
      "full_name": { 
        "query": "John Smith",
        "operator": "and"
      }
    }
  }
}
```

&emsp;&emsp;第7、11行，`first_name`和`last_name`的域值被拷贝到`full_name`。

&emsp;&emsp;第30行，`first_name`和`last_name`这两个域依然能分别用来查询frist_name以及last_name，而`full_name`可以用来同时查询frist_name以及last_name。

&emsp;&emsp;一些重要点：

- 域值被拷贝，而不是terms被拷贝（域值在进行分词处理后生成的一个或多个最小分词单元成为term）
- `_source`字段中的值不会显示被拷贝的域值
- 同一个域值可以被拷贝到多个域中：`"copy_to": [ "field_1", "field_2" ]`
- 你不能递归的方式进行域值拷贝，比如先field_1拷贝到field_2，然后field_2拷贝到field_3，并且期望通过这种方式将field_1的域值拷贝到field_3中，这是不可行的。你应该使用将一个域值直接拷贝到多个域的方式


>NOTE：域值类型是objects的域不支持copy_to，例如date_range。

#### doc_values
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/doc-values.html)

&emsp;&emsp;大部分的域默认情况下都会被[索引](####index)，使得可以被用于搜索。倒排索引使得可以在有序的term集合中找到该term，并且能马上获取到包含 term的文档列表。

&emsp;&emsp;排序、聚合以及脚本中访问域值需要一种不同的数据访问模式（data access pattern）。我们需要能够根据文档（文档号）找到这个文档中包含的某个域的所有域值，而不是根据域值找到文档。

&emsp;&emsp;DocValues一种 on-disk的数据结构。这种基于文档的索引，使得上述的数据访问模式的成为可能。DocValues使用列式存储column-oriented 存储了跟\_source字段相同的值，使得排序跟聚合更高效。DocValues几乎支持所有的域类型，除了[text](####Text type family)跟annotated_text这两类域。

##### Doc-value-only fields

&emsp;&emsp;[Numeric types](####Numeric field types), [date types](####Date field type),  [boolean type](####Boolean field type), [ip type](####IP field type), [geo_point type](####Geopoint field type) 和[keyword type](####Keyword type family)这些域只开启了docValues（在Lucene中只用正排的数据结构存储）而没有用倒排索引存储，这些域仍旧能够实现搜索，但是查询性能较差。这种索引方式是一种disk usage和查询性能的折中（tradeoff），适合用于那些不用于查询或者查询性能不重要的域。这使得只包含docValues的字段非常适合通常不用于过滤的字段，例如metric data上的计量表（gauges）或计数器。

&emsp;&emsp;Doc-value-only域可以通过下面方式配置：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "status_code": { 
        "type":  "long"
      },
      "session_id": { 
        "type":  "long",
        "index": false
      }
    }
  }
}
```

&emsp;&emsp;第6行，`status_code`是一个普通的`long`类型的域。

&emsp;&emsp;第10行，`session_id`关闭了`index`，使得该域是`Doc-value-only`域，并且只开启DocValues。

##### Disabling doc values

&emsp;&emsp;所有支持DocValues的域都会默认开启DocValues，如果你确定这个域不需要进行聚合、排序，或者在脚本中访问这个域的域值，那么你可以关闭DocValues来节省磁盘空间：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "status_code": { 
        "type":       "keyword"
      },
      "session_id": { 
        "type":       "keyword",
        "doc_values": false
      }
    }
  }
}
```

&emsp;&emsp;`status_code`默认开启`doc_values`

&emsp;&emsp;`status_code`关闭了`doc_values`，但是仍然能被用于查询。

> NOTE：你不能关闭[wildcard ](####Keyword type family)域的`doc_values`。

#### dynamic(mapping parameter)
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/dynamic.html)

&emsp;&emsp;当你索引的一篇文档中包含一个新的域时，Elasticsearch会[动态的添加域](###Dynamic mapping)，添加到文档中，或者文档中某个对象域（object field）中，下面的例子在文档中添加了一个String类型的域`username`，以及对象域`name`，在这个对象域中添加了两个域：

```text
PUT my-index-000001/_doc/1
{
  "username": "johnsmith",
  "name": { 
    "first": "John",
    "last": "Smith"
  }
}

GET my-index-000001/_mapping 
```

&emsp;&emsp;第4行，`name`对象域下的两个域是`name.first`和`name.last`

&emsp;&emsp;第10行，查看这个索引的mapping

&emsp;&emsp;下面这篇文档增加两个string field： `email`和`name.middle`: 

```text
PUT my-index-000001/_doc/2
{
  "username": "marywhite",
  "email": "mary@white.com",
  "name": {
    "first": "Mary",
    "middle": "Alice",
    "last": "White"
  }
}

GET my-index-000001/_mapping
```

##### Setting dynamic on inner objects

&emsp;&emsp;[inner object](####Object field type)会继承parent object或者mapping type中的`dynamic`参数，在下面的例子，在mapping顶层中关闭了dynamic，所以顶层中新的域不会被动态添加。

&emsp;&emsp;然而，`user.social_networks`这个object域开启了动态mapping，所以这一层的新的域会被动态添加：

```text
PUT my-index-000001
{
  "mappings": {
    "dynamic": false, 
    "properties": {
      "user": { 
        "properties": {
          "name": {
            "type": "text"
          },
          "social_networks": {
            "dynamic": true, 
            "properties": {}
          }
        }
      }
    }
  }
}
```

&emsp;&emsp;第4行，mapping类型那一层关闭了动态mapping

&emsp;&emsp;第6行，`user`这个object域继承了mapping类型一层的`dynamic`的参数

&emsp;&emsp;第12行，`social_networks`这个inner object开启了动态mapping

##### Parameters for dynamic

&emsp;&emsp;`dynamic`的不同值控制了是否动态的添加新的域，可选值如下：

- true：新的域会被自动添加（默认值）
- runtime：新的域会被作为[runtime fields](###Runtime fields)进行动态添加，这些域不会被索引，查询阶段可以从`_source`中加载出来
- false：新的域会被忽略，这些域不会被索引也无法用于搜索，但仍然存在与`_source`中，这些域不会被添加到mapping中，必须要显示添加
- strict：如果发现有新的域出现，那么异常会被抛出并且这篇文档会被reject，新的域必须在mapping中显示添加

#### eager_global_ordinals
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/eager-global-ordinals.html)

##### What are global ordinals?

&emsp;&emsp;为了能支持在聚合和其他操作中可以从文档中找到域值，Elasticsearch使用了一个成为[doc values](####doc_values)的数据结构。例如`keyword`这种Term-based的域类型（field type）会使用ordinal mapping这种更紧凑的数据结构来存储doc values。ordinal mapping的[工作原理](https://www.amazingkoala.com.cn/Lucene/DocValues/2019/0219/34.html)是给每一个term分配一个基于字典序，递增有序的整数。会为每篇文档存储这个域的doc values的ordinal（数字），而不是域值对应的原始term，通过一个lookup Structure实现ordinal和term之间的转化。

&emsp;&emsp;在聚合中使用ordinal能极大的提高性能。例如在分片级别（shard-level）`terms aggregation`中使用ordinal来收集文档号并放入到分桶中，并且在跨分片对查询结果合并（combining）前将ordinal转化为对应的原始的term值。

&emsp;&emsp;每一个index segment定义了自己的ordinal mapping。但是聚合是需要收集分片上的所有数据。所以为了能在分片级别使用ordinal用于聚合，Elasticsearch创建了一个名为`global ordinals`的统一映射（ordinal mapping）。这个global ordinal mapping是一个segment original，为每一个段构建并维护了global ordinal 到local ordinal的映射关系。

&emsp;&emsp;如果查询中包含了下列的component就会使用Global ordinals：

- 一些使用`keyword`，`ip`， `flattened`域的bucket aggregation。包含了上文中提到的`terms aggregation`，同样的还有`composite`，`diversified_sampler`以及`significant_terms`。
- 使用`text`域的bucket aggregation需要这个域开启[fielddata](#####fielddata mapping parameter)。
- Operations on parent and child documents from a `join`  field, including `has_child` queries and `parent` aggregations.

> NOTE: global ordinal mapping会占用heap memory，属于[field data cache](####Field data cache settings)的一部分。
> 对high cardinality fields（不同的域值数量很多）进行聚合会使用很多的内存并且会触发[field data circuit breaker](#####Field data circuit breaker)。

##### Loading global ordinals

&emsp;&emsp;查询期间，在ordinals可以被使用前必须先构建global ordinal mapping。默认情况下，在需要使用global ordinal时并且只在这类查询第一次发起后才会构建这个mapping。如果你想要优化索引速度（Indexing speed），这是一种正确的方式。但如果查询性能的优先级最高，建议尽快生成将要执行聚合的域的global ordinals：

```text
PUT my-index-000001/_mapping
{
  "properties": {
    "tags": {
      "type": "keyword",
      "eager_global_ordinals": true
    }
  }
}
```

&emsp;&emsp;开启`eager_global_ordinals`后，在分片[refresh](####Refresh API)后就会构建global ordinals。Elasticsearch总是在展示（expose）变更的索引内容前先载入它们，将构建global ordinal的开销从查询期间转移到索引期间。Elasticsearch将总是在创建一个新的副本分片时急切的（eagerly）构建global ordinal，同样的会发生在增加副本数量或者在新的节点重新分配一个分片时。

&emsp;&emsp;可以通过更新`eager_global_ordinals`这个设置来关闭Eager loading：

```text
PUT my-index-000001/_mapping
{
  "properties": {
    "tags": {
      "type": "keyword",
      "eager_global_ordinals": false
    }
  }
}
```

##### Avoiding global ordinal loading

&emsp;&emsp;通常来说，global ordinal不会在载入时间跟内存占用方面有很大的开销。然而在大的分片上或者域中包含了很多唯一term上载入global ordinal的开销会比较昂贵。因为global ordinal提供了分片上所有段的统一的mapping，当出现一个新段后需要重新完整的构建global ordinal：

&emsp;&emsp;在某些情况下，可以完全避免加载global ordinal：

- `terms`，`sampler`，`significant_terms`支持[execution_hint](#####Execution hint)参数来控制分桶的收集。默认是值`global_ordinals`，但可以设置为`map`直接使用term值。
- 如果一个分片被[force-merged](####Force merge API)到单个段中，那么它的segment ordinal就已经相当于global ordinal。在这种情况下，Elasticsearch不需要构建一个global ordinal mapping并且不会有因为使用global ordinals产生的开销。注意的是，处于性能原因，你应该只force-merge那些你不再往里写入的索引。

#### enabled(mapping parameter)
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/enabled.html)

&emsp;&emsp;Elasticsearch会尝试索引你提供的所有的域，但有时候你只是想保存某些域而不想索引它们。例如你想要使用Elasticsearch来存储web session，你也许要索引session ID和 last update time，但是你不想对session data进行查询或者聚合操作。

&emsp;&emsp;`enabled`参数只能在mapping的顶层top-level定义并且只能作用[object](####Object field type)类型的域，这会使得Elasticsearch不再解析object域下所有内容，JSON内容仍然可以从[\_source](####\_source field)中检索到，无法通过其他方式查询或者进行存储：

```text
UT my-index-000001
{
  "mappings": {
    "properties": {
      "user_id": {
        "type":  "keyword"
      },
      "last_updated": {
        "type": "date"
      },
      "session_data": { 
        "type": "object",
        "enabled": false
      }
    }
  }
}

PUT my-index-000001/_doc/session_1
{
  "user_id": "kimchy",
  "session_data": { 
    "arbitrary_object": {
      "some_array": [ "foo", "bar", { "baz": 2 } ]
    }
  },
  "last_updated": "2015-12-06T18:20:22"
}

PUT my-index-000001/_doc/session_2
{
  "user_id": "jpountz",
  "session_data": "none", 
  "last_updated": "2015-12-06T18:22:13"
}
```

&emsp;&emsp;第11行，`session_data`域的`enable`参数为false

&emsp;&emsp;第22行，任意类型的数据都可以赋值给`session_data`域，Elasticsearch不会解析这些数据（不解析意味着可以给一个object域赋非JSON格式的数据）

&emsp;&emsp;第33行，`session_data`域同样会忽略不是JSON类型的数据

&emsp;&emsp;可以在mapping中直接定义`enable`参数为false，这样使得所有的内容可以在`_source`字段中检索到但是不会被索引。

```text
PUT my-index-000001
{
  "mappings": {
    "enabled": false 
  }
}

PUT my-index-000001/_doc/session_1
{
  "user_id": "kimchy",
  "session_data": {
    "arbitrary_object": {
      "some_array": [ "foo", "bar", { "baz": 2 } ]
    }
  },
  "last_updated": "2015-12-06T18:20:22"
}

GET my-index-000001/_doc/session_1 

GET my-index-000001/_mapping 
```

&emsp;&emsp;第4行，`enable`参数将作用mapping中的所有域

&emsp;&emsp;第19行，文档可以被检索到

&emsp;&emsp;第21行，这个索引的mapping中没有任何的域的信息

&emsp;&emsp;The `enabled` setting for existing fields and the top-level mapping definition cannot be updated。

&emsp;&emsp;由于Elasticsearch完全不会去解析域值，所以可以把非object格式(JSON格式)的值赋值给`object`域：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "session_data": {
        "type": "object",
        "enabled": false
      }
    }
  }
}

PUT my-index-000001/_doc/session_1
{
  "session_data": "foo bar" 
}
```

&emsp;&emsp;成功添加了文档，并且`session_data`域的域值不是JSON类型。

#### format(mapping parameter)
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping-date-format.html#strict-date-time)

#### ignore_above
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ignore-above.html)

&emsp;&emsp;字符串长度超过`ignore_above`中设置的值不会被索引或者存储，对于字符串数组，`ignore_above`会作用apply到数组中每一个字符串，并且超过`ignore_above`中设置的值不会被索引或者存储。

> NOTE：All strings/array elements will still be present in the `_source` field, if the latter is enabled which is the default in Elasticsearch.

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "message": {
        "type": "keyword",
        "ignore_above": 20 
      }
    }
  }
}

PUT my-index-000001/_doc/1 
{
  "message": "Syntax error"
}

PUT my-index-000001/_doc/2 
{
  "message": "Syntax error with some long stacktrace"
}

GET my-index-000001/_search 
{
  "aggs": {
    "messages": {
      "terms": {
        "field": "message"
      }
    }
  }
}
```

&emsp;&emsp;第7行，这个域会忽略字符串长度超过20的域值

&emsp;&emsp;第13行，这篇文档能成功索引

&emsp;&emsp;第18行，这篇文档会被索引，但是`message`域不会被索引

&emsp;&emsp;第23行，两篇文档都会被搜索到，但聚合中只有第一篇文档的message的值会被呈现present

> TIP：可以通过[update mapping API](####Update mapping API)更新`ignore_above`的值。

&emsp;&emsp;这个选项也可以用来处理Lucene中单个term的最大长度只支持到32766的问题。

> NOTE：`ignore_above`计算的字符的数量，但Lucene是按照字节计算的，如果你使用UTF-8那么长度应该限制到 32766/4 =8191，因为UTF-8最多用4个字节来描述一个字符。

#### ignore_malformed
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ignore-malformed.html)

&emsp;&emsp;有时候你不用过度控制待写入的数据，用户可能发送了[date](####Data)类型的`login`域，但是又发送了一个email地址的`login`域。

&emsp;&emsp;尝试索引一个数据类型错误的域默认情况下会抛出异常，并且reject这个文档，`ignore_malformed`参数如果设置为true，允许忽略异常，数据格式异常的域不会被索引，其他的域会被正常处理。

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "number_one": {
        "type": "integer",
        "ignore_malformed": true
      },
      "number_two": {
        "type": "integer"
      }
    }
  }
}

PUT my-index-000001/_doc/1
{
  "text":       "Some text value",
  "number_one": "foo" 
}

PUT my-index-000001/_doc/2
{
  "text":       "Some text value",
  "number_two": "foo" 
}
```

&emsp;&emsp;第19行，这篇文档的`text`域会被索引，`number_one`域不会被索引

&emsp;&emsp;第25行，这篇文档会被reject因为`number_two`域不允许数据格式错误的域值

&emsp;&emsp;下面的mapping类型允许设置`ignore_malformed`参数：

| [Numeric](####Numeric field types) | `long`, `integer`, `short`, `byte`, `double`, `float`, `half_float`, `scaled_float` |
| :----------------------------------------------------------: | ------------------------------------------------------------ |
| [Date](####Date field type) | `date`                                                       |
| [Date nanoseconds](####Date nanoseconds field type) | `date_nanos`                                                 |
| [Geopoint](####Geopoint field type) | `geo_point` for lat/lon points                               |
| [Geoshape](####Geoshape field type) | `geo_shape` for complex shapes like polygons                 |
| [IP](####IP field type) | `ip` for IPv4 and IPv6 addresses                             |

>  TIP：可以通过[update mapping API](####Update mapping API)更新`ignore_malformed`的值。

##### Index-level default

&emsp;&emsp;在索引层index level设置`index.mapping.ignore_malformed`会对mapping中所有域生效，如果mapping中的某些类型不支持这个参数会忽略该设置。

```text
PUT my-index-000001
{
  "settings": {
    "index.mapping.ignore_malformed": true 
  },
  "mappings": {
    "properties": {
      "number_one": { 
        "type": "byte"
      },
      "number_two": {
        "type": "integer",
        "ignore_malformed": false 
      }
    }
  }
}
```

&emsp;&emsp;第8行，`number_one`域会继承索引层的设置

&emsp;&emsp;第13行，`number_two`域的`ignore_malformed`会覆盖索引层的设置

##### Dealing with malformed fields

&emsp;&emsp;`ignore_malformed`开启后，数据格式错误的域在索引期间会被忽略，无论如何建议保留那些包含数据格式错误域的文档的数量，否则查询[\_ignored](####\_ignored field)域就没有意义了。Elasticsearch可以使用`_ignored`域结合`exists`,`term` or `terms`来查询哪些文档中有数据格式错误的域。

##### Limits for JSON Objects

&emsp;&emsp;下面的数据类型不能使用`ignore_malformed`：

- [Nested data type](####Nested field type)
- [Object data type](####Object field type)
- [Range data types](####Range field types)

&emsp;&emsp;`ignore_malformed`参数不能用于JSON objects。JSON object是使用`{}`包住的类型，它包含的数据映射了nested, object, and range数据类型。

&emsp;&emsp;如果你提交了数据格式错误的JSON object，Elasticsearch会无视`ignore_malformed`参数的设置并返回错误并且reject这个文档。

#### index(mapping parameter)
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping-index.html)

&emsp;&emsp;`index`这个参数用来控制域值是否要索引，该值可以是true或者false，默认值为true，没有进行索引的域通常是无法用于查询的。

#### index_options
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-options.html)

&emsp;&emsp;`index_options`参数用来控制哪些信息被添加到倒排索引[inverted index](https://www.amazingkoala.com.cn/Lucene/Index/2019/0222/36.html)中，用于查询或者高亮。只有term-based的域比如[text](####Text type family)、[keyword](####Keyword type family)才支持该配置。

&emsp;&emsp;`index_options`参数的可选值如下所示，注意的是每个参数值包含上一个参数值中添加的参数，比如`freq`包含`docs`；`positions`同时包含`freq`和`docs`：

- docs：只有文档号被添加到倒排索引中，可以解决这个问题：term是否在这个域中？
- freqs：文档号跟词频会被添加到倒排索引中，词频会用于打分，词频大意味着文档能获得较高的分数
- positions（默认值）：文档号跟词频、位置会被添加到倒排索引中，位置信息可以用于[proximity or phrase queries](####Match phrase query)
- offsets：文档号跟词频、位置。term的开始跟结束的偏移信息（可以用于定位term在字符串中的位置）会被添加到倒排索引中，offsets可以用于提高[highlighting](###Highlighting)的性能。

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "text": {
        "type": "text",
        "index_options": "offsets"
      }
    }
  }
}

PUT my-index-000001/_doc/1
{
  "text": "Quick brown fox"
}

GET my-index-000001/_search
{
  "query": {
    "match": {
      "text": "brown fox"
    }
  },
  "highlight": {
    "fields": {
      "text": {} 
    }
  }
}
```

&emsp;&emsp;第27行，因为第7行设置了`offsets`参数，`text`域将默认使用倒排信息（posting）来实现高亮。

#### index_phrases
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-phrases.html)

&emsp;&emsp;开启这个参数后，2个term组成的域值会被索引到额外的域（separate filed）中。这使得exact phrase（no slop） 查询可以更高效，代价就是索引大小会增加。注意的是只有移除了停用词后才能达到最佳性能，因为包含了停用词的短语将不会使用subsidiary field并且会回滚到标准的短语查询。默认是false。

#### index_prefixes
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-prefixes.html)

&emsp;&emsp;`index_prefixes`参数对term的前缀进行索引来加速前缀查询。接收下面的可选设置：

- `min_chars`

&emsp;&emsp;用于索引的前缀长度最小值。该值必须大于0，默认值是2（inclusive）

- `max_chars`

&emsp;&emsp;用于索引的前缀长度最大值。该值必须小于20，默认值是5（inclusive）

&emsp;&emsp;下面的例子使用默认的前缀长度设置创建了一个text域：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "body_text": {
        "type": "text",
        "index_prefixes": { }    
      }
    }
  }
}
```

&emsp;&emsp;第7行，空的object即使用默认的`min_chars`和`max_chars`设置。

&emsp;&emsp;这个例子使用了自定义的前缀长度设置：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "full_name": {
        "type": "text",
        "index_prefixes": {
          "min_chars" : 1,
          "max_chars" : 10
        }
      }
    }
  }
}
```

#### meta(mapping parameter)
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping-field-meta.html)

&emsp;&emsp;域的附加元信息（metadata）（key-value键值对信息），元数据对Elasticsearch是不透明的，他只是用来在多个应用间分享在同一个索引中某个域的元信息，比如域值的单位unit：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "latency": {
        "type": "long",
        "meta": {
          "unit": "ms"
        }
      }
    }
  }
}
```

>NOTE：域的元数据最多可以设置5个，key的长度必须小于等于20，value的长度必须小于等于50

>NOTE：域的元数据可以通过mapping更新进行变更，新的元信息会覆盖旧的元信息

&emsp;&emsp;Elastic产品会使用下面2个标准的域的元数据，你可以遵循这些元数据约定来获得更好的数据开箱即用体验

##### unit

&emsp;&emsp;数值类型的单位，例如`percent`, `byte`或者 [time unit](###API conventions)，默认情况域是没有单位的，除了数值类型的域，百分比单位约定 `1`即`100%`。

##### metric_type

&emsp;&emsp;metric_type同样用于数值类型的域，可选值是`gauge`跟`counter`，gauge是一种单值测量，可以随着时间的推移而上升或下降，例如温度。counter是一个单值累积计数器，它只上升，比如web服务器处理的请求数。默认情况下，域不会使用metric_type参数。仅对数值类型的域有效。

#### fields
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/multi-fields.html)

&emsp;&emsp;经常出于某些目的需要对同一个域执行不同的索引方式，这就是`multi-fields`的设计初衷。例如，一个字符串的域值可以作为`text`域使得可以全文检索，也可以作为`keyword`域使得可以用于排序、聚合：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "city": {
        "type": "text",
        "fields": {
          "raw": { 
            "type":  "keyword"
          }
        }
      }
    }
  }
}

PUT my-index-000001/_doc/1
{
  "city": "New York"
}

PUT my-index-000001/_doc/2
{
  "city": "York"
}

GET my-index-000001/_search
{
  "query": {
    "match": {
      "city": "york" 
    }
  },
  "sort": {
    "city.raw": "asc" 
  },
  "aggs": {
    "Cities": {
      "terms": {
        "field": "city.raw" 
      }
    }
  }
}
```

&emsp;&emsp;第8行，`city.raw`域是`city`域的`keyword`版本

&emsp;&emsp;第31行，`city`域可以用于全文检索

&emsp;&emsp;第35、40行，`city.raw`可以用于排序跟聚合

&emsp;&emsp;你可以通过[update mapping API](####Update mapping API)对现有的域添加`multi-filed`

&emsp;&emsp;`multi-field`域完全独立与parent filed（上面的例子中，`city.raw`的parent filed就是`city`），意味着它不会继承parent field的任何参数。当然了，Multi-fields不会更改`_source`中的内容。

##### Multi-fields with multiple analyzers

&emsp;&emsp;另一个multi-fields的用例就是对同一个域的域值采用不同的分词器来得到更好的关联（better relevance）。比如我们可以对域值使用[standard analyzer](####Standard analyzer)，将这个域值分为多个词（word），或者对这个域值使用[english analyzer](####Language analyzers) ，将英文单词转为root format，并且写入到索引：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "text": { 
        "type": "text",
        "fields": {
          "english": { 
            "type":     "text",
            "analyzer": "english"
          }
        }
      }
    }
  }
}

PUT my-index-000001/_doc/1
{ "text": "quick brown fox" } 

PUT my-index-000001/_doc/2
{ "text": "quick brown foxes" } 

GET my-index-000001/_search
{
  "query": {
    "multi_match": {
      "query": "quick brown foxes",
      "fields": [ 
        "text",
        "text.english"
      ],
      "type": "most_fields" 
    }
  }
}
```

&emsp;&emsp;第5行，`text`域使用`standard`（默认）分词器

&emsp;&emsp;第8行，`text.english`域使用`english`分词器

&emsp;&emsp;第19、22行，索引了两篇文档，一篇文档中包含了`fox`而另一篇则包含了`foxes`

&emsp;&emsp;第33行，同时查询`text`跟`text.english`域，这两个域都会影响文档的打分

&emsp;&emsp;`text`域在第一篇文档中包含了`fox`并且在第二篇文档中包含了`foxes`。`text.english`域在两篇文档中都包含了`fox`，因为`foxes`被词根化stemmed为成了`fox`。

&emsp;&emsp;查询中的字符串同样会对于`text`域使用`standard`分词器，并且对于`text.english`域使用`english`分词器。词根化stemmed的域允许如果是查询`foxes`，会去匹配包含`fox`的文档，使得我们可以尽可能的匹配更多的文档。当查询没有词根化unstemmed的`text`域时，我们会提高那些同时匹配`foxes`的文档的分数。

#### normalizer(mapping parameter)
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/normalizer.html)

&emsp;&emsp;[keyword](####Keyword type family)的属性`normalizer`类似与[analyzer](####analyzer(mapping parameter))，只是它能保证分析链（analysis chain）只会生成单个token。

&emsp;&emsp;`normalizer`先作用到keyword然后再进行索引，同样的在查询期间，查询`kewword`域时会执行一个query parser，例如[match](####Match query) query或者term-level query例如[term](####Term query) query。

&emsp;&emsp;可以使用一个简单的elasticsearch自带的（ship with）名为`lowercase`的normalizer，自定义的normalizer可以在analysis中定义：

```text
PUT index
{
  "settings": {
    "analysis": {
      "normalizer": {
        "my_normalizer": {
          "type": "custom",
          "char_filter": [],
          "filter": ["lowercase", "asciifolding"]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "foo": {
        "type": "keyword",
        "normalizer": "my_normalizer"
      }
    }
  }
}

PUT index/_doc/1
{
  "foo": "BÀR"
}

PUT index/_doc/2
{
  "foo": "bar"
}

PUT index/_doc/3
{
  "foo": "baz"
}

POST index/_refresh

GET index/_search
{
  "query": {
    "term": {
      "foo": "BAR"
    }
  }
}

GET index/_search
{
  "query": {
    "match": {
      "foo": "BAR"
    }
  }
}
```

&emsp;&emsp;上面的查询会匹配到1和2这两篇文档，因为`BAR`同时在索引和查询期间被转化为`bar`。

```text
{
  "took": $body.took,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped" : 0,
    "failed": 0
  },
  "hits": {
    "total" : {
        "value": 2,
        "relation": "eq"
    },
    "max_score": 0.4700036,
    "hits": [
      {
        "_index": "index",
        "_id": "1",
        "_score": 0.4700036,
        "_source": {
          "foo": "BÀR"
        }
      },
      {
        "_index": "index",
        "_id": "2",
        "_score": 0.4700036,
        "_source": {
          "foo": "bar"
        }
      }
    ]
  }
}
```

&emsp;&emsp;同样的，由于keyword的值在索引前就先被转化了，意味着聚合操作返回的是normalized values：

```text
GET index/_search
{
  "size": 0,
  "aggs": {
    "foo_terms": {
      "terms": {
        "field": "foo"
      }
    }
  }
}
```

&emsp;&emsp;返回：

```text
{
  "took": 43,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped" : 0,
    "failed": 0
  },
  "hits": {
    "total" : {
        "value": 3,
        "relation": "eq"
    },
    "max_score": null,
    "hits": []
  },
  "aggregations": {
    "foo_terms": {
      "doc_count_error_upper_bound": 0,
      "sum_other_doc_count": 0,
      "buckets": [
        {
          "key": "bar",
          "doc_count": 2
        },
        {
          "key": "baz",
          "doc_count": 1
        }
      ]
    }
  }
}
```

#### norms(mapping parameter)
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/norms.html)

#### null_value
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/null-value.html)

&emsp;&emsp;`null`不能被索引或者搜索，当一个域被设置为`null`（空的数组或者数组元素都是`null`），该域被认为是没有值的。

&emsp;&emsp;`null_value`允许将那些为null的域值替换为你指定的值，使得可以被索引和搜索，例如：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "status_code": {
        "type":       "keyword",
        "null_value": "NULL" 
      }
    }
  }
}

PUT my-index-000001/_doc/1
{
  "status_code": null
}

PUT my-index-000001/_doc/2
{
  "status_code": [] 
}

GET my-index-000001/_search
{
  "query": {
    "term": {
      "status_code": "NULL" 
    }
  }
}
```

&emsp;&emsp;第7行，使用字符串`NULL`替换那些为null的域值。

&emsp;&emsp;第20行，空的数组不包含显示的null值，该域值不会被替换为`NULL`

&emsp;&emsp;第27行，域名status_code、域值NULL的查询可以匹配文档1，不会匹配文档2

>IMPORTANT：`null_value`的域值类型必须跟这个域的类型一致，long类型的域不能设置为字符串类型的`null_value`

>NOTE：`null_value`只会影响数据的索引和查询，它不会修改_source中文档的数据

#### position_increment_gap
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/position-increment-gap.html)


#### properties
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/properties.html)

#### search_analyzer
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-analyzer.html)

&emsp;&emsp;通常来说，在索引和查询期间应该应用（apply）相同的[analyzer](####analyzer(mapping parameter))来保证query中的term跟倒排索引中的term有相同的格式（in the same format）。

&emsp;&emsp;但有的时候在查询期间使用不同的分词器是有意义的，比如使用[edge_ngram tokenizer](####Edge n-gram tokenizer) 用于autocomplete或者查询期间的同义词查询。

&emsp;&emsp;默认情况下，查询会使用mapping中定义的`analyzer`，但是可以用`search_analyzer`设置覆盖：

```text
PUT my-index-000001
{
  "settings": {
    "analysis": {
      "filter": {
        "autocomplete_filter": {
          "type": "edge_ngram",
          "min_gram": 1,
          "max_gram": 20
        }
      },
      "analyzer": {
        "autocomplete": { 
          "type": "custom",
          "tokenizer": "standard",
          "filter": [
            "lowercase",
            "autocomplete_filter"
          ]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "text": {
        "type": "text",
        "analyzer": "autocomplete", 
        "search_analyzer": "standard" 
      }
    }
  }
}

PUT my-index-000001/_doc/1
{
  "text": "Quick Brown Fox" 
}

GET my-index-000001/_search
{
  "query": {
    "match": {
      "text": {
        "query": "Quick Br", 
        "operator": "and"
      }
    }
  }
}
```

&emsp;&emsp;第13行，Analysis设置中自定义的分词器`autocomplete`

&emsp;&emsp;第28，29行，`text`域使用在索引期间使用`autocomplete`分词器，而在查询期间使用`standard`分词器

&emsp;&emsp;第37行，这个域对应的这些term会被索引：[q, qu, qui, quic, quick, b, br, bro, brow, brown, f, fo, fox]

&emsp;&emsp;第45行，query中查询的term为：[quick, br]

&emsp;&emsp;见[Index time search-as-you- type ](https://www.elastic.co/guide/en/elasticsearch/guide/2.x/_index_time_search_as_you_type.html)查看这个例子的详细介绍。

> TIP：可以使用[update mapping API](####Update mapping API)对现有的域上更新`search_analyzer`设置。注意的是，Note, that in order to do so, any existing "analyzer" setting and "type" need to be repeated in the updated field definition。

#### similarity
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/similarity.html)

&emsp;&emsp;Elasticsearch允许你为每一个域配置一个文本打分算法（text scoring algorithm）或者`similarity`。参数`similarity`允许你提供一个简单文本打分算法而不是使用默认的`BM25`，例如`boolean`。

|  BM25   | 即[Okapi BM25 algorithm](https://en.wikipedia.org/wiki/Okapi_BM25)，Elasticsearch以及Lucene默认使用这个算法 |
| :-----: | :----------------------------------------------------------: |
| boolean | 一个简单的布尔算法，不需要全文本ranking并且打分值基于是否匹配到了查询中的term。Boolean算法给term打出的分数跟他们的query boost一样 |

&emsp;&emsp;参数`similarity`可以在域最开始创建时设置在这个域的层级：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "default_field": { 
        "type": "text"
      },
      "boolean_sim_field": {
        "type": "text",
        "similarity": "boolean" 
      }
    }
  }
}
```

&emsp;&emsp;第5行，这个域名为`default_field`的域使用`BM25`算法

&emsp;&emsp;第10行，这个域名为`boolean_sim_field`的域使用`boolean`算法


#### store(mapping parameter)
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping-store.html)

&emsp;&emsp;默认情况下，域值被[indexed](####index)使得可以被搜索到，但是他们没有存储（store）。意味着该域能用于查询，但是原始的域值（origin source value）不能被检索到。

&emsp;&emsp;通常来说没有关系，因为域值早已经是[\_source](####\_source field)域的一部分，默认被存储了。如果你想检索单个域或者一些域的域值而不是整个`_source`， 你可以使用[source filtering](###Retrieve selected fields from a search)来达到你的目的。

&emsp;&emsp;在有些情况下`store`一个域的域值是合理的（make sense）。比如说你有一篇文档有`title`、`date`以及一个域值很大的`content`域。你可能只想获取`title`、`date`的域值而不用从域值很大（因为\_source中包含所有域的域值）的`_source`域中提取出来：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "store": true 
      },
      "date": {
        "type": "date",
        "store": true 
      },
      "content": {
        "type": "text"
      }
    }
  }
}

PUT my-index-000001/_doc/1
{
  "title":   "Some short title",
  "date":    "2015-01-01",
  "content": "A very long content field..."
}

GET my-index-000001/_search
{
  "stored_fields": [ "title", "date" ] 
}
```

&emsp;&emsp;第7、11行，`title`跟`date`域的域值被存储了

&emsp;&emsp;第29行，请求将检索`title`跟`date`的域值（不是从`_source`中提取）

> NOTE：
>
> Stored fields returned as arrays
>
> 为了一致性，stored fields总是以数组方式返回，因为没法知道原始的域值是单值，多值还是一个空的数组。如果你想获取这个域原始的域值（输入文档中的值），你应该从`_source`中获取。

#### term_vector
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/term-vector.html)

&emsp;&emsp;Term vectors包含了经过[analysis](##Text analysis)处理后生成的terms的信息，包括：

- terms的列表
- 每个term的位置（先后顺序）
- term在原始的字符串中的开始跟结束的偏移位置
- 负载（payload，不是所有的term都会有payload）每个不同位置（在原始字符串中的位置）的term关联了一个二进制数据的payload

&emsp;&emsp;这些term vector会被存储所以可以针对某一篇文档进行检索。

&emsp;&emsp;`term_vector`可以接受下面的参数：

|               no                |         不会存储term vectors（默认）         |
| :-----------------------------: | :------------------------------------------: |
|               yes               | 只有term（位置、偏移、负载不会存储）会被存储 |
|         with_positions          |                存储term跟位置                |
|          with_offsets           |                存储term跟偏移                |
|     with_positions_offsets      |            存储term、位置以及偏移            |
|     with_positions_payloads     |            存储term、位置以及负载            |
| with_positions_offsets_payloads |         存储term、位置、偏移以及负载         |

&emsp;&emsp;fast vector highlighter需要参数`with_positions_offsets`。[term vectors API](####Term vectors API)用来检索存储的信息。

> WARNING：参数`with_positions_offsets`设置后会使得这个域的索引大小翻倍。

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "text": {
        "type":        "text",
        "term_vector": "with_positions_offsets"
      }
    }
  }
}

PUT my-index-000001/_doc/1
{
  "text": "Quick brown fox"
}

GET my-index-000001/_search
{
  "query": {
    "match": {
      "text": "brown fox"
    }
  },
  "highlight": {
    "fields": {
      "text": {} 
    }
  }
}
```

&emsp;&emsp;term vectors开启后，`text`域会默认使用fast vector highlighter。

### Mapping limit settings

（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mapping-settings-limit.html)
&emsp;&emsp;使用下面的设置限制mapping（手动或者动态创建）中域的数量，防止因为文档（比如每一篇文档都会增加一些域）导致mapping explosion。

#### index.mapping.total_fields.limit

&emsp;&emsp;索引中域的数量最大值。Field and object mappings，以及field aliases都会纳入到统计中并受到限制。默认值为`1000`。

> IMPORTANT：这个限制是为了防止mappings和查询变的很大（too large）。数量越高会导致性能下降以及内存问题，特别是集群处于高负载或者低资源时。
> 如果你提高这个设置，我们建议你同时提高[indices.query.bool.max_clause_count](####Search settings)，这个设置限制了一个Query中clause数量的最大值。

> TIP：如果你mapping中包含 a large，arbitrary set of keys，考虑使用[flattened](####Flattened field type) 数据类型。

#### index.mapping.depth.limit

&emsp;&emsp;某个域的最大深度，用来估量inner object的数量。例如，如果所有的域都在root object level，那么深度为`1`。如果某个域有一个object mapping，那么深度就是`2`。默认值为`20`。

#### index.mapping.nested_fields.limit

&emsp;&emsp;索引中[nested](####Nested field type)类型的域的数量最大值。`nested`类型应该只用于特殊的用例中，数组中的object需要各自独立的进行查询。为了保护mapping的不当设计，这个设置会限制每一个索引中`nested`类型的域的数量。默认值是`50`。

#### index.mapping.nested_objects.limit

&emsp;&emsp;一篇文档中所有`nested`类型的域中JSON object的数量总和最大值。当一篇文档中包含太多的nested object时，防止出现OOM的错误。默认值是`10000`。

#### index.mapping.field_name_length.limit

&emsp;&emsp;域的名字长度最大值。这个设置不能解决mappings explosion的问题，但是如果你想要限制filed length时仍然是有用的。通常不需要设置它。除非用户增加了大量的域，并且每个域的域名长度非常的长，否则使用默认值就可以了。默认值是`Long.MAX_VALUE (no limit)`。

#### index.mapping.dimension_fields.limit

&emsp;&emsp;预览功能（[Dynamic](####Dynamic index settings)，integer）

&emsp;&emsp;Elastic内部使用。

&emsp;&emsp;索引中时序维度（time series dimension）数量的最大值。默认值为`16`。

&emsp;&emsp;你可以使用[time_series_dimension](#####Parameters for ip fields) mapping参数将某个域标记为一个dimension。

### Removal of mapping types
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/removal-of-types.html)

&emsp;&emsp;Elasticsearch 8.0.0不再支持mapping types。见7.x中的[removal of types](https://www.elastic.co/guide/en/elasticsearch/reference/7.17/removal-of-types.html)了解如何不使用mapping Type并迁移你的集群。

## Text analysis
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis.html)

&emsp;&emsp;Text analysis是将无结构的文本（unstructured text），例如email的body或者产品描述，转化为优化查询的有结构的格式（structured format）的过程。

#### When to configure text analysis

&emsp;&emsp;Elasticsearch在索引和查询[text](####Text type family)域时会执行text analysis。

&emsp;&emsp;如果你的索引中没有`text`域，那你不需要任何设置，可以跳过本章节内容。

&emsp;&emsp;然而，如果你使用了`text`域或者你的文本查询（text analysis）没有返回期望的结果，那[configuring text analysis](###Configure text analysis)可以提供一定的帮助。如果你正在使用Elasticsearch实现下面的功能，那你也应该要查看下analysis configuration：

- 构建一个搜索引擎
- 挖掘无结构的数据
- 对特定语言进行微调（Fine-tune）搜索
- 进行词典或语言研究（Perform lexicographic or linguistic research）

#### In this section

- [Overview](###Text analysis overview)
- [Concepts](###Text analysis concepts)
- [Configure text analysis](###Configure text analysis)
- [Built-in analyzer reference](###Built-in analyzer reference)
- [Tokenizer reference](###Tokenizer reference)
- [Token filter reference](###Token filter reference)
- [Character filters reference](###Character filters reference)
- [Normalizers](###Normalizers)

### Text analysis overview
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-overview.html)

&emsp;&emsp;文本分析（Text analysis）使得Elasticsearch可以执行全文检索（full-text search），查询结果将返回所有相关的结果而不是返回精确的匹配结果。

&emsp;&emsp;如果你查询`Quick fox jumps`，你可能希望返回的文档中包含`A quick brown fox jumps over the lazy dog`，并且你可能还想要文档中包含像`fast fox`或者` foxes leap`相关的词。

#### Tokenization

&emsp;&emsp;Analysis通过`tokenization`使得全文检索成为可能。将一个文本break down成较小的多个块（smaller chunks），称为tokens。大部分情况下，token是一个个独立的单词（individual word）。

&emsp;&emsp;如果你将一个短语`the quick brown fox jumps`作为单个string，然后用户查询`quick fox`，则不会匹配到这个短语。如果对这个短语进行tokenize，并且分别索引每一个词，这个query string中的term会被分别的查询。意味着这个短语会被`quick fox`，`fox brown`或者其他variations匹配到。

#### Normalization

&emsp;&emsp;Tokenization使得可以匹配到独立的term（individual term），但只是字符匹配（match literally），意思是：

- 查询`Quick`不会匹配到`quick`，尽管你想要查询它们中的一个且能匹配到另一个
- 尽管`fox`和`foxes`有相同的词根（root word），但查询`foxes`不会匹配到`fox`，反之亦然（vice versa）
- 查询`jums`不会匹配到`leaps`。它们没有相同的词根，它们是同义词（synonyms）并且有相同的意思

&emsp;&emsp;为了解决上述的问题，text analysis可以将这些token normalize到一个标准格式。这使得你可以匹配到一些token，尽管这些token跟查询的term不是完全一样，但是它们足够的相似使得可以产生关联，例如：

- `Quick`可以小写为`quick`
- `foxes`可以被stemmed，或者reduce成它的词根：`fox`
- `jump`和`leap`是同义词，可以索引为单个词：`jump`

&emsp;&emsp;为了查询时能如期（intend）的匹配到这些词，你可以对query string应用相同的Tokenization和Normalization。例如`Foxes leap`的查询可以被normalized 成`fox jump`的查询。

#### Customize text analysis

&emsp;&emsp;Text analysis通过分词器[analyzer](####Anatomy of an analyzer)实现，一系列的规则来管理整个的处理过程。

&emsp;&emsp;Elasticsearch包含一个默认的分词器（analyzer），名为[standard analyzer](####Standard analyzer)，这个分词器在大部分的开箱即用用例中都表现不错。

&emsp;&emsp;如果你想自定（tailor）查询的体验，你可以选择不同的[built-in analyzer](###Built-in analyzer reference)，甚至[configure a custom one](####Create a custom analyzer)。自定义的分词器可以控制分词处理过程中的一些步骤，包括：

- 在Tokenization前更改文本（change text）
- text如何转化为token
- 在索引和搜索前，Normalization对token进行更改

### Text analysis concepts
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-concepts.html)

&emsp;&emsp;这一章节介绍Elasticsearch中text analysis的一些基本内容：

- [Anatomy of an analyzer](####Anatomy of an analyzer)
- [Index and search analysis](####Index and search analysis)
- [Stemming](####Stemming)
- [Token graphs](####Token graphs)

#### Anatomy of an analyzer
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analyzer-anatomy.html)

&emsp;&emsp;分词器（analyzer）可以是内置或者自定义的，它是包含了三个lower-level building blocks的包：character filters, tokenizers, 和 token filters。

##### Character filters(concept)

&emsp;&emsp;原始的文本作为一个字符流（a stream of characters），character filter接受这个字符流，通过adding，removing，或者changing character的方式对字符流进行转化。例如character filter可以用来将Hindu-Arabic numerals (٠١٢٣٤٥٦٧٨٩) 转化为 Arabic-Latin equivalents (0123456789)或者从流中移除（strip）HTML的element，比如`<b>`。

&emsp;&emsp;一个分词器可以没有，或者多个[character filter](###Character filters reference)。有序的依次应用（apply）这些character filter。

##### Tokenizer(concept)

&emsp;&emsp;Tokenizer接受字符流，将其拆分（break up）为独立的token（individual token），token通常是独立的一个词（individual word），然后输出一个token流（a stream of tokens）。例如[whitespace](####Whitespace tokenizer)会将文本按照空格进行token的划分，它会将`Quick brown fox!`划分成`[Quick, brown, fox!]`。

&emsp;&emsp;tokenizer同样负责记录每一个term的先后顺序，位置，以及字符的开始跟结束的[偏移](https://www.amazingkoala.com.cn/Lucene/Index/2019/0428/55.html)信息。

&emsp;&emsp;一个分词器只能有一个[Tokenizer](###Tokenizer reference)。

##### Token filters(concept)

&emsp;&emsp;token filters接受token流，并且add，remove，或者更改token。例如，[lowercase](####Lowercase token filter)这个token filters会将所有的token转成小写，[stop](####Stop token filter) token filter会从token流中移除常见的词（common word，停用词），比如`the`。[synonym](####Synonym token filter) token filter会将同义词放到token流中。

&emsp;&emsp;Token filters不允许更改token的位置以及偏移信息。

&emsp;&emsp;一个分词器可以没有，或者多个[Token filters](###Token filter reference)，有序的依次应用（apply）这些token filters。

#### Index and search analysis
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-index-search-time.html)

&emsp;&emsp;Text analysis会在下面两个时间点发生：

##### Index time

&emsp;&emsp;当索引一篇文档时，所有的[text](####Text type family)的域值都会被分词（analyzed）。

##### Search time

&emsp;&emsp;当在`text`域上执行[full-text search](###Full text queries)时，query string（用户查询的内容）会被分词。

&emsp;&emsp;Search time也被称为query time。

&emsp;&emsp;analyzer或者一系列的analysis rules在不同的时间点分别称为`index analyzer`和`search analyzer`。

##### How the index and search analyzer work together

&emsp;&emsp;在大多数情况下，Index time和search time应该使用相同的analyzer。这样能保证域值跟query string都被更改（change）成相同的tokens。这样的话在搜索时就能达到期望的token匹配。

###### example

&emsp;&emsp;下面的值作为`text`域的域值进行索引：

```text
The QUICK brown foxes jumped over the dog!
```

&emsp;&emsp;如果Index analyzer将上文的域值转化为成token并进行了normalizes，在这个例子中，每一个token代表了一个词：

```text
[ quick, brown, fox, jump, over, dog ]
```

&emsp;&emsp;这些token都会被索引。

&emsp;&emsp;随后，一位用户会在相同的`text`域上进行以下的查询：

```text
"Quick fox"
```

&emsp;&emsp;用户想要匹配刚刚上文中索引的句子：`The QUICK brown foxes jumped over the dog!`。

&emsp;&emsp;但是用户提供的query string中不存在精确的词（exact word）来匹配文档中的内容：

- `Quick` vs `QUICK`
- `fox` vs `foxes`

&emsp;&emsp;为了能正确的查询，query string要使用相同的analyzer。这个analyzer会产生下面的tokens：

```text
[ quick, fox ]
```

&emsp;&emsp;执行查询时，Elasticsearch会比较query string中tokens 和`text`域索引的tokens：

| Token | Query string | `text` field |
| :---: | :----------: | :----------: |
| quick |      X       |      X       |
| brown |              |      X       |
|  fox  |      X       |      X       |
| jump  |              |      X       |
| over  |              |      X       |
|  dog  |              |      X       |

&emsp;&emsp;由于域值跟query string使用相同方式进行分词，因此它们会生成相同的token。`quick`和`fox`是精确匹配，意味着查询会匹配到包含`"The QUICK brown foxes jumped over the dog!"`的文档，满足用户期望。

##### When to use a different search analyzer

&emsp;&emsp;有时候index time和search time需要使用不同的analyzer，不过这种场景不是很常见（while less common）。Elasticsearch允许你[specify a separate search analyzer](#####How Elasticsearch determines the search analyzer)来处理这种场景。

&emsp;&emsp;通常来说，当域值和query string使用相同格式的token（same form of tokens）后得到不在期望内或者不相关的匹配时才会考虑使用不同的（separate）search analyzer。

###### example

&emsp;&emsp;Elasticsearch用来创建一个搜索引擎来匹配那些以查询词为前缀的结果。例如，查询词`tr`应该匹配`tram`或者`trope`，而不会匹配`taxi`或者`bat`。

&emsp;&emsp;一篇文档被添加到搜索引擎的索引中，这篇文档包含一个`text`类型的域的值：

```text
"Apple"
```

&emsp;&emsp;index analyzer会将这个域值转化为tokens并且执行normalizes。在这个例子中，每一个token是这个词的各种前缀。

```text
[ a, ap, app, appl, apple]
```

&emsp;&emsp;这些token都会被索引。

&emsp;&emsp;随后，用户在相同的`text`域上进行搜索：

```text
"appli"
```

&emsp;&emsp;用户期望匹配以`appli`为前缀的结果，比如`appliance`或者`application`。这个查询不应该匹配到`apply`。

&emsp;&emsp;然而如果使用index analyzer来处理这个query string，它会生成下面的tokens：

```text
[ a, ap, app, appl, appli ]
```

&emsp;&emsp;当Elasticsearch拿着query string中的tokens去匹配`apply`的tokens时，它会找到好几个匹配的结果：

| Token | appli | apple |
| :---: | :---: | :---: |
|   a   |   X   |   X   |
|  ap   |   X   |   X   |
|  app  |   X   |   X   |
| appl  |   X   |   X   |
| appli |       |   X   |

&emsp;&emsp;这意味着错误的（erroneous）匹配到了`apple`，而且不仅仅会匹配到`apply`，还会匹配到所有以`a`为前缀的结果。

&emsp;&emsp;要修复这种情况，可以为query string指定一个不同的search analyzer来处理`text`域。

&emsp;&emsp;在这个例子中，你可以指定一个search analyzer来生成单个token而不是生成前缀集合：

```text
[ appli]
```

&emsp;&emsp;这个query string token只会匹配以`appli`为前缀的结果，更接近用户期望的查询结果。

#### Stemming
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/stemming.html)

&emsp;&emsp;Stemming（词根化）将一个词reduce到这个词的root form（词根）。使得这个词的各种变形都能在查询时匹配到。

&emsp;&emsp;例如，`walking`和`walked`都可以被stemmed到相同的root word：`walk`。一旦进行stemming，`walking`、`walked`、`walk`任意一个出现在查询中都会匹配到另外两个。

&emsp;&emsp;Stemming属于language-dependent，但通常就是移除词的前缀跟后缀。

&emsp;&emsp;有些情况下，经过stemming后的词可能就不是一个真实的词。例如`jumping`和`jumpiness`可以stemmed为`jumpi`。`jumpi`不是一个真实存在的词，但是这不会影响查询。如果所有词的变形都可以reduce为相同的词根，就可以正确的匹配到。

##### Stemmer token filters

&emsp;&emsp;在Elasticsearch中，在[stemmer token filters](#####Stemmer token filter)中处理stemming。这些token filter根据它们不同词根化的方式可以分类为：

- [Algorithmic stemmers](#####Algorithmic stemmers)，基于一系列的规则执行词根化
- [Dictionary stemmers](#####Dictionary stemmers)，基于查找词典执行词根化

&emsp;&emsp;由于词根化会更改token，我们建议在[index and search analysis](####Index and search analysis)中使用相同的stemmer token filters。

##### Algorithmic stemmers

&emsp;&emsp;Algorithmic stemmers通过应用一系列的规则将每一个词reduce到它的词根（root form）。例如，一个用于英文的algorithmic stemmers会移除复数（plural）的`-s`跟`-es`后缀。

&emsp;&emsp;Algorithmic stemmers有以下的一些优点：

- 它们要求更少的步骤并且开箱即用
- 它们占用少量的内存
- 通常比[Dictionary stemmers](#####Dictionary stemmers)的处理速度快

&emsp;&emsp;然而大多数的algorithmic stemmers只会改变词的文字（text of a word）。这意味着一些irregular word，它们本身不包含词根的内容，这类词就不能很好的词根化：

- `be`，`are`和`am`
- `mouse`和`mice`
- `foot`和`feet`

&emsp;&emsp;下面的token filters使用了algorithmic stemming：

- [stemmer](####Stemmer token filter)，为一些语言提供了algorithmic stemming，带有一些额外的变形（variants）
- [kstem](####KStem token filter)，用于英语的词根化，它结合了内建字典（built-in dictionary）的algorithmic stemming
- [porter_stem](####Porter stem token filter)，推荐使用的用于英语的词根化
- [snowball](####Snowball token filter)，使用[Snowball-based](https://snowballstem.org)，用于多种语言

##### Dictionary stemmers

&emsp;&emsp;Dictionary stemmers从提供的字典中查找词。将未词根化的word variants替换为字典中词根化的词。

&emsp;&emsp;理论上，dictionary stemmers适用于：

- irregular words的词根化
- 辨别出那些拼写类似但含义毫无相关的单词，比如：
  - `organ`和`organization`
  - `broker`和`broken`

&emsp;&emsp;实践中，algorithmic stemmers通常优于（outperform）dictionary stemmers。因为dictionary stemmers有以下的缺点：

- Dictionary quality
  - dictionary stemmer的效果取决于字典。为了能很好的工作，这些字典必须包含大量的词，需要定期更新，并且根据不同的语言进行变更。通常来说，当一个词典在开始使用后，它的不完整性以及一些条目就已经过时了。
- Size and performance
  - Dictionary stemmers必须将所有的词，前缀，后缀从字典中载入到内存。这会带来大量的RAM开销。Low-quality的字典在去除前缀和后缀方面的效率也可能较低，这会显著减缓词根提取过程

&emsp;&emsp;你可以使用[hunspell token filter](####Hunspell token filter)来执行 dictionary stemming。

> TIPs：如果可以的话，我们建议在使用 [hunspell token filter](####Hunspell token filter)前先使用algorithmic stemmer。

##### Control stemming

&emsp;&emsp;有时候stemming会将一些词共享同一个词根但是这些词之间只是拼写相似但是含义是没有任何关联的。例如，`skies`和`skiing`会被词根化为`ski`。

&emsp;&emsp;为了防止出现上述问题以及更好的控制stemming，你可以使用下面的token filters：

- [stemmer_override](####Stemmer override token filter)，可以定义指定tokens的词根化规则
- [keyword_marker](####Keyword marker token filter)，可以指定token作为一个keyword，随后的stemmer token filters不会对keyword token词根化
- [conditional](####Conditional token filter)，可以标记token为keyword word，类似`keyword_marker`

&emsp;&emsp;对于内置（built-in）的[language analyzers](####Language analyzers)，你可以使用[stem_exclusion](######Excluding words from stemming )参数来指定不需要词根化的词。

#### Token graphs
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/token-graphs.html)

&emsp;&emsp;当[tokenizer ](#####Tokenizer(concept))将文本转化为tokens后，它同时会记录下面的信息：

- 流中每一个token的`position`
- `positionLength`，一个token跨越（span）的位置数量（number of position）

&emsp;&emsp;使用上述信息可以创建一个[directed acyclic graph](https://en.wikipedia.org/wiki/Directed_acyclic_graph)，称为`token graph`。在一个token graph中，每一个位置代表了一个节点（node）。每一个token代表了一个edge或者arc，指向下一个位置。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/token-graph-qbf-ex.svg">

##### Synonyms

&emsp;&emsp;一些[token filters](#####Token filters(concept))会增加新的token到现有的token stream中，例如同义词。这些同义词通常跟现有的tokens拥有相同的position。

&emsp;&emsp;在下面的图中，`quick`和它的同义词`fast`有相同的位置：`0`。它们跨越相同的位置：

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/token-graph-qbf-synonym-ex.svg">

##### Multi-position tokens

&emsp;&emsp;有些token filters会增加跨越多个位置的token。这个token是multi-word的同义词，例如使用`atm`作为`automatic teller machine`的同义词。

&emsp;&emsp;然而只有一些token filters，比如`graph token filters`，会为multi-position的token记录准确的`positionLength`。这类token filter包括：

- [synonym_graph](####Synonym graph token filter)
- [word_delimiter_graph](####Word delimiter graph token filter)

&emsp;&emsp;Some tokenizers, such as the [nori_tokenizer](https://www.elastic.co/guide/en/elasticsearch/plugins/8.2/analysis-nori-tokenizer.html), also accurately decompose compound tokens into multi-position tokens。

&emsp;&emsp;下图中，`domain name system`和它的同义词`dns`，它们的position都是`0`。然而`dns`的`positionLength`为`3`。其他的token在图中的`positionLength`的值默认就是`1`。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/token-graph-dns-synonym-ex.svg">

###### Using token graphs for search

&emsp;&emsp;[Indexing](####Index and search analysis)会忽略`positionLength`的属性并且不支持包含multi-position tokens的token graph。

&emsp;&emsp;然而[match](####Match query)和[match_phrase](####Match phrase query) query会使用这些图从单个query string中生成多个sub-queries。

&emsp;&emsp;某个用户使用`match_phrase`查询下面的短语：

```text
domain name system is fragile
```

&emsp;&emsp;在[search analysis](####Index and search analysis)中，`dns`是`domain name system`的同义词，会被添加到query string对应的token stream中。`dns`这个token的`positionLength`的值为`3`。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/token-graph-dns-synonym-ex.svg">

&emsp;&emsp;`match_phrase`使用这个图来生成下面的sub-queries：

```text
dns is fragile
domain name system is fragile
```

&emsp;&emsp;意味着查询会匹配包含`dns is fragile`或者`domain name system is fragile`的文档。

###### Invalid token graphs

&emsp;&emsp;下面的token filter会添加token，这些token会跨越多个位置，但是只将`positionLength`记录为默认值：`1`。

- [synonym](####Synonym token filter)
- [word_delimiter](####Word delimiter token filter)

&emsp;&emsp;这意味着这些filters会为包含这些token的流生产出错误的（invalid）token graph。

&emsp;&emsp;在下图中，`dns`是multi-position，它是`domain name system`的同义词。然而`dns`的`positionLength`为`1`，导致一个错误的图。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/token-graph-dns-invalid-ex.svg">

&emsp;&emsp;避免使用错误的token图用于搜索。错误的图会导致不满足期望的查询结果。

### Configure text analysis
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/configure-text-analysis.html)

&emsp;&emsp;默认情况下，Elasticsearch会使用默认的[standard analyzer](####Standard analyzer)用于所有的文本分析（text analysis）。`standard`分词器（analyzer）作为开箱即用能满足大多数的语言和用例。如果你选择`standard`，那么不需要其他的任何配置。

&emsp;&emsp;如果`standard`满足不了你的需求，你可以查看以及测试Elasticsearch提供的其他内置的[built-in analyzers](###Built-in analyzer reference)。内置的分词器也不需要进行配置，但是可以使用一些参数来调节分词器的行为。例如你可以对`standard`配置一个自定义的停用词列表。

&emsp;&emsp;如果内置的的分词器都不能满足你的需求，你可以创建并测试你自定义的分词器。自定义的分词器包括选择和组合不同的[analyzer components](####Anatomy of an analyzer)，让你能控制分词的过程。

- [Test an analyzer](####Test an analyzer)
- [Configuring built-in analyzers](####Configuring built-in analyzers)
- [Create a custom analyzer](####Create a custom analyzer)
- [Specify an analyzer](####Specify an analyzer)

#### Test an analyzer
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/test-analyzer.html)

&emsp;&emsp;[analyze API ](####Analyze API)是一个非常有用（invaluable）的工具来查看分词器的分词效果。请求中可以指定内置的分词器：

```text
POST _analyze
{
  "analyzer": "whitespace",
  "text":     "The quick brown fox."
}
```

&emsp;&emsp;API会返回如下的结果：

```text
{
  "tokens": [
    {
      "token": "The",
      "start_offset": 0,
      "end_offset": 3,
      "type": "word",
      "position": 0
    },
    {
      "token": "quick",
      "start_offset": 4,
      "end_offset": 9,
      "type": "word",
      "position": 1
    },
    {
      "token": "brown",
      "start_offset": 10,
      "end_offset": 15,
      "type": "word",
      "position": 2
    },
    {
      "token": "fox.",
      "start_offset": 16,
      "end_offset": 20,
      "type": "word",
      "position": 3
    }
  ]
}
```

&emsp;&emsp;你也可以测试下面的组合：

- 一个tokenizer
- 没有或者多个token filters
- 没有或者多个character filters

```text
POST _analyze
{
  "tokenizer": "standard",
  "filter":  [ "lowercase", "asciifolding" ],
  "text":      "Is this déja vu?"
}
```

&emsp;&emsp;API会返回如下的结果：

```text
{
  "tokens": [
    {
      "token": "is",
      "start_offset": 0,
      "end_offset": 2,
      "type": "<ALPHANUM>",
      "position": 0
    },
    {
      "token": "this",
      "start_offset": 3,
      "end_offset": 7,
      "type": "<ALPHANUM>",
      "position": 1
    },
    {
      "token": "deja",
      "start_offset": 8,
      "end_offset": 12,
      "type": "<ALPHANUM>",
      "position": 2
    },
    {
      "token": "vu",
      "start_offset": 13,
      "end_offset": 15,
      "type": "<ALPHANUM>",
      "position": 3
    }
  ]
}
```

> Positions and character offsets
> 从上文中的`analyze` API可以看出，分词器不仅仅会将词转化为term，并且还会有序的记录每一个term的相对位置（用于短语查询或者word proximity查询），以及每一个term在原始文本中的开始跟结束的偏移位置（用于高亮查询）

&emsp;&emsp;或者在指定的索引上运行`analyze` API时指定一个[custom analyzer](####Create a custom analyzer)：

```text
PUT my-index-000001
{
  "settings": {
    "analysis": {
      "analyzer": {
        "std_folded": { 
          "type": "custom",
          "tokenizer": "standard",
          "filter": [
            "lowercase",
            "asciifolding"
          ]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "my_text": {
        "type": "text",
        "analyzer": "std_folded" 
      }
    }
  }
}

GET my-index-000001/_analyze 
{
  "analyzer": "std_folded", 
  "text":     "Is this déjà vu?"
}

GET my-index-000001/_analyze 
{
  "field": "my_text", 
  "text":  "Is this déjà vu?"
}
```
&emsp;&emsp;第6行，定义一个名为`std_folded`的分词器

&emsp;&emsp;第21行，`my_text`域使用`std_folded`分词器进行分词

&emsp;&emsp;第27行，要想引用这个分词器，`analyze` API必须要指定索引名

&emsp;&emsp;第29行，通过分词器的名字来指定一个分词器

&emsp;&emsp;第35行，对`my_text`域进行分词


&emsp;&emsp;API会返回如下的结果：

```text
{
  "tokens": [
    {
      "token": "is",
      "start_offset": 0,
      "end_offset": 2,
      "type": "<ALPHANUM>",
      "position": 0
    },
    {
      "token": "this",
      "start_offset": 3,
      "end_offset": 7,
      "type": "<ALPHANUM>",
      "position": 1
    },
    {
      "token": "deja",
      "start_offset": 8,
      "end_offset": 12,
      "type": "<ALPHANUM>",
      "position": 2
    },
    {
      "token": "vu",
      "start_offset": 13,
      "end_offset": 15,
      "type": "<ALPHANUM>",
      "position": 3
    }
  ]
}
```

#### Configuring built-in analyzers
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/configuring-analyzers.html)

&emsp;&emsp;内置的分词器（analyzer）可以直接使用而不需要进行任何的配置。而一些内置的分词器支持参数配置来改变分词器的行为。例如，[standard analyzer](####Standard analyzer)可以配置自定义的停用词集合：

```text
PUT my-index-000001
{
  "settings": {
    "analysis": {
      "analyzer": {
        "std_english": { 
          "type":      "standard",
          "stopwords": "_english_"
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "my_text": {
        "type":     "text",
        "analyzer": "standard", 
        "fields": {
          "english": {
            "type":     "text",
            "analyzer": "std_english" 
          }
        }
      }
    }
  }
}

POST my-index-000001/_analyze
{
  "field": "my_text", 
  "text": "The old brown cow"
}

POST my-index-000001/_analyze
{
  "field": "my_text.english", 
  "text": "The old brown cow"
}
```

&emsp;&emsp;第6行，我们定义了一个基于`standard`的分词器`std_english`，但是使用了英语的停用词

&emsp;&emsp;第17行，`my_text`域直接使用了`standard`分词器，没有进行任何的配置。

&emsp;&emsp;第31行，不会移除停用词，所以分词结果为：[`the, old, brown, cow`]

&emsp;&emsp;第21行，`my_text.english`域使用了`std_english`，所以英语停用词会被移除，所以第37行的分词结果为：[`old, brown, cow`]

#### Create a custom analyzer
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-custom-analyzer.html)

&emsp;&emsp;当内置的分词器（analyzer）无法满足你的需求时，你可以创建一个`custom`分词器，并使用合适的组合：

- 没有或者多个[character filters](###Character filters reference)
- 一个[tokenizer](###Tokenizer reference)
- 没有或者多个[token filters](###Token filter reference)

##### Configuration

&emsp;&emsp;`custom`分词器接受以下的参数：

|          type          | 分词器类型，可以是[built-in analyzer](###Built-in analyzer reference)类型。对于custom analyzer，使用`custom`或者omit这个参数 |
| :--------------------: | :----------------------------------------------------------: |
|       tokenizer        |     内置或者自定义的[tokenizer](###Tokenizer reference)      |
|      char_filter       | 可选参数，一个或者多个内置或者自定义[character filters](###Character filters reference) |
|         filter         | 可选参数，一个或者多个内置或者自定义[token filters](###Token filter reference) |
| position_increment_gap | When indexing an array of text values, Elasticsearch inserts a fake "gap" between the last term of one value and the first term of the next value to ensure that a phrase query doesn’t match two terms from different array elements. Defaults to `100`. See [position_increment_gap](####position_increment_gap) for more. |

##### Example configuration

&emsp;&emsp;这个例子使用了以下的组合：

###### Character Filter

- [HTML Strip Character Filter](####HTML strip character filter)

###### Tokenizer

- [Standard Tokenizer](####Standard tokenizer)

###### Token Filters

- [Lowercase Token Filter](####Lowercase token filter)
- [ASCII-Folding Token Filter](####ASCII folding token filter)

```text
PUT my-index-000001
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_custom_analyzer": {
          "type": "custom", 
          "tokenizer": "standard",
          "char_filter": [
            "html_strip"
          ],
          "filter": [
            "lowercase",
            "asciifolding"
          ]
        }
      }
    }
  }
}

POST my-index-000001/_analyze
{
  "analyzer": "my_custom_analyzer",
  "text": "Is this <b>déjà vu</b>?"
}
```

&emsp;&emsp;第7行，对于`custom`分词器，使用参数`type`的值为`custom`或者omit`type`这个参数。

&emsp;&emsp;例子：

```text
PUT my-index-000001
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_custom_analyzer": { 
          "char_filter": [
            "emoticons"
          ],
          "tokenizer": "punctuation",
          "filter": [
            "lowercase",
            "english_stop"
          ]
        }
      },
      "tokenizer": {
        "punctuation": { 
          "type": "pattern",
          "pattern": "[ .,!?]"
        }
      },
      "char_filter": {
        "emoticons": { 
          "type": "mapping",
          "mappings": [
            ":) => _happy_",
            ":( => _sad_"
          ]
        }
      },
      "filter": {
        "english_stop": { 
          "type": "stop",
          "stopwords": "_english_"
        }
      }
    }
  }
}

POST my-index-000001/_analyze
{
  "analyzer": "my_custom_analyzer",
  "text": "I'm a :) person, and you?"
}
```

&emsp;&emsp;第6行，分配给这个索引一个默认自定义的分词器，`my_custom_analyzer`。这个分词器使用了一个自定义的tokenizer，character filter以及token filter。这个分词器omit了`type`参数。

&emsp;&emsp;第18行，定义了一个自定义的名为`punctuation`的tokenizer。

&emsp;&emsp;第24行，定义了一个自定义的名为`emoticons`的character filter。

&emsp;&emsp;第33行，定义了一个自定义的名为`english_stop`的token filter。

&emsp;&emsp;上面的例子会生成下面的term：

```text
[ i'm, _happy_, person, you ]
```

#### Specify an analyzer
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/specify-analyzer.html#specify-index-time-analyzer)

&emsp;&emsp;Elasticsearch提供了多种方法（a variety of ways）来指定一个内置的或者自定义的分词器（analyzer）。

- By text field, index, or query
- [Index and search analysis](####Index and search analysis)

> TIP: Keep it simply
> 在不同的level（filed-level，index-level）和不同的时间点（索引或查询期间）能够灵活的指定分词器is good，但只有必要时才这么做。
> 
> 大多数情况下，简单的方式可以work well：为每一个`text`域指定分词器。见[Specify the analyzer for a field](#####Specify the analyzer for a field)。
> 
> 这个方式会按Elasticsearch的默认行为工作，让你在索引和查询阶段使用相同的分词器。也能让你快速的通过[get mapping API](####Get mapping API)了解哪个域使用了哪个分词器。
> 
> 如果你不为你的索引创建mapping，你可以使用[index templates](##Index templates)来达到相同的效果。

##### How Elasticsearch determines the search analyzer

&emsp;&emsp;Elasticsearch会有序的检查下面的参数来检测使用了哪种index analyzer：

- 域的参数[analyzer](####analyzer)，见[Specify the analyzer for a field](#####Specify the analyzer for a field)。
- 索引设置：`analysis.analyzer.default`。见[Specify the default analyzer for an index](#####Specify the analyzer for a field)

&emsp;&emsp;如果没有指定这些参数，那么默认使用[standard analyzer](####Standard analyzer)。

##### Specify the analyzer for a field

&emsp;&emsp;你可以使用mapping参数[analyzer](####analyzer(mapping parameter))为每一个`text`域指定分词器。

&emsp;&emsp;下面的[create index API](####Create index API)要求为`title`域指定`whitespace`分词器：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "analyzer": "whitespace"
      }
    }
  }
}
```

##### Specify the default analyzer for an index

&emsp;&emsp;除了在field-level指定分词器，你可以使用`analysis.analyzer.default`设置一个fallback analyzer。

&emsp;&emsp;下面的[create index API](####Create index API)为索引`my-index-000001`设置了一个名为`simple`的fallback analyzer。

```text
PUT my-index-000001
{
  "settings": {
    "analysis": {
      "analyzer": {
        "default": {
          "type": "simple"
        }
      }
    }
  }
}
```

##### Specify the search analyzer for a query

&emsp;&emsp;在执行[full-text query](###Full text queries)时，你可以使用`analyzer`参数来指定 search analyzer，它会覆盖任何其他的search analyzer。

&emsp;&emsp;下面的[search API](####Search API)在[match](####Match query) query中设置了一个`stop`的分词器。

```text
GET my-index-000001/_search
{
  "query": {
    "match": {
      "message": {
        "query": "Quick foxes",
        "analyzer": "stop"
      }
    }
  }
}
```

##### Specify the search analyzer for a field

&emsp;&emsp;你可以使用[search_analyzer](####analyzer)参数为每一个`text`域指定一个search analyzer。

&emsp;&emsp;如果提供了search analyzer，必须使用`analyzer`参数来指定index analyzer。

&emsp;&emsp;下面的[create index API](####Create index API)为`title`域指定了一个`simply`分词器作为search analyzer。

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "analyzer": "whitespace",
        "search_analyzer": "simple"
      }
    }
  }
}
```

##### Specify the default search analyzer for an index

&emsp;&emsp;当[creating an index](####Create index API)时，你可以使用`analysis.analyzer.default_search`设置一个默认的search analyzer。

&emsp;&emsp;如果设置了默认的search analyzer，你必须要使用`analysis.analyzer.default`设置默认的index analyzer。

&emsp;&emsp;下面的[creating an index](####Create index API)中，为名为`my-index-000001`的索引设置了一个默认的名为`whitespace`分词器作为默认的search analyzer。

```text
PUT my-index-000001
{
  "settings": {
    "analysis": {
      "analyzer": {
        "default": {
          "type": "simple"
        },
        "default_search": {
          "type": "whitespace"
        }
      }
    }
  }
}
```

### Built-in analyzer reference
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-analyzers.html)

#### Language analyzers
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-lang-analyzer.html)

##### Configuring language analyzers

###### Excluding words from stemming 

#### Standard analyzer
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-standard-analyzer.html)

&emsp;&emsp;`standard` 分词器（analyzer）是默认的分词器，如果没有指定分词器则使用`standard`。`standard`提供了基于语法的分词（基于Unicode Text Segmentation algorithm，见 [Unicode Standard Annex #29](https://unicode.org/reports/tr29/)）,它能很好的对大多数语言进行分词。

##### Example output

```text
POST _analyze
{
  "analyzer": "standard",
  "text": "The 2 QUICK Brown-Foxes jumped over the lazy dog's bone."
}
```

&emsp;&emsp;上面的句子会生成下面的term：

```text
[ the, 2, quick, brown, foxes, jumped, over, the, lazy, dog's, bone ]
```

##### Configuration

&emsp;&emsp;`standard`分词器接收下面的参数：

###### max_token_length

&emsp;&emsp;token的最大长度。如果一个token的长度超过了`max_token_length`，会按照`max_token_length`长度进行分割。默认值是`255`。

###### stopwords

&emsp;&emsp;预设的停用词，例如`_english_`或者包含了停用词的数组。默认值为`_none_`。

###### stopwords_path

&emsp;&emsp;包含停用词的文件的路径

&emsp;&emsp;见[Stop Token Filter](####Stop token filter)查看停用词的配置。

##### Example configuration

&emsp;&emsp;在这个例子中，我们将`standard`分词器的参数`max_token_length`设置为`5`（只是为了展示参数效果），然后使用预先定义的英语停用词：

```text
PUT my-index-000001
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_english_analyzer": {
          "type": "standard",
          "max_token_length": 5,
          "stopwords": "_english_"
        }
      }
    }
  }
}

POST my-index-000001/_analyze
{
  "analyzer": "my_english_analyzer",
  "text": "The 2 QUICK Brown-Foxes jumped over the lazy dog's bone."
}
```

&emsp;&emsp;上面的例子能生成下面的term：

```text
[ 2, quick, brown, foxes, jumpe, d, over, lazy, dog's, bone ]
```

##### Definition

&emsp;&emsp;`standard`分词器包括下面的内容：

###### Tokenizer

- [Standard Tokenizer](####Standard tokenizer)

###### Token Filters

- [Lower Case Token Filter](####Lowercase token filter)
- [Stop Token Filter](####Stop token filter)（默认关闭）

&emsp;&emsp;如果你需要自定义配置参数外的`standard`分词器，你需要重建一个`custom`的分词器然后修改它。通常是添加token filter。这样将重建一个内置的`standard`分词器。

```text
PUT /standard_example
{
  "settings": {
    "analysis": {
      "analyzer": {
        "rebuilt_standard": {
          "tokenizer": "standard",
          "filter": [
            "lowercase"       
          ]
        }
      }
    }
  }
}
```

&emsp;&emsp;第9行，你可以在`lowercase`后添加其他的token filter。

#### Language analyzers
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-lang-analyzer.html#english-analyzer)

#### Whitespace analyzer
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-whitespace-analyzer.html)

&emsp;&emsp;`whitespace`分词器（analyzer）会在根据空格将文本划分出term。

##### Example output

```text
POST _analyze
{
  "analyzer": "whitespace",
  "text": "The 2 QUICK Brown-Foxes jumped over the lazy dog's bone."
}
```

&emsp;&emsp;上面的句子会生成下面的term：

```text
[ The, 2, QUICK, Brown-Foxes, jumped, over, the, lazy, dog's, bone. ]
```

##### Configuration

&emsp;&emsp;`whitespace` 分词器不可以配置。

##### Definition

&emsp;&emsp;`whitespace`分词器包含下面的内容：

###### Tokenizer

- [Whitespace Tokenizer](####Whitespace tokenizer)

&emsp;&emsp;如果你需要自定义`whitespace`分词器，你可以重新创建一个名为`custom`分词器然后修改它，通常添加token filter。这将重建内置的`whitespace`分词器：

```text
PUT /whitespace_example
{
  "settings": {
    "analysis": {
      "analyzer": {
        "rebuilt_whitespace": {
          "tokenizer": "whitespace",
          "filter": [         
          ]
        }
      }
    }
  }
```

&emsp;&emsp;第8行，你可以在这里添加token filter。

### Tokenizer reference
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-tokenizers.html)

#### Edge n-gram tokenizer
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-edgengram-tokenizer.html)

#### Standard tokenizer
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-standard-tokenizer.html)

&emsp;&emsp;`standard` tokenizer提供了基于语法的分词（基于Unicode Text Segmentation algorithm，见 [Unicode Standard Annex #29](https://unicode.org/reports/tr29/)）,它能很好的对大多数语言进行分词。

##### Example output

```text
POST _analyze
{
  "tokenizer": "standard",
  "text": "The 2 QUICK Brown-Foxes jumped over the lazy dog's bone."
}
```

&emsp;&emsp;上面的句子会生成下面的term：

```text
[ The, 2, QUICK, Brown, Foxes, jumped, over, the, lazy, dog's, bone ]
```

##### Configuration

&emsp;&emsp;`standard` tokenizer接收下面的参数：

###### max_token_length

&emsp;&emsp;token的最大长度。如果一个token的长度超过了`max_token_length`，会按照`max_token_length`长度进行划分。默认值是`255`。

##### Example configuration

&emsp;&emsp;在这个例子中，我们将`standard`tokenizer的参数`max_token_length`配置为`5`（至少为了展示参数效果)：

```text
PUT my-index-000001
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_analyzer": {
          "tokenizer": "my_tokenizer"
        }
      },
      "tokenizer": {
        "my_tokenizer": {
          "type": "standard",
          "max_token_length": 5
        }
      }
    }
  }
}

POST my-index-000001/_analyze
{
  "analyzer": "my_analyzer",
  "text": "The 2 QUICK Brown-Foxes jumped over the lazy dog's bone."
}
```

&emsp;&emsp;上面的例子会生成下面的term：

```text
[ The, 2, QUICK, Brown, Foxes, jumpe, d, over, the, lazy, dog's, bone ]
```

#### Whitespace tokenizer
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-whitespace-tokenizer.html)

&emsp;&emsp;`whitespace` tokenizer会在根据空格将文本划分出term。

##### Example output

```text
 POST _analyze
{
  "tokenizer": "whitespace",
  "text": "The 2 QUICK Brown-Foxes jumped over the lazy dog's bone."
}
````

&emsp;&emsp;上面的句子将生成下面的term：

```text
[ The, 2, QUICK, Brown-Foxes, jumped, over, the, lazy, dog's, bone. ]
```

##### Configuration

&emsp;&emsp;`whitespace` tokenizer接收下面的参数：

###### max_token_length

&emsp;&emsp;token的最大长度。如果一个token的长度超过了`max_token_length`，会按照`max_token_length`长度进行划分。默认值是`255`。

### Token filter reference
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-tokenfilters.html)

#### ASCII folding token filter
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-asciifolding-tokenfilter.html)


#### Conditional token filter
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-condition-tokenfilter.html)

#### Hunspell token filter
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-hunspell-tokenfilter.html)

#### Keyword marker token filter
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-keyword-marker-tokenfilter.html)

#### KStem token filter
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-kstem-tokenfilter.html)

#### Lowercase token filter
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-lowercase-tokenfilter.html)

&emsp;&emsp;将token text都转为小写的token。例如你可以使用`lowercase` filter将`THE Lazy DoG`转化为`the lazy dog`。

&emsp;&emsp;除了默认的filter，`lowercase` token filter还提供了访问Lucene中为Greek, Irish, and Turkish特别定制的lowercase filter。

##### Example

&emsp;&emsp;下面的[analyze API](####Analyze API)使用默认的`Lowercase` filter 将`THE Quick FoX JUMPs`转化为小写。

```text
GET _analyze
{
  "tokenizer" : "standard",
  "filter" : ["lowercase"],
  "text" : "THE Quick FoX JUMPs"
}
```

&emsp;&emsp;这个filter产生下面的token：

```text
[ the, quick, fox, jumps ]
```

##### Add to an analyzer

&emsp;&emsp;下面的[create index API ](####Analyze API)配置了一个custom analyzer，它使用了`lowercase` filter：

```text
PUT lowercase_example
{
  "settings": {
    "analysis": {
      "analyzer": {
        "whitespace_lowercase": {
          "tokenizer": "whitespace",
          "filter": [ "lowercase" ]
        }
      }
    }
  }
}
```

##### Configurable parameters

###### language

&emsp;&emsp;（optional）特定的一些语言对应的lowercase token filter。可选值为：

- greek：使用Lucene中的[GreekLowerCaseFilter](https://lucene.apache.org/core/9_1_0/analysis/common/org/apache/lucene/analysis/el/GreekLowerCaseFilter.html)
- irish：使用Lucene中的[IrishLowerCaseFilter](https://lucene.apache.org/core/9_1_0/analysis/common/org/apache/lucene/analysis/ga/IrishLowerCaseFilter.html)
- turkish：使用Lucene中的[TurkishLowerCaseFilter](https://lucene.apache.org/core/9_1_0/analysis/common/org/apache/lucene/analysis/tr/TurkishLowerCaseFilter.html)

&emsp;&emsp;如果不指定该参数，则默认使用[LowerCaseFilter](https://lucene.apache.org/core/9_1_0/analysis/common/org/apache/lucene/analysis/core/LowerCaseFilter.html)。

##### Customize

&emsp;&emsp;若要自定义`lowercase` filter，为新的自定义的token filter创建所需要的基本要素（basis）。

&emsp;&emsp;下面的例子中自定义了用于Greek语言的`lowercase` filter：

```text
PUT custom_lowercase_example
{
  "settings": {
    "analysis": {
      "analyzer": {
        "greek_lowercase_example": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": ["greek_lowercase"]
        }
      },
      "filter": {
        "greek_lowercase": {
          "type": "lowercase",
          "language": "greek"
        }
      }
    }
  }
}
```

#### Porter stem token filter
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-porterstem-tokenfilter.html)

#### Shingle token filter
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-shingle-tokenfilter.html)

#### Snowball token filter
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-snowball-tokenfilter.html)

#### Stemmer token filter
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-stemmer-tokenfilter.html)

#### Stemmer override token filter
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-stemmer-override-tokenfilter.html)

#### Stop token filter
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-stop-tokenfilter.html)

&emsp;&emsp;从token stream中移除[stop words](https://en.wikipedia.org/wiki/Stop_word)。

&emsp;&emsp;如果没有自定义停用词，会默认移除下列的英文单词：

```text
a, an, and, are, as, at, be, but, by, for, if, in, into, is, it, no, not, of, on, or, such, that, the, their, then, there, these, they, this, to, was, will, with
```

&emsp;&emsp;除了英语的停用词，`stop` filter支持预先定义好的[stop word lists for several languages](#####Stop words by language)。你也可以用数组或者文件来定义属于你的停用词。

&emsp;&emsp;`stop` filter使用的是Lucene的[StopFilter](https://lucene.apache.org/core/9_1_0/analysis/common/org/apache/lucene/analysis/core/StopFilter.html)。

##### Example

&emsp;&emsp;下面的analyzer API请求中使用`stop` filter将移除`a quick fox jumps over the lazy dog`中的`a`和`the`：

```text
GET /_analyze
{
  "tokenizer": "standard",
  "filter": [ "stop" ],
  "text": "a quick fox jumps over the lazy dog"
}
```

&emsp;&emsp;这个filter会生成下面的token：

```text
[ quick, fox, jumps, over, lazy, dog ]
```

##### Add to an analyzer

&emsp;&emsp;下面的[create index API](####Create index API)请求使用`stop` filter来配置一个新的自定义的[custom analyzer](####Create a custom analyzer)：

```text
PUT /my-index-000001
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_analyzer": {
          "tokenizer": "whitespace",
          "filter": [ "stop" ]
        }
      }
    }
  }
}
```

##### Configurable parameters

###### stopwords

&emsp;&emsp;（Optional, string or array of strings）。用于指定语言类型，例如`_arabic_`或者`_thai_`。默认值是[\_english\_](######_english_)。

&emsp;&emsp;每一种语言对应一个预设的停用词列表，在Lucene中提供。见[Stop words by language](#####Stop words by language)查看支持的语言以及其停用词。

&emsp;&emsp;也接受停用词数组。

&emsp;&emsp;若要使用空的停用词，使用`_none_`。

###### stopwords_path

&emsp;&emsp;（Optional, string) 文件路径，该文件中包含待移除的停用词。

&emsp;&emsp;这个路径必须是绝对路径或者是`config`下的相对路径。文件的字符编码必须是UTF-8。文件中每一个停用词必须用换行符分隔。

###### ignore_case

&emsp;&emsp;（Optional, Boolean) 如果为`true`，停用词的匹配是大小写不敏感的。例如，如果该参数为`true`，那么`the`这个停用词会会移除`the`，`THE`，或者`The`。默认值是`false`。

###### remove_trailing

&emsp;&emsp;（Optional, Boolean) 如果为`true`，并且token stream的最后一个token是停用词，则移除这个token。默认值是`true`。

&emsp;&emsp;当使用[completion suggester](#####Completion Suggester)时，这个参数应该要设置为`false`。这样能保证`green a`这个query能匹配并且建议出`green apple`并且仍然能够移除其他的停用词。

##### Customize

&emsp;&emsp;要自定义`stop` filter，需要创建一个新的custom token filter所需要的基本要素。你可以使用可配置的参数来更改filter。

&emsp;&emsp;例如，下面的请求中创建了一个自定义的大小写不敏感的`stop` filter。这个filter会移除[\_english\_](######\_english\_)中的停用词。

```text
PUT /my-index-000001
{
  "settings": {
    "analysis": {
      "analyzer": {
        "default": {
          "tokenizer": "whitespace",
          "filter": [ "my_custom_stop_words_filter" ]
        }
      },
      "filter": {
        "my_custom_stop_words_filter": {
          "type": "stop",
          "ignore_case": true
        }
      }
    }
  }
}
```

&emsp;&emsp;你也可以指定自定义的停用词。例如下面的请求中创建了一个自定义的大小写敏感的`stop` filter，它只移除`and`，`is`和`the`：

```text
PUT /my-index-000001
{
  "settings": {
    "analysis": {
      "analyzer": {
        "default": {
          "tokenizer": "whitespace",
          "filter": [ "my_custom_stop_words_filter" ]
        }
      },
      "filter": {
        "my_custom_stop_words_filter": {
          "type": "stop",
          "ignore_case": true,
          "stopwords": [ "and", "is", "the" ]
        }
      }
    }
  }
}
```

##### Stop words by language

&emsp;&emsp;下列是参数[stopwords](######stopwords)支持的参数值。并且链接指向了Lucene中每一种语言对应的停用词：

###### \_arabic\_
[Arabic stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/ar/stopwords.txt)

###### \_armenian\_
[Armenian stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/hy/stopwords.txt)

###### \_basque\_
[Basque stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/eu/stopwords.txt)

###### \_bengali\_
[Bengali stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/bn/stopwords.txt)

###### \_brazilian\_  (Brazilian Portuguese)
[Brazilian Portuguese stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/br/stopwords.txt)

###### \_bulgarian\_
[Bulgarian stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/bg/stopwords.txt)

###### \_catalan\_
[Catalan stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/ca/stopwords.txt)

###### \_cjk\_  (Chinese, Japanese, and Korean)
[CJK stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/cjk/stopwords.txt)

###### \_czech\_
[Czech stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/cz/stopwords.txt)

###### \_danish\_
[Danish stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/snowball/danish_stop.txt)

###### \_dutch\_
[Dutch stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/snowball/dutch_stop.txt)

###### \_english\_
[English stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/java/org/apache/lucene/analysis/en/EnglishAnalyzer.java#L48)

###### \_estonian\_
[Estonian stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/et/stopwords.txt)

###### \_finnish\_
[Finnish stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/snowball/finnish_stop.txt)

###### \_french\_
[French stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/snowball/french_stop.txt)

###### \_galician\_
[Galician stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/gl/stopwords.txt)

###### \_german\_
[German stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/snowball/german_stop.txt)

###### \_greek\_
[Greek stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/el/stopwords.txt)

###### \_hindi\_
[Hindi stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/hi/stopwords.txt)

###### \_hungarian\_
[Hungarian stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/snowball/hungarian_stop.txt)

###### \_indonesian\_
[Indonesian stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/id/stopwords.txt)

###### \_irish\_
[Irish stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/ga/stopwords.txt)

###### \_italian\_
[Italian stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/snowball/italian_stop.txt)

###### \_latvian\_
[Latvian stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/lv/stopwords.txt)

###### \_lithuanian\_
[Lithuanian stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/lt/stopwords.txt)

###### \_norwegian\_
[Norwegian stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/snowball/norwegian_stop.txt)

###### \_persian\_
[Persian stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/fa/stopwords.txt)

###### \_portuguese\_
[Portuguese stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/snowball/portuguese_stop.txt)

###### \_romanian\_
[Romanian stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/ro/stopwords.txt)

###### \_russian\_
[Russian stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/snowball/russian_stop.txt)

###### \_sorani\_
[Sorani stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/ckb/stopwords.txt)

###### \_spanish\_
[Spanish stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/snowball/spanish_stop.txt)

###### \_swedish\_
[Swedish stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/snowball/swedish_stop.txt)

######  \_thai\_
[Thai stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/th/stopwords.txt)

###### \_turkish\_
[Turkish stop words](https://github.com/apache/lucene/blob/main/lucene/analysis/common/src/resources/org/apache/lucene/analysis/tr/stopwords.txt)

#### Synonym token filter
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-synonym-tokenfilter.html)

#### Synonym graph token filter
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-synonym-graph-tokenfilter.html)

#### Word delimiter token filter
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-word-delimiter-graph-tokenfilter.html)

#### Word delimiter graph token filter
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-word-delimiter-graph-tokenfilter.html)

### Character filters reference
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-charfilters.html)

#### HTML strip character filter
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-htmlstrip-charfilter.html)

#### Mapping character filter
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-mapping-charfilter.html)

&emsp;&emsp;`mapping` character filter接收一个key，value的map。在character stream中遇到map中的key时，则将其替换为map中对应的value。

&emsp;&emsp;这是一种greedy的匹配。按照最长模板进行匹配。替换值（replacement）可以是一个空的string。

&emsp;&emsp;`mapping` filter 使用了 Lucene中的[MappingCharFilter](https://lucene.apache.org/core/9_1_0/analysis/common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)。

##### Example

&emsp;&emsp;下面的[analyze API](####Analyze API)使用了`mapping` filter 将Hindu-Arabic numerals (٠١٢٣٤٥٦٧٨٩) 转化为Arabic-Latin equivalents (0123456789)。将文本内容`My license plate is ٢٥٠١٥`转化为了`My license plate is 25015`。

```text
GET /_analyze
{
  "tokenizer": "keyword",
  "char_filter": [
    {
      "type": "mapping",
      "mappings": [
        "٠ => 0",
        "١ => 1",
        "٢ => 2",
        "٣ => 3",
        "٤ => 4",
        "٥ => 5",
        "٦ => 6",
        "٧ => 7",
        "٨ => 8",
        "٩ => 9"
      ]
    }
  ],
  "text": "My license plate is ٢٥٠١٥"
}
```

&emsp;&emsp;上面的filter会生成下面的文本：

```text
[ My license plate is 25015 ]
```

##### Configurable parameters

###### mappings

&emsp;&emsp;(Required\*, array of strings) 数组类型的mappings，包含了key到value的映射。

&emsp;&emsp;必须指定这个参数或者`mappings_path`中的一个。

###### mappings_path

&emsp;&emsp;(Required\*, string) 文件的路径，该文件中包含了key到value的映射。

&emsp;&emsp;该路径必须是绝对路径或者是相对于`config`的相对路径。文件中的字符编码必须是UTF-8。文件中每一个key到value必须用换行符区分。

&emsp;&emsp;必须指定这个参数或者`mappings`中的一个。

##### Customize and add to an analyzer

&emsp;&emsp;要自定义`mappings` filter，创建一个自定义的character filter所需要的基本要素，你可以使用可配置的参数来修改它。

&emsp;&emsp;下面的[create index API](####Create index API)请求配置了一个新的[custom analyzer](####Create a custom analyzer)，它使用了一个自定义的`mappings` filter，`my_mappings_char_filter`。

&emsp;&emsp;`my_mappings_char_filter` filter将表情符号（emoticons）`:)，:(`替换成相同意思的文本：

```text
PUT /my-index-000001
{
  "settings": {
    "analysis": {
      "analyzer": {
        "my_analyzer": {
          "tokenizer": "standard",
          "char_filter": [
            "my_mappings_char_filter"
          ]
        }
      },
      "char_filter": {
        "my_mappings_char_filter": {
          "type": "mapping",
          "mappings": [
            ":) => _happy_",
            ":( => _sad_"
          ]
        }
      }
    }
  }
}
```

&emsp;&emsp;上面的[analyze API](####Analyze API)请求使用自定义的`my_mappings_char_filter`将文本`I'm delighted about it :(`中的`:(`替换成了`_sad_`。

```text
GET /my-index-000001/_analyze
{
  "tokenizer": "keyword",
  "char_filter": [ "my_mappings_char_filter" ],
  "text": "I'm delighted about it :("
}
```

&emsp;&emsp;这个filter会生成下面的文本：

```text
[ I'm delighted about it _sad_ ]
```


### Normalizers
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/analysis-normalizers.html)

&emsp;&emsp;Normalizers跟分词器类似，区别在于它只输出单个token。因此，它没有[tokenizer](###Tokenizer reference)并且只接收一些有效的character filter和token filter。只有对单个字符处理的filter是允许在Normalizers中使用的。例如lowercase filter是允许的，而stemming filter则不允许，因为它需要将关键字作为一个整体来处理。下面的filter可以在Normalizers中使用：`arabic_normalization`, `asciifolding`, `bengali_normalization`, `cjk_width`, `decimal_digit`, `elision`, `german_normalization`, `hindi_normalization`, `indic_normalization`, `lowercase`, `persian_normalization`, `scandinavian_folding`, `serbian_normalization`, `sorani_normalization`, `uppercase`。

&emsp;&emsp;Elasticsearch使用`lowercase`用于内置的normalizers。其他形式的normalizers要求进行配置。

#### Custom normalizers

&emsp;&emsp;自定义的normalizers可以定义[character filter](###Character filters reference)和[token filter](###Token filter reference)的集合。

```text
PUT index
{
  "settings": {
    "analysis": {
      "char_filter": {
        "quote": {
          "type": "mapping",
          "mappings": [
            "« => \"",
            "» => \""
          ]
        }
      },
      "normalizer": {
        "my_normalizer": {
          "type": "custom",
          "char_filter": ["quote"],
          "filter": ["lowercase", "asciifolding"]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "foo": {
        "type": "keyword",
        "normalizer": "my_normalizer"
      }
    }
  }
}
```

## Index templates
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-templates.html)

>这个主题介绍了在Elasticsearch 7.8引入的composable index template。关于之前版本的index template是如何工作的见[legacy template documentation](####Create or update index template API)。
>

&emsp;&emsp;在创建一个索引时，index template告诉Elasticsearch如何进行创建。对于[data streams](##Data streams)，index template用于创建流的[backing](####Backing indices)索引。Template先于索引的创建。手动或者通过索引一篇文档创建一个索引后，template setting会作为创建索引的一个基本要素（ basis）。

&emsp;&emsp;一共有index template和[component template](####Create or update component template API)两种类型的模板。component template构建配置了mapping，settings，alias这几块并可以复用。你可以使用一个或多个component template来构造index template，但不能直接作用到（apply）索引集合（a set of indices）。index templates 可以包含一个component template集合，直接指定settings，mappings和aliases。

&emsp;&emsp;应用（apply）index template的几个条件：

- composable template比legacy template优先级高。如果composable template没有匹配到索引，legacy template可能会匹配到并且应用。
- 如果使用显示的设置（explicit settings）来创建索引并且同时匹配到了一个index template。那么在[create index](####Create index API)请求中的settings比index template和component template中的优先级高。
- 如果一个新的data stream或者索引匹配到了多个index template，则使用优先级最高的index template

##### Avoid index pattern collisions

&emsp;&emsp; Elasticsearch有内置的index template，每一个的优先级都是`100`，如下：

- logs-*-*
- metrics-*-*
- synthetics-*-*

&emsp;&emsp;[Elastic Agent](https://www.elastic.co/guide/en/fleet/8.2/fleet-overview.html)使用这些模板来创建data streams。Fleet integration创建的index template使用类似的overlapping index patterns并且优先级是`200`。

&emsp;&emsp;如果你使用Fleet或者Elastic Agent， 那么你的index template的优先级要小于`100`来防止覆盖掉这些模板。若要避免意外的作用了这些内置模板，采取下面一个或者多个方法：

- 若要关闭内置的index template和component template，使用[cluster update settings API](####Cluster update settings API)将[stack.templates.enabled](#####stack.templates.enabled)设置为`false`。
- 使用一个不会被 覆盖的index pattern
- 给overlapping pattern的模板一个`priority`大于`200`的值。例如如果你不想要使用Fleet或者Elastic Agent并且想要为`logs-*`这个index pattern创建一个模板，那么将你的模板的priority的值设置为`500`。这能保证你的模板能被应用于`logs-*`而不是使用内置的模板。

#### Create index template

&emsp;&emsp;使用[index template](####Create or update index template API) 和[put component template](####Create or update component template API)APIs来创建和更新index templates。你也可以在Kibana用意Stack Management来[manage index templates](###Index management in Kibana)。

&emsp;&emsp;下面的请求创建了两个component templates。

```text
PUT _component_template/component_template1
{
  "template": {
    "mappings": {
      "properties": {
        "@timestamp": {
          "type": "date"
        }
      }
    }
  }
}

PUT _component_template/runtime_component_template
{
  "template": {
    "mappings": {
      "runtime": { 
        "day_of_week": {
          "type": "keyword",
          "script": {
            "source": "emit(doc['@timestamp'].value.dayOfWeekEnum.getDisplayName(TextStyle.FULL, Locale.ROOT))"
          }
        }
      }
    }
  }
}
```

&emsp;&emsp;第18行，这个component template添加了一个名为`day_of_week`的[runtime field](####Map a runtime field)的域，用于一个新的索引匹配到模板时。

&emsp;&emsp;下面的请求创建了一个由上面的component template组成的index template。

```text
PUT _index_template/template_1
{
  "index_patterns": ["te*", "bar*"],
  "template": {
    "settings": {
      "number_of_shards": 1
    },
    "mappings": {
      "_source": {
        "enabled": true
      },
      "properties": {
        "host_name": {
          "type": "keyword"
        },
        "created_at": {
          "type": "date",
          "format": "EEE MMM dd HH:mm:ss Z yyyy"
        }
      }
    },
    "aliases": {
      "mydata": { }
    }
  },
  "priority": 500,
  "composed_of": ["component_template1", "runtime_component_template"], 
  "version": 3,
  "_meta": {
    "description": "my custom"
  }
}
```

### Simulate multi-component templates
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/simulate-multi-component-templates.html)

&emsp;&emsp;模板不仅仅由多个component template进行组合，也可以由index template自身。有两个simulation APIs用来查看最终的索引设置（index settings）。（当你指定了一个新的模板后，由于现有的模板可能跟这个新的模板有相同的index patter，由于优先级问题导致新的模板无法作用到新创建的索引，那么可以通过simulate来查看某个索引对应的index template，或者查看某个模板设置，见[Simulate index template API](####Simulate index template API)和[ Simulate index API](####Simulate index API)）

&emsp;&emsp;若要模拟（[simulate](####Simulate index API)）出将要应用到某个指定的索引的索引设置，可以通过以下请求获取。 

```text
POST /_index_template/_simulate_index/my-index-000001
```

&emsp;&emsp;若要模拟（[simulate](####Simulate index template API)）出某个模板的模板设置，可以通过以下请求获取。

```text
POST /_index_template/_simulate/template_1
```
&emsp;&emsp;你也可以在simulate 请求body中指定一个模板定义（新的模板设置）。这可以让你在添加一个新的模板前先校验下settings。（校验的目的之一是查看新的模板会不会被现有的模板覆盖或者覆盖现有的模板）

```text
PUT /_component_template/ct1
{
  "template": {
    "settings": {
      "index.number_of_shards": 2
    }
  }
}

PUT /_component_template/ct2
{
  "template": {
    "settings": {
      "index.number_of_replicas": 0
    },
    "mappings": {
      "properties": {
        "@timestamp": {
          "type": "date"
        }
      }
    }
  }
}

POST /_index_template/_simulate
{
  "index_patterns": ["my*"],
  "template": {
    "settings" : {
        "index.number_of_shards" : 3
    }
  },
  "composed_of": ["ct1", "ct2"]
}
```

&emsp;&emsp;下面的响应中展示了将会应用到匹配到的索引配置中的settings，mappings，以及aliases。任何被覆盖的模板中的配置会被simulate 请求中指定模板定义或者优先级更高的现有的模板取代。

```text
{
  "template" : {
    "settings" : {
      "index" : {
        "number_of_shards" : "3",   
        "number_of_replicas" : "0",
        "routing" : {
          "allocation" : {
            "include" : {
              "_tier_preference" : "data_content"
            }
          }
        }
      }
    },
    "mappings" : {
      "properties" : {
        "@timestamp" : {
          "type" : "date"           
        }
      }
    },
    "aliases" : { }
  },
  "overlapping" : [
    {
      "name" : "template_1",        
      "index_patterns" : [
        "my*"
      ]
    }
  ]
}
```

&emsp;&emsp;第5行，分片的数量来自simulate请求中的值，替代了index template`ct1`中的值

&emsp;&emsp;第19行，`@timestap`域的定义来自index template`ct2`

&emsp;&emsp;第 27行，被覆盖的模板信息，它们有较低的优先级



## Data streams
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl.html)

&emsp;&emsp;数据流（data stream）允许你跨多个索引append-only方式存储时间序列数据，同时为你提供单个命名资源用于请求。Data stream非常适合用于logs、events、metrics以及其他源源不断生成的数据。

&emsp;&emsp;你可以直接向一个数据流提交索引和查询请求。数据流可以自动的将请求路由到存储流数据（stream's data）的[backing indices](####Backing indices)中。你可以使用[index lifecycle management(ILM)](###ILM: Manage the index lifecycle)实现backing indeices的自动化管理。例如，你可以使用ILM自动将较老的backing indices移到成本更低的硬件上以及删除不再需要的数据。ILM可以帮助你降低随着不断增长的数据带来的开销和成本。

#### Backing indices

&emsp;&emsp;一个数据流由一个或者多个[hidden](#####index.hidden)、自动生成的backing indices组成。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/data-streams-diagram.svg">

&emsp;&emsp;一个数据流要求有一个匹配的index template（[index template](##Index templates)）。这个模板包含了用于配置流的backing indices的mappings和settings。

&emsp;&emsp;每一个索引到数据流中的文档必须包含一个`@timestamp`字段，映射为[date](####Date field type)或者[date_nanos](####Date nanoseconds field type)域类型。如果index template没有指定`@timestamp`域。Elasticsearch会根据默认选项映射一个`date`类型的`@timestmap`字段。

&emsp;&emsp;同一个index template可以用于多个数据流。你不能删除一个正在被某个数据流使用的index template。

#### Read requests

&emsp;&emsp;当你向一个数据流提交一个读请求时，数据流会将这个请求路由到所有的backing indice上。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/data-streams-search-request.svg">

#### Write index

&emsp;&emsp;最近创建的backing index是数据流的write index。数据流只向这个索引中增加文档。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/data-streams-index-request.svg">

&emsp;&emsp;你不能往其他backing indices中添加新的文档，甚至不能直接向这些索引中发送请求。

&emsp;&emsp;你同样不能在write index上执行下面的操作，免得阻碍（hinder）索引写入：

- [Clone](####Clone index API)
- [Delete](####Delete index API)
- [Shrink](####Shrink index API)
- [Split](####Split index API)

#### Rollover

&emsp;&emsp;[rollover](####Rollover API)操作会创建一个新的backing index，这个索引成为数据流中新的write index。

&emsp;&emsp;我们建议使用[ILM](###ILM: Manage the index lifecycle)，它能在write index达到一个指定的寿命（age）或者大小后自动的转存（roll over）数据流。如果有必要的话，你也可以[manually roll over](####Manually roll over a data stream)一个数据流。

#### Generation

&emsp;&emsp;每一个数据流会追踪它的generation：一个六位数字，0作为填充值的整数，用来累计（cumulative）数据流转存次数，从`000001`开始。

&emsp;&emsp;当创建一个backing index后，这个索引按下面的方式约定命名

```text
.ds-<data-stream>-<yyyy.MM.dd>-<generation>
```

&emsp;&emsp;`<yyyy.MM.dd> `是backing index的创建时间。generation数值越大的backing index包含更多最近的数据。例如，`web-server-logs`这个数据流的generation的值为`34`。这个流在2099年3月7日创建，那么就命名为`.ds-web-server-logs-2099.03.07-000034`。

&emsp;&emsp;有些[shink](####Shrink index API)或者[restore](###Restore a snapshot)操作会更改backing index的名字。这些名字的变更不会数据流中移除backing index。

#### Append-only

&emsp;&emsp;数据流被设计为用于现有数据很少或者从不更新的场景。你不能向数据流中现有的文档发送更新或者删除的请求。与之替换使用[update by query](####Update documents in a data stream by query)和[delete by query](####Delete documents in a data stream by query) APIs。

&emsp;&emsp;如果有必要的话，你可以使用[update or delete documents ](####Update or delete documents in a backing index)向文档的backing index中提交请求。

> TIP：如果你要频繁的更新或删除时序数据，使用write index的index alias来替换数据流。见[Manage time series data without data streams](####Manage time series data without data streams)。

### Set up a data stream 
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/set-up-a-data-stream.html#set-up-a-data-stream)

&emsp;&emsp;按照下面几个步骤来设置一个数据流

1. [Create an index lifecycle policy](####Create an index lifecycle policy)
2. [Create component templates](####Create component templates(data stream))
3. [Create an index template](####Create an index template(data stream))
4. [Create the data stream](####Create the data stream(data stream))
5. [Secure the data stream](####Secure the data stream)

&emsp;&emsp;你也可以[convert an index alias to a data stream](####Convert an index alias to a data stream)。

>IMPORTANT：如果你使用Fleet或者Elastic Agent，可以跳过这个教程。Fleet和Elastic Agent会为你设置数据流。见Fleet的[data streams](https://www.elastic.co/guide/en/fleet/8.2/data-streams.html)文档。

#### Create an index lifecycle policy

&emsp;&emsp;尽管是可选的，我们建议你使用ILM来实现数据流的backing  indice的管理自动化。ILM需要一个索引生命周期策略。

&emsp;&emsp;在Kibana中创建一个索引生命周期策略，打开主菜单然后选择**Stack Management > Index Lifecycle Policies**，点击**Create policy**。

&emsp;&emsp;你也可以通过[create lifecycle policy API](####Create or update lifecycle policy API)创建。

```json
PUT _ilm/policy/my-lifecycle-policy
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover": {
            "max_primary_shard_size": "50gb"
          }
        }
      },
      "warm": {
        "min_age": "30d",
        "actions": {
          "shrink": {
            "number_of_shards": 1
          },
          "forcemerge": {
            "max_num_segments": 1
          }
        }
      },
      "cold": {
        "min_age": "60d",
        "actions": {
          "searchable_snapshot": {
            "snapshot_repository": "found-snapshots"
          }
        }
      },
      "frozen": {
        "min_age": "90d",
        "actions": {
          "searchable_snapshot": {
            "snapshot_repository": "found-snapshots"
          }
        }
      },
      "delete": {
        "min_age": "735d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}
```

#### Create component templates(data stream)

#### Create an index template(data stream)

#### Create the data stream(data stream)

#### Secure the data stream

#### Convert an index alias to a data stream

#### Get information about a data stream

#### Delete a data stream


### Use a data stream
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/use-a-data-stream.html#manually-roll-over-a-data-stream)

#### Manually roll over a data stream

#### Update documents in a data stream by query

#### Delete documents in a data stream by query

#### Update or delete documents in a backing index

### Change mappings and settings for a data stream
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/data-streams-change-mappings-and-settings.html)

&emsp;&emsp;每一个data steam有一个[matching index template](####Create an index template(data stream))。模板中的mappings和index settings会应用到这个stream创建出来的新的backing index。也同时包括stream自动创建出来的的第一个backing index。

&emsp;&emsp;在创建一个data stream之前，我们建议你考虑好模板中的mappings和settings。

&emsp;&emsp;如果你随后需要更改某个data stream的mappings或settings，你有以下的选项：

- [Add a new field mapping to a data stream](####Add a new field mapping to a data stream)
- [Change an existing field mapping in a data stream](####Change an existing field mapping in a data stream)
- [Change a dynamic index setting for a data stream](####Change a dynamic index setting for a data stream)
- [Change a static index setting for a data stream](####Change a static index setting for a data stream)

> TIP：如果你的更改中包含对现有的field mappings或[static index settings](##Index modules)做更改，通常需要reindex来作用到data stream的backing index 中。如果你已经执行了reindex，你可以使用相同的过程来添加新的field mappings以及更改[dynamic index settings](##Index modules)。见[Use reindex to change mappings or settings](####Use reindex to change mappings or settings)。

#### Add a new field mapping to a data stream

&emsp;&emsp;若要向data stream中为一个新的域添加一个mapping，按照下面的步骤执行：

1. 更新data steam使用的index template。保证新的field mapping添加到这个stream以后创建的backing index中。

&emsp;&emsp;例如，名为`my-data-stream`的data stream现在使用的是名为`my-data-steram-template`的模板。

&emsp;&emsp;下面的[create or update index template](##Index templates)请求向模板中为新的域`message`添加了一个mapping。

```text
PUT /_index_template/my-data-stream-template
{
  "index_patterns": [ "my-data-stream*" ],
  "data_stream": { },
  "priority": 500,
  "template": {
    "mappings": {
      "properties": {
        "message": {                              
          "type": "text"
        }
      }
    }
  }
}
```

&emsp;&emsp;第7行，为新的域`message`添加一个mapping。

2. 使用[update mapping API](####Update mapping API)添加新的域的mapping到data stream中。默认情况下会添加到stream中现有的backing index中，包括write index。

&emsp;&emsp;下面的更新mapping API请求将新的域`message`添加到名为`my-data-stream`的data stream中。

```text
PUT /my-data-stream/_mapping
{
  "properties": {
    "message": {
      "type": "text"
    }
  }
}
```

&emsp;&emsp;若只向stream中的write index添加mapping，在更新mapping API中添加请求参数`write_index_only`为`true`。

&emsp;&emsp;下面的更新mapping请求中将新的域`message`的mapping只添加到write index中。没有添加到这个stream的其他backing index中。

```text
PUT /my-data-stream/_mapping?write_index_only=true
{
  "properties": {
    "message": {
      "type": "text"
    }
  }
}
```

#### Change an existing field mapping in a data stream

&emsp;&emsp;每一个[mapping parameter](###Mapping parameters)的文档中都会告知是否可以使用[update mapping API](####Update mapping API)更新现有的域。若要为已有的域更新这些参数，按照下面的步骤执行：

1. 更新data steam使用的index template。保证新的field mapping添加到这个stream以后创建的backing index中。

&emsp;&emsp;例如，名为`my-data-stream`的data stream现在使用的是名为`my-data-steram-template`的模板。

&emsp;&emsp;下面的[create or update index template](##Index templates)请求为`host.ip`修改了参数。域的[ignore_malformed](####ignore_malformed)的值设置为`true`。

```text
PUT /_index_template/my-data-stream-template
{
  "index_patterns": [ "my-data-stream*" ],
  "data_stream": { },
  "priority": 500,
  "template": {
    "mappings": {
      "properties": {
        "host": {
          "properties": {
            "ip": {
              "type": "ip",
              "ignore_malformed": true            
            }
          }
        }
      }
    }
  }
}
```

&emsp;&emsp;第13行，修改`host.ip`的`ignore_malformed`的值为`true`。

2. 使用[update mapping API](####Update mapping API)添加新的域的mapping到data stream中。默认情况下会添加到stream中现有的backing index中，包括write index。

&emsp;&emsp;下面的[update mapping API](####Update mapping API)请求目标是`my-data-stream`。该请求将`host.ip`域的`ignore_malformed`的值设置为`true`。

```text
PUT /my-data-stream/_mapping
{
  "properties": {
    "host": {
      "properties": {
        "ip": {
          "type": "ip",
          "ignore_malformed": true
        }
      }
    }
  }
}
```

&emsp;&emsp;若只向stream中的write index添加mapping，在更新mapping API中添加请求参数`write_index_only`为`true`。

&emsp;&emsp;下面的更新mapping请求只对`my-data-stream`的write index中的`host.ip`域变更。这个更改不会作用到stream的其他backing index。

```text
PUT /my-data-stream/_mapping?write_index_only=true
{
  "properties": {
    "host": {
      "properties": {
        "ip": {
          "type": "ip",
          "ignore_malformed": true
        }
      }
    }
  }
}
```

&emsp;&emsp;除了支持更新mapping参数，我们不建议更改现有域的mapping或者类型，即使是在data stream匹配到的index template或者它的backing index中。更改现有域的mapping会invalidate已经索引的数据。

&emsp;&emsp;如果你需要修改现有域的mapping，那么创建一个新的data stream然后reindex。见[Use reindex to change mappings or settings](####Use reindex to change mappings or settings)。

#### Change a dynamic index setting for a data stream

&emsp;&emsp;若要为data stream更改[dynamic index template](###Index Settings)，按照下面的步骤执行：

1. 更新data steam使用的index template。保证setting能应用到到这个stream以后创建的backing index中。

&emsp;&emsp;例如，名为`my-data-stream`的data stream现在使用的是名为`my-data-steram-template`的模板。

&emsp;&emsp;下面的[create or update index template](##Index templates)请求将模板的`index.refresh_interval`设置为`30s`（30秒）。

```text
PUT /_index_template/my-data-stream-template
{
  "index_patterns": [ "my-data-stream*" ],
  "data_stream": { },
  "priority": 500,
  "template": {
    "settings": {
      "index.refresh_interval": "30s"             
    }
  }
}
```

第8行，将`index.refresh_interval`设置为`30s`。

2. 使用[update mapping API](####Update mapping API)更新data stream的index setting。默认情况下会添加到stream中现有的backing index中，包括write index。

&emsp;&emsp;下面的更新index settings API请求为`my-data-stream`更新设置`index.refresh_interval`。

```text
PUT /my-data-stream/_settings
{
  "index": {
    "refresh_interval": "30s"
  }
}
```

> IMPORTANT：若要修改`index.lifecycle.name`，首先使用[remove policy API](####Remove policy from index API)移除现有的ILM策略。见[Switch lifecycle policies](#####Switch lifecycle policies)。


#### Change a static index setting for a data stream

&emsp;&emsp;只有在backing index创建时才能设置[Static index settings](##Index modules)。你不能使用[update index settings API](####Update index settings API)更新static index settings。

&emsp;&emsp;若要将新的static setting应用到以后的backing index上，那么更新data steam使用的index template。更新之后会自动的应用到新创建的backing index上。

&emsp;&emsp;例如，`my-data-stream-template`是一个`my-data-stream`使用的现有的index template。

&emsp;&emsp;下面的[create or update index template API](##Index templates)请求向模板中添加了新的`sort.field`和`sort.order`两个设置。

```text
PUT /_index_template/my-data-stream-template
{
  "index_patterns": [ "my-data-stream*" ],
  "data_stream": { },
  "priority": 500,
  "template": {
    "settings": {
      "sort.field": [ "@timestamp"],             
      "sort.order": [ "desc"]                    
    }
  }
}
```

&emsp;&emsp;第8行， 增加index setting `sort.field`

&emsp;&emsp;第9行，增加index setting `sort.order`

&emsp;&emsp;如果需要的话，你可以[roll over the data stream](###Use a data stream)来马上将设置应用到data stream的write index上。应用到在rollover之后新添加到stream的数据上。然而不会影响data stream中现有的索引和已有的数据上。

&emsp;&emsp;若要应用static settings changes到现有的backing index上，你必须创建一个新的data stream然后reindex。见[Use reindex to change mappings or settings](####Use reindex to change mappings or settings)。

#### Use reindex to change mappings or settings

&emsp;&emsp;你可以使用reindex修改data stream的mapping或settings。这么做通常是因为要对已有域的类型做变更或者要为backing index更新static index settings。

&emsp;&emsp;若要reindex某个data stream。首先创建或者更新index template，这样才能包含你想要的修改后的mapping或settings。你随后就可以将现有的数据reindex到一个新的匹配到模板的data stream中。模板中变更的mapping和setting都会应用到添加到新的data stream中的每一篇文档和backing index。

&emsp;&emsp;按照下面的步骤执行：

1. 为新的data stream选择一个名字或者index pattern。这个新的data stream会包含你现有的stream中的数据。

&emsp;&emsp;你可以使用[ resolve index API](####Resolve index API)检查下 名字或者pattern会不会匹配到现有的索引，aliases或者data stream。如果有，你应该考虑使用其他的名字或者pattern。

&emsp;&emsp;下面的resolve index API请求会检查现有的索引，aliases或者data stream有没有以`new-data-stream`开头的。如果没有，index pattern `new-data-stream*` 可以用于创建新的data stream。

```text
GET /_resolve/index/new-data-stream*
```

&emsp;&emsp;API返回了下面的响应，指出现有的目标（existing target）中不匹配这个pattern。

```text
{
  "indices": [ ],
  "aliases": [ ],
  "data_streams": [ ]
}
```

2. 创建或者更新一个index template。这个模板引应该包含你想要应用到新的data stream中的backing index的mappings和settings。

&emsp;&emsp;这个index template必须满足[requirements for a data stream template](####Create an index template(data stream))。他应该在`index_patterns`属性中同样包含之前选择的名字或者index patter。

> TIP：如果你只增加或者修改小部分东西，我们建议你通过拷贝现有的模板，根据你的需要进行修改，然后用于创建一个新的模板。

&emsp;&emsp;例如，名为`my-data-stream`的data stream现在使用的是名为`my-data-steram-template`的模板。

&emsp;&emsp;下面的[create or update index template API ](##Index templates)请求创建了一个新的index template，名为`new-data-stream-template`，`new-data-stream-template`将`my-data-stream-template`作为基准，作出了下面的改动：

- `index_patterns`中匹配以`new-data-stream`开头 的索引或data stream
- `@timestamp`域使用`date_nanos`域而不是使用`date`
- 模板中包含了`sort.field`和`sort.order`的index settings，原来的`my-data-stream-template`没有这两个setting

```text
PUT /_index_template/new-data-stream-template
{
  "index_patterns": [ "new-data-stream*" ],
  "data_stream": { },
  "priority": 500,
  "template": {
    "mappings": {
      "properties": {
        "@timestamp": {
          "type": "date_nanos"                 
        }
      }
    },
    "settings": {
      "sort.field": [ "@timestamp"],          
      "sort.order": [ "desc"]                 
    }
  }
}
```

&emsp;&emsp;第10行，`@timestamp`域的类型修改为`date_nanos`

&emsp;&emsp;第15行，增加index setting `sort.field`

&emsp;&emsp;第16行，增加index setting `sort.order`

3. 使用[create data stream API ](####Create data stream API)手动创建新的data stream。data stream的名字必须匹配定义在index template中的`index_patterns`属性。

&emsp;&emsp;我们不建议[indexing new data to create this data stream](####Create data stream API)。因为随后你将从已有的data stream中的旧数据reindex到这个data stream中。这会导致一个或者多个backing index会同时包含新旧数据。

> IMPORTANT：Mixing new and old data in a data stream
> 尽管新旧数据的混合是安全，但它会影响data retention。如果你要删除旧的索引，你可能会意外的删除一个同时包含新旧数据的索引。为了防止过早的（premature）数据丢失，你需要保留这样的backing index直到里面最新的数据可以被删除。

&emsp;&emsp;下面的create data stream API请求的目标是`new-data-stream`，它会匹配到index template中的`new-data-stream-template`。因为现有的索引或data stream没有使用这个名字，所以这个请求会创建名为`new-data-stream`的data stream。

```text
PUT /_data_stream/new-data-stream
```

4. 如果你不想在新的data stream中混合新旧数据，那么先暂停新文档的索引。尽管新旧数据的混合是安全，但它会影响data retention。如果你要删除旧的索引，你可能会意外的删除一个同时包含新旧数据的索引。为了防止过早的（premature）数据丢失，你需要保留这样的backing index直到里面最新的数据可以被删除。

5. 如果你使用ILM来[automate rollover](###Tutorial: Automate rollover with ILM)，那么降低ILM的poll interval。这能保证在下一次rollover检查之前，当前的write index不会增长的过大。默认情况下，ILM每10分钟检查下rollover的条件。

&emsp;&emsp;下面[cluster update settings API ](####Cluster update settings API)请求将`indices.lifecycle.poll_interval`的值降低为`1m`。

```text
PUT /_cluster/settings
{
  "persistent": {
    "indices.lifecycle.poll_interval": "1m"
  }
}
```

6. 使用`op_type` 中的`create`将你的数据reindex到新的data stream中

&emsp;&emsp;如果你想要按照原来索引的顺序来对数据进行划分，你可以允许多个reindex请求。这些reindex请求可以使用独立的backing index作为输入源。你可以使用[get data stream API](####Get data stream API)查看backing index列表。

&emsp;&emsp;例如，你计划将`my-data-stream` reindex到`new-data-steram`中，然而你想要为`my-data-steram`中的每一个backing index分别提交一个reindex请求，从最旧的backing index开始。这样就能保留原来索引的顺序。

&emsp;&emsp;下面的get data stream API请求查看了`my-data-stream`的信息，包含了一个backing index的列表。

```text
GET /_data_stream/my-data-stream
```

&emsp;&emsp;响应中的`indices`包含了stream中当前的backing index。数组中第一个元素就是最旧的backing index的信息。

```text
{
  "data_streams": [
    {
      "name": "my-data-stream",
      "timestamp_field": {
        "name": "@timestamp"
      },
      "indices": [
        {
          "index_name": ".ds-my-data-stream-2099.03.07-000001", 
          "index_uuid": "Gpdiyq8sRuK9WuthvAdFbw"
        },
        {
          "index_name": ".ds-my-data-stream-2099.03.08-000002",
          "index_uuid": "_eEfRrFHS9OyhqWntkgHAQ"
        }
      ],
      "generation": 2,
      "status": "GREEN",
      "template": "my-data-stream-template",
      "hidden": false,
      "system": false,
      "allow_custom_routing": false,
      "replicated": false
    }
  ]
}
```

&emsp;&emsp;第10行，`my-data-steram`的`indices`数组中第一个条目中包含了这个stream中最旧的名为`.ds-my-data-stream-2099.03.07-000001`的backing index信息。

&emsp;&emsp;下面的[reindex API](####Reindex API)请求将`.ds-my-data-stream-2099.03.07-000001`中的文档拷贝到`new-data-steam`中。这个请求的`op_type`是`create`。

```text
POST /_reindex
{
  "source": {
    "index": ".ds-my-data-stream-2099.03.07-000001"
  },
  "dest": {
    "index": "new-data-stream",
    "op_type": "create"
  }
}
```

&emsp;&emsp;你也可以在每次的请求中使用一个query对一个文档子集进行reindex。

&emsp;&emsp;下面的[reindex API](####Reindex API)请求将`my-data-steram`中的文档拷贝到`new-data-stream`中，请求中使用了[range query](####Range query)，只reindex了上周的文档。注意的是这个请求的`op_type`是`craete`。

```text
POST /_reindex
{
  "source": {
    "index": "my-data-stream",
    "query": {
      "range": {
        "@timestamp": {
          "gte": "now-7d/d",
          "lte": "now/d"
        }
      }
    }
  },
  "dest": {
    "index": "new-data-stream",
    "op_type": "create"
  }
}
```

7. 如果你之前修改了ILM的poll interval，那么在reindex完成后改回到原来的值。防止master node上没必要的负载。

&emsp;&emsp;下面的cluster update settings API请求将`indices.lifecycle.poll_interval`设置为默认值。

```text
PUT /_cluster/settings
{
  "persistent": {
    "indices.lifecycle.poll_interval": null
  }
}
```

8. 使用新的data stream恢复索引。在这个stream上的查询会使用新的数据以及reindex的数据。

9. 一旦你验证完所有的reindex数据在新的data stream是可见的，你就可以安全的移除旧的stream。

&emsp;&emsp;下面的[delete data stream API ](####Delete data stream API)请求删除了`my-data-steam`，这个请求同样会删除它包含的所有的backing index和其他任何数据。

```text
DELETE /_data_stream/my-data-stream
```

## Ingest pipelines
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ingest.html)

&emsp;&emsp;Ingest pipelines能让你在索引之前对你的数据执行常用的转换（common transformation）。例如你可以使用pipeline来移除域，从文本中提取值，并且丰富你的数据（enrich your data）。

&emsp;&emsp;一个pipeline由一系列称为[processors](###Ingest processor reference)的可配置的任务组成。每一个processor顺序的运行，对incoming document做对应的更改。在运行完processor后，Elasticsearch将转换后的document添加到data stream或者索引中。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ingest-process.svg">

&emsp;&emsp;你可以使用Kibana的**Ingest Pipelines** 功能或者[ingest APIS](###Ingest APIs)来管理ingest pipeline。Elasticsearch将pipeline存储在[cluster state](####Cluster state API)中。

#### Prerequisites(ingest pipeline)

- 节点角色（node role）为[Ingest node](#####Ingest node)负责pipeline的处理。若要使用ingest pipeline，你的集群中必须至少有一个节点角色为`ingest`的节点。对于繁重的ingest负载，我们建议你创建一个[dedicated ingest nodes](#####Ingest node)
- 如果开启了Elasticsearch security feature，你必须有`manage_pipeline`的[cluster privilege](#####Cluster privileges)才能管理ingest pipeline。若要使用Kibana的**Ingest Pipeline** 功能，你也需要有`cluster:monitor/nodes/info`的cluster privilege。

#### Create and manage pipelines

&emsp;&emsp;在Kibana中，打开主菜单并且点击**Stack Management > Ingest Pipelines**。从下拉菜单中，你可以：

- 查看你的pipeline列表以及drill down后查看详情
- 编辑或者克隆现有的pipeline
- 删除pipeline

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ingest-pipeline-list.png">


&emsp;&emsp;若要创建一个pipeline，点击**Create pipeline > New pipeline**。可以查看[Example: Parse logs](###Example: Parse logs in the Common Log Format)这个示例教程。

> NOTE：**New pipeline from CSV**可以让你使用一个CSV来创建一个ingest pipeline，它将自定义的数据映射到[Elastic Common Schema (ECS)](https://www.elastic.co/guide/en/ecs/8.2/index.html)。将你的数据映射到ECS使得数据更易于查询并且能让你从其他数据集中复用（reuse）可视化。见[Map custom data to ECS](https://www.elastic.co/guide/en/ecs/8.2/ecs-converting.html)。

&emsp;&emsp;你也可以使用[ingest APIs](###Ingest APIs)来创建和管理pipeline。下面的[create pipeline API](####Create or update pipeline API)请求中创建了一个包含2个[set ](####Set processor) processor ，以及一个[lowercase](####Lowercase processor) processor的pipeline。这三个processor会根据指定的顺序有序执行。

```text
PUT _ingest/pipeline/my-pipeline
{
  "description": "My optional pipeline description",
  "processors": [
    {
      "set": {
        "description": "My optional processor description",
        "field": "my-long-field",
        "value": 10
      }
    },
    {
      "set": {
        "description": "Set 'my-boolean-field' to true",
        "field": "my-boolean-field",
        "value": true
      }
    },
    {
      "lowercase": {
        "field": "my-keyword-field"
      }
    }
  ]
}
```

#### Manage pipeline versions

&emsp;&emsp;当你创建或者更新一个pipeline时，你可以指定一个可选的`version`的整数值。你可以使用version以及参数[if_version](#####Query parameters(Create or update pipeline API))来有条件的更新pipeline。一次成功的更新操作会提高pipeline的版本号。

```text
PUT _ingest/pipeline/my-pipeline-id
{
  "version": 1,
  "processors": [ ... ]
}
```

&emsp;&emsp;若要通过API不设置`version`，可以在替换或者更新pipeline时不指定`versopm`参数。

#### Test a pipeline

&emsp;&emsp;在生产中使用pipeline前，我们建议你先用样例文档（sample document）进行测试。当在Kibana中创建或者编辑了一个pipeline，点击**Document** tab页的**Add documents**，提供样例文档并点击**Run the pipeline**。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/test-a-pipeline.png">

&emsp;&emsp;你也可以使用[simulate pipeline API](####Simulate pipeline API)来测试pipeline。你可以在请求路径中指定一个配置好的pipeline。例如下面的请求中测试了`my-pipeline`。

```text
POST _ingest/pipeline/my-pipeline/_simulate
{
  "docs": [
    {
      "_source": {
        "my-keyword-field": "FOO"
      }
    },
    {
      "_source": {
        "my-keyword-field": "BAR"
      }
    }
  ]
}s
```

&emsp;&emsp;或者你可以指定一个在请求body中指定pipeline和它的processor。

```text
POST _ingest/pipeline/_simulate
{
  "pipeline": {
    "processors": [
      {
        "lowercase": {
          "field": "my-keyword-field"
        }
      }
    ]
  },
  "docs": [
    {
      "_source": {
        "my-keyword-field": "FOO"
      }
    },
    {
      "_source": {
        "my-keyword-field": "BAR"
      }
    }
  ]
}
```

&emsp;&emsp;这个API会返回转换后的文档：

```text
{
  "docs": [
    {
      "doc": {
        "_index": "_index",
        "_id": "_id",
        "_source": {
          "my-keyword-field": "foo"
        },
        "_ingest": {
          "timestamp": "2099-03-07T11:04:03.000Z"
        }
      }
    },
    {
      "doc": {
        "_index": "_index",
        "_id": "_id",
        "_source": {
          "my-keyword-field": "bar"
        },
        "_ingest": {
          "timestamp": "2099-03-07T11:04:04.000Z"
        }
      }
    }
  ]
}
```

#### Add a pipeline to an indexing request

&emsp;&emsp;在请求参数中使用`pipeline`对单篇或者多篇文档应用一个pipeline

```text
POST my-data-stream/_doc?pipeline=my-pipeline
{
  "@timestamp": "2099-03-07T11:04:05.000Z",
  "my-keyword-field": "foo"
}

PUT my-data-stream/_bulk?pipeline=my-pipeline
{ "create":{ } }
{ "@timestamp": "2099-03-07T11:04:06.000Z", "my-keyword-field": "foo" }
{ "create":{ } }
{ "@timestamp": "2099-03-07T11:04:07.000Z", "my-keyword-field": "bar" }
```

&emsp;&emsp;你也可以在[update by query](####Update By Query API)或者 [reindex](####Reindex API) API中使用`pipeline`参数：

```text
POST my-data-stream/_update_by_query?pipeline=my-pipeline

POST _reindex
{
  "source": {
    "index": "my-data-stream"
  },
  "dest": {
    "index": "my-new-data-stream",
    "op_type": "create",
    "pipeline": "my-pipeline"
  }
}
```

#### Set a default pipeline

&emsp;&emsp;使用索引设置[index.default_pipeline](######index.default_pipeline)来设置一个默认的pipeline。如果没有指定`pipeline`参数，Elasticsearch则会应用（apply ）这个默认的pipeline。

#### Pipelines for Beats

&emsp;&emsp;如果为Elastic Beat添加一个ingest pipeline，可以在`<BEAT_NAME>.yml`中的`output.elasticsearch`下指定参数`pipeline`。

```text
output.elasticsearch:
  hosts: ["localhost:9200"]
  pipeline: my-pipeline
```

#### Pipelines for Fleet and Elastic Agent

&emsp;&emsp;[Fleet](https://www.elastic.co/guide/en/fleet/8.2/index.html)会为integration自动的添加ingest pipeline。Fleet会使用包含了[pipeline index settings](####Set a default pipeline)的[index templates](##Index templates)来 应用pipeline。Elasticsearch会基于[stream’s naming scheme](https://www.elastic.co/guide/en/fleet/8.2/data-streams.html#data-streams-naming-scheme)匹配到这些模板到你的Fleet data streams中。

> WARNING：不要修改Fleet的ingest pipeline或者对你的Fleet integration使用自定义的pipeline。这么做会破坏你的Fleet data streams。

&emsp;&emsp;Fleet不会为**Custom logs** integration提供一个ingest pipeline。你可以使用两种方法中的一种来为你的integration安全的指定一个pipeline：[index template](#####Option 1: Index template) 或者一个[custom configuration](#####Option 2: Custom configuration)。

##### Option 1: Index template

1. [Create](####Create and manage pipelines)并且[test](####Test a pipeline)你的ingest pipeline。将你的pipeline命名为`logs-<dataset-name>-default`。这样方便为你的integration进行追踪（track）。

```text
PUT _ingest/pipeline/logs-my_app-default
{
  "description": "Pipeline for `my_app` dataset",
  "processors": [ ... ]
}
```
2. 创建一个[index template](##Index templates)，包含在index setting中设置的pipeline的[index.default_pipeline](#####index.default_pipeline)和[index.final_pipeline](#####index.final_pipeline)。保证模板中开启了[data stream](####Create an index template(data stream))。这个模板的index pattern应该匹配`logs-<dataset-name>-*`。

&emsp;&emsp;你可以使用Kibana的[Index Management](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-mgmt.html#manage-index-templates)功能来[create index template API](####Create or update index template API)。

&emsp;&emsp;例如，下面的请求创建了一个模板来匹配`logs-my_app-*`。这个模板使用了component template，它包含了`index.default_pipeline`这个index setting。

```text
# Creates a component template for index settings
PUT _component_template/logs-my_app-settings
{
  "template": {
    "settings": {
      "index.default_pipeline": "logs-my_app-default",
      "index.lifecycle.name": "logs"
    }
  }
}

# Creates an index template matching `logs-my_app-*`
PUT _index_template/logs-my_app-template
{
  "index_patterns": ["logs-my_app-*"],
  "data_stream": { },
  "priority": 500,
  "composed_of": ["logs-my_app-settings", "logs-my_app-mappings"]
}
```

3. 在Fleet中增加或者编辑**Custom logs** integration时，点击**Configure integration > Custom log file > Advanced options**。
4. 在**Dataset name**中，指定你的数据集名字，Fleet会为integration添加新的数据并输出（resulting）`logs-<dataset-name>-default` data stream。

&emsp;&emsp;例如，如果你的数据集名字是`my_app`，Fleet将新的数据添加到`logs-my_app-default`数据流。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/custom-logs.png">

5. 使用[rollover API](####Rollover API)来roll over你的数据流。这将保证Elasticsearch为integration将index template和pipeline设置应用到新的数据上。

```text
POST logs-my_app-default/_rollover/
```

##### Option 2: Custom configuration

1. [Create](####Create and manage pipelines)和[test](####Test a pipeline)你的ingest pipeline。默认命名pipeline的名字为`logs-<dataset-name>-default`。这使得让你的integration更易于追踪（track）。

&emsp;&emsp;例如，下面的请求中为数据集`my-app`创建了一个pipeline。这个pipeline的名字是`logs-my_app-default`。

```text
PUT _ingest/pipeline/logs-my_app-default
{
  "description": "Pipeline for `my_app` dataset",
  "processors": [ ... ]
}
```

2. 当你在Fleet中添加或者编辑你的**Custom logs** integration。点击**Configure integration > Custom log file > Advanced options**。

3. 在**Dataset name**，指定你的数据集名字。Fleet将为integration添加新的数据并且输出到`logs-<dataset-name>-default` 数据流中。

&emsp;&emsp;例如，如果你的数据集名字是`my_app`，Fleet将添加新的数据到`logs-my_app-default` 数据流中。

4. 在**Custom Configurations**，在`pipeline`策略设置中指定你的pipeline。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/custom-logs-pipeline.png">

##### Elastic Agent standalone

&emsp;&emsp;如果你用standalone方式启动Elastic Agent。你可以使用包含[index.default_pipeline](#####index.default_pipeline)和[index.final_pipeline](#####index.final_pipeline) index setting的[index template](##Index templates)来应用pipeline。或者你可以在`elastic-agent.yml`中指定`pipeline`策略。见[Install standalone Elastic Agents](https://www.elastic.co/guide/en/fleet/8.2/install-standalone-elastic-agent.html)。

#### Access source fields in a processor

&emsp;&emsp;Processor对incoming document的source field有读写权限。若要在一个processor中访问一个field，使用其域名。下面的process `set`访问了`my-long-field`。

```text
PUT _ingest/pipeline/my-pipeline
{
  "processors": [
    {
      "set": {
        "field": "my-long-field",
        "value": 10
      }
    }
  ]
}
```

&emsp;&emsp;你也可以前置（prepend）`_source`前缀。

```text
PUT _ingest/pipeline/my-pipeline
{
  "processors": [
    {
      "set": {
        "field": "_source.my-long-field",
        "value": 10
      }
    }
  ]
}
```

&emsp;&emsp;使用`.`（dot notation）来访问object fields。

> IMPORTANT：如果你的文档中包含flattened object，使用[dot_expander](####Dot expander processor)先进行expand。其他的ingest processor不能访问flattened object。


```text
PUT _ingest/pipeline/my-pipeline
{
  "processors": [
    {
      "dot_expander": {
        "description": "Expand 'my-object-field.my-property'",
        "field": "my-object-field.my-property"
      }
    },
    {
      "set": {
        "description": "Set 'my-object-field.my-property' to 10",
        "field": "my-object-field.my-property",
        "value": 10
      }
    }
  ]
}
```

&emsp;&emsp;多个processor参数支持[Mustache template snippets](https://mustache.github.io/)。为了能在template snippet中访问域值，使用三个大括号（curly brackets）包住域名。你可以使用template snippets动态的设置域名。

```text
PUT _ingest/pipeline/my-pipeline
{
  "processors": [
    {
      "set": {
        "description": "Set dynamic '<service>' field to 'code' value",
        "field": "{{{service}}}",
        "value": "{{{code}}}"
      }
    }
  ]
}
```

#### Access metadata fields in a processor

&emsp;&emsp;Processor能通过名字访问下面的metadata filed：

- \_index
- \_id
- \_routing
- \_dynamic_templates

```text
PUT _ingest/pipeline/my-pipeline
{
  "processors": [
    {
      "set": {
        "description": "Set '_routing' to 'geoip.country_iso_code' value",
        "field": "_routing",
        "value": "{{{geoip.country_iso_code}}}"
      }
    }
  ]
}
```

&emsp;&emsp;使用一个Mustache template snippet来访问metadata filed的值。例如`{{{_routing}}}`将retrieve一篇文档的 routing value。

```text
PUT _ingest/pipeline/my-pipeline
{
  "processors": [
    {
      "set": {
        "description": "Use geo_point dynamic template for address field",
        "field": "_dynamic_templates",
        "value": {
          "address": "geo_point"
        }
      }
    }
  ]
}
```

&emsp;&emsp;The set processor above tells ES to use the dynamic template named geo_point for the field address if this field is not defined in the mapping of the index yet. This processor overrides the dynamic template for the field address if already defined in the bulk request, but has no effect on other dynamic templates defined in the bulk request.

> WARNING： 如果你[automatically generate](######Create document IDs automatically) document id，你不能在processor中使用{{{\_id}}}，因为Elasticsearch在ingest之后才会自动分配`_id`值。

#### Access ingest metadata in a processor

&emsp;&emsp;Ingest processors可以使用key `_ingest`添加并且访问ingest metadata。

&emsp;&emsp;不同于source和metadata field，Elasticsearch默认不会索引ingest metadata。Elasticsearch同样允许以`_ingest`开头的source fields，如果你的数据中包含了这种source fields，使用`_source._ingest`来访问它们。

&emsp;&emsp;pipeline默认只创建名为`_ingest.timestamp`的ingest metadata域。这个域包含的是Elasticsearch收到文档索引请求时的timestamp。若要索引`_ingest.timestamp`或者其他ingest metadata域，使用`set`processor。

```text
PUT _ingest/pipeline/my-pipeline
{
  "processors": [
    {
      "set": {
        "description": "Index the ingest timestamp as 'event.ingested'",
        "field": "event.ingested",
        "value": "{{{_ingest.timestamp}}}"
      }
    }
  ]
}
```

#### Handling pipeline failures

&emsp;&emsp;pipeline的processor有序运行的。默认情况下，当其中一个processor允许失败或者遇到一个错误时，pipeline的处理就会停止下来。

&emsp;&emsp;若要忽略某个processor的失败并且运行剩余的 processor。将`ignore_failure`为`true`。

```text
PUT _ingest/pipeline/my-pipeline
{
  "processors": [
    {
      "rename": {
        "description": "Rename 'provider' to 'cloud.provider'",
        "field": "provider",
        "target_field": "cloud.provider",
        "ignore_failure": true
      }
    }
  ]
}
```

&emsp;&emsp;使用`on_failure`参数指定processor list，在一个processor失败后马上就运行它们。如果指定了`on_failure`，即使没有配置任何processor，Elasticsearch之后会运行pipeline中剩余的processor。

```text
PUT _ingest/pipeline/my-pipeline
{
  "processors": [
    {
      "rename": {
        "description": "Rename 'provider' to 'cloud.provider'",
        "field": "provider",
        "target_field": "cloud.provider",
        "on_failure": [
          {
            "set": {
              "description": "Set 'error.message'",
              "field": "error.message",
              "value": "Field 'provider' does not exist. Cannot rename to 'cloud.provider'",
              "override": false
            }
          }
        ]
      }
    }
  ]
}
```

&emsp;&emsp;`on_failure`中嵌套processor用于嵌套错误的处理

```text
PUT _ingest/pipeline/my-pipeline
{
  "processors": [
    {
      "rename": {
        "description": "Rename 'provider' to 'cloud.provider'",
        "field": "provider",
        "target_field": "cloud.provider",
        "on_failure": [
          {
            "set": {
              "description": "Set 'error.message'",
              "field": "error.message",
              "value": "Field 'provider' does not exist. Cannot rename to 'cloud.provider'",
              "override": false,
              "on_failure": [
                {
                  "set": {
                    "description": "Set 'error.message.multi'",
                    "field": "error.message.multi",
                    "value": "Document encountered multiple ingest errors",
                    "override": true
                  }
                }
              ]
            }
          }
        ]
      }
    }
  ]
}
```

&emsp;&emsp;你也可以为一个pipeline指定`on_failure`。如果某个没有设置`on_failure`的processor失败后，Elasticsearch会使用这个pipeline-level的参数作为一个fallback。Elasticsearch不会尝试运行pipeline中剩余的processor。

```text
PUT _ingest/pipeline/my-pipeline
{
  "processors": [ ... ],
  "on_failure": [
    {
      "set": {
        "description": "Index document to 'failed-<index>'",
        "field": "_index",
        "value": "failed-{{{ _index }}}"
      }
    }
  ]
}
```

&emsp;&emsp;pipeline失败后的额外信息可以在document的metadata filed中查看：`on_failure_message`, `on_failure_processor_type`, `on_failure_processor_tag`, and `on_failure_pipeline`。这些域的信息只能在`on_failure`中才能被访问。

&emsp;&emsp;下面的例子中使用了document的metadata fields，它包含了pipeline 失败后的信息：

```text
PUT _ingest/pipeline/my-pipeline
{
  "processors": [ ... ],
  "on_failure": [
    {
      "set": {
        "description": "Record error information",
        "field": "error_information",
        "value": "Processor {{ _ingest.on_failure_processor_type }} with tag {{ _ingest.on_failure_processor_tag }} in pipeline {{ _ingest.on_failure_pipeline }} failed with message {{ _ingest.on_failure_message }}"
      }
    }
  ]
}
```

#### Conditionally run a processor

&emsp;&emsp;每一个processor都支持可选的`if`条件，使用[Painless script](https://www.elastic.co/guide/en/elasticsearch/painless/8.2/painless-guide.html)编写。如果提供了这个配置，processor只有在`if`条件为`ture`的情况下才会运行。

> IMPORTANT： `if`条件允许域Painless的[ingest processor context](https://www.elastic.co/guide/en/elasticsearch/painless/8.2/painless-ingest-processor-context.html)。在`if`条件中，`ctx`的值是只读的。

```text
PUT _ingest/pipeline/my-pipeline
{
  "processors": [
    {
      "drop": {
        "description": "Drop documents with 'network.name' of 'Guest'",
        "if": "ctx?.network?.name == 'Guest'"
      }
    }
  ]
}
```

&emsp;&emsp;如果开启了集群设置[script.painless.regex.enabled](######script.painless.regex.enabled)，你可以在`if`条件脚本中使用正则表达式。见[Painless regular expressions](https://www.elastic.co/guide/en/elasticsearch/painless/8.2/painless-regexes.html)了解支持的语法。

> TIP：如果可以的话，避免在`if`条件中使用复杂或者开销大的脚本。然而你可以使用[Kibana consloe](https://www.elastic.co/guide/en/kibana/8.2/console-kibana.html#configuring-console)的 triple quote syntax来编写以及调试larger script。

```text
PUT _ingest/pipeline/my-pipeline
{
  "processors": [
    {
      "drop": {
        "description": "Drop documents that don't contain 'prod' tag",
        "if": """
            Collection tags = ctx.tags;
            if(tags != null){
              for (String tag : tags) {
                if (tag.toLowerCase().contains('prod')) {
                  return false;
                }
              }
            }
            return true;
        """
      }
    }
  ]
}
```

&emsp;&emsp;你可以使用[stored script](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-scripting-stored-scripts.html)作为`if`条件中的脚本。

```text
PUT _scripts/my-prod-tag-script
{
  "script": {
    "lang": "painless",
    "source": """
      Collection tags = ctx.tags;
      if(tags != null){
        for (String tag : tags) {
          if (tag.toLowerCase().contains('prod')) {
            return false;
          }
        }
      }
      return true;
    """
  }
}

PUT _ingest/pipeline/my-pipeline
{
  "processors": [
    {
      "drop": {
        "description": "Drop documents that don't contain 'prod' tag",
        "if": { "id": "my-prod-tag-script" }
      }
    }
  ]
}
```

&emsp;&emsp;Incoming document通常包含object field。如果一个processor script尝试访问一个域，但是parent object不存在，Elasticsearch会返回一个`NullPointerException`。若要避免这些异常，使用[null safe operators](https://www.elastic.co/guide/en/elasticsearch/painless/8.2/painless-operators-reference.html#null-safe-operator)，例如`?.`，你的script就可以变的null safe。

&emsp;&emsp;例如，`ctx.network?.name.equalsIgnoreCase('Guest')` 不是null safe。`ctx.network?.name`可能返回null，将脚本重写为`'Guest'.equalsIgnoreCase(ctx.network?.name)`的话就是null safe，因为`Guest`总是non-null。

&emsp;&emsp;如果无法确保是null safe，就添加一个显示的null检查。

```text
PUT _ingest/pipeline/my-pipeline
{
  "processors": [
    {
      "drop": {
        "description": "Drop documents that contain 'network.name' of 'Guest'",
        "if": "ctx.network?.name != null && ctx.network.name.contains('Guest')"
      }
    }
  ]
}
```

#### Conditionally apply pipelines

&emsp;&emsp;基于你的规则，在[pipeline](####Pipeline processor)中使用`if` 条件来为你的文档作用pipeline。你可以在[index template](##Index templates)中使用这个pipeline作为[default pipeline](####Set a default pipeline)。

```text
PUT _ingest/pipeline/one-pipeline-to-rule-them-all
{
  "processors": [
    {
      "pipeline": {
        "description": "If 'service.name' is 'apache_httpd', use 'httpd_pipeline'",
        "if": "ctx.service?.name == 'apache_httpd'",
        "name": "httpd_pipeline"
      }
    },
    {
      "pipeline": {
        "description": "If 'service.name' is 'syslog', use 'syslog_pipeline'",
        "if": "ctx.service?.name == 'syslog'",
        "name": "syslog_pipeline"
      }
    },
    {
      "fail": {
        "description": "If 'service.name' is not 'apache_httpd' or 'syslog', return a failure message",
        "if": "ctx.service?.name != 'apache_httpd' && ctx.service?.name != 'syslog'",
        "message": "This pipeline requires service.name to be either `syslog` or `apache_httpd`"
      }
    }
  ]
}
```

#### Get pipeline usage statistics

&emsp;&emsp;使用[node stats](####Nodes stats API) API来获取全局的以及per-pipeline的ingest 统计数据。来检测出哪些pipeline运行频次最多或者处理时间耗时最多。

### Example: Parse logs in the Common Log Format
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/common-log-format-example.html)

&emsp;&emsp;在这个教程中，你将使用[ingest pipeline](##Ingest pipelines)以及[common log format](https://en.wikipedia.org/wiki/Common_Log_Format)，在索引前对服务器日志进行解析。在开始之前，先查看使用ingest pipeline前的[prerequisite](#### Prerequisites(ingest pipeline))。

&emsp;&emsp;你要解析的日志如下所示：

```text
212.87.37.154 - - [30/May/2099:16:21:15 +0000] \"GET /favicon.ico HTTP/1.1\"
200 3638 \"-\" \"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6)
AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36\"
```

&emsp;&emsp;这些日志中包含了timestamp，IP address，以及user agent。你要让这三个信息在Elasticsearch有自己的字段，使得可以用于快速的查询以及可视化。你同样想知道这些请求的出处。

1. 在Kibana中，打开主菜单并且点击**Stack Management > Ingest Pipelines**。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ingest-pipeline-list.png">

2. 点击**Create pipeline > New pipeline**
3. 给这个pipeline提供一个名字以及描述
4. 添加一个[grok processor](####Grok processor)来解析日志消息。
   1. 点击**Add a processor**并且选择Grok processor类型
   2. 设置**Field**为`message`并且**Patterns**设置为下面的[grok pattern](###Grok basics)
   3. 点击**Add**保存processor
   4. 设置processor的描述信息为`Extract fields from 'message'`
```text
%{IPORHOST:source.ip} %{USER:user.id} %{USER:user.name} \\[%{HTTPDATE:@timestamp}\\] \"%{WORD:http.request.method} %{DATA:url.original} HTTP/%{NUMBER:http.version}\" %{NUMBER:http.response.status_code:int} (?:-|%{NUMBER:http.response.body.bytes:int}) %{QS:http.request.referrer} %{QS:user_agent}
```
5. 为timestamp，IP address，和user agent增加processor。如下配置processor：

|             Processor type             |   Field    |          Additional options           |                   Description                   |
| :------------------------------------: | :--------: | :-----------------------------------: | :---------------------------------------------: |
|       [Date](####Date processor)       | @timestamp | **Formats**: `dd/MMM/yyyy:HH:mm:ss Z` | Format '@timestamp' as 'dd/MMM/yyyy:HH:mm:ss Z' |
|      [GeoIP](####GeoIP processor)      | source.ip  |    **Target field**: `source.geo`     |   Add 'source.geo' GeoIP data for 'source.ip'   |
| [User agent](####User agent processor) | user_agent |                                       |        Extract fields from 'user_agent'         |

&emsp;&emsp;如下所示：

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ingest-pipeline-processor.png">

&emsp;&emsp;下面四个processor将会顺序执行：

&emsp;&emsp;Grok > Date > GeoIP > User agent

&emsp;&emsp;你也可以使用箭头图标重新排序

&emsp;&emsp;或者，你可以点击**Import processors**链接然后定义如下的JSON：

```text
{
  "processors": [
    {
      "grok": {
        "description": "Extract fields from 'message'",
        "field": "message",
        "patterns": ["%{IPORHOST:source.ip} %{USER:user.id} %{USER:user.name} \\[%{HTTPDATE:@timestamp}\\] \"%{WORD:http.request.method} %{DATA:url.original} HTTP/%{NUMBER:http.version}\" %{NUMBER:http.response.status_code:int} (?:-|%{NUMBER:http.response.body.bytes:int}) %{QS:http.request.referrer} %{QS:user_agent}"]
      }
    },
    {
      "date": {
        "description": "Format '@timestamp' as 'dd/MMM/yyyy:HH:mm:ss Z'",
        "field": "@timestamp",
        "formats": [ "dd/MMM/yyyy:HH:mm:ss Z" ]
      }
    },
    {
      "geoip": {
        "description": "Add 'source.geo' GeoIP data for 'source.ip'",
        "field": "source.ip",
        "target_field": "source.geo"
      }
    },
    {
      "user_agent": {
        "description": "Extract fields from 'user_agent'",
        "field": "user_agent"
      }
    }
  ]

}
```

6. 若要测试pipeline，点击**Add documents**。
7. 在**Document** tab页，提供了一个文档文档样例用于测试：

```text
[
  {
    "_source": {
      "message": "212.87.37.154 - - [05/May/2099:16:21:15 +0000] \"GET /favicon.ico HTTP/1.1\" 200 3638 \"-\" \"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36\""
    }
  }
]
```

8. 点击**Run the pipeline**然后验证pipeline是否按预期运行
9. 如果一切看着都可以，关闭面板，然后点击**Create pipeline**。

&emsp;&emsp;现在你已经准备就绪将日志数据索引到[data stream](##Data streams)中。

10. 创建一个[index template](##Index templates)并开启[data stream](####Create an index template(data stream))。

```text
PUT _index_template/my-data-stream-template
{
  "index_patterns": [ "my-data-stream*" ],
  "data_stream": { },
  "priority": 500
}
```

11. 使用你创建的pipeline索引文档。

```text
POST my-data-stream/_doc?pipeline=my-pipeline
{
  "message": "89.160.20.128 - - [05/May/2099:16:21:15 +0000] \"GET /favicon.ico HTTP/1.1\" 200 3638 \"-\" \"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36\""
}
```

12. 若要进行校验，查询data stream来检索文档。下面的查询使用[filter_path](####Response Filtering)来只返回[document source](####\_source field)。

```text
GET my-data-stream/_search?filter_path=hits.hits._source
```

&emsp;&emsp;这个API返回如下：

```text
{
  "hits": {
    "hits": [
      {
        "_source": {
          "@timestamp": "2099-05-05T16:21:15.000Z",
          "http": {
            "request": {
              "referrer": "\"-\"",
              "method": "GET"
            },
            "response": {
              "status_code": 200,
              "body": {
                "bytes": 3638
              }
            },
            "version": "1.1"
          },
          "source": {
            "ip": "89.160.20.128",
            "geo": {
              "continent_name" : "Europe",
              "country_name" : "Sweden",
              "country_iso_code" : "SE",
              "city_name" : "Linköping",
              "region_iso_code" : "SE-E",
              "region_name" : "Östergötland County",
              "location" : {
                "lon" : 15.6167,
                "lat" : 58.4167
              }
            }
          },
          "message": "89.160.20.128 - - [05/May/2099:16:21:15 +0000] \"GET /favicon.ico HTTP/1.1\" 200 3638 \"-\" \"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36\"",
          "url": {
            "original": "/favicon.ico"
          },
          "user": {
            "name": "-",
            "id": "-"
          },
          "user_agent": {
            "original": "\"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36\"",
            "os": {
              "name": "Mac OS X",
              "version": "10.11.6",
              "full": "Mac OS X 10.11.6"
            },
            "name": "Chrome",
            "device": {
              "name": "Mac"
            },
            "version": "52.0.2743.116"
          }
        }
      }
    ]
  }
}
```

### Ingest processor reference
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/processors.html)

#### Date processor
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/date-processor.html)


#### Date index name processor
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/date-index-name-processor.html)

#### Dissect processor
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/dissect-processor.html)

#### Dot expander processor
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/dot-expand-processor.html)

#### GeoIP processor
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/geoip-processor.html)

#### Grok processor
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/grok-processor.html)

#### Lowercase processor
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/lowercase-processor.html)

#### Set processor
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/set-processor.html)

#### User agent processor
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/user-agent-processor.html)


## Aliases
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/aliases.html#write-index)

&emsp;&emsp;Alias是一组数据流（[data stream](##Data streams)）或者索引的别名（secondary name）。大多数的Elasticsearch API接受用一个alias来代替一个数据流或者索引。

&emsp;&emsp;你可以在任何时间修改alias对应的数据流或者索引。如果你使用alias在你的应用中进行查询，你可以在不需要修改你代码并且不需要停机（downtime）的情况下reindex data。

#### Alias types

&emsp;&emsp;目前有两种类型的alias：

- data stream alias指向一个或者多个数据流
- index alias 指向一个或者多个索引

&emsp;&emsp;alias不能同时指向数据流和索引。你不能添加一个数据流的[backing index](####Backing indices)作为index alias。

#### Add an alias

&emsp;&emsp;使用 [aliases API](####Aliases API)的`add`动作对一个现有的数据流或者索引起个别名。如果别名不存在，那么这个请求就会进行创建。

```text
POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "logs-nginx.access-prod",
        "alias": "logs"
      }
    }
  ]
}
```

&emsp;&emsp;API的`index`和`indices`支持通配符（\*）。如果通配符模板同时匹配到数据流或者索引则会返回错误。

```text
POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "logs-*",
        "alias": "logs"
      }
    }
  ]
}
```

#### Remove an alias

&emsp;&emsp;使用alias API的`remove`动作来移除别名。

```text
POST _aliases
{
  "actions": [
    {
      "remove": {
        "index": "logs-nginx.access-prod",
        "alias": "logs"
      }
    }
  ]
}
```

#### Multiple actions

&emsp;&emsp;你可以使用alias API在一个原子操作中执行多个动作。

&emsp;&emsp;例如，别名`logs`指向了一个数据流。下面的请求将交换别名（swap alias）。在这次交换期间，这个`logs`别名没有downtime（还可以用于查询）并且不会在同一时间指向多个流。

```text
POST _aliases
{
  "actions": [
    {
      "remove": {
        "index": "logs-nginx.access-prod",
        "alias": "logs"
      }
    },
    {
      "add": {
        "index": "logs-my_app-default",
        "alias": "logs"
      }
    }
  ]
}
```

#### Add an alias at index creation

&emsp;&emsp;你可以在创建索引或者数据流时使用[component](####Create or update component template API)或者[index template](####Create or update index template API)为它们添加别名。

```text
# Component template with index aliases
PUT _component_template/my-aliases
{
  "template": {
    "aliases": {
      "my-alias": {}
    }
  }
}

# Index template with index aliases
PUT _index_template/my-index-template
{
  "index_patterns": [
    "my-index-*"
  ],
  "composed_of": [
    "my-aliases",
    "my-mappings",
    "my-settings"
  ],
  "template": {
    "aliases": {
      "yet-another-alias": {}
    }
  }
}
```

&emsp;&emsp;你也可以在[create index API](####Create index API)请求中指定索引别名：

```text
# PUT <my-index-{now/d}-000001>
PUT %3Cmy-index-%7Bnow%2Fd%7D-000001%3E
{
  "aliases": {
    "my-alias": {}
  }
}
```

#### View aliases

&emsp;&emsp;可以使用不带参数的get alias API来获取你的集群的别名列表。

```text
GET _alias
```

&emsp;&emsp;在`_alias`前指定数据流或者索引来查看它们的别名。

```text
GET my-data-stream/_alias
```

&emsp;&emsp;在`_alias`后指定别名来查看对应的数据量或者索引。

```text
GET _alias/logs
```

#### Write_index

&emsp;&emsp;你可以为别名使用`is_write_index`来指定一个用于写入的索引或者数据流。Elasticsearch会将对这个别名的任何写请求路由到这个索引或者数据流。

```text
POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "logs-nginx.access-prod",
        "alias": "logs"
      }
    },
    {
      "add": {
        "index": "logs-my_app-default",
        "alias": "logs",
        "is_write_index": true
      }
    }
  ]
}
```

&emsp;&emsp;如果一个别名指向多个索引或者多个数据流，并且没有设置`is_write_index`，这次写请求会被reject。如果一个别名指向一个索引并且没有设置`is_write_index`，那么这个索引默认作为write index。数据流的别名不会自动的设置为write data stream，及时这个别名只指向了一个数据流。

> TIP：我们建议使用数据流来存储append-only的时序数据（time series data）。如果你经常会更新或者删除现有的时序数据，转而使用index alias来指定write index。见[Manage time series data without data streams](####Manage time series data without data streams)。


#### Filter an alias

&emsp;&emsp;`filter`选项使用 [Query DSL](##Query DSL)来限制通过别名能查询到的文档。

```text
POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "my-index-2099.05.06-000001",
        "alias": "my-alias",
        "filter": {
          "bool": {
            "filter": [
              {
                "range": {
                  "@timestamp": {
                    "gte": "now-1d/d",
                    "lt": "now/d"
                  }
                }
              },
              {
                "term": {
                  "user.id": "kimchy"
                }
              }
            ]
          }
        }
      }
    }
  ]
}
```

#### Routing

&emsp;&emsp;使用`routing`选项将对别名发起的请求[route](####\_routing field)到指定的分片。这样可以让你利用[shard cache](####Shard request cache settings)来加速查询。数据流不支持这个选项。

```text
POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "my-index-2099.05.06-000001",
        "alias": "my-alias",
        "routing": "1"
      }
    }
  ]
}
```

&emsp;&emsp;使用`index_routing`和`search_routing`给索引跟查询指定不同的路由值。在指定后，这些选项会用覆盖`routing`的值并执行它们各自的操作。

```text
POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "my-index-2099.05.06-000001",
        "alias": "my-alias",
        "search_routing": "1",
        "index_routing": "2"
      }
    }
  ]
}
```

## Search your data
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-your-data.html)


### Highlighting
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/highlighting.html)

### Long-running searches
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/async-search-intro.html)

&emsp;&emsp;Elasticsearch通常允许你搜索大量的数据。有这样的场景，当一个查询在很多分片上查询，可能查询了非常大的数据集或者多个[remote cluster](###Remote clusters)，对于这种查询可能无法在毫秒级别返回。当你需要执行long-running查询时，同步等待结果返回不是一种好的方式。异步查询（async search）可以让你提交一个查询请求，然后异步执行。可以监控请求的过程，随后去获取结果。你也可以在这个查询全部完成前让部分结果可见并且返回。

&emsp;&emsp;你可以使用 [submit async search](#####Submit async search API) API提交一个异步查询请求。[get async search](#####Get async search) API允许你监控一个异步查询请求的过程并且获取结果。可以通过[delete async search API](#####Delete async search)删除一个正在进行中的异步查询。

### Near real-time search
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/near-real-time.html)

&emsp;&emsp;[documents and indices](###Data in documents and indices)中说到当在Elasticsearch中存储一篇文档时，文档在被索引后可以接近实时（near real-time search，简称NRT）（1秒内）的进行搜索。如何定义NRT？

&emsp;&emsp;Elasticsearch依赖的Java库-Lucene中介绍了按段搜索（per-segment search）的概念。一个段类似一个倒排索引（inverted index），但是`index`在Lucene中的概念是"段的集合+ commit point"。 在一次提交（commit）后，一个新的段添加到commit point并且清空缓存（buffer is cleared）。

&emsp;&emsp;位于Elasticsearch和磁盘中间的是文件系统缓存（filesystem cache）。内存中的索引缓存（FIgure 1）会写入到一个新的段中（Figure 2）。新的段首先写入到文件系统缓存中（开销小）然后随后写入到磁盘中（开销大）。然而当文档还在缓存中时，它就可以被打开并且像其他文件读取。

> Figure 1. Lucene中新的文档内存中的索引缓存

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/lucene-in-memory-buffer.png">

&emsp;&emsp;Lucene允许新的段用于写入或者打开，使得这些段包含的段对搜索可见，并且不需要完整的提交（full commit）。相比较提交到磁盘这是一种轻量的操作，可以在不降低性能的情况下频繁的执行这些操作。

> Figure 2. 缓存的内容写入到了一个段中，并且这个段对搜索可见，但是这个段还没有提交

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/lucene-written-not-committed.png">

&emsp;&emsp;在Elasticsearch中，写入并打开一个新段成为`refresh`。一次refresh操作使得自上一次refresh后在索引上的所有操作对查询可见。你可以通过下面的方式来控制refresh：

- 等待刷新间隔（默认一秒）
- 设置[?refresh](####?refresh(api))选项
- 使用[Refresh API](####Refresh API)显示的完成一次refresh（POST \_refresh）

&emsp;&emsp;默认情况下，Elasticsearch每一秒周期性的执行refresh，但是只在最新30s内收到一次或者多次查询的索引上才会执行。这就是为什么说Elasticsearch是近实时搜索：文档的变化不会马上对搜索可见，但是在这个时间（timeframe）内变成可见。

### Paginate search results
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/paginate-search-results.html)

&emsp;&emsp;默认情况下，查询只会返回10条匹配的结果。你可以通过[search API](####Search API)中的`from + size`参数来通过翻页方式查询一个较大的数据集。`from`参数描述了结果集的一个起始位置，`size`参数描述了从`from`位置开始的size条结果。这两个参数的组合定义了一个页面的结果集。

```text
GET /_search
{
  "from": 5,
  "size": 20,
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  }
}
```

&emsp;&emsp;避免使用`from + size`来分页太深或一次请求太多结果。查询通常会扫描多个分片，每一个分片会将查询对应的命中的结果以及`from`之前的结果都读取到内存中。所以深度分页或者获取一个很大的结果集这些操作会极大地提高内存跟CPU的使用量，导致节点性能降低或者失败。

&emsp;&emsp;默认情况下，你不能通过`from + size`的方式来获得超过10000条的结果集。通过[index.max_result_window](####index.max_result_window)这个索引配置进行安全的限制。

&emsp;&emsp;如果你需要通过分页查询来获得超过10000的结果集，那么可以换成[search_after](###Search after)参数来实现。

>WARNING：Elasticsearch使用Lucene的内部文档id作为tie-breakers。相同数据的分片之间，这些内部文档id可能完全不同。在进行分页搜索时，您可能偶尔会看到具有相同排序值的文档排序不一致

#### Search after

&emsp;&emsp;你可以使用`search_after`参数和上一页的[sort values](###Sort search results)来检索retrieve下一页的结果。

&emsp;&emsp;使用`search_after`要求每次的查询都要有相同的`query`以及`sort value`。如果在多次的上述请求时发生了[refresh](###Near real-time search)，结果的顺序可能会改变，导致页面之间的结果不一致，为了避免这种情况，可以创建一个[point in time (PIT) ](####Point in time API)来保存搜索的当前索引状态。

```text
POST /my-index-000001/_pit?keep_alive=1m
```

&emsp;&emsp;这个接口返回一个PIT ID。

```text
{
  "id": "46ToAwMDaWR5BXV1aWQyKwZub2RlXzMAAAAAAAAAACoBYwADaWR4BXV1aWQxAgZub2RlXzEAAAAAAAAAAAEBYQADaWR5BXV1aWQyKgZub2RlXzIAAAAAAAAAAAwBYgACBXV1aWQyAAAFdXVpZDEAAQltYXRjaF9hbGw_gAAAAA=="
}
```

&emsp;&emsp;提交一次查询并带上`sort`参数来获得第一页的结果，如果使用PIT，在`pit.id`参数中指定PIT ID并且请求参数中移除目标data stream或者index。


>IMPORTANT：所有的PIT请求会带一个名为`_shard_doc`用于排序的内置的tiebreaker域，tiebreaker域也可以显示指定。如果你不能使用PIT，建议在`sort`中增加一个tiebreaker域。每篇文档中应该有一个tiebreaker域并且域值是唯一的。如果文档中没有tiebreaker域，分页结果中可能有丢失或者重复的数据。


>NOTE：当排序顺序是`_shard_doc`并且不需要命中总数时，Search after请求会得到优化并且查询更快。如果你想遍历所有的结果并且不关心排序，那么这是一个最高效的选项。

>IMPORTANT：如果`sort`域在一些目标data stream或者index中是[date](####Date field type)类型但是在其他目标data stream或者index是[date_nanos](####Date nanoseconds field type)类型，那么使用`numeric_type`参数和`format`参数为`sort`域指定一个[date format](####format(mapping parameter))，将`sort`域的值转化为相同格式的值（single resolution）。否则Elasticsearch 不会在每个请求中正确解释（interpret） search after 参数。

```text
GET /_search
{
  "size": 10000,
  "query": {
    "match" : {
      "user.id" : "elkbee"
    }
  },
  "pit": {
    "id":  "46ToAwMDaWR5BXV1aWQyKwZub2RlXzMAAAAAAAAAACoBYwADaWR4BXV1aWQxAgZub2RlXzEAAAAAAAAAAAEBYQADaWR5BXV1aWQyKgZub2RlXzIAAAAAAAAAAAwBYgACBXV1aWQyAAAFdXVpZDEAAQltYXRjaF9hbGw_gAAAAA==", 
    "keep_alive": "1m"
  },
  "sort": [ 
    {"@timestamp": {"order": "asc", "format": "strict_date_optional_time_nanos", "numeric_type" : "date_nanos" }}
  ]
}
```

&emsp;&emsp;第10行，这次查询对应的PIT ID。

&emsp;&emsp;第13行，`_shard_doc`上显示指定一个升序的tiebreak字段，为这次查询返回有序的结果。

&emsp;&emsp;查询响应中包含了一个数组，数组中的值是用于排序这条结果的`sort`的域值（排序值）。如果你使用了PIT，数组中最后一个排序值是一个tiebreaker。在使用PIT时，名为`_shard_doc`的 tiebreaker值会在每一次查询请求中自动的添加。`_shard_doc`的值是 PIT 中的分片索引和 Lucene 的内部 doc ID的组合值。你也可以在查询请求中显示指定tiebreaker实现自定义排序：

```text
GET /_search
{
  "size": 10000,
  "query": {
    "match" : {
      "user.id" : "elkbee"
    }
  },
  "pit": {
    "id":  "46ToAwMDaWR5BXV1aWQyKwZub2RlXzMAAAAAAAAAACoBYwADaWR4BXV1aWQxAgZub2RlXzEAAAAAAAAAAAEBYQADaWR5BXV1aWQyKgZub2RlXzIAAAAAAAAAAAwBYgACBXV1aWQyAAAFdXVpZDEAAQltYXRjaF9hbGw_gAAAAA==", 
    "keep_alive": "1m"
  },
  "sort": [ 
    {"@timestamp": {"order": "asc", "format": "strict_date_optional_time_nanos"}},
    {"_shard_doc": "desc"}
  ]
}
```

&emsp;&emsp;第10行，这次查询对应的PIT ID。

&emsp;&emsp;第13行，`_shard_doc`上显示指定一个降序的tiebreak字段，为这次查询返回有序的结果。

```text
{
  "pit_id" : "46ToAwMDaWR5BXV1aWQyKwZub2RlXzMAAAAAAAAAACoBYwADaWR4BXV1aWQxAgZub2RlXzEAAAAAAAAAAAEBYQADaWR5BXV1aWQyKgZub2RlXzIAAAAAAAAAAAwBYgACBXV1aWQyAAAFdXVpZDEAAQltYXRjaF9hbGw_gAAAAA==", 
  "took" : 17,
  "timed_out" : false,
  "_shards" : ...,
  "hits" : {
    "total" : ...,
    "max_score" : null,
    "hits" : [
      ...
      {
        "_index" : "my-index-000001",
        "_id" : "FaslK3QBySSL_rrj9zM5",
        "_score" : null,
        "_source" : ...,
        "sort" : [                                
          "2021-05-20T05:30:04.832Z",
          4294967298                              
        ]
      }
    ]
  }
}
```

&emsp;&emsp;第2行，Updated `id` for the point in time

&emsp;&emsp;第16行，最后一个结果对应的两个排序值

&emsp;&emsp;第18行，`pid_id`中每一篇文档的唯一的tiebreaker值

&emsp;&emsp;为了获得下一页的结果，使用上一次查询中最后一个结果对应的排序值（包含tiebreaker）作为`search_after`的参数。如果使用了一个PIT，那么在`pit.id`参数中使用最新的PIT ID。这次查询的`query`和`sort`参数必须不能被更改，`from`参数必须是0（默认值）或者`-1`。

```text
GET /_search
{
  "size": 10000,
  "query": {
    "match" : {
      "user.id" : "elkbee"
    }
  },
  "pit": {
    "id":  "46ToAwMDaWR5BXV1aWQyKwZub2RlXzMAAAAAAAAAACoBYwADaWR4BXV1aWQxAgZub2RlXzEAAAAAAAAAAAEBYQADaWR5BXV1aWQyKgZub2RlXzIAAAAAAAAAAAwBYgACBXV1aWQyAAAFdXVpZDEAAQltYXRjaF9hbGw_gAAAAA==", 
    "keep_alive": "1m"
  },
  "sort": [
    {"@timestamp": {"order": "asc", "format": "strict_date_optional_time_nanos"}}
  ],
  "search_after": [                                
    "2021-05-20T05:30:04.832Z",
    4294967298
  ],
  "track_total_hits": false                        
}
```

&emsp;&emsp;第10行，上一次查询中返回的PIT ID

&emsp;&emsp;第16行，上一次查询结果中最后一个结果的排序值

&emsp;&emsp;第20行，关闭追踪命中的总数来加速分页查询

&emsp;&emsp;你可以重复上述操作来获得额外的分页结果。如果使用了PIT，你可以在每一次查询请求中使用`keep_alive`参数来增加PIT的保留周期（retention period）。

&emsp;&emsp;当你结束查询时，你应该删除PIT。

```text
DELETE /_pit
{
    "id" : "46ToAwMDaWR5BXV1aWQyKwZub2RlXzMAAAAAAAAAACoBYwADaWR4BXV1aWQxAgZub2RlXzEAAAAAAAAAAAEBYQADaWR5BXV1aWQyKgZub2RlXzIAAAAAAAAAAAwBYgACBXV1aWQyAAAFdXVpZDEAAQltYXRjaF9hbGw_gAAAAA=="
}
```

#### Scroll search results

>IMPORTANT：我们不再建议使用scroll API进行深度分页查询。如果你需要保留索引状态（index state）同时分页查询10000+的结果，使用PIT的[search_after](####Search after)。

&emsp;&emsp;尽管一次查询请求返回的是单页（single page）的结果，`scroll` API 可以用于在单次查询请求中检索大量的结果（甚至是所有的结果），就像在传统数据库上使用游标一样。

&emsp;&emsp;Scrolling不是用来（intend for）实时用户请求（real time user request），而是用于处理大量的数据。 比如说为了将一个data stream或index的内容重新索引到具有不同配置的新的data stream或索引中。

> **Client support for scrolling and reindexing**
> Some of the officially supported clients provide helpers to assist with scrolled searches and reindexing:
>Perl
>    &emsp;&emsp;See [Search::Elasticsearch::Client::5_0::Bulk](https://metacpan.org/pod/Search::Elasticsearch::Client::5_0::Bulk) and [Search::Elasticsearch::Client::5_0::Scroll](https://metacpan.org/pod/Search::Elasticsearch::Client::5_0::Scroll)
>Python
>    &emsp;&emsp;See [elasticsearch.helpers.\*](https://elasticsearch-py.readthedocs.org/en/master/helpers.html)
>JavaScript
>    &emsp;&emsp;See [client.helpers.\*](https://www.elastic.co/guide/en/elasticsearch/client/javascript-api/current/client-helpers.html)

>NOTE：从scroll查询返回的数据反应了data stream或者index在最初的执行查询时的状态，就像是快照一样。接下来文档发生的变化（增加，更新或者删除）只有在下次新的scroll查询中生效。

&emsp;&emsp;为了能使用scrolling，最初的查询请求应该在query string中指定`scroll`参数，这个参数用来告诉Elasticsearch保留"search context"的时间（见[Keeping the search context alive](#####Keeping the search context alive)）， 比如`?scroll=1m`。

```text
POST /my-index-000001/_search?scroll=1m
{
  "size": 100,
  "query": {
    "match": {
      "message": "foo"
    }
  }
}
```

&emsp;&emsp;上述的查询结果中会包含一个`_scroll_id`，这个值需要传给`scroll` API用于检索下一批数据。

```text
POST /_search/scroll                                                               
{
  "scroll" : "1m",                                                                 
  "scroll_id" : "DXF1ZXJ5QW5kRmV0Y2gBAAAAAAAAAD4WYm9laVYtZndUQlNsdDcwakFMNjU1QQ==" 
}
```

&emsp;&emsp;第1行，可以使用`GET`或`POST`并且URL中不应该包含`index`的名字----索引的名字在最开始的查询中指定

&emsp;&emsp;第3行，`scroll`参数告诉Elasticsearch接着保留1分钟的search context

&emsp;&emsp;第4行，`scroll_id`参数

&emsp;&emsp;`size`参数允许你配置每次返回的结果数量上限。每次调用`scroll` API会返回下一批数据直到没有数据返回，比如`hits`数组为空。

>IMPORTANT：最初的查询请求以及每一个接下来的scroll请求都会返回`_scroll_id`。`_scroll_id`可能会发生变化，但是不总是每次都会变化。无论如何，应该使用最新收到的`_scroll_id`。

>NOTE：如果请求中指定了聚合（aggregation），只有最开始的查询响应中会包含聚合结果。

>NOTE：当排序字段是`_doc`时使用Scroll查询会得到优化。如果你想要遍历所有的结果而不关心结果顺序，这是最高效的选项：

```text
GET /_search?scroll=1m
{
  "sort": [
    "_doc"
  ]
}
```

##### Keeping the search context alive

&emsp;&emsp;scroll查询将返回最开始的查询请求匹配的所有文档。它会忽略随后对这些文档的更改。`scroll_id`定义了一个`search context`来保留一切（keep track of everything）使得Elasticsearch能正确的返回文档。这个`search context`在最开始的请求中创建并且通过随后的请求来保留这个`search context`。

&emsp;&emsp;`scroll`参数（传给`search`请求以及每一个`scroll`请求）告诉Elasticsearch保留search context的时间。该值（比如`1m`，见 [Time units](###API conventions)）不需要设置的太大来处理所有的数据--而是只需要足够长的时间来处理上一次的结果集。每一次`scroll`查询（使用`scroll`参数）设置一个新的过期时间。如果某次`scroll`请求没有传递`scroll`参数，那么释放`search context`回作为这次`scroll`请求的一部分。

&emsp;&emsp;通常来说，后台的合并程序会把较小的段合并为一个新的，更大的段来优化索引。一旦这些较小的段不再被使用就会被删除。在执行`scroll`查询时，合并既然会继续，但是`scroll`查询会阻止旧的段被删除，因为这些段正在被`scroll`查询使用。

> TIP：保留旧的段意味着需要更多的磁盘空间以及文件句柄（file handle）。保证你已经将你的节点配置了足够的空闲的文件句柄。见[File Descriptors](####File Descriptors)。

&emsp;&emsp;另外， 如果一个段中包含了被删除或者更新的文档，那么`search context`会在最开始的查询请求的那个时间点保持追踪（keep track）段中的每一篇文档是否都live 。如果你在一个索引上有很多open scroll并且这个索引还不断的删除或者更新文档，那么你要保证你的节点有足够的堆空间。

> NOTE：为了防止出现打开太多的scroll而产生问题，在超过一定的限制后。用户不允许再open scroll。默认情况下，open scroll的最大值是500.可以通过`search.max_open_scroll_context`来修改这个限制。

&emsp;&emsp; 你可以通过[note stats API](####Nodes stats API)来检查打开了多少个`search context`：

```text
GET /_nodes/stats/indices/search
```

##### Clear scroll

&emsp;&emsp;`search context`会在`scroll`超时后自动的移除。然而保留scroll打开需要一定的开销，如[previous section](#####Keeping the search context alive)中讨论的。所以一旦不需要scroll查询后 就可以通过`clear-scroll`API来显示的关闭：

```text
DELETE /_search/scroll
{
  "scroll_id" : "DXF1ZXJ5QW5kRmV0Y2gBAAAAAAAAAD4WYm9laVYtZndUQlNsdDcwakFMNjU1QQ=="
}
```

&emsp;&emsp;可以通过数组方式删除多个scroll IDs：

```text
DELETE /_search/scroll
{
  "scroll_id" : [
    "DXF1ZXJ5QW5kRmV0Y2gBAAAAAAAAAD4WYm9laVYtZndUQlNsdDcwakFMNjU1QQ==",
    "DnF1ZXJ5VGhlbkZldGNoBQAAAAAAAAABFmtSWWRRWUJrU2o2ZExpSGJCVmQxYUEAAAAAAAAAAxZrUllkUVlCa1NqNmRMaUhiQlZkMWFBAAAAAAAAAAIWa1JZZFFZQmtTajZkTGlIYkJWZDFhQQAAAAAAAAAFFmtSWWRRWUJrU2o2ZExpSGJCVmQxYUEAAAAAAAAABBZrUllkUVlCa1NqNmRMaUhiQlZkMWFB"
  ]
}
```

&emsp;&emsp;所有的`search context`都可以通过`_all`参数清除：

```text
DELETE /_search/scroll/_all
```

&emsp;&emsp;`scroll_id`可以作为一个query string参数或者在请求body中。多个scroll IDs使用逗号分隔：

```text
DELETE /_search/scroll/DXF1ZXJ5QW5kRmV0Y2gBAAAAAAAAAD4WYm9laVYtZndUQlNsdDcwakFMNjU1QQ==,DnF1ZXJ5VGhlbkZldGNoBQAAAAAAAAABFmtSWWRRWUJrU2o2ZExpSGJCVmQxYUEAAAAAAAAAAxZrUllkUVlCa1NqNmRMaUhiQlZkMWFBAAAAAAAAAAIWa1JZZFFZQmtTajZkTGlIYkJWZDFhQQAAAAAAAAAFFmtSWWRRWUJrU2o2ZExpSGJCVmQxYUEAAAAAAAAABBZrUllkUVlCa1NqNmRMaUhiQlZkMWFB
```

#### Sliced scroll

&emsp;&emsp;（未完成）

### Retrieve selected fields from a search
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-fields.html)

&emsp;&emsp;默认情况下，查询响应中的每一个命中的结果中都会包含文档的[\_source](####_source field)，它是一个整个JSON object，即被索引的文档。下面有两个推荐的方法在一次查询请求中检索选中的域（域）：

- 在index mapping中使用[fields option](####The fields option)展示要提取的域
- 在索引期间使用[\_source option](####The _source option)指定你需要访问的原始数据

&emsp;&emsp;你可以同时使用这些方法，`fields`这个方法更好些因为它能同时参考（consult）文档数据和index mappings。在某些情况下，你可能想要[other methods](####Other methods of retrieving data)来检索数据。

#### The fields option

&emsp;&emsp;若要在查询响应中检索指定的域，则使用`fields`参数。相比较引用`_source`，`fields`参数提供了几个有点，特别是，使用`fields`参数可以：

- 以一种标准化的方式返回每一个匹配mapping类型的值
- accepts [multi-fields ](####fields)和[field aliases](####Alias field type)
- 格式化日期和空间数据类型
- 检索出[runtime field values](####Retrieve a runtime field)
- 返回在索引期间通过脚本计算出的域
- 使用[lookup runtime fields](#####Retrieve fields from related indices)返回关联索引中的域

&emsp;&emsp;Other mapping options are also respected。包括[ignore_above](####ignore_above)，[ignore_malformed](####ignore_malformed)，[null_value](####null_value)。

&emsp;&emsp;`fields`选项返回的值的方式跟Elasticsearch索引它们的方式是匹配的。对于standard fields，`fields`选项在`_source`中找到值，然后解析并使用mappings进行format。

##### Retrieve specific fields

&emsp;&emsp;下面的查询请求使用`fields`参数检索`user.id`、`http.response`下所有的域、`@timestamp`这些域的值。

&emsp;&emsp;使用object notation，你可以传递一个[format](####format(mapping parameter))自定义时间或者地理空间的值的格式。

```text
POST my-index-000001/_search
{
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  },
  "fields": [
    "user.id",
    "http.response.*",         
    {
      "field": "@timestamp",
      "format": "epoch_millis" 
    }
  ],
  "_source": false
}
```

&emsp;&emsp;第10行，可以是完整的域名或者是通配符pattern。

&emsp;&emsp;第13行，使用`format`参数对域值进行format

> NOTE：默认情况下，当请求的`fields`选项使用了像`*`的通配符pattern，例如`_id`或者`_index`这些文档元数据是不会返回的。然而，当显示的使用这些域名时，`_id`、`_routing`、`_ignored`、`_index`、`_version`这些元数据域才返回。

##### Response always returns an array

&emsp;&emsp;`fields`响应中返回每一个域的数组值，即使有些域在`_source`是单值。这是因为Elasticsearch没有专门的数组类型，并且任何域都有可能存在多值。`fields`参数同样不能保证数组中的值有一定的顺序。见[arrays](####Arrays)的mapping文档了解更多背景。

&emsp;&emsp;响应中的`fields`块中包含一个平铺的list。因为`fields`参数不会获取整个object，只会返回leaf fields。

```text
{
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "my-index-000001",
        "_id" : "0",
        "_score" : 1.0,
        "fields" : {
          "user.id" : [
            "kimchy"
          ],
          "@timestamp" : [
            "4098435132000"
          ],
          "http.response.bytes": [
            1070000
          ],
          "http.response.status_code": [
            200
          ]
        }
      }
    ]
  }
}
```

##### Retrieve nested fields

&emsp;&emsp;使用`fields`返回[nested fields](####Nested field type)的响应中其他普通的object fields都少许不同。在普通的`object`fields 中的leaf values的返回内容是平铺的列表（flat list），而`nested field`返回的内容也是一个平铺的列表，列表中是分组为一个个object，每一个object是nested fields的一个域并且域值也是一个数组。多层的nested fields按照这个规则嵌套下去。

&emsp;&emsp;下面的mapping中，`user`是一个nested field，索引下面的文档并检索`user`field中所有的域：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "group" : { "type" : "keyword" },
      "user": {
        "type": "nested",
        "properties": {
          "first" : { "type" : "keyword" },
          "last" : { "type" : "keyword" }
        }
      }
    }
  }
}

PUT my-index-000001/_doc/1?refresh=true
{
  "group" : "fans",
  "user" : [
    {
      "first" : "John",
      "last" :  "Smith"
    },
    {
      "first" : "Alice",
      "last" :  "White"
    }
  ]
}

POST my-index-000001/_search
{
  "fields": ["*"],
  "_source": false
}
```

&emsp;&emsp;下面的响应中，对`first`和`last`名字进行了分组而不是平铺出来。

```text
{
  "took": 2,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 1,
      "relation": "eq"
    },
    "max_score": 1.0,
    "hits": [{
      "_index": "my-index-000001",
      "_id": "1",
      "_score": 1.0,
      "fields": {
        "group" : ["fans"],
        "user": [{
            "first": ["John"],
            "last": ["Smith"]
          },
          {
            "first": ["Alice"],
            "last": ["White"]
          }
        ]
      }
    }]
  }
}
```

&emsp;&emsp;nested field会根据他们的nested path进行分组，不受检索时使用的pattern的影响。例如，如果你在上面的例子中只查询`user.first`：

```text
POST my-index-000001/_search
{
  "fields": ["user.first"],
  "_source": false
}
```

&emsp;&emsp;响应中只返回user的first name，但是仍然是上面的例子的结构：

```text
{
  "took": 2,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 1,
      "relation": "eq"
    },
    "max_score": 1.0,
    "hits": [{
      "_index": "my-index-000001",
      "_id": "1",
      "_score": 1.0,
      "fields": {
        "user": [{
            "first": ["John"]
          },
          {
            "first": ["Alice"]
          }
        ]
      }
    }]
  }
}
```

&emsp;&emsp;然而，如果`fields`中的目标只填写`user`域，那不会返回任何值因为没法匹配任何一个leaf fields。

##### Retrieve unmapped fields

&emsp;&emsp;默认情况下，`fields`参数只返回mapped fields的值，然而，Elasticsearch允许存储`_source`中的域，并且这些域是unmapped。 例如设置[dynamic  field mapping](####Dynamic field mapping)的值为`false`或者使用一个object域并且设置`enabled`为`false`。这些选项使得关闭解析并且不索引object的内容。

&emsp;&emsp;若要在object中检索`_source`中的unmapped域，那么在`fields`中 使用`include_unmapped`选项：

```text
PUT my-index-000001
{
  "mappings": {
    "enabled": false 
  }
}

PUT my-index-000001/_doc/1?refresh=true
{
  "user_id": "kimchy",
  "session_data": {
     "object": {
       "some_field": "some_value"
     }
   }
}

POST my-index-000001/_search
{
  "fields": [
    "user_id",
    {
      "field": "session_data.object.*",
      "include_unmapped" : true 
    }
  ],
  "_source": false
}
```

&emsp;&emsp;第4行，关闭所有的mapping

&emsp;&emsp;第24行，包含匹配这个field pattern的unmapped fields

&emsp;&emsp;这个响应会包含`session_data.object.*`路径中的域值，即使这些域是unmapped。`user.id`同样是unmapped，但是没有出现在响应中因为没有为那个field pattern设置`include_unmapped`。

```text
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "my-index-000001",
        "_id" : "1",
        "_score" : 1.0,
        "fields" : {
          "session_data.object.some_field": [
            "some_value"
          ]
        }
      }
    ]
  }
}
```


##### Ignored field values

&emsp;&emsp;响应中的`fields`块中只返回合法索引的值。如果你的查清请求要求的域值忽略某些值，这些值是malformed或者太大，这些域值会在另一个`ignored_field_values`块中返回。

&emsp;&emsp;在这个例子中，我们索引了一篇文档，文档中的一个值被限制了长度导致没有添加到索引中，所以在查询请求中分开的显示：

```text
PUT my-index-000001
{
  "mappings": {
    "properties": {
      "my-small" : { "type" : "keyword", "ignore_above": 2 }, 
      "my-large" : { "type" : "keyword" }
    }
  }
}

PUT my-index-000001/_doc/1?refresh=true
{
  "my-small": ["ok", "bad"], 
  "my-large": "ok content"
}

POST my-index-000001/_search
{
  "fields": ["my-*"],
  "_source": false
}
```

&emsp;&emsp;第5行，这个域有长度限制

&emsp;&emsp;第13行，这个文档有一个域值超过了长度限制所以没有被索引

&emsp;&emsp;在这个响应中，在`ignored_field_values`路径中包含了被限制了长度的域值。这个域值是从文档的原始JSON source中检索出来的并且就是原始的数据，不会被formatted或者任何方式处理。不同于成功写入到索引的域，它们的域值在`fields`块中返回。

```text
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "my-index-000001",
        "_id" : "1",
        "_score" : 1.0,
        "_ignored" : [ "my-small"],
        "fields" : {
          "my-large": [
            "ok content"
          ],
          "my-small": [
            "ok"
          ]
        },
        "ignored_field_values" : {
          "my-small": [
            "bad"
          ]
        }
      }
    ]
  }
}
```

#### The \_source option

&emsp;&emsp;你可以使用`_source`参数选择返回source中的哪些域。这种称为`source filtering`。

&emsp;&emsp;下面的search API请求设置了请求body参数`_source`为`false`。故响应中没有包括document source。

```text
GET /_search
{
  "_source": false,
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  }
}
```

&emsp;&emsp;若要返回一部分source fields，可以在`_source`参数中指定 一个通配符（`*`）pattern。下面的search API请求只返回`obj`域和它的properties。

```text
GET /_search
{
  "_source": "obj.*",
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  }
}
```

&emsp;&emsp;你也可以在`_source`中指定一个通配符pattern数组。下面的search API请求只返回`obj1`和`obj2`域和它们的properties。

```text
GET /_search
{
  "_source": [ "obj1.*", "obj2.*" ],
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  }
}
```

&emsp;&emsp;为了更好的控制，你可以在`_source`中指定一个object，里面包含`includes`和`excludes` patterns。

&emsp;&emsp;如果使用了`includes`属性，只返回匹配它里面的pattern的域。你可以使`excludes`从这个子集中进一步排除。

&emsp;&emsp;如果没有指定`includes`属性，所有的document source都会返回，除了匹配`excludes`中的pattern的域。

&emsp;&emsp;下面的search API请求返回`obj1`和`obj2`的域，但是排除所有child field为`description`的域。

```text
GET /_search
{
  "_source": {
    "includes": [ "obj1.*", "obj2.*" ],
    "excludes": [ "*.description" ]
  },
  "query": {
    "term": {
      "user.id": "kimchy"
    }
  }
}
```

#### Other methods of retrieving data

&emsp;&emsp;通常来说使用`field`是比较好的，除非你必须一定要载入stored或者`docvalue_fields`。

&emsp;&emsp;文档的`_source`在Lucene中作为单独一个域存储的。这种结构意味着需要加载整个`_source`对象，即使你只需要里面的部分内容。为了避免这种情况，你可以尝试其他办法加载域：

- 使用[docvalue_fields](#####Doc value fields)参数获取选择的域的域值。当需要返回较小数量的域时是一个好的选择，支持doc values例如keyword和date。
- 使用[stored_fields](####Stored fields-1)获取stored field的域值（使用了[store](####store) mapping parameter的域）

&emsp;&emsp;Elasticsearch总是尝试从`_source`中加载域值。这种方式跟source filtering是一样的实现方式，即Elasticsearch需要加载并解析整个`_source`进行检索，即使只需要获取某一个域。

##### Doc value fields

&emsp;&emsp;你可以使用[docvalue_fields](#####Doc value fields)参数在查询响应中返回一个或多个[dov values](####doc_values)的域。

&emsp;&emsp;Doc Values跟`_source`一样都会存储，但以列式存储的数据结构存放，用于排序和聚合。由于每一个域都是各自独立存储，Elasticsearch只需要读取请求中指定的域，并能避免加载整个文档`_source`。

&emsp;&emsp;Doc values are stored for supported fields by default。然而，doc value不支持[text](####Text type family)或者[text_annotated](https://www.elastic.co/guide/en/elasticsearch/plugins/8.2/mapper-annotated-text-usage.html)域。

&emsp;&emsp;下面的查询请求使用`docvalue_fields`参数检索user.id，所有以`http.response`开头的以及`@timestamp`的doc value。

```text
GET my-index-000001/_search
{
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  },
  "docvalue_fields": [
    "user.id",
    "http.response.*", 
    {
      "field": "date",
      "format": "epoch_millis" 
    }
  ]
}
```

&emsp;&emsp;第10行，同时支持完整的域名和通配符pattern

&emsp;&emsp;第13行，使用object notation，你可以传递一个`format`参数指定自定义的format应用到doc value上。[Date fields](####Date field type)支持[date format](####format(mapping parameter))，[Numeric fields](####Numeric field types)支持[DecimalFormat pattern](https://docs.oracle.com/javase/8/docs/api/java/text/DecimalFormat.html)。其他的域不支持`format`参数。

> TIP：你不可以为nested object使用`docvalue_fields`参数 来检索doc value。如果你指定了一个nested object，则返回一个空的数组。若要访问nested fields，使用[inner_hits](###Retrieve inner hits)参数中的`docvalue_fields`属性

##### Stored fields

&emsp;&emsp;

##### Script fields

### Retrieve inner hits
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/inner-hits.html)

### Search across clusters
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-cross-cluster-search.html)

&emsp;&emsp;**Cross-cluster search**能让你对一个或者多个集群运行单个查询请求。例如你可以使用CCS（Cross-cluster Search）对存储在不同的数据中心的集群上的日志数据进行过滤和分析。

#### Supported APIs

&emsp;&emsp;下面的APIs支持CCS：

- [Search](####Search API)
- [Async search](####Async search)
- [Multi search](####Multi search API)
- [Search template](###Search templates)
- [Multi search template](####Multi search template API)
- [Field capabilities](####Field capabilities API)
- preview[EQL search](####EQL search API)
- preview[SQL search](####SQL search API)
- preview[Vector tile search](####Vector tile search API)

#### Prerequisites

- CCS要求有remote cluster。若要在Elasticsearch服务上设置remote cluster，见[configure remote clusters on Elasticsearch Service](https://www.elastic.co/guide/en/cloud/current/ec-enable-ccs.html)，如果你在自己的硬件上运行Elasticsearch，见[Remote clusters](###Remote clusters)。

- 确保你的remote cluster配置支持CCS，见[Supported cross-cluster search configurations](####Supported cross-cluster search configurations)

- 本地的coordinating node必须要有[remote_cluster_client](######Remote-eligible node)角色

- 如果你使用[sniff mode](#####Sniff mode)，本地的coordinating node必须能够连接到remote cluster上的seed和gateway节点
  - 我们建议使用gateway node，它具有coordinating node的服务能力。seed node可以是这些gateway node的一个子集

- 如果你使用[proxy mode](#####Proxy mode)，本地的coordinating node必须能够连接配置的`proxy_address`。这个地址必须能够路由到remote cluster上的gateway和coordinating节点

- CCS在本地集群和remote cluster上要求有不同的security privilege。见[Configure privileges for cross-cluster search](#####Configure privileges for cross-cluster search)和[Configure privileges for cross-cluster search and Kibana](####Configure privileges for cross-cluster search and Kibana)

#### Cross-cluster search examples

##### Remote cluster setup

&emsp;&emsp;下面的[cluster update settings](####Cluster update settings API) API请求添加了三个remote集群：`cluster_one`，`cluster_two`，`cluster_three`。

```text
PUT _cluster/settings
{
  "persistent": {
    "cluster": {
      "remote": {
        "cluster_one": {
          "seeds": [
            "127.0.0.1:9300"
          ]
        },
        "cluster_two": {
          "seeds": [
            "127.0.0.1:9301"
          ]
        },
        "cluster_three": {
          "seeds": [
            "127.0.0.1:9302"
          ]
        }
      }
    }
  }
}
```

##### Search a single remote cluster

&emsp;&emsp;在查询请求中，指定remote cluster上的data stream以及索引：`<remote_cluster_name>:<target>`。

&emsp;&emsp;下面的[search](####Search API) API请求在名为`cluster_one`的单个remote cluster上查询索引`my-index-000001`。

```text
GET /cluster_one:my-index-000001/_search
{
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  },
  "_source": ["user.id", "message", "http.response.status_code"]
}
```

&emsp;&emsp;API返回下面的响应：

```text
{
  "took": 150,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "failed": 0,
    "skipped": 0
  },
  "_clusters": {
    "total": 1,
    "successful": 1,
    "skipped": 0
  },
  "hits": {
    "total" : {
        "value": 1,
        "relation": "eq"
    },
    "max_score": 1,
    "hits": [
      {
        "_index": "cluster_one:my-index-000001", 
        "_id": "0",
        "_score": 1,
        "_source": {
          "user": {
            "id": "kimchy"
          },
          "message": "GET /search HTTP/1.1 200 1070000",
          "http": {
            "response":
              {
                "status_code": 200
              }
          }
        }
      }
    ]
  }
}
```

&emsp;&emsp;第23行，查询响应中的`_index`参数包含了remote cluster的名字

##### Search multiple remote clusters

&emsp;&emsp;下面的[search](###Search APIs) API请求在三个集群上查询索引`my-index-000001`：

- 你的本地集群（local cluster）
- 两个远端集群（remote cluster），`cluster_one`和`cluster_two`

```text
GET /my-index-000001,cluster_one:my-index-000001,cluster_two:my-index-000001/_search
{
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  },
  "_source": ["user.id", "message", "http.response.status_code"]
}
```

&emsp;&emsp;API返回下面的响应：

```text
{
  "took": 150,
  "timed_out": false,
  "num_reduce_phases": 4,
  "_shards": {
    "total": 3,
    "successful": 3,
    "failed": 0,
    "skipped": 0
  },
  "_clusters": {
    "total": 3,
    "successful": 3,
    "skipped": 0
  },
  "hits": {
    "total" : {
        "value": 3,
        "relation": "eq"
    },
    "max_score": 1,
    "hits": [
      {
        "_index": "my-index-000001", 
        "_id": "0",
        "_score": 2,
        "_source": {
          "user": {
            "id": "kimchy"
          },
          "message": "GET /search HTTP/1.1 200 1070000",
          "http": {
            "response":
              {
                "status_code": 200
              }
          }
        }
      },
      {
        "_index": "cluster_one:my-index-000001", 
        "_id": "0",
        "_score": 1,
        "_source": {
          "user": {
            "id": "kimchy"
          },
          "message": "GET /search HTTP/1.1 200 1070000",
          "http": {
            "response":
              {
                "status_code": 200
              }
          }
        }
      },
      {
        "_index": "cluster_two:my-index-000001", 
        "_id": "0",
        "_score": 1,
        "_source": {
          "user": {
            "id": "kimchy"
          },
          "message": "GET /search HTTP/1.1 200 1070000",
          "http": {
            "response":
              {
                "status_code": 200
              }
          }
        }
      }
    ]
  }
}
```

&emsp;&emsp;第24行，这篇文档的`_index`参数没有集群的名字，说明这个文档来自本地集群

&emsp;&emsp;第41行，这篇文档来自`cluster_one`

&emsp;&emsp;第58行，这篇文档来自`cluster_two`

#### Optional remote clusters

&emsp;&emsp;默认情况下，如果请求中的某个remote cluster返回了一个错误或者不可用（unavailable），那么CCS就会失败。使用cluster setting `skip_unavailable`标记特定的remote cluster在CCS时是可选的。

&emsp;&emsp;如果`skip_unavailable`为`true`。CCS将：

- 在搜索过程中如果节点不可用则跳过这个remote cluster。响应中的`_cluster.skipped`的值包含跳过的集群数量
- 忽略remote cluster返回的错误，比如不可用的分片或者索引的相关错误。这可能包含跟查询参数例如[allow_no_indices](#####allow_no_indices)以及[ignore_unavailable](#####ignore_unavailable)相关的错误
- 在查询remote cluster时，忽略[allow_partial_search_results](######allow_partial_search_results)参数和相关集群设置`search.default_allow_partial_results`。这意味着remote cluster可能返回部分结果

&emsp;&emsp;下面的[cluster update settings ](####Cluster update settings API) API请求将`cluster_two`的`skip_unavailable`设置为`true`。

```text
PUT _cluster/settings
{
  "persistent": {
    "cluster.remote.cluster_two.skip_unavailable": true
  }
}
```

&emsp;&emsp;如果`cluster_two`在CCS期间失去连接或者不可用。Elasticsearch不会在最终的结果中包含在那个集群中匹配到的文档。

#### How cross-cluster search handles network delays

&emsp;&emsp;由于CCS涉及到发送请求到remote cluster上，任何的网络延迟都会影响查询速度。为了避免慢查询（slow search），CCS提供了两个的方法来处理网络延迟：

- [Minimize network roundtrips](#####Minimize network roundtrips)
  - 默认情况下，Elasticsearch会降低remote  cluster之间的网络往返（network roundtrip）的次数，能降低网络延迟对查询速度的影响。然而，Elasticsearch不能为large search request降低网络往返，比如[scroll](####Scroll search results)或[inner hits](###Retrieve inner hits)
  - 见[Minimize network roundtrips](#####Minimize network roundtrips)了解其工作方式
- [Don’t minimize network roundtrips](#####Don’t minimize network roundtrips)
  - 对于scroll或者inner hits这种查询，Elasticsearch会像每一个remote cluster发送多个传出（outgoing）传入（ingoing）请求。你可以将参数[ccs_minimize_roundtrips](######ccs_minimize_roundtrips)设置为`false`来选择当前的方法。While typically slower, this approach may work well for networks with low latency
  - 见[Don’t minimize network roundtrips](#####Don’t minimize network roundtrips)了解其工作方式

##### Minimize network roundtrips

&emsp;&emsp;下面是在你minimize network roundtrips后，CCS的工作方式。

1. 你发送一个CCS请求到你的本地集群。集群中的coordinating node接收并解析这个请求。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ccs-min-roundtrip-client-request.svg">

2. coordinating node向每一个集群发送一个查询请求，包括本地的集群。每一个集群各自独立的执行这个查询请求，将它们自己的cluster-level的设置应用到这个请求上。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ccs-min-roundtrip-cluster-search.svg">

3. 每一个remote cluster将其查询结果返回到coordinating node。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ccs-min-roundtrip-cluster-results.svg">

4. 从每一个集群收集完结果后，coordinating node将最终的结果到CCS的响应中。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ccs-min-roundtrip-client-response.svg">

##### Don’t minimize network roundtrips

&emsp;&emsp;下面是没有minimize network roundtrips后，CCS的工作方式。

1. 你发送一个CCS请求到你的本地集群。集群中的coordinating node接收并解析这个请求。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ccs-min-roundtrip-client-request.svg">

2. coordinating node向每一个remote cluster发送一个[search shards](####Search shards API) API请求。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ccs-min-roundtrip-cluster-search.svg">

3. 每一个remote cluster将其响应发送回coordinating node。这个响应中包含了CCS将会执行索引和分片信息。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ccs-min-roundtrip-cluster-results.svg">

4. coordinating node发送一个查询请求到每一个分片，包括它所在的集群。每一个分片各自独立的执行查询请求。

> WARNING：当没有最小化网络往返时，这个查询的执行过程就像是所有的数据都在coordinating node的集群中。我们建议更新cluster-level的设置来限制查询，例如`action.search.shard_count.limit`，`pre_filter_shard_size`和`max_concurrent_shard_requests`。如果限制值太低，查询可能被reject。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ccs-dont-min-roundtrip-shard-search.svg">

5. 每一个分片将其查询结果返回到coordinating node。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ccs-dont-min-roundtrip-shard-results.svg">

6.  从每一个集群收集完结果后，coordinating node将最终的结果到CCS的响应中。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ccs-min-roundtrip-client-response.svg">

#### Supported cross-cluster search configurations

&emsp;&emsp;在8.0+中，Elastic支持本地从本地集群到remote cluster的查询的版本：

- 之前的minor版本
- 相同的版本
- 相同major版本下更新的minor版本

&emsp;&emsp;Elastic同样支持从本地集群（某个major版本中的最新的minor版本）到remote cluster（下一个major版本中的所有minor版本）的查询。例如，7.17版本的本地集群可以从8.x的任意版本的集群上查询。

|                           | **Remote cluster version** |          |      |      |      |      |
| :-----------------------: | :------------------------: | :------: | :--: | :--: | ---- | ---- |
| **Local cluster version** |            6.8             | 7.1–7.16 | 7.17 | 8.0  | 8.1  | 8.2  |
|            6.8            |             √              |    √     |  √   |  ×   | ×    | ×    |
|         7.1–7.16          |             √              |    √     |  √   |  ×   | ×    | ×    |
|           7.17            |             √              |    √     |  √   |  √   | √    | √    |
|            8.0            |             ×              |    ×     |  √   |  √   | √    | √    |
|            8.1            |             ×              |    ×     |  ×   |  √   | √    | √    |
|            8.2            |             ×              |    ×     |  ×   |  ×   | √    | √    |


> IMPORTANT：对于[EQL search API](####EQL search API)，本地和远端集群必须使用相同的Elasticsearch版本。

&emsp;&emsp;例如，8.0的本地集群可以查询7.17和所有8.x的集群。然而，8.0的本地集群不能查询7.16或者6.8的集群。

&emsp;&emsp;所有集群都有的功能才能被支持。在某个remote cluster使用某个不具备的功能会导致undefined behavior。

&emsp;&emsp;CCS中使用了不支持的配置可能可以正常工作。然而，Elastic没有测试过这种查询，不能保证其行为能正确工作。

##### Ensure cross-cluster search support

&emsp;&emsp;保证支持CCS最简单的方式就是每一个集群保持相同的版本。如果需要维护不同版本的集群，你可以：

- 维护一个专用的集群用于CCS。Keep this cluster on the earliest version needed to search the other clusters。例如，如果你有7.17和8.x集群，你可以维护一个专用的7.17集群作为本地集群用于CCS。
- 让每一个集群之间保持不超过一个minor版本的差异。这样能让你使用任意一个集群作为本地集群用于CCS。

##### Cross-cluster search during an upgrade

&emsp;&emsp;在本地集群上执行rolling upgrade时仍然可以查询remote cluster。然而，本地集群中的coordinating node的"upgrade from"和"upgrade to"的版本必须和remote cluster上的gateway node兼容。

> WARNING：Running multiple versions of Elasticsearch in the same cluster beyond the duration of an upgrade is not supported。

&emsp;&emsp;见[Upgrading Elasticsearch](https://www.elastic.co/guide/en/elastic-stack/8.2/upgrading-elasticsearch.html)了解更多升级信息。

### Search multiple data streams and indices
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-multiple-indices.html)

&emsp;&emsp;若要搜索多个data stream和索引，则使用逗号隔开添加到[search API](####Search API)请求路径上。

&emsp;&emsp;下面的请求查询索引`my-index-000001`和`my-index-000002`。

```text
GET /my-index-000001,my-index-000002/_search
{
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  }
}
```

&emsp;&emsp;你也可以使用一个index pattern查询多个data stream和索引。

&emsp;&emsp;下面的请求目标是`my-index-*`的index pattern。这个请求会查询集群中名字以`my-index-`开头的data stream或者索引。

```text
GET /my-index-*/_search
{
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  }
}
```

&emsp;&emsp;若要查询集群中所有的data stream和索引，将请求参数的目标改为`_all`或者`*`。

&emsp;&emsp;下面的请求都是相同的，会查询集群中所有的data stream和index。

```text
GET /_search
{
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  }
}

GET /_all/_search
{
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  }
}

GET /*/_search
{
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  }
}
```

#### Index boost

&emsp;&emsp;在查询多个索引索引时，你可以使用`indices_boost`参数boost一个或多个索引的结果。适用于从某些索引返回的结果比其他索引中的结果重要的场景。

> NOTE：你不能对data stream使用`indices_boost`。

```text
GET /_search
{
  "indices_boost": [
    { "my-index-000001": 1.4 },
    { "my-index-000002": 1.3 }
  ]
}
```

&emsp;&emsp;也可以用于aliases和index pattern：

```text
GET /_search
{
  "indices_boost": [
    { "my-alias":  1.4 },
    { "my-index*": 1.3 }
  ]
}
```

&emsp;&emsp;如果匹配到了多个，则使用先匹配到的对应的boost。例如，如果某个索引属于`alias1`同时匹配了`my-index*` pattern，那么 boost value为`1.4`。

### Search shard routing
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-shard-routing.html#search-adaptive-replica)

&emsp;&emsp;为了应对硬件故障以及提高搜索能力（search capacity），Elasticsearch会在多个节点上存储索引数据对应的多个副本分片。当允许一个查询请求时，Elasticsearch会选择一个包含索引数据副本的节点并且将这个查询请求转发到那个节点上的分片上。这个处理过程就是众所周知的`search shard routing`或者`routing`。

#### Adaptive replica selection

&emsp;&emsp;默认情况下，Elasticsearch使用`adaptive replica selection`来路由查询请求。这个方法使用[shard allocation awareness](####Cluster-level shard allocation and routing settings)和下面的条件来选择有资格的节点（eligible node）：

- coordinating node和eligible node之间上一次的响应时间
- eligible node上一次（previous）查询花费的时间
- eligible node的`search` [threadpool](####Thread pools) 的队列大小

&emsp;&emsp;Adaptive replica selection被设计为用于降低查询延迟。你可以使用[cluster settings API](####Cluster update settings API)设置`cluster.routing.use_adaptive_replica_selection`为`false`来关闭它。如果关闭了adaptive replica selection，Elasticsearch使用轮询（round-robin）方式来路由查询请求，这可能会导致查询缓慢。

#### Set a preference

&emsp;&emsp;默认情况下，adaptive replica selection从所有的eligible node和分片中进行选择。然而你可能只想从一个本地节点（local node）获取数据或者基于硬件因素将查询路由到指定的节点。或者你想要将重复的查询（repeated searcher）发送到同一个分片，使得可以利用cache。

&emsp;&emsp;若要为查询请求限制eligible node和分片集合，你可以使用查询的API's的参数[preference](######preference)。

&emsp;&emsp;例如，下面的查询请求中使用了`preference`为`_local`的参数来查询索引`my-index-000001`。它将查询限制到本地节点（local node）。如果local node没有这个索引数据的分片，这个请求会使用adaptive replica selection去其他eligible node，这个节点作为一个fallback。

```text
GET /my-index-000001/_search?preference=_local
{
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  }
}
```

&emsp;&emsp;你也可以使用`preference`参数并基于提供的值将查询路由到指定的分片。如果集群状态（cluster state）和选择的分片没有发生变更，查询会使用相同的`preference`的值以相同的顺序被路由到相同的分片。

&emsp;&emsp;我们建议使用一个独一无二的`preference`值，例如用户的名字或者web session ID。这个值不能以`_`开头。

> TIP：你可以使用这个方法为频繁的以及资源密集（resource-intensive）的查询使用缓存结果。如果分片没有发生变更，使用相同`preference`值的重复的查询会从相同的[shard request cache](####Shard request cache settings)中检索到结果。对于时序用例，比如说日志，旧的索引中的数据几乎不会更新，就可以从这个cache中直接返回结果

&emsp;&emsp;下面的查询请求使用了值为`my-custom-shard-string`的`preference`去索引`my-index-000001`上进行查询。

```text
GET /my-index-000001/_search?preference=my-custom-shard-string
{
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  }
}
```

> NOTE：如果集群状态和选择的分片发生了变更，相同的`preference`的查询可能不会以相同的顺序路由到相同的分片。导致这个的原因很多，包括分片重新分配（shard relocation）以及分片错误（shard failure）。节点也会reject一个查询请求，Elasticsearch会将它路由到其他的节点。

#### Use a routing value

&emsp;&emsp;当你索引一篇文档时，你可以指定一个可选的[routing value](####\_routing field
)，这样会将这篇文档索引到一个指定的分片上。

&emsp;&emsp;例如，下面的请求使用`my-routing-value`来路由一篇文档。

```text
POST /my-index-000001/_doc?routing=my-routing-value
{
  "@timestamp": "2099-11-15T13:12:00",
  "message": "GET /search HTTP/1.1 200 1070000",
  "user": {
    "id": "kimchy"
  }
}
```

&emsp;&emsp;你可以在查询的API's参数`routing`中使用相同的路由值。这能保证在相同的分片上查询。

```text
GET /my-index-000001/_search?routing=my-routing-value
{
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  }
}
```

&emsp;&emsp;你可以指定多个用逗号分隔的路由值：

```text
GET /my-index-000001/_search?routing=my-routing-value,my-routing-value-2
{
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  }
}
```

#### Search concurrency and parallelism

&emsp;&emsp;默认情况下，Elasticsearch不会基于请求命中的分片数量来reject这个查询请求。然而命中大量的分片会带来大量的CPU和内存的使用。

> TIP：为了防止索引有大量的分片，见[Avoid oversharding](###Avoid oversharding)。

&emsp;&emsp;你可以使用名为`query parameter`的查询参数（query parameter）来控制一个查询请求在一个节点上并发查询的分片数量。 这可以防止某个请求过度消耗（overloading）某个集群。这个查询参数的默认最大值是`5`。

```text
GET /my-index-000001/_search?max_concurrent_shard_requests=3
{
  "query": {
    "match": {
      "user.id": "kimchy"
    }
  }
}
```

&emsp;&emsp;你也可以使用集群设置`action.search.shard_count.limit`来限制查询命中的分片数量，在命中太多分片后就reject这个请求。你可以使用[cluster settings API](####Cluster update settings API)来配置`action.search.shard_count.limit`。

### Search templates
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-template.html)

&emsp;&emsp;search template一种查询模版并且保存在Elasticsearch中-，你可以使用不同的变量覆盖search template中的变量。

&emsp;&emsp;如果你使用Elasticsearch作为查询后端（search backend），你可以把用户在搜索框的输入作为search template的变量。这使得你不需要将Elasticsearch的查询语法暴露给用户而执行查询。

&emsp;&emsp;如果你使用Elasticsearch用于自定义的应用，search template可以让你在不更改你的应用代码的情况下修改你的查询（DSL）。

#### Create a search template

&emsp;&emsp;可以使用[create stored script API](####Create or update stored script API)创建或者更新一个search template。

&emsp;&emsp;请求中的`source`支持[search API](####Search API)的请求body中相同的参数。`source`同样支持[Mustache](https://mustache.github.io/)变量，通常用双括号包裹：`{{my-var}}`。当你允许某个template search，Elasticsearch会使用`params`中的值替换这些变量。

&emsp;&emsp;search template必须使用`mustache`语法。

&emsp;&emsp;下面的请求创建了一个`id`为`my-search-template`的search template。

```text
PUT _scripts/my-search-template
{
  "script": {
    "lang": "mustache",
    "source": {
      "query": {
        "match": {
          "message": "{{query_string}}"
        }
      },
      "from": "{{from}}",
      "size": "{{size}}"
    },
    "params": {
      "query_string": "My query string"
    }
  }
}
```

&emsp;&emsp;Elasticsearch存储search template作为集群状态中的Mustache [scripts](##Scripting)。Elasticsearch在`template` script context中编译search template。设置中限制或者关闭script同样会影响search template。

#### Validate a search template

&emsp;&emsp;使用[render search template API](####Render search template API)以及不同的`params`测试一个模板。

```text
POST _render/template
{
  "id": "my-search-template",
  "params": {
    "query_string": "hello world",
    "from": 20,
    "size": 10
  }
}
```

&emsp;&emsp;替换变量后（rendered），template输出一个[search request body](####Search API)。

```text
{
  "template_output": {
    "query": {
      "match": {
        "message": "hello world"
      }
    },
    "from": "20",
    "size": "10"
  }
}
```

&emsp;&emsp;你也可以使用API来测试inline template。

```text
POST _render/template
{
    "source": {
      "query": {
        "match": {
          "message": "{{query_string}}"
        }
      },
      "from": "{{from}}",
      "size": "{{size}}"
    },
  "params": {
    "query_string": "hello world",
    "from": 20,
    "size": 10
  }
}
```

#### Run a templated search

&emsp;&emsp;可以使用[search template API](####Search template API)运行一个search template。你可以在每一次的请求中指定不同的`params`。

```text
GET my-index/_search/template
{
  "id": "my-search-template",
  "params": {
    "query_string": "hello world",
    "from": 0,
    "size": 10
  }
}
```

&emsp;&emsp;响应中返回的属性跟[search API](####Search API)的响应是一样的。

```text
{
  "took": 36,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 1,
      "relation": "eq"
    },
    "max_score": 0.5753642,
    "hits": [
      {
        "_index": "my-index",
        "_id": "1",
        "_score": 0.5753642,
        "_source": {
          "message": "hello world"
        }
      }
    ]
  }
}
```

#### Run multiple templated searches

&emsp;&emsp;可以使用[multi search template API](####Multi search template API)在单个请求中运行多个template search。相比较多个独立的请求，这种方式的请求的开销更小并且速度更快。

```text
GET my-index/_msearch/template
{ }
{ "id": "my-search-template", "params": { "query_string": "hello world", "from": 0, "size": 10 }}
{ }
{ "id": "my-other-search-template", "params": { "query_type": "match_all" }}
```

#### Get search templates

&emsp;&emsp;可以使用[get stored script API](####Get stored script API)检索search template。

```text
GET _scripts/my-search-template
```

&emsp;&emsp;可以使用[cluster state API](####Cluster state API)获取所有的search template列表以及其他存储的脚本。

```text
GET _cluster/state/metadata?pretty&filter_path=metadata.stored_scripts
```

#### Delete a search template

&emsp;&emsp;可以使用[delete stored script API](####Delete stored script API)删除一个search template。

```text
DELETE _scripts/my-search-template
```

#### Set default values

&emsp;&emsp;使用下面的语法为变量设置一个默认值：

```text
{{my-var}}{{^my-var}}default value{{/my-var}}
```

&emsp;&emsp;如果某个template search中没有在`params`中指定一个值，那么查询会使用默认值。例如，下面的template中为`from`和`size`设置了默认值。

```text
POST _render/template
{
  "source": {
    "query": {
      "match": {
        "message": "{{query_string}}"
      }
    },
    "from": "{{from}}{{^from}}0{{/from}}",
    "size": "{{size}}{{^size}}10{{/size}}"
  },
  "params": {
    "query_string": "hello world"
  }
}
```

#### URL encode strings

&emsp;&emsp;使用`{{#url}}`功能进行URL编码。

```text
POST _render/template
{
  "source": {
    "query": {
      "term": {
        "url.full": "{{#url}}{{host}}/{{page}}{{/url}}"
      }
    }
  },
  "params": {
    "host": "http://example.com",
    "page": "hello-world"
  }
}
```

&emsp;&emsp;填充后的值如下：

```text
{
  "template_output": {
    "query": {
      "term": {
        "url.full": "http%3A%2F%2Fexample.com%2Fhello-world"
      }
    }
  }
}
```

#### Concatenate values

&emsp;&emsp;使用`{{#join}}`功能对数组里面的值用逗号拼接。例如下面的例子中拼接了两个email地址。

```text
POST _render/template
{
  "source": {
    "query": {
      "match": {
        "user.group.emails": "{{#join}}emails{{/join}}"
      }
    }
  },
  "params": {
    "emails": [ "user1@example.com", "user_one@example.com" ]
  }
}
```

&emsp;&emsp;填充后的值如下：

```text
{
  "template_output": {
    "query": {
      "match": {
        "user.group.emails": "user1@example.com,user_one@example.com"
      }
    }
  }
}
```

&emsp;&emsp;你也可以自定义指定一个分隔符。

```text
POST _render/template
{
  "source": {
    "query": {
      "range": {
        "user.effective.date": {
          "gte": "{{date.min}}",
          "lte": "{{date.max}}",
          "format": "{{#join delimiter='||'}}date.formats{{/join delimiter='||'}}"
	      }
      }
    }
  },
  "params": {
    "date": {
      "min": "2098",
      "max": "06/05/2099",
      "formats": ["dd/MM/yyyy", "yyyy"]
    }
  }
}
```

&emsp;&emsp;填充后的值如下：

```text
{
  "template_output": {
    "query": {
      "range": {
        "user.effective.date": {
          "gte": "2098",
          "lte": "06/05/2099",
          "format": "dd/MM/yyyy||yyyy"
        }
      }
    }
  }
}
```

#### Convert to JSON

&emsp;&emsp;使用`{{#toJson}}`功能将变量值用JSON表示。

&emsp;&emsp;例如，下面的template中使用`{{#toJson}}`传递一个数组。为了保证请求体式一个合法的JSON，`source`的值需要为一个string format。

```text
POST _render/template
{
  "source": "{ \"query\": { \"terms\": { \"tags\": {{#toJson}}tags{{/toJson}} }}}",
  "params": {
    "tags": [
      "prod",
      "es01"
    ]
  }
}
```

&emsp;&emsp;填充后的值如下：

```text
{
  "template_output": {
    "query": {
      "terms": {
        "tags": [
          "prod",
          "es01"
        ]
      }
    }
  }
}
```

&emsp;&emsp;你也可以`{{#toJson}}`传递object。

```text
POST _render/template
{
  "source": "{ \"query\": {{#toJson}}my_query{{/toJson}} }",
  "params": {
    "my_query": {
      "match_all": { }
    }
  }
}
```

&emsp;&emsp;填充后的值如下：

```text
{
  "template_output" : {
    "query" : {
      "match_all" : { }
    }
  }
}
```

&emsp;&emsp;你也可以传递一个object数组。

```text
POST _render/template
{
  "source": "{ \"query\": { \"bool\": { \"must\": {{#toJson}}clauses{{/toJson}} }}}",
  "params": {
    "clauses": [
      {
        "term": {
          "user.id": "kimchy"
        }
      },
      {
        "term": {
          "url.domain": "example.com"
        }
      }
    ]
  }
}
```

&emsp;&emsp;填充后的值如下：

```text
{
  "template_output": {
    "query": {
      "bool": {
        "must": [
          {
            "term": {
              "user.id": "kimchy"
            }
          },
          {
            "term": {
              "url.domain": "example.com"
            }
          }
        ]
      }
    }
  }
}
```

#### Use conditions

&emsp;&emsp;使用下面的语法来根据条件进行创建：

```text
{{#condition}}content{{/condition}}
```

&emsp;&emsp;如果条件变量为`true`。Elasticsearch会展示内容。例如，如果`year_scope`的值为`true`，下面的template search中会选择过去一年的数据。

```text
POST _render/template
{
  "source": "{ \"query\": { \"bool\": { \"filter\": [ {{#year_scope}} { \"range\": { \"@timestamp\": { \"gte\": \"now-1y/d\", \"lt\": \"now/d\" } } }, {{/year_scope}} { \"term\": { \"user.id\": \"{{user_id}}\" }}]}}}",
  "params": {
    "year_scope": true,
    "user_id": "kimchy"
  }
}
```

&emsp;&emsp;填充后的值如下：

```text
{
  "template_output" : {
    "query" : {
      "bool" : {
        "filter" : [
          {
            "range" : {
              "@timestamp" : {
                "gte" : "now-1y/d",
                "lt" : "now/d"
              }
            }
          },
          {
            "term" : {
              "user.id" : "kimchy"
            }
          }
        ]
      }
    }
  }
}
```

&emsp;&emsp;如果`year_scope`为`false`。这个template search会查询任何时间段的数据。

```text
POST _render/template
{
  "source": "{ \"query\": { \"bool\": { \"filter\": [ {{#year_scope}} { \"range\": { \"@timestamp\": { \"gte\": \"now-1y/d\", \"lt\": \"now/d\" } } }, {{/year_scope}} { \"term\": { \"user.id\": \"{{user_id}}\" }}]}}}",
  "params": {
    "year_scope": false,
    "user_id": "kimchy"
  }
}
```

&emsp;&emsp;填充后的值如下：

```text
{
  "template_output" : {
    "query" : {
      "bool" : {
        "filter" : [
          {
            "term" : {
              "user.id" : "kimchy"
            }
          }
        ]
      }
    }
  }
}
```

&emsp;&emsp;可以使用下面的语法创建if-else条件：

```text
{{#condition}}if content{{/condition}} {{^condition}}else content{{/condition}}
```

&emsp;&emsp;例如，如果`year_scope`为`true`，则获取过去一年的数据，否则获取过去一天的数据。

```text
POST _render/template
{
  "source": "{ \"query\": { \"bool\": { \"filter\": [ { \"range\": { \"@timestamp\": { \"gte\": {{#year_scope}} \"now-1y/d\" {{/year_scope}} {{^year_scope}} \"now-1d/d\" {{/year_scope}} , \"lt\": \"now/d\" }}}, { \"term\": { \"user.id\": \"{{user_id}}\" }}]}}}",
  "params": {
    "year_scope": true,
    "user_id": "kimchy"
  }
}
```


### Sort search results
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/sort-search-results.html)

&emsp;&emsp;允许你添加一个或者多个域用于排序。每一个排序即可以正序也可以是倒序。排序规则按域定义，也可以指定一些特殊的域名例如`_score`意味着根据打分排序，`_doc`意味着根据索引顺序排序。

&emsp;&emsp;假设有以下的index mapping：

```text
PUT /my-index-000001
{
  "mappings": {
    "properties": {
      "post_date": { "type": "date" },
      "user": {
        "type": "keyword"
      },
      "name": {
        "type": "keyword"
      },
      "age": { "type": "integer" }
    }
  }
}
```

```text
GET /my-index-000001/_search
{
  "sort" : [
    { "post_date" : {"order" : "asc", "format": "strict_date_optional_time_nanos"}},
    "user",
    { "name" : "desc" },
    { "age" : "desc" },
    "_score"
  ],
  "query" : {
    "term" : { "user" : "kimchy" }
  }
}
```

> NOTE：`_doc`不是很实用，但用于排序时，却是效率最高的。所以如果你不关心返回的文档顺序，那你应该使用`_doc`排序。特别有助于[scrolling](###Scroll search results)。


#### Sort Values

&emsp;&emsp;查询响应中包含每一个文档的排序值（`sort` value）。可以使用`format`参数为[date](####Date field type)和[date_nanos](####Date nanoseconds field type)域的sort value指定[date format](####format(mapping parameter))。下面的查询返回中，`post_date`域的域值作为排序值，并且使用format格式为：`strict_date_optional_time_nanos`。

```text
GET /my-index-000001/_search
{
  "sort" : [
    { "post_date" : {"format": "strict_date_optional_time_nanos"}}
  ],
  "query" : {
    "term" : { "user" : "kimchy" }
  }
}
```

#### Sort Order

&emsp;&emsp;`order`选项有两个值可选：

- asc
  - Sort in ascending order
- desc
  - Sort in descending order

&emsp;&emsp;根据`_score`排序时，默认的`order`选项为`desc`，而根据其他字段排序时，`order`选项默认值为`asc`。

#### Sort mode option

&emsp;&emsp;Elasticsearch支持根据数组或者多值域（multi-valued field）排序。`mode`选项控制了选择数组中哪一个值用于作为文档的排序值。`mode`选项有以下的值可选：

- min
  - 选择最小的值
- max
  - 选择最大的值
- sum
  - 所有值的和作为排序值。只适用于数值类型的数组
- avg
  - 所有值的平均值作为排序值。只适用于数值类型的数组
- median
  - 所有值的中位数作为排序值。只适用于数值类型的数组

&emsp;&emsp;sort order为`asc`的默认sort mode为`min`，即选择最小值。sort order为`desc`的默认sort mode为`max`，即选择最大值。

##### Sort mode example usage

&emsp;&emsp;下面的例子中，price域是一个多值域。在这个例子中命中的结果会按照`asc`以及price中所有值的平均值排序。

```text
PUT /my-index-000001/_doc/1?refresh
{
   "product": "chocolate",
   "price": [20, 4]
}

POST /_search
{
   "query" : {
      "term" : { "product" : "chocolate" }
   },
   "sort" : [
      {"price" : {"order" : "asc", "mode" : "avg"}}
   ]
}
```

#### Sorting numeric fields

&emsp;&emsp;对于数值类型的域也是可以通过`numeric_typ`选项进行类型转换。该选项可选的值为：`["double", "long", "date", "date_nanos"]`，使得可以跨多个data stream或者索引对不同mapping类型的相同域名进行排序。

&emsp;&emsp;如果有以下两个索引：

```text
PUT /index_double
{
  "mappings": {
    "properties": {
      "field": { "type": "double" }
    }
  }
}
```

```text
PUT /index_long
{
  "mappings": {
    "properties": {
      "field": { "type": "long" }
    }
  }
}
```

&emsp;&emsp;由于`field`这个域在两个索引中分别是`double`和`long`类型，所以默认情况下不能在同时查询这两个索引时根据这个字段进行排序。然而你可以使用`numeric_type`选项强制转化为同一个类型使得可以用于排序：

```text
POST /index_long,index_double/_search
{
   "sort" : [
      {
        "field" : {
            "numeric_type" : "double"
        }
      }
   ]
}
```

&emsp;&emsp;在上面的例子中，索引`index_long`中的值转化为一个double使得兼容索引`index_double`中的值。当然也可以将一个floating值转化为long，but note that in this case floating points are replaced by the largest value that is less than or equal (greater than or equal if the value is negative) to the argument and is equal to a mathematical integer。

&emsp;&emsp;同样的也可以用于`date`和`date_nanos`域。如果有以下两个索引：

```text
PUT /index_double
{
  "mappings": {
    "properties": {
      "field": { "type": "date" }
    }
  }
}
```

```text
PUT /index_long
{
  "mappings": {
    "properties": {
      "field": { "type": "date_nanos" }
    }
  }
}
```

&emsp;&emsp;两个索引中`field`的值使用不同的格式（different resolution）存储使得`date`类型的值总是排在`date_nanos`之后（asc）。在使用了`numeric_typ`选项后，就可以将它们设置为单个格式（single resolution）。如果设置为`date`，那么`date_nanos`的值会被转化为millisecond，如果设置为`date_nanos`，那么`date`会被转化为nanoseconds。

```text
POST /index_long,index_double/_search
{
   "sort" : [
      {
        "field" : {
            "numeric_type" : "date_nanos"
        }
      }
   ]
}
```

> WARNING：为了防止出现溢出，对于1970年之前和2262年之后的`date_nanos`的转化不能表示为long。

#### Sorting within nested objects.

&emsp;&emsp;Elasticsearch同样支持根据object内部的域或者更深层的域进行排序。根据nested filed排序时使用的`nested`排序选项可以有以下的属性：

- path
  - 定义了对哪一个object排序（Defines on which nested object to sort）。真正的排序字段必须是这个object中的字段。 When sorting by nested field, this field is mandatory
- filter
  - 每一层nested object以及inner nested object都可以指定filter，判断排序字段能否用于排序，如果条件不满足，则视为missing value（见下面的例子）。Common case is to repeat the query / filter inside the nested filter or query. By default no filter is active.
- max_children
  - 选择排序值（Sort value）时，object的最大深度（maximum number of children）。默认值为`unlimited`
- nested
  - 跟顶层（top-level）的`nested`是一样的，只是在当前的nested object中需要使用其他的nested path（见下面的例子）

> NOTE：如果排序中定义了一个nested field但是没有`nested` 的上下文（见下面的例子），Elasticsearch会抛出错误异常

##### Nested sorting examples

&emsp;&emsp;在下面的例子中，`offer`是一个`nested`类型，需要指定`path`，否则Elasticsearch不知道选择哪一层的nested中的排序值进行排序

```text
POST /_search
{
   "query" : {
      "term" : { "product" : "chocolate" }
   },
   "sort" : [
       {
          "offer.price" : {
             "mode" :  "avg",
             "order" : "asc",
             "nested": {
                "path": "offer",
                "filter": {
                   "term" : { "offer.color" : "blue" }
                }
             }
          }
       }
    ]
}
```

&emsp;&emsp;在下面的例子中，`parent`和`child`是`nested`域。需要在每一层指定`nested.path`，否则Elasticsearch不知道选择哪一层的nested中的排序值进行排序。

```text
POST /_search
{
   "query": {
      "nested": {
         "path": "parent",
         "query": {
            "bool": {
                "must": {"range": {"parent.age": {"gte": 21}}},
                "filter": {
                    "nested": {
                        "path": "parent.child",
                        "query": {"match": {"parent.child.name": "matt"}}
                    }
                }
            }
         }
      }
   },
   "sort" : [
      {
         "parent.child.age" : {
            "mode" :  "min",
            "order" : "asc",
            "nested": {
               "path": "parent",
               "filter": {
                  "range": {"parent.age": {"gte": 21}}
               },
               "nested": {
                  "path": "parent.child",
                  "filter": {
                     "match": {"parent.child.name": "matt"}
                  }
               }
            }
         }
      }
   ]
}
```

&emsp;&emsp;nested sorting 同样支持根据脚本或者地理位置排序。

#### Missing Values

&emsp;&emsp;`missing`参数指的是如何对哪些缺少排序字段的文档进行排序：`missing`的值可以设置为`_last`、`_first`或者自定义的值（该值会被用于排序）。默认值为`_last`。

&emsp;&emsp;例如：

```text
GET /_search
{
  "sort" : [
    { "price" : {"missing" : "_last"} }
  ],
  "query" : {
    "term" : { "product" : "chocolate" }
  }
}
```

> NOTE：如果nested inner object没有匹配到`nested.filter`，则视为缺少排序字段，即使用missing value。

#### Ignoring Unmapped Fields

&emsp;&emsp;默认情况下，如果某个域没有对应的mapping，那么根据这个域排序的请求会失败。`unmapped_type`选项允许你忽略没有对应mapping的域，不根据他们进行排序。这个参数的值可以用于determine what sort values to emit。下面是使用这个参数的例子：

```text
GET /_search
{
  "sort" : [
    { "price" : {"unmapped_type" : "long"} }
  ],
  "query" : {
    "term" : { "product" : "chocolate" }
  }
}
```

&emsp;&emsp;如果所有的索引都没有`price`对应的mapping，Elasticsearch将认为存在`long`类型的mapping（防止报错），并且所有的文档都没有这个域的域值。

#### Geo Distance Sorting

&emsp;&emsp;允许根据`_geo_distance`排序。下面的例子中，假设`pin.location`是`geo_point`类型的域：

```text
GET /_search
{
  "sort" : [
    {
      "_geo_distance" : {
          "pin.location" : [-70, 40],
          "order" : "asc",
          "unit" : "km",
          "mode" : "min",
          "distance_type" : "arc",
          "ignore_unmapped": true
      }
    }
  ],
  "query" : {
    "term" : { "user" : "kimchy" }
  }
}
```

- distance_type

&emsp;&emsp;如果计算距离。可以是`arc`（默认值）或者`plane`（计算更快，但是长距离以及靠近两极时的计算的结果不精确）

- mode

&emsp;&emsp;如果有多个域值时选择哪一个用于排序。默认情况下，当按照ascending order时会采用最短距离，按照descending order时采用最长距离。支持的可选值有`min`、`max`、`medina`以及`avg`。

- unit

&emsp;&emsp;计算排序时使用的单位。默认值为`m`(米)。

- ignore_unmapped

&emsp;&emsp;unmapped filed是否视为missing value。设置为`true`后跟上文中的Ignoring Unmapped Fields是一样的处理方式，设置为`false`后，unmapped filed会导致查询失败。

> NOTE：geo distance sorting不支持配置missing value：当文档中没有域值用于计算距离时总是被认为距离为`Infinity`

&emsp;&emsp;下面提供的坐标格式都是支持的：

##### Lat Lon as Properties

```text
GET /_search
{
  "sort" : [
    {
      "_geo_distance" : {
        "pin.location" : {
          "lat" : 40,
          "lon" : -70
        },
        "order" : "asc",
        "unit" : "km"
      }
    }
  ],
  "query" : {
    "term" : { "user" : "kimchy" }
  }
}
```

##### Lat Lon as String

&emsp;&emsp;`lat`、`lon`的格式。

```text
GET /_search
{
  "sort": [
    {
      "_geo_distance": {
        "pin.location": "40,-70",
        "order": "asc",
        "unit": "km"
      }
    }
  ],
  "query": {
    "term": { "user": "kimchy" }
  }
}
```

##### Geohash

```text
GET /_search
{
  "sort": [
    {
      "_geo_distance": {
        "pin.location": "drm3btev3e86",
        "order": "asc",
        "unit": "km"
      }
    }
  ],
  "query": {
    "term": { "user": "kimchy" }
  }
}
```

##### Lat Lon as Array

&emsp;&emsp;[`lon`, `lat`]的格式，注意的时这里lon/lat的前后顺序是为了符合[GeoJSON](https://geojson.org)。

```text
GET /_search
{
  "sort": [
    {
      "_geo_distance": {
        "pin.location": [ -70, 40 ],
        "order": "asc",
        "unit": "km"
      }
    }
  ],
  "query": {
    "term": { "user": "kimchy" }
  }
}
```

##### Multiple reference points

&emsp;&emsp;可以使用数组提供多个geo points，数组元素的格式可以是上文中提到的所有的格式，例如

```text
GET /_search
{
  "sort": [
    {
      "_geo_distance": {
        "pin.location": [ [ -70, 40 ], [ -71, 42 ] ],
        "order": "asc",
        "unit": "km"
      }
    }
  ],
  "query": {
    "term": { "user": "kimchy" }
  }
}
```

&emsp;&emsp;最终一篇文档的距离是文档中所有点的 min/max/avg(取决于`mode`的值)到排序请求中给出的点的距离。

#### Script Based Sorting

&emsp;&emsp;允许基于自定义的脚本进行排序，见下面的例子：

```text
GET /_search
{
  "query": {
    "term": { "user": "kimchy" }
  },
  "sort": {
    "_script": {
      "type": "number",
      "script": {
        "lang": "painless",
        "source": "doc['field_name'].value * params.factor",
        "params": {
          "factor": 1.1
        }
      },
      "order": "asc"
    }
  }
}
```

#### Track Scores

&emsp;&emsp;当根据某个域排序后，就不会对文档打分。设置`track_scores`为true后才会进行打分计算。

```text
GET /_search
{
  "track_scores": true,
  "sort" : [
    { "post_date" : {"order" : "desc"} },
    { "name" : "desc" },
    { "age" : "desc" }
  ],
  "query" : {
    "term" : { "user" : "kimchy" }
  }
}
```

#### Memory Considerations

&emsp;&emsp;排序时，用于排序的域会加载到内存中。这意味着每一个分片都应该有足够的内存来处理他们。对于用于排序的string类型，它不应该设置为analyzed / tokenized。对于数值类型，如果可以的话，建议显示的（explicit）设置为narrower types（例如`short`、`ingeger`以及`float`）。

## Query DSL
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl.html)

#### Allow expensive queries

### Query and filter context
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-filter-context.html)

#### Relevance scores

&emsp;&emsp;默认情况下，Elasticsearch根据相关性分数（relevance score）对匹配到的结果进行排序。相关性分数衡量每个文档与查询的匹配程度。

&emsp;&emsp;relevance score是一个正浮点数，在[search API](###Request body search) 的` _score`元数据字段中返回。`_score`越高，跟文档越相关。每一个查询类型计算相关性分数是不同的，分数的计算也取决于query clause是在**query**还是**filter**的上下文（context）中运行。

#### Query context

&emsp;&emsp;在query context中，一个query clause会回答这个问题：文档跟这个query clause的匹配程度是多少？除了决定是否匹配文档，另外query clause还要计算一个相关性分数，这个分数在`_source`元数据字段中返回。

&emsp;&emsp;query context通过参数`query`生效，见[search](######query) API中的query参数。

#### Filter context

&emsp;&emsp;在filter  context中，一个query clause会回答这个问题：文档跟这个query clause匹配吗？答案是简单的`匹配`或者`不匹配`。不会计算分数。filter context最常用于过滤结构化的数据：

- `timestamp`域的域值在2015到2016的范围内吗
- `status`域的域值是`published`吗

&emsp;&emsp;常用的filters会被Elasticsearch自动缓存来提高性能。

&emsp;&emsp;filter context通过参数`filter`生效，比如[bool  query](####Boolean query)中的`filter`或者`must_not`参数，以及 [constan_score](####Constant score query) query和 [filter](####Filter aggregation)聚合中的参数`filter`。

#### Example of query and filter contexts

&emsp;&emsp;下面的例子中是一个同时使用了query context和filter context的`search` API。这个查询将会匹配满足下面所有条件的文档：

- `title`域的域值中包含`search`
- `content`域的域值中包含`Elasticsearch`
- `exact`域的域值为`published`
- `publish_date`域的域值大于等于`2015-01-01`

```text
GET /_search
{
  "query": { 
    "bool": { 
      "must": [
        { "match": { "title":   "Search"        }},
        { "match": { "content": "Elasticsearch" }}
      ],
      "filter": [ 
        { "term":  { "status": "published" }},
        { "range": { "publish_date": { "gte": "2015-01-01" }}}
      ]
    }
  }
}
```

&emsp;&emsp;第3行，参数`query`说明使用了query context

&emsp;&emsp;第4行，`bool`和`must`在query context中用来描述文档的匹配程度

&emsp;&emsp;第8行，参数`filter`说明使用了filter context，`term`和`query`在filter context中用来过滤掉不匹配的文档，并且不会影响匹配的文档的分数。

>WARNING：query context中计算出的分数是一个精确的浮点型数值。只有24位的精度。超过有效数字精度的分数将被转换为浮点数，但会丢失精度。

>TIP：用于影响匹配的文档的分数（文档的匹配程度）的query clause放在query context中，其他的query clause放在filter context中。


### Compound queries
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/compound-queries.html)

&emsp;&emsp;复合查询（compound query）中封装了符合的或者叶子查询（compound or leaf query）。

&emsp;&emsp;这组复合查询中有这些query：

- [bool query](####Boolean query)：通过定义`must`, `should`,`must_not`, `filter`  clause来组合多个leaf或者compound query的默认query。`must`和`should`的query clause都会进行打分并且组合打分值。the more matching clauses, the better。`must_not`和`filter`的query clause则在[filter context](####Filter context)中执行。
- [boosting query](####Boosting query)：返回满足`positive` query的文档，如果该文档同时满足`negative` query则降低这篇文档的分数。
- [constant_score query](####Constant score query)：这是一个封装其他query的query，并且这个constant_score_query在filter context中运行。所有匹配的文档在`_score`中返回一个常量分数
- [dis_max query](####Disjunction max query)：这是一个封装了多个query（我们称之为子query）的query。满足匹配的文档至少满足了一个子query的查询条件。bool query中对于一篇文档的打分值会组合每一个子query对文档的打分，而dis_max_query则是使用最佳的子query对一篇文档的打分值
- [function_score query](####Function score query)：使用function修改main query返回的分数，以考虑流行度（popularity）、新近度（recency）、距离或使用脚本实现的自定义算法（custom algorithms）等因素

#### Boolean query
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-bool-query.html)

&emsp;&emsp;Boolean query是一种组合了其他query用于匹配文档的query。bool query对应（map）Lucene中的`BooleanQuery`。通过一个或者多个boolean clause来构建Boolean query，这些clause各自有一个`occur`的类型。类型的种类包括：

|  Occur   |                         Description                          |
| :------: | :----------------------------------------------------------: |
|   must   | 匹配到的文档必须满足这个clause（query），同时这个query会对文档进行打分 |
|  filter  | 匹配到的文档必须满足这个clause（query），但是跟`must`不同的是，不会计算这个query对文档的打分值。Filter clause在[filter context](####Filter context)下执行，意味着忽略打分值并且这个query可以被缓存（Lucene中会缓存满足这个query的文档号） |
|  should  | 在匹配到的文档中，存在满足这个clause（query）的查询条件的一个或者多个文档 |
| must_not | 匹配到的所有文档都不能满足这个clause（query） 的查询条件。这个clause在[filter context](####Filter context)下执行，意味着忽略打分值并且这个query可以被缓存。因为忽略了打分值，所有的文档分数都是`0` |

&emsp;&emsp;`bool query`采取的是`more-matches-is-better`的方法。所以如果某篇文档同时满足`must`和`should`的查询条件，那么这篇文档的打分值会累加，最后在`_sorce`中返回。

```text
POST _search
{
  "query": {
    "bool" : {
      "must" : {
        "term" : { "user.id" : "kimchy" }
      },
      "filter": {
        "term" : { "tags" : "production" }
      },
      "must_not" : {
        "range" : {
          "age" : { "gte" : 10, "lte" : 20 }
        }
      },
      "should" : [
        { "term" : { "tags" : "env1" } },
        { "term" : { "tags" : "deployed" } }
      ],
      "minimum_should_match" : 1,
      "boost" : 1.0
    }
  }
}
```

##### Using minimum_should_match

&emsp;&emsp;你可以使用参数`minimum_should_match`来指定匹配到的文档必须满足至少`minimum_should_match`个（或者某个比例）`occur`为`should`的clause（query）。

&emsp;&emsp;如果 `bool query`中至少包含一个`should` clause并且没有`must`或`filter`的clause，这个参数的默认值为`1`。

&emsp;&emsp;见[minimum_should_match](###minimum_should_match parameter)了解这个参数更多的可选值。

##### Scoring with bool.filter

&emsp;&emsp;在元素（element）`filter`下指定的query不会影响文档的打分—返回的打分值（score）都是`0`。只有在元素`query`中指定的query才会影响文档的打分值（下面例子中的bool query，不过这个query中的子query仍然是不用打分的query）。接下来的三个例子中都是返回那些满足`status`域的域值为`active`的文档。

&emsp;&emsp;下面第一个query返回的所有文档的打分值都是`0`，因为没有指定用于打分的query。

```text
GET _search
{
  "query": {
    "bool": {
      "filter": {
        "term": {
          "status": "active"
        }
      }
    }
  }
}
```

&emsp;&emsp;`bool query`中有一个`match_all`的query，这个query将另所有的文档的打分值为`1.0`。

```text
GET _search
{
  "query": {
    "bool": {
      "must": {
        "match_all": {}
      },
      "filter": {
        "term": {
          "status": "active"
        }
      }
    }
  }
}
```

&emsp;&emsp;`constant_score`这个query的行为跟上面第二个例子是一模一样的。这个query将另所有的文档的打分值为`1.0`。

```text
GET _search
{
  "query": {
    "constant_score": {
      "filter": {
        "term": {
          "status": "active"
        }
      }
    }
  }
}
```

##### Named queries

&emsp;&emsp;每一个query都可以在顶层定义中设置一个`_name`。你可以使用被命名的query（named query）来追踪返回的文档被哪个query命中了。如果使用了named query，响应中的每一个结果中都会包含一个`mathced_queries`的属性。

```text
GET /_search
{
  "query": {
    "bool": {
      "should": [
        { "match": { "name.first": { "query": "shay", "_name": "first" } } },
        { "match": { "name.last": { "query": "banon", "_name": "last" } } }
      ],
      "filter": {
        "terms": {
          "name.last": [ "banon", "kimchy" ],
          "_name": "test"
        }
      }
    }
  }
}
```


#### Boosting query
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-boosting-query.html)

&emsp;&emsp;返回匹配`positive` query的文档并且降低匹配`negative` query的文档的相关性分数（[relevance score](####Relevance scores)）。

&emsp;&emsp;你可以通过`boosting` query来对相关文档进行降级（demote）而不是从查询结果中排除掉这些文档。

##### Example request

```text
GET /_search
{
  "query": {
    "boosting": {
      "positive": {
        "term": {
          "text": "apple"
        }
      },
      "negative": {
        "term": {
          "text": "pie tart fruit crumble tree"
        }
      },
      "negative_boost": 0.5
    }
  }
}
```

##### Top-level parameters for boosting

###### positive

&emsp;&emsp;（必选值，query object）你想要执行的query。所有的文档都必须匹配这个query。

###### negative

&emsp;&emsp;（必选值，query object）用来降低匹配到的文档的[relevance score](####Relevance scores)。

&emsp;&emsp;如果返回的文档匹配了`positive`并且`negative` query，`boosting` query会按照下面的方式来计算[relevance score](####Relevance scores)：

1. 从`positive` query中获取原始的相关性分数
2. 与`negative_boost`的值做乘法

###### negative_boost

&emsp;&emsp;（必选值，float）该值0~1.0范围内的浮点数。用来降低匹配了`negative` query 的文档的[relevance score](####Relevance scores)。

#### Constant score query
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-constant-score-query.html)

&emsp;&emsp;封装了一个[ filter query ](####Boolean query)并且每一个匹配到的文档的相关性分数等于参数`boos`的值。

```text
GET /_search
{
  "query": {
    "constant_score": {
      "filter": {
        "term": { "user.id": "kimchy" }
      },
      "boost": 1.2
    }
  }
}
```

##### Top-level parameters for constant_score

###### filter

&emsp;&emsp;（required，query object）[Filter query](####Boolean query)是你想要运行的query，任何返回的文档都必须匹配这个query。

&emsp;&emsp;Filter query 不会计算 [relevance score](####Relevance scores)，为了提高性能，Elasticsearch会自动的缓存使用频繁的filter query。

###### boost

&emsp;&emsp;（Optional，float）一个浮点型的数字用于作为满足`filter` query的所有文档固定的[relevance score](####Relevance scores)。默认值是`1.0`。

#### Disjunction max query
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-dis-max-query.html)

&emsp;&emsp;返回的文档匹配一个或者多个封装的query，成为query clause或者clause。

&emsp;&emsp;如果一篇返回的文档匹配了多个query clause，`dis_max` query会将query clause中打分最高的值作为相关性分数，基于参数`tie_breaker`再加上其他匹配的子query（subquery）对应的打分值。

##### Example

```text
GET /_search
{
  "query": {
    "dis_max": {
      "queries": [
        { "term": { "title": "Quick pets" } },
        { "term": { "body": "Quick pets" } }
      ],
      "tie_breaker": 0.7
    }
  }
}
```

##### Top-level parameters for dis_max

###### queries

&emsp;&emsp;(Required, array of query objects) 包含一个或者多个query clause。返回的文档必须匹配一个或者多个这些query。如果一篇文档匹配了多个query，Elasticsearch会采用最大的相关性分数（每个query都会对这篇文档打分）。

###### tie_breaker

&emsp;&emsp;(Optional, float) 该值是0~1.0范围内的浮点数，用来增加匹配到了多个query clause的文档的 [relevance score](####Relevance scores)。默认值为`0.0`。

&emsp;&emsp;You can use the `tie_breaker` value to assign higher relevance scores to documents that contain the same term in multiple fields than documents that contain this term in only the best of those multiple fields, without confusing this with the better case of two different terms in the multiple fields。

&emsp;&emsp;如果一篇文档匹配了多个clause，`dis_max` query 按照下面的方式为这篇文档计算relevance score：

1. 从匹配到的clause中选出最高的relevance score
2. `tie_breaker`参数的值与其他任何匹配的clause的分数做乘法
3. 上述两个结果相加
   - Lucene中的计算公式：`scoreMax + otherScoreSum * tieBreakerMultiplier`
   - scoreMax为clause打分最高的，otherScoreSum为其他clause的打分和值，tieBreakerMultiplier即tie_breaker的值

&emsp;&emsp;如果`tie_breaker`的值大于`0.0`，所有匹配到的clause都有用，但是分数最高的那个clause最重要。（If the tie_breaker value is greater than 0.0, all matching clauses count, but the clause with the highest score counts most）

#### Function score query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-function-score-query.html)

### Full text queries
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/full-text-queries.html)

&emsp;&emsp;full text query可以让查询[analyzed text fields](##Text analysis)，例如邮件的内容。查询的内容会跟索引期间的内容一样使用相同的分词器处理。

&emsp;&emsp;属于full text query的query包括：

| [intervals query](####Intervals query) |      |
| :----------------------------------------------------------: | ---- |
| [match query](####Match query) |      |
| [match_bool_prefix query](####Match boolean prefix query) |      |
| [match_phrase query](####Match phrase query) |      |
| [match_phrase_prefix query](####Match phrase prefix query) |      |
| [multi_match query](####Multi-match query) |      |
| [combined_fields query](####Combined fields) |      |
| [query_string query](####Query string query) |      |
| [simple_query_string query](####Simple query string query) |      |

#### Intervals query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-intervals-query.html)

#### Match query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-match-query.html)

#### Match boolean prefix query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-match-bool-prefix-query.html)

#### Match phrase query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-match-query-phrase.html)

#### Match phrase prefix query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-match-query-phrase-prefix.html)

#### Combined fields
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-combined-fields-query.html)

#### Multi-match query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-multi-match-query.html)

#### Query string query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-query-string-query.html)

>TIP：本节内容介绍的是`query_string`这个query类型，更多关于在Elasticsearch中运行一个查询query的内容见[Search your data](##Search your data).

&emsp;&emsp;基于提供的查询字符串（query string），使用一个语法严格的解析器并且返回文档。

&emsp;&emsp;这个query使用一个[syntax](#####Query string syntax)进行解析并且基于`AND`或者`NOT`操作符对query string进行切分。然后在返回匹配的文档前，这个query独立的 [analyzer](##Text analysis)每一个切分的文本。

&emsp;&emsp;你可以使用`query_string` query创建一个包含通配符，跨多个域以及更多信息的复杂查询。虽然通用（versatile），但这个query很严格，如果query string中包含任何无效语法，则返回错误。

>WARNING：由于任意非法语法都会导致这个query返回错误，我们不建议在搜索框（search boxes）中使用`query_string`中使用这个查询。
>如果你不需要支持query syntax，可以考虑使用[match query](####Match query)。如果你需要query syntax的功能，可以使用[simple_query_string](####Simple query string query)，这种query没有那么严格的语法。

##### Example request

&emsp;&emsp;当你运行下面的查询，`query_string` query会将`(new york city) OR (big apple)`划分两个部分：`new york city` 以及 `big apple`。随后在返回匹配到的文档前，`content`域的分词器分别将每一部分的值分成token。由于query syntax没有使用whitespace作为一个操作符，`new york city`会原样的传给分词器。

```text
GET /_search
{
  "query": {
    "query_string": {
      "query": "(new york city) OR (big apple)",
      "default_field": "content"
    }
  }
}
```

##### Top-level parameters for query_string

###### query

&emsp;&emsp;（Required, string）你想要解析并且用于查询的查询字符串（query string）。见[Query string syntax](######Query string syntax)。

###### default_field

&emsp;&emsp;（Required, string）如果没有在query string指定域，那么就使用这个默认域进行查询。支持通配符（\*）。

&emsp;&emsp;默认是index setting中的[index.query.default_field](#####index.query.default_field)，默认值是`*`。`*`会提取出所有符合（eligible）term queries and filters the metadata fields的域。如果没有指定`prefix`，所有提取出来的域会构建成一个query。

&emsp;&emsp;查询会跨所有符合的域但是不包括[nested documents](####Nested field type)，可以使用[nested query](#####Nested query)来查询。

&emsp;&emsp;对于定义了大量字段的mapping，在所有符合的（eligible）域上进行搜索的开销是很大的。

&emsp;&emsp;单次查询有域的数量的限制，在[search setting](####Search settings)中定义了`indices.query.bool.max_clause_count`，默认值是4096。

###### allow_leading_wildcard

&emsp;&emsp;(Optional, Boolean) 如果为`true`，通配符`*`以及`?`允许作为query string的首个字符。默认值是`true`。

###### analyze_wildcard

&emsp;&emsp;(Optional, Boolean) 如果为`true`，会尝试分析（analyze）query string中的通配字符。默认值是`false`。

###### analyzer

&emsp;&emsp;(Optional, string) 用来将query string进行分词。默认是`default_field`字段在[index-time analyzer](####Specify an analyzer)时的分词器。如果没有设置analyzer，则使用索引默认的分词器。

###### auto_generate_synonyms_phrase_query

##### Note

###### Query string syntax


#### Simple query string query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-simple-query-string-query.html)

#### Joining queries
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/joining-queries.html)

##### Nested query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-nested-query.html#query-dsl-nested-query)

### Specialized queries
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/specialized-queries.html)

#### More like this query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-mlt-query.html)

#### Rank feature query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-rank-feature-query.html)

#### Script query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-script-query.html)

#### Script score query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-script-score-query.html)


### Term-level queries
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/term-level-queries.html)

#### Exists query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-exists-query.html)

#### Range query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-range-query.html)

#### Term query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-term-query.html)

#### Terms query
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-terms-query.html)


#### IDs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-ids-query.html#query-dsl-ids-query)

&emsp;&emsp;基于IDs返回文档，这个查询使用了文档的IDs，IDs被存储在[\_id](####_id-field)字段中。

##### 请求示例

```text
GET /_search
{
  "query": {
    "ids" : {
      "values" : ["1", "4", "100"]
    }
  }
}
```

##### Top-level parameters for ids

###### values

&emsp;&emsp;（Required，String数组）[document IDs](####_id-field)数组。


### minimum_should_match parameter
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/query-dsl-minimum-should-match.html)

## Aggregations
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations.html)

### Bucket aggregations
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-bucket.html)

#### Filter aggregation
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-bucket-filter-aggregation.html)

#### Composite aggregation
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-bucket-composite-aggregation.html#_date_histogram)

> WARNING：composite aggregation开销是昂贵的。在你生产中部署composite aggregation前先进行测试。

&emsp;&emsp;多个分桶（multi-bucket）的聚合会根据不同的source创建composite buckets。

&emsp;&emsp;跟其他`multi-bucket` aggregation不同的是，你可以使用`composite` aggregation有效地对来自多层次聚合（multi-level）aggregation的所有桶进行分页。这个聚合提供了一个方法流式处理一个指定的聚合的所有分桶，跟[scroll](####Scroll search results)处理文档类似。

&emsp;&emsp;composite buckets通过对提取/创建出的每一篇文档中的值进行组合（combination），并且每一种组合都认为是一个composite bucket。

&emsp;&emsp;例如，在下面这个文档中：

```text
{
  "keyword": ["foo", "bar"],
  "number": [23, 65, 76]
}
```

&emsp;&emsp;下面的composite bucket中，使用`keyword`和`number`作为聚合结果的source filed。

```text
{ "keyword": "foo", "number": 23 }
{ "keyword": "foo", "number": 65 }
{ "keyword": "foo", "number": 76 }
{ "keyword": "bar", "number": 23 }
{ "keyword": "bar", "number": 65 }
{ "keyword": "bar", "number": 76 }
```

##### Value sources

&emsp;&emsp;`sources`参数定义了构建compost bucket时使用的source filed。在`sources`中的定义先后顺序控制了key的返回顺序。

> NOTE: 当你定义`sources`时，你必须使用一个唯一键

&emsp;&emsp;`sources`参数的类型只能是下列中的一种：

- [Terms](######\_Terms)
- [Histogram](######\_Histogram)
- [Date histogram](######\_Date histogram)
- [GeoTile grid](######\_GeoTile grid)

###### \_Terms

&emsp;&emsp;`terms`这个value source类似一个简答的`terms` aggregation。这个值从一个域中提出出来，跟`terms` aggregation一样。

&emsp;&emsp;例子：

```text
GET /_search
{
  "size": 0,
  "aggs": {
    "my_buckets": {
      "composite": {
        "sources": [
          { "product": { "terms": { "field": "product" } } }
        ]
      }
    }
  }
}
```

&emsp;&emsp;跟`terms` aggregation一样，可以为composite bucket通过[runtime field](###Runtime fields)创建一个值：

```text
GET /_search
{
  "runtime_mappings": {
    "day_of_week": {
      "type": "keyword",
      "script": """
        emit(doc['timestamp'].value.dayOfWeekEnum
          .getDisplayName(TextStyle.FULL, Locale.ROOT))
      """
    }
  },
  "size": 0,
  "aggs": {
    "my_buckets": {
      "composite": {
        "sources": [
          {
            "dow": {
              "terms": { "field": "day_of_week" }
            }
          }
        ]
      }
    }
  }
}
```

&emsp;&emsp;尽管很相似，但是`terms`这个 value source 不支持`terms` aggregation中的参数。对于支持的value source参数，见：

- [Order](#####Order)
- [Missing bucket](#####Missing bucket)

###### \_Histogram

&emsp;&emsp;`histogram` 这个value source可以应用到（apply to）数值类型的值（numeric value）上，构建值之间固定大小的间隔。`interval`参数定义数值类型的值如何被转化（transform）。比如说一个`interval`为5时，任何数值都会转到其最接近的间隔，`101`这个值会被转化为`100`。这个key用于描述100到105的值。

&emsp;&emsp;例如：

```text
GET /_search
{
  "size": 0,
  "aggs": {
    "my_buckets": {
      "composite": {
        "sources": [
          { "histo": { "histogram": { "field": "price", "interval": 5 } } }
        ]
      }
    }
  }
}
```

&emsp;&emsp;跟`histogram` aggregation 一样，可以为composite bucket通过[runtime field](###Runtime fields)创建一个值：

```text
GET /_search
{
  "runtime_mappings": {
    "price.discounted": {
      "type": "double",
      "script": """
        double price = doc['price'].value;
        if (doc['product'].value == 'mad max') {
          price *= 0.8;
        }
        emit(price);
      """
    }
  },
  "size": 0,
  "aggs": {
    "my_buckets": {
      "composite": {
        "sources": [
          {
            "price": {
              "histogram": {
                "interval": 5,
                "field": "price.discounted"
              }
            }
          }
        ]
      }
    }
  }
}
```

###### \_Date histogram

&emsp;&emsp;`date_histogram` 类似与`histogram`，只是间隔值用的是date/time。

```text
GET /_search
{
  "size": 0,
  "aggs": {
    "my_buckets": {
      "composite": {
        "sources": [
          { "date": { "date_histogram": { "field": "timestamp", "calendar_interval": "1d" } } }
        ]
      }
    }
  }
}
```

&emsp;&emsp;上面的例子创建了一个按天作为时间间隔，并将所有`timestamp`的值转化成最近的时间间隔。可用的间隔表达式有：`year`, `quarter`, `month`, `week`,`day`, `hour`,`minute`, `second`。

&emsp;&emsp;时间值也可以缩写为目前支持的[time units](####Time units)。注意的是小数点的时间值是不支持的，但是你可以转化为其他单位来解决这个问题（比如说`1.5h`可以替换为`90m`）。

- Format

&emsp;&emsp;在源码中，date会用一个64位的milliseconds-since-the-epoch数字表示。这些时间戳返回后会作为bucket key。可以返回格式化的日期字符串，而不是使用格式参数指定的格式：

```text
GET /_search
{
  "size": 0,
  "aggs": {
    "my_buckets": {
      "composite": {
        "sources": [
          {
            "date": {
              "date_histogram": {
                "field": "timestamp",
                "calendar_interval": "1d",
                "format": "yyyy-MM-dd"         
              }
            }
          }
        ]
      }
    }
  }
}
```

&emsp;&emsp; 第13行，见[format pattern](#####Date Format/Pattern)

- Time Zone

&emsp;&emsp;Date-Times在Elasticsearch使用UTC存储。默认情况下，所有的分组和rounding都是在UTC下完成。`time_zone`参数用来指示分桶应该使用不同的时区。

&emsp;&emsp; 时区可以通过一个ISO  8601 UTC offset（比如 `+01:00` 或者`-08:00`）或者一个timezone id来指定，这个timezone id在TZ database中使用，例如`America/Los_Angele`。

- Offset

&emsp;&emsp;使用`offset`参数通过指定正偏移和负偏移的持续时间来改变每一个分桶的开始值（start value of each bucket）。例如`1h`代表一个小时，`1d`代表一天。见[Time units](####Time units)见更多选项。

&emsp;&emsp;例如当使用了`day`作为时间间隔。那么每一个分桶的时间是凌晨到午夜。将`offset`参数设置为`6h`后，每一个分桶中的时间就变更为`6am`到`6am`：

```text
PUT my-index-000001/_doc/1?refresh
{
  "date": "2015-10-01T05:30:00Z"
}

PUT my-index-000001/_doc/2?refresh
{
  "date": "2015-10-01T06:30:00Z"
}

GET my-index-000001/_search?size=0
{
  "aggs": {
    "my_buckets": {
      "composite" : {
        "sources" : [
          {
            "date": {
              "date_histogram" : {
                "field": "date",
                "calendar_interval": "day",
                "offset": "+6h",
                "format": "iso8601"
              }
            }
          }
        ]
      }
    }
  }
}
```

&emsp;&emsp;上面的请求会将6am开始的文档分组到分桶内，而不是从凌晨开始。

```text
{
  ...
  "aggregations": {
    "my_buckets": {
      "after_key": { "date": "2015-10-01T06:00:00.000Z" },
      "buckets": [
        {
          "key": { "date": "2015-09-30T06:00:00.000Z" },
          "doc_count": 1
        },
        {
          "key": { "date": "2015-10-01T06:00:00.000Z" },
          "doc_count": 1
        }
      ]
    }
  }
}
```

> NOTE: 每一分桶开始的`offse`已经用`time_zone`进行了调整。

###### \_GeoTile grid

##### Order

##### Missing bucket

#### Date histogram aggregation
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-bucket-datehistogram-aggregation.html#calendar_and_fixed_intervals)

#### Date range aggregation
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-bucket-daterange-aggregation.html)

##### Date Format/Pattern

#### Histogram aggregation
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-bucket-histogram-aggregation.html)

##### Histogram fields

#### Range aggregation
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-bucket-range-aggregation.html)

#### Significant text aggregation
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-bucket-significanttext-aggregation.html)

#### Terms aggregation
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-bucket-terms-aggregation.html)

##### Execution hint

### Metrics aggregations
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-metrics-stats-aggregation.html)

#### Avg aggregation
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-metrics-avg-aggregation.html)

#### Cardinality aggregation
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-metrics-cardinality-aggregation.html)

#### Max aggregation
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-metrics-max-aggregation.html)

#### Scripted metric aggregation
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-metrics-scripted-metric-aggregation.html)

##### Scope of scripts

#### Stats aggregation
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-metrics-avg-aggregation.html)

#### Sum aggregation
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-metrics-sum-aggregation.html)

#### Top metrics aggregation
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-metrics-top-metrics.html)

### Pipeline aggregations
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-pipeline.html)

#### Bucket script aggregation
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-pipeline-bucket-script-aggregation.html)

#### Bucket selector aggregation
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-aggregations-pipeline-bucket-selector-aggregation.html)

## Scripting 
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-scripting.html)

### How to write scripts
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-scripting-using.html)

#### Scripts, caching, and search speed
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/scripts-and-search-speed.html)

#### Grokking grok
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/grok.html)


## Data management
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/data-management.html)

&emsp;&emsp;你在Elasticsearch中存储的数据通常是下面类别中的一种：

- Content：你想要查询的items集合，比如产品目录。
- Time series data：不断产生并且带有时间戳的数据流，比如日志（log entry）。

&emsp;&emsp;Content可能会经常更新，但是Content的价值随着时间的流逝仍然是相对不变的。你可能想要快速的检索items，并且不关心这些数据的新旧。

&emsp;&emsp;时序数据（Time series data）随着时间的流逝（over time）不断的增长（accumulate），所以你需要平衡存储开销和数据的价值的策略。这些数据随着时间的推移（as it ages），变的不重要并且很少被访问。所以你可以将数据移动到更低成本，更低性能的硬件中。对于最旧的数据（for your oldest data），最重要的是你仍然可以访问它，但是如果查询时间很长也是可以接受的。

&emsp;&emsp;Elasticsearch可以基于下面的内容帮你管理你的数据：

- 在data node上根据不同的性能属性定义[multiple tiers](###Data tiers)
- 根据你的性能要求以及[index lifecycle management](###ILM: Manage the index lifecycle)的保留策略（retention policy）自动的在不同的data tiers中进行转化。
- 利用[searchable snapshots](###Searchable snapshots)将数据存储在一个remote repository中， 为你的较旧的数据提供弹性。同时降低了操作开销并且保持查询性能。
- 在较低性能的硬件上执行数据的异步查询[asynchronous searches](###Long-running searches)

### ILM: Manage the index lifecycle
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-lifecycle-management.html)

&emsp;&emsp;你可以通过配置index lifecycle management（ILM），根据你的性能、弹性（resiliency）、保留（retention）需求来自动的管理索引。你可以使用ILM用于：

- 你可以在一个索引中文档数量达到某个大小时使用新的索引（spin up a new index）
- 按照每天、每周、每月创建一个新的索引并且归档之前的索引
- 删除过时的（stale）索引来强制执行数据保留标准

&emsp;&emsp;你可以通过Kidddbana Management或者ILM接口来创建管理索引生命周期策略。当你使用Elastic Agent、Beats、或者Logstash时会自动创建默认的ILM策略。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/index-lifecycle-policies.png">

> Tips: 使用[snapshot lifecycle policies](####Automate snapshots with SLM)自动备份你的索引以及管理snapshot。


- [Tutorial: Customize built\-in policies](###Tutorial: Customize built-in ILM policies)
- [Tutorial: Automate rollover](###Tutorial: Automate rollover with ILM)
- [Overview](###ILM overview)
- [Concepts](###ILM concepts)
- [Configure a lifecycle policy](###Configure a lifecycle policy)
- [Migrate index allocation filters to node roles](###Migrate index allocation filters to node roles)
- [Troubleshooting index lifecycle management errors](###Troubleshooting index lifecycle management errors)
- [Start and stop index lifecycle management](###Start and stop index lifecycle management)
- [Manage existing indices](###Manage existing indices)
- [Skip rollover](###Skip rollover)
- [Restore a managed data stream or index](###Restore a managed data stream or index)
- [Index lifecycle management APIs](###Index lifecycle management APIs)
- [Index lifecycle actions](###Index lifecycle actions)

### Tutorial: Customize built-in ILM policies
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/example-using-index-lifecycle-policy.html)

&emsp;&emsp;Elasticsearch包含了下列内置的ILM策略

- logs
- metris
- synthetics

&emsp;&emsp;Elastic Agent使用这些策略为data streams管理backing索引。这个教程展示如何基于你的性能、弹性（resiliency）、保留（retention）需求使用Kibana的**Index Lifecycle Policies**来自定义策略。

#### Scenario

&emsp;&emsp;你想要将日志文件发送给Elasticsearch集群用于可视化和分析数据，这些数据有下列的保留需求（retention requirement）

- 当write index达到50GB或者30天，转存（roll over）到一个新的索引
- 在转存后，在hot tier保留30天
- 30天以后转存
  - 将索引移动到warm tier
  - 将副本分片数量设置为1
  - [Force merge](####Force merge API)索引的段来释放被删除的文档占用的空间
- 在转存90后天删除索引

#### Prerequisites

&emsp;&emsp;要完成这个教程，你需要准备下面的内容:

- 集群中有hot和warm tier
  - Elasticsearch Service：在Elasticsearch Service上的部署默认有一个hot tier。若要添加一个warm tier，点击你的部署并点击**Add capacity**

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/tutorial-ilm-ess-add-warm-data-tier.png">

- Self-managed cluster：赋予节点`data_hot`和`data_warm`的角色，见[Data tiers.](###Data tiers)。

&emsp;&emsp;例如，在warm tier中的每一个节点的`elasticsearch.yml`中包含`data_warm`的节点角色：

```text
node.roles: [ data_warm ]
```

&emsp;&emsp;安装Elastic Agent并配置为将日志发送到你的Elasticsearch集群。

#### View the policy

&emsp;&emsp;Elastic Agent使用index pattern为`logs-*-*`的data streams来存储日志监控数据。内置的`logs`ILM策略自动的为data stream管理backing indices。

&emsp;&emsp;若要在Kibana中查看`logs`策略：

1. 打开菜单并进入**Stack Management > Index Lifecycle Policies**
2. 选择**logs**策略

&emsp;&emsp;`logs`策略使用了默认推荐的rollover：当前write index的大小达到50GB或者30天后就开始写入到新的索引。

&emsp;&emsp;若要查看或者修改rollover设置，点击hot阶段的**Advanced settings **。然后关闭**Use recommended defaults**来展示rollover设置。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/tutorial-ilm-hotphaserollover-default.png">

#### Modify the policy

&emsp;&emsp;默认的`logs`策略设计为防止生成很多小的按日创建的索引。你可以修改这个策略来满足你的性能要求以及资源使用管理。

1. 激活warm阶段并点击**Advanced settings**
   1. 设置**Move data into phase when**为**30 days old**。这个索引在转存后的30天移动到warm阶段
   2. 开启**Set replicas**并修改**Number of replicas**为**1**
   3. 开启**Force merge data**并设置**Number of segments**为**1**


<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/tutorial-ilm-modify-default-warm-phase-rollover.png"> 

2. 在warm阶段，点击trash 图标来开启delete阶段。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/tutorial-ilm-enable-delete-phase.png"> 

&emsp;&emsp;在delete阶段，将**Move data into phase when**设置为**90 days old**。这个索引在转存后30天被删除。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/tutorial-ilm-delete-rollover.png"> 

3. 点击**Save Policy**

### Tutorial: Automate rollover with ILM
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/getting-started-index-lifecycle-management.html#manage-time-series-data-without-data-streams)

&emsp;&emsp;当你不停的将带有时间戳的文档索引到Elasticsearch中，你通常会使用[data stream](###Data streams)，使得你可以周期性的转存（roll over）到一个新的索引。能让你实现一个 hot-warm-cold的架构，为你的最新数据满足性能要求，控制随着时间增加的成本，执行保留策略，并且能最大力度利用你的数据（get the most out of  your data）。

> TIP：Data streams最适合用于[append-only](####Append-only)的用例。如果你需要跨多个索引经常更新或者删除现有的文档，我们建议你使用一个index alias和index template。你仍然可以用ILM来管理并且rollover alias indices。直接跳到[Manage time series data without data streams](####Manage time series data without data streams)。

&emsp;&emsp;若要使用ILM自动的rollover并且管理一个data stream，你可以：

1. [Create a lifecycle policy](####Create a lifecycle policy)来定义合适的阶段和动作
2. [Create an index template](####Create an index template to create the data stream and apply the lifecycle policy)来创建data stream以及应用ILM策略， 为backing index配置索引设置和mapping
3. [Verify indices are moving through the lifecycle phases ](####Check lifecycle progress) as expected

&emsp;&emsp;见[rollover](####Rollover(concept))了解rolling indices。

> IMPORTANT：When you enable index lifecycle management for Beats or the Logstash Elasticsearch output plugin, lifecycle policies are set up automatically. You do not need to take any other actions. You can modify the default policies through [Kibana Management](###Tutorial: Customize built-in ILM policies) or the ILM APIs.

#### Create a lifecycle policy

&emsp;&emsp;生命周期策略指定了索引生命周期的阶段以及在每一个阶段中需要执行的动作。一个生命周期可以有最多5个阶段：`hot`，`warm`， `cold`，`frozen`，`delete`。

&emsp;&emsp;例如，你可能定义了一个`timeseries_policy`，它有两个阶段：

- `hot`阶段定义了一个rollover动作：当索引的大小达到为50GB（`max_primary_shard_size`）或者达到30天（`max-age`）就进行转存（roll over）
- `delete`阶段定义了在转存后的90天（`min_age`）就移除索引

> NOTE：`min_age`是相对于转存的时间而不是索引的创建时间。

&emsp;&emsp;你可以通过Kibana或者[create or update policy ](####Create or update lifecycle policy API) API来创建策略。若要通过Kibana创建策略，打开菜单并进入**Stack Management > Index Lifecycle Policies**，点击**Create policy**。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/create-policy.png">

&emsp;&emsp;API例子：

```text
PUT _ilm/policy/timeseries_policy
{
  "policy": {
    "phases": {
      "hot": {                                
        "actions": {
          "rollover": {
            "max_primary_shard_size": "50GB", 
            "max_age": "30d"
          }
        }
      },
      "delete": {
        "min_age": "90d",                     
        "actions": {
          "delete": {}                        
        }
      }
    }
  }
}
```

&emsp;&emsp;第5行，`min_age`默认值是`0ms`，所以新的索引会马上进入到`hot`阶段

&emsp;&emsp;第8行，满足任意一个后就触发`rollover`动作

&emsp;&emsp;第14行，转存后90天将索引移动到`delete`阶段

&emsp;&emsp;第16行，当索引进入到delete阶段后触发`delete`动作

#### Create an index template to create the data stream and apply the lifecycle policy

&emsp;&emsp;若要建立一个data stream， 首先创建一个index template来定制生命周期策略。因为模板用于data stream，所以它必须包括`data_stream`的定义。

&emsp;&emsp;例如，你可能创建一个名为`timeseries_template`的模板用于一个名为`timeseries`的data stream。

- `index.lifecycle.name`指定生命周期策略的名字，并应用到data stream中。

&emsp;&emsp;你可以使用Kibana创建模板向导程序来添加模板。在Kibana中，打开菜单并进入**Stack Management > Index Management**。在**Index Templates**页，点击**Create template**。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/create-index-template.png">

&emsp;&emsp;向导程序调用[create or update index template API](####Create or update index template API)并使用你指定的选项创建index template。

&emsp;&emsp;API例子：

```text
PUT _index_template/timeseries_template
{
  "index_patterns": ["timeseries"],                   
  "data_stream": { },
  "template": {
    "settings": {
      "number_of_shards": 1,
      "number_of_replicas": 1,
      "index.lifecycle.name": "timeseries_policy"     
    }
  }
}
```

&emsp;&emsp;第3行，当文档索引到`timeseries`中时应用这个模板

&emsp;&emsp;第8行，使用ILM策略的名字来管理data stream

#### Create the data stream

&emsp;&emsp;索引一篇文档到[index template](##Index templates)中定义的名字或定义在`index_patterns`中的wildcard pattern中。只要现有的data stream，索引或者index alias没有使用这个名字，那么索引请求会自动的创建单个backing index和其对应的data stream。Elasticsearch自动的将请求的文档索引到backing index中，这个backing index作为这个流的[write index](####Write index)。

&emsp;&emsp;例如，下面的请求创建了名为`timeseries`的data stream并且首先创建了first generation名为`.ds-timeseries-2099.03.08-000001`的backing index。

```text
POST timeseries/_doc
{
  "message": "logged the request",
  "@timestamp": "1591890611"
}
```

&emsp;&emsp;当生命周期策略中的rollover条件满足后，`rollover`动作就会：

- 创建名为`.ds-timeseries-2099.03.08-000002`的second generation的backing index。因为它是名为`timeseries` 的data stream中的backing index，名为`timeseries_template`的模板会将配置作用到这个新的索引
- 由于它是名为`timeseries`  的data stream中最新的索引，最新创建的backing index `.ds-timeseries-2099.03.08-000002`成为data stream的write index

&emsp;&emsp;每次rollover 条件满足后就重复上面的处理过程。你可以跨data stream中所有的backing indices进行查询。由名为`timeseries`的data steam，名为`timeseries_policy`的策略管理。写操作被路由到当前的write index。读操作由所有的backing indices处理。

#### Check lifecycle progress

&emsp;&emsp;若要获得管理中的索引（managed index）的状态，你可以使用ILM explain API，能让你获得：

- index正处于的阶段以及进入这个阶段的时间
- 当前正在执行的动作和步骤
- 是否有错误发生或者处理被阻塞

&emsp;&emsp;例如，下面的请求获取了名为`timeseries` 的data stream的backing indices的信息：

```text
GET .ds-timeseries-*/_ilm/explain
```

&emsp;&emsp;下面的响应显示data stream的first generation backing index正在等待`hot`阶段中的`rollover`动作。它仍在这个状态并且ILM继续调用`check-rollove-ready`直到rollover的条件满足：

```text
{
  "indices": {
    ".ds-timeseries-2099.03.07-000001": {
      "index": ".ds-timeseries-2099.03.07-000001",
      "index_creation_date_millis": 1538475653281,
      "time_since_index_creation": "30s",        
      "managed": true,
      "policy": "timeseries_policy",             
      "lifecycle_date_millis": 1538475653281,
      "age": "30s",                              
      "phase": "hot",
      "phase_time_millis": 1538475653317,
      "action": "rollover",
      "action_time_millis": 1538475653317,
      "step": "check-rollover-ready",            
      "step_time_millis": 1538475653317,
      "phase_execution": {
        "policy": "timeseries_policy",
        "phase_definition": {                    
          "min_age": "0ms",
          "actions": {
            "rollover": {
              "max_primary_shard_size": "50gb",
              "max_age": "30d"
            }
          }
        },
        "version": 1,
        "modified_date_in_millis": 1539609701576
      }
    }
  }
}
```

&emsp;&emsp;第6行，age of the index，用于跟`max_age`计算出rollover的时间

&emsp;&emsp;第8行，管理这个索引的策略名字

&emsp;&emsp;第10行，age of the indexed，用于转移到下一个阶段（在这个例子中跟age of the index一样）

&emsp;&emsp;第15行，ILM在这个索引上正在执行的步骤

&emsp;&emsp;第19行，当前阶段的定义（`hot`阶段的定义）

#### Manage time series data without data streams

&emsp;&emsp;尽管[data streams](##Data streams)是用于扩展和管理时序数据的一种便捷方式，它们被设计为用于append-only。我们认识到存在一些用例需要就地（in place）更新或者删除数据并且data streams不支持直接删除跟更新请求，所以Index APIs需要直接用于data stream的backing indice上。

&emsp;&emsp;在这些情况下，你可以使用index alias来管理包含时序数据的索引并且周期性的roll override到一个新的索引。

若要使用ILM并使用index alias来自动的rollover以及管理时序数据，你需要：

1. 创建一个生命周期策略，定义合适的阶段和动作，见[Create a lifecycle policy](####Create a lifecycle policy)
2. [Create an index template](####Create an index template to apply the lifecycle policy)将策略应用到每一个新的索引
3. [Bootstrap an index](####Bootstrap the initial time series index with a write index alias)作为最开始的write index
4. [Verify indices are moving through the lifecycle phases](####Check lifecycle progress) as expected

#### Create an index template to apply the lifecycle policy

&emsp;&emsp;若要在rollover上自动的将生命周期策略应用到新的write index上，那么在用于创建新索引的index template中指定策略。

&emsp;&emsp;例如，你可能创建了一个`timeseries_template`的模板，它将作用到索引名匹配了`timeseries-*`index pattern的索引。

&emsp;&emsp;若要自动的rollover，那么在模板中配置两个ILM设置：

- `index.lifecycle.name`指定生命周期策略的名字，该策略作用到匹配了index pattern的新的索引上
- `index.lifecycle.rollover_alias`指定了index alias，当rollover动作出发后，它将被roll over

&emsp;&emsp;你可以使用Kibana创建模板向导程序来添加模板。若要访问向导程序，打开菜单并且进入**Stack Management > Index Management**。在**Index Templates**页面，点击**Create template**。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/create-template-wizard.png">

&emsp;&emsp;创建模板请求：

```text
PUT _index_template/timeseries_template
{
  "index_patterns": ["timeseries-*"],                 
  "template": {
    "settings": {
      "number_of_shards": 1,
      "number_of_replicas": 1,
      "index.lifecycle.name": "timeseries_policy",      
      "index.lifecycle.rollover_alias": "timeseries"    
    }
  }
}
```

&emsp;&emsp;第3行，模板将作用到索引名以`timeseries-`开头的索引上

&emsp;&emsp;第8行，生命周期策略的名字，将作用到每一个新的索引上

&emsp;&emsp;第9行，alias的名字用于引用这些索引，要求策略使用rollover动作。

#### Bootstrap the initial time series index with a write index alias

&emsp;&emsp;你需要引导一个最开始的索引并且为你模板中的rollover alias将这个索引指为write index。这个索引的名字必须匹配模板中的index pattern并且以数字结尾。在转存时，这个数字会递增用于生成新的索引的名字。

&emsp;&emsp;例如，下面的请求创建了一个名为`timeseries-000001`的索引，并且让它作为名为 `timeseries` 的alias的write index。

```text
PUT timeseries-000001
{
  "aliases": {
    "timeseries": {
      "is_write_index": true
    }
  }
}
```

&emsp;&emsp;当满足了rollover的条件，`rollover`动作就会：

- 创建一个名为`timeseries-000002`的新的索引。这个名字匹配了`timeseries-*` pattern，所以`timeseries_template`模板中的设置会作用到这个新的索引上
- 将这个新的索引指为write index并且让引导索引（`timeseries-000001`）变成只读

#### Check lifecycle progress(index)

&emsp;&emsp;对管理中的索引检索其状态信息类似于data stream，见[check progress section ](####Check lifecycle progress)了解更多信息。唯一的不同是 indices namespace，so retrieving the progress will entail the following api call:

```text
GET timeseries-*/_ilm/explain
```

### Index management in Kibana
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-mgmt.html)

&emsp;&emsp;Kibana的**Index Management **功能是一种简单方便的用于管理你集群的索引，[data streams](##Data streams)，[index template](##Index templates)的方法。实践良好的（practising）索引管理可确保以最具成本效益的方式正确存储数据。

#### What you’ll learn

&emsp;&emsp;你将会学习到如何：

- 查看和编辑索引设置（index setting）
- 查看索引的mapping和statistic
- 执行index-level的操作，例如refresh
- 查看和管理data stream
- 创建index template自动的配置新的data stream和索引

#### Required permissions

&emsp;&emsp;如果你使用了Elasticsearch security feature，则需要下面的[security privileges](####Security privileges)：

- `monitor` cluster privilege 用于访问Kibana的**Index Management**功能
- `iew_index_metadata`和`manage` index privilege用于查看data stream和索引数据
- `manage_index_templates` cluster privilege 用于管理index template

&emsp;&emsp;若要在Kibana中添加这些privilege，进入**Stack Management > Security > Roles**。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/management_index_labels.png">

&emsp;&emsp;**Index Management**页面包含了你的索引概览。索引名旁边的标签（badge）告知这个索引是一个[follower index](####Create follower API)，[rollup index](####Get rollup index capabilities API)，还是一个[frozen](####Unfreeze index API)。

&emsp;&emsp;点击某个标签后将只显示这个标签类型的索引。你也可以用搜索框过滤索引。

&emsp;&emsp;你也可以下钻每一个索引来查看[index settings](####Index Settings)，[mapping](##Mapping)和statistic。在这个视图中，你也可以编辑index settings。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/management_index_details.png">

#### Perform index-level operations

&emsp;&emsp;使用**Manage**菜单执行index-level的操作。这个菜单在索引详情视图中可见，或者当你在索引概览页面选择一个或多个索引时可见。菜单包含了下面的动作：

- [Close index](####Close index API)
- [Force merge index](####Force merge API)
- [Refresh index](####Refresh API)
- [Flush index](####Flush API)
- [Delete index](####Delete index API)
- [Add lifecycle policy](###Configure a lifecycle policy)

#### Manage data streams

&emsp;&emsp;**Data Streams**视图中列出了你的data steams并且能让你测试或者删除它们。

&emsp;&emsp;若要查看data stream的更多的信息，比如它当前的generation或者当前的索引生命周期策略，那么点击流的名字。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/management_index_data_stream_stats.png">

&emsp;&emsp;若要查看backing index的信息，点击该索引即可。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/management_index_data_stream_backing_index.png">

#### Manage index templates

&emsp;&emsp;**Index Templates**视图中列出了你的模板以及测试，编辑，克隆和删除。对某个index template的更改不会影响现有的索引。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/management-index-templates.png">

&emsp;&emsp;如果你还没有任何的模板，你可以使用**Create template**向导程序创建模板。

##### Try it: Create an index template

&emsp;&emsp;在这个教程中，你将会创建一个模板然后使用它配置两个新的索引。

- **Step 1. Add a name and index pattern**

1. 在**Index Templates**视图中，打开**Create template**的向导程序

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/management_index_create_wizard.png">

2. 在**Name**中，输入`my-indeex-template`
3. 设置**Index pattern**为`my-index-*`，使得模板匹配这种Index pattern的索引
4. 不填写**Data stream**，**Priority**，**Version**以及**\_meta field**

- **Step 2. 添加settings，mappings和aliases**

1. 添加[component templates](####Create or update component template API)到你的index template中

&emsp;&emsp;Component templates时预先配置好的mappings, index settings, and aliases的集合，你可以跨多个Index template使用。标签指出了某个component template是否包含mappings（M），Index settings（S），aliases（A），或者是三者都有。

&emsp;&emsp;Component templates是可选的，对于这个教程，不要添加任何的component templates。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/management_index_component_template.png">

2. 定义Index settings（可选）。对于这个教程，可以不填
3. 定义一个mapping，它包含一个名为`geo`类型为[object](####Object field type)域，以及名为`coordinates`类型为[geo_point](####Geopoint field type)的子域（child filed）

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/management-index-templates-mappings.png">

&emsp;&emsp;或者你可以点击**Load JSON**，用JSON形式定义mapping：

```text
{
  "properties": {
    "geo": {
      "properties": {
        "coordinates": {
          "type": "geo_point"
        }
      }
    }
  }
}
```

&emsp;&emsp;你可以在**Dynamic templates**和**Advanced options**页面创建额外的mapping配置。对于这个教程，不创建任何额外的mappings。

4. 定义名为`my-index`的alias：

```text
{
  "my-index": {}
}
```

5. 在review 页面，检查整体的配置。如果一切看起来没有问题就点击**Create template**。

- **Step 3. Create new indices**

&emsp;&emsp;你现在可以使用你的index template来创建新的索引

1. 索引下面的文档创建两个索引：`my-index-000001`和`my-index-000002`

```text
POST /my-index-000001/_doc
{
  "@timestamp": "2019-05-18T15:57:27.541Z",
  "ip": "225.44.217.191",
  "extension": "jpg",
  "response": "200",
  "geo": {
    "coordinates": {
      "lat": 38.53146222,
      "lon": -121.7864906
    }
  },
  "url": "https://media-for-the-masses.theacademyofperformingartsandscience.org/uploads/charles-fullerton.jpg"
}

POST /my-index-000002/_doc
{
  "@timestamp": "2019-05-20T03:44:20.844Z",
  "ip": "198.247.165.49",
  "extension": "php",
  "response": "200",
  "geo": {
    "coordinates": {
      "lat": 37.13189556,
      "lon": -76.4929875
    }
  },
  "memory": 241720,
  "url": "https:\//theacademyofperformingartsandscience.org\/people/type:astronauts/name:laurel-b-clark/profile"
}
```

2. 使用[get index API](####Get index API)查看新索引的配置，这个索引使用了你之前创建的index template。

```text
GET /my-index-000001,my-index-000002
```

### ILM overview
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/overview-index-lifecycle-management.html)

&emsp;&emsp;你可以创建以及应用（apply）索引生命周期管理（ILM：index lifecycle management）并依据性能、弹性（resiliency）、保留（retention）需求来管理你的索引。

&emsp;&emsp;索引生命周期策略能触发下列的动作：

- Rollover：当前索引占用大小、包含的文档数量达到某个值，或者达到某个寿命（age）
- Shrink：减少索引的主分片数量
- Force merge：触发[force merge](####Force merge API)来降低索引的分片中的段的数量。
- Delete：永久的移除索引中的所有数据和元数据

&emsp;&emsp;ILM使得在热-暖-冷数据架构中管理索引变得更加容易。这种架构在处理比如日志（logs）、指标（metrics）这类时序数据中非常常见。

&emsp;&emsp;你可以这么指定：

- 当达到分片大小、包含的文档数量、寿命的最大值时转存（rollover）到一个新的索引
- 当索引不再更新，可以减少主分片的数量
- 通过force merge永久的移除被标记为删除的文档
- 将索引移动到性能较低的硬件中
- 当可用性（availability）没那么重要时，可以减少分片的数量
- 当索引可以被安全的移除时

&emsp;&emsp;例如，当你将成群的ATMs的指标数据写入到Elasticsearch中时，可以定义下面的策略：

- 当索引主分片的大小达到50GB时，转存到新的索引中
- 将老的索引（old index）移到[warm phase](####Index lifecycle)，标记为只读，并且收缩（shrink）为单个分片。
- 7天以后把索引移到[cold phase](####Index lifecycle)，并且移到成本较低的硬件中。
- 当达到30天的保留周期时就删除索引。


>IMPORTANT：想要使用ILM必须保证每个节点的版本号是一致的。在一个版本号混在的集群中使用ILM可能可以创建并应用策略，但是不能保证可以按照预期运行。在集群中尝试使用一个策略时，如果其对应的动作（action）不是所有节点都支持的话会引发错误。

### ILM concepts
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-concepts.html)

- [Index lifecycle](####Index lifecycle)
- [Rollover](####Rollover(concept))
- [Policy updates](####Lifecycle policy updates)

#### Index lifecycle
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-index-lifecycle.html)

&emsp;&emsp;ILM定义了五个生命周期阶段：

- Hot：这个索引的更新、查询操作非常的活跃
- Warm：这个索引不再更新，并且仍然会被查询
- Cold：这个索引不再更新并且很少（infrequent）被查询。索引中的数据仍然可以被搜索到，但是允许查询速度较慢
- Frozen：这个索引不再更新并且几乎（rare）不会被查询。索引中的数据仍然可以被搜索到，但是允许查询速度特别慢
- Delete：不再需要这个索引，可以被移除

&emsp;&emsp;一个索引的生命周期策略指定了哪个阶段是适用的（applicable）、在每一个阶段执行哪些动作、什么时候转变阶段（transition between phases）。

&emsp;&emsp;在你创建索引时可以手动应用一个生命周期策略。对于时序索引（time series indices），你需要用index template在时序中创建新的索引，并且将生命周期策略关联到index template。当转存（rollover）索引时，手动应用的策略不会自动应用到新索引。

&emsp;&emsp;如果你使用Elasticsearch的安全功能。ILM将以最后更新策略的用户的身份执行操作。ILM only has the [roles](####Defining roles) assigned to the user at the time of the last policy update。

##### Phase transitions

&emsp;&emsp;ILM根据生命周期中的寿命（age）来移动索引（move index）。你可以在每一个阶段设置一个寿命最小值（maximum age）来控制转变的时机（the timing of transition）。对于一个索引移动到另一个阶段来说，当前阶段的所有动作都必须已经完成并且the index must be older than the minimum age of the next phase。下一个阶段配置的最小寿命必须高于当前阶段。比如说warm阶段的寿命设置为10天，那么下一个阶段，即cold阶段的寿命要么不设置，要么设置成一个大于等于10天的值。

&emsp;&emsp;寿命最小值默认值为0，意味着当前阶段所有的动作都完后就马上转变到下一个阶段。

&emsp;&emsp;如果索引有未分配的分片，并且[cluster health status](####Cluster health API)的状态为yellow，仍然可以根据生命周期策略转移到下一个阶段。然后由于Elasticsearch只会在状态为green的集群上执行相关的clean up工作，所以可能会有不可预料的副作用。

&emsp;&emsp;为了避免不断增加的磁盘使用量和可靠性问题，请及时处理集群运行状况问题。

##### Phase execution

&emsp;&emsp;ILM控制了一个阶段中所有动作的执行顺序以及执行步骤来执行必要的索引操作。

&emsp;&emsp;当索引进入到一个阶段，ILM缓存索引元数据（index metadata）中的阶段定义（phase definition）。这使得不会因为更新策略将索引置入一个用于无法退出阶段的状态。如果可以安全的应用变更，ILM会更新已经缓存的阶段定义。如果不能，则使用缓存的定义继续执行下去。

&emsp;&emsp;ILM会周期的运行检查某个索引是否满足策略规则（policy criteria）并执行必要的步骤。为了避免race condition，ILM可能需要执行多次才能执行所有的步骤，每一个步骤都需要完成一个动作。例如，如果ILM检测到一个索引满足转存规则，它开始执行这个步骤并且要求完成这个转存动作。如果它到达了一个点使得不能安全的进行入到下一步骤，那么终止执行。下一次ILM运行后，它会继续执行（pick up execution where it left off）。这意味着即使`indices.lifecycle.poll_interval`设置为10分钟并且索引满足转存规则。也有可能需要20分钟来完成转存。

##### Phase actions

&emsp;&emsp;ILM支持每一个阶段中以下的动作，ILM有序执行这些动作。

- Hot
  - [Set Priority](####Set priority)
  - [Unfollow](####Unfollow)
  - [Rollover](####Rollover)
  - [Read-Only](####Read only)
  - [Shrink](####Shrink)
  - [Force Merge](####Force merge)
  - [Searchable Snapshot](####Searchable snapshot)

- Warm
  - [Set Priority](####Set priority)
  - [Unfollow](####Unfollow)
  - [Read-Only](####Read only)
  - [Allocate](####Allocate)
  - [Migrate](####Migrate)
  - [Shrink](####Shrink)
  - [Force Merge](####Force merge)

- Cold
  - [Set Priority](####Set priority)
  - [Unfollow](####Unfollow)
  - [Read-Only](####Read only)
  - [Searchable Snapshot](####Searchable snapshot)
  - [Allocate](####Allocate)
  - [Migrate](####Migrate)

- Frozen
  - [Searchable Snapshot](####Searchable snapshot)

- Delete
  - [Wait For Snapshot](####Wait for snapshot)
  - [Delete](####Delete)

#### Rollover(concept)
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-rollover.html)

&emsp;&emsp;当索引日志或者指标这类时序数据的时候，你不能往单个索引中无限的写入。为了能满足你的索引和查询性能要求以及资源使用的管理，你往一个索引中写入数据直到满足阈值，然后再创建一个新的索引，并开始写到这个索引中。使用 rolling indices可以让你：

- 优化active index，在高性能的`hot`节点上有high ingest rates
- 优化`warm`节点上的查询性能
- 将旧的，很少被访问的数据移动到低成本的`code`节点上
- 根据你的保留策略（retention policy），通过删除整个索引的方式来删除数据

&emsp;&emsp;我们建议使用[data streams](####Create data stream API)来管理时序数据。Data streams自动的跟踪write index并且保持配置最小化。

&emsp;&emsp;每一个data stream要求一个[index template](##Index templates)，模板中包括：

- 用于data stream的名字或者通配符（`*`)
- data stream的timestamp域。这个域必须是[date](####Date field typec)或者[date_nanos](####Date nanoseconds field type)的date类型，每一篇索引到data stream的文档都要包含这个域
- mappings和settings要应用到每一个创建的[backing index](####Backing indices)_上

&emsp;&emsp;Data streams为append-only data设计，data stream name可以用于作为操作（read，write，shrink等等）目标。如果你的用例要求就地（in place）更新数据。你可以使用[index aliases](##Aliases)来管理你的时序数据。然而需要一些配置步骤以及概念：

- 一个`index template`为序列中的每一个新索引指定了settings。你通常要通过有多少hot node就使用多少分片的方式来为提取（ingestion）优化
- 一个`index alias`引用了整个的索引集合
- 单个索引指为（designate）`write index`。这里处理所有的写请求的active index。On each rollover，新的索引会作为write index

##### Automatic rollover

&emsp;&emsp;ILM能让你自动的基于索引大小（index size）、文档数量（document count），寿命（age）转存（roll over）到一个新的索引。当触发了一个rollover，就会创建一个新的索引。write alias会被更新指向新的索引，接下来的更新都会写入到新的索引中。

> TIP：基于大小，文档数量，或者寿命的转存比基于时间（time-based）的更可取（prefer）。在任意的时间（arbitrary time）进行rolling over经常会生成小的索引，对性能和资源使用有负面的影响。

#### Lifecycle policy updates
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/update-lifecycle-policy.html)

&emsp;&emsp;你可以通过修改当前的策略或者切换到一个不同的策略来改变索引的生命周期或者rolling indices的收集的管理。

&emsp;&emsp;为了保证更新策略后不会让某个索引进入到某个状态后无法退出当前的阶段，因此在进入阶段后，阶段定义（phase definition）缓存在index metadata中。如果能安全的应用变更，ILM就更新缓存的阶段定义。如果不能，使用缓存的定义继续阶段的执行。

&emsp;&emsp;当索引进入到下一个阶段，它会使用更新后的策略中的阶段定义。

##### How changes are applied

&emsp;&emsp;当某个策略最开始应用到某个索引时，索引获取到了最新的策略版本号。如果你更新了策略，策略的版本号将被变更，ILM能检测侧到索引正在使用一个较早版本的策略，这个策略需要更新。

&emsp;&emsp;对`min_age`的更改不会propagate到缓存的定义。修改一个阶段的`min_age`不会影响当前正在执行那个阶段的索引。

&emsp;&emsp;例如，如果你创建了一个策略包含了一个hot 阶段并且没有指定`min_age`。当应用策略后，索引马上进入到这个阶段。如果你随后更新了策略并指定了值为一天的`min_age`。那不会影响已经进入到hot阶段的索引。策略更新后，新创建的索引只有等到一天后才能进入hot阶段。

##### How new policies are applied

&emsp;&emsp;当你应用一个策略来管理索引，索引会使用上一个策略来完成当前的策略。索引会在进入到下一个阶段时使用新的策略。

### Index lifecycle actions
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-actions.html)

| [Allocate](####Allocate) | 将分片移到性能特征（performance characteristic）不同的节点上并减少副本的数量 |
| :------: | ---- |
| [Delete](####Delete) | 永久移除索引 |
| [Force Merge](####Force merge(1)) | 减少索引中段的数量并且清楚（purge）被删除的文档。索引被置为只读 |
| [Migrate](####Migrate) | 将索引的分片迁移到当前ILM阶段对应的数据层（[data tier](###Data tiers)）中 |
| [Read-Only](####Read only) | 阻塞索引的写入操作 |
| [Rollover](####Rollover) | 为rollover alias移除作为write index的索引，并开始为新索引建立索引 |
| [Searchable Snapshot](####Searchable snapshot) | 在配置好的仓库中添加一个被管理的索引的快照，挂载这个快照使其成为一个可以用于搜索的快照。 |
| [Set Priority](####Set priority) | 降低索引在生命周期中的优先级，以确保首先恢复hot索引 |
| [Shrink](####Shrink) | 收缩到新的索引中并减少主分片的数量 |
| [Unfollow](####Unfollow) | 将一个follower index转化为普通索引。会在执行rollover、shrink、searchable snapshot动作前自动执行该动作 |
| [Wait For Snapshot](####Wait for snapshot) | 保证在删除索引前已经创建好了snapshot |



#### Allocate
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-allocate.html)

&emsp;&emsp;可以在warm、cold阶段使用该动作（action）。

&emsp;&emsp;更新（update）索引设置来变更（change）哪些节点允许存放（host）索引分片以及变更副本分片（replica）的数量。

&emsp;&emsp;不允许在hot阶段执行这个allocate动作。索引最初的分配必须通过手动或者[index template](##Index templates)实现。

&emsp;&emsp;你可以配置这个动作来同时修改分配规则（allocation rules）和副本数量，只修改分配规则或者只修改副本数量。见[Scalability and resilience](###Scalability and resilience: clusters, nodes, and shards)了解更多Elasticsearch如何使用副本来实现伸缩性（scaling）的信息。见[index-level shard allocation filtering](###Index-level shard allocation filtering)了解更多Elasticsearch如何控制指定索引的分片（shard）分配。

##### Options

&emsp;&emsp;你必须指定副本的数量或者至少包含一个`include`、`exclude`、`require`选项。空的allocate动作是非法的。

&emsp;&emsp;见[Index-level shard allocation filtering](###Index-level shard allocation filtering)了解更多使用自定义属性进行分片分配的信息。

###### number_of_replicas

&emsp;&emsp;（可选项，整数）索引的副本分片数量。

###### total_shards_per_node

&emsp;&emsp;（可选项，整数）在单个Elasticsearch节点上索引的分片数量最大值

###### include

&emsp;&emsp;（可选项，object）分配一个索引到一些节点，这些节点至少包含一个指定的自定义的属性。

###### exclude

&emsp;&emsp;（可选项，object）分配一个索引到一些节点，这些节点不包含任何自定义的属性。

###### require

&emsp;&emsp;（可选项，object）分配一个索引到一些节点，这些节点包含所有自定义的属性。

##### Example

&emsp;&emsp;下面的策略中的allocate动作将索引的副本分片数量更改为2。在任何单个节点上放置的索引分片不超过200个。否则不改变索引分配规则。

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "warm": {
        "actions": {
          "allocate" : {
            "number_of_replicas" : 2,
            "total_shards_per_node" : 200
          }
        }
      }
    }
  }
}
```

###### Assign index to nodes using a custom attribute

&emsp;&emsp;下面策略中的allocate动作将索引分配到一些节点，这些节点需要包含值为`hot`或者`warm`的`box_type`属性。

&emsp;&emsp;为了指明（designate）一个节点的`box_type`，你可以在节点配置（node configuration）中设置这个自定义的属性。例如说，在`elasticsearch.yml`中设置` node.attr.box_type: hot`。见[Enabling index-level shard allocation filtering](###Index-level shard allocation filtering)了解更多信息。

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "warm": {
        "actions": {
          "allocate" : {
            "include" : {
              "box_type": "hot,warm"
            }
          }
        }
      }
    }
  }
}
```

###### Assign index to nodes based on multiple attributes

&emsp;&emsp;allocate动作可以基于多个节点属性将索引分配到节点。下面的allocate动作基于`box_type`和`storage`这两个节点属性进行索引的节点分配。

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "cold": {
        "actions": {
          "allocate" : {
            "require" : {
              "box_type": "cold",
              "storage": "high"
            }
          }
        }
      }
    }
  }
}
```

######  Assign index to a specific node and update replica settings

&emsp;&emsp;下面策略中的allocate动作会将索引的副本分片数量更新为1个并且索引会被分配到带有值为`cold`的`box_type`属性的节点中。

&emsp;&emsp;为了指明（designate）一个节点的`box_type`，你可以在节点配置（node configuration）中设置这个自定义的属性。例如说，在`elasticsearch.yml`中设置` node.attr.box_type: cold`。见[Enabling index-level shard allocation filtering](###Index-level shard allocation filtering)了解更多信息。

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "warm": {
        "actions": {
          "allocate" : {
            "number_of_replicas": 1,
            "require" : {
              "box_type": "cold"
            }
        }
        }
      }
    }
  }
}
```

#### Delete
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-delete.html)

&emsp;&emsp;可以在delete阶段使用该动作。

&emsp;&emsp;永久移除索引。

##### Options

###### delete_searchable_snapshot

&emsp;&emsp;（可选值，布尔值）删除在上一个阶段生成可以用于搜索的快照（searchable snapshot）。默认值为true。这个适用于任意上一个阶段中使用了[searchable snapshot](####Searchable snapshot)这个动作。

##### Example

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "delete": {
        "actions": {
          "delete" : { }
        }
      }
    }
  }
}
```

#### Force merge(1)
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-forcemerge.html)

&emsp;&emsp;可以在hot、warm阶段使用该动作。

&emsp;&emsp;[Force merge](####Force merge API)将索引合并到指定数量的[segments](####Index segments API)中，这个动作会另索引变为[read-noly](####Dynamic index settings)。


>NOTE：forcemerge这个动作属于best effort。这个动作有可能在一些分片正在分配时执行，在这种情况下这些分片不会进行合并。

&emsp;&emsp;在`hot`阶段使用`forcemerge`动作需要有`rollover`动作。如果没有配置`rollover`动作，ILM会reject这个策略。

##### Performance considerations

&emsp;&emsp;Force merge是一个资源密集型（resource-intensive）的操作。如果一次性触发太多的force merge，将会对你的集群造成负面的影响。这种情况经常发生在你将一个包含force merge动作的ILM策略应用到现有的（existing）索引中。如果这些索引满足`min_age`规则（criteria），它们会马上被处理并经历多个阶段。你可以通过提高`min_age`或者设置`index.lifecycle.origination_date`来修改计算索引寿命的方式来阻止这种情况。

&emsp;&emsp;如果发生了force merge任务队列堆积，你可能需要增加force merge线程池的大小，使得索引可以并行的进行force merge。你可以通过配置`thread_pool.force_merge.size`  进行更改[cluster setting](####Cluster get settings API)。

>IMPORTANT：这种操作会造成持续性的（cascading）性能影响，监控好集群的性能并且提高线程池的大小慢慢的减少堆积。

&emsp;&emsp;Force merge动作会根据索引处于的阶段在对应的节点执行。处于`hot`阶段的forcemerge会在潜在较快的hot节点执行，同时对提取（ingestion）影响更大。处于`warm`阶段的forcemerge会在warm节点执行，执行时间会潜在较长，但是不会影响`hot`层的提取（ingestion）。

##### Option

###### max_num_segemnts

&emsp;&emsp;（必选，整数）合并后的段的数量。通过设置为1来实现完全的合并（fully merge）。

###### inde_codec

&emsp;&emsp;（可选值，字符串）用来对文档存储的codec。唯一可以设置的值是`best_compression`，使用了 [DEFLATE](https://en.wikipedia.org/wiki/Deflate)实现较高的压缩率但是较低的[存储域](https://www.amazingkoala.com.cn/Lucene/suoyinwenjian/2020/1013/169.html)的性能。不指定该参数则使用默认的[LZ4 codec](https://www.amazingkoala.com.cn/Lucene/yasuocunchu/2019/0226/37.html)。

>WARNING：如果使用`best_compression`，ILM将在force merge之前先[close](####Close index API)并且[re-open](####Open index API)。当关闭后，索引的读写操作将不可见。

##### Example

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "warm": {
        "actions": {
          "forcemerge" : {
            "max_num_segments": 1
          }
        }
      }
    }
  }
}
```


#### Migrate
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-migrate.html)

&emsp;&emsp;可以在warm、cold阶段使用该动作。

&emsp;&emsp;通过更新索引设置[index.routing.allocation.include.\_tier\_preference](######index.routing.allocation.include.\_tier\_preference)将索引移动到当前阶段对应的[data tier](###Data tiers)中。ILM自动在warm和cold阶段注入（inject）该动作。如果要阻止自动迁移（migration），你可以显示指定migrate动作，并且将enabled参数设置为`false`。

&emsp;&emsp;If the cold phase defines a searchable snapshot action the migrate action will not be injected automatically in the cold phase because the managed index will be mounted directly on the target tier using the same [\_tier\_preference](######index.routing.allocation.include.\_tier\_preference) infrastructure the migrate actions configures。

&emsp;&emsp;在`warm`阶段，如果[index.routing.allocation.include.\_tier\_preference](######index.routing.allocation.include.\_tier\_preference)设置为`data_warm`，`data_hot`，会将索引移动到[warm tier](####Warm tier)的节点上。如果没有一个节点是warm tier，那么就移动到 [hot tier](####Hot tier)。

&emsp;&emsp;在`cold`阶段，如果[index.routing.allocation.include.\_tier\_preference](######index.routing.allocation.include.\_tier\_preference)设置为`data_cold`，`data_warm`，`data_hot`，会将索引移动到[Cold tier](####Cold tier)的节点上。如果没有一个节点是cold tier，那么就移动到warm tier，否则移动到hot tier。

&emsp;&emsp;`frozen`阶段不允许执行migrate动作。这个阶段会直接将searchable snapshot挂载到`data_frozen`,`data_cold`,`data_warm`,`data_hot`中的一个。先移动到[frozen tier](####Frozen tier)，如果没有一个节点是frozen tier，则移动到cold tier，否则移动到warm tier，最终移动移到hot tier。

&emsp;&emsp;hot阶段不允许执行migrate动作。最初索引的分配是自动[执行](####Data tier index allocation)的，可以通过手动或者[index template](##Index templates)配置。

##### Option

###### enabled

&emsp;&emsp;（可选值，布尔）用于控制是否在migrate阶段自动的执行迁移动作。默认值是true。

##### Example

&emsp;&emsp;下面的策略中，ILM将索引迁移到warm node前先通过[allocate](####Allocate)动作减少副本的数量。

> NOTE：显示指定开启migrate动作是不需要的，ILM会自动执行migrate动作除非你关闭迁移。

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "warm": {
        "actions": {
          "migrate" : {
          },
          "allocate": {
            "number_of_replicas": 1
          }
        }
      }
    }
  }
}
```

##### Disable automatic migration

&emsp;&emsp;下面的策略中关闭了migrate动作并且将这个索引分配那些配置了`rack_id`的值为`one`或者`two`的节点。

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "warm": {
        "actions": {
          "migrate" : {
           "enabled": false
          },
          "allocate": {
            "include" : {
              "rack_id": "one,two"
            }
          }
        }
      }
    }
  }
}
```

#### Read only
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-readonly.html)

&emsp;&emsp;可以在hot、warm、cold阶段使用该动作。

&emsp;&emsp;使得索引变成只读，不再允许写入和元数据变更（metadata changes）操作。

&emsp;&emsp;在`hot`阶段使用`readonly`动作必须同时配置`rollover`动作。如果没有配置`rollover`动作，ILM会reject这个策略。

##### Options

&emsp;&emsp;无

##### Example

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "warm": {
        "actions": {
          "readonly" : { }
        }
      }
    }
  }
}
```

#### Rollover
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-rollover.html)

&emsp;&emsp;可以在hot阶段使用该动作。

&emsp;&emsp;当现有的索引满足一个或者多个转存（rollover）条件后转存到一个新的索引。

> IMPORTANT：如果rollover动作作用在一个[follower index](####Create follower API)，执行策略前需要等待leader index转存结束（或者[otherwise marked complete](###Skip rollover)），然后通过[unfollow](####Unfollow)动作将follower index转化为一个普通索引（regular index）。

&emsp;&emsp;转存对象可以是[data stream](###Data Streams)或者[index alias](##Aliases)。当转存对象是数据流（data stream）时，新的索引会变成数据流的write index并且提高它的generation。

&emsp;&emsp;要转存一个index alias，alias和它的的write index必须满足下面的条件：

- 索引名字必须满足这个pattern `^.*-\d+$`，例如（my-index-00001）
- `index.lifecycle.rollover_alias`必须配置为alias进行转存
- 索引必须是alias的[write index](####Write_index)

&emsp;&emsp;例如如果`my-index-000001`是名为`my_data`的alias。那么必须配置下面的设置：

```text
PUT my-index-000001
{
  "settings": {
    "index.lifecycle.name": "my_policy",
    "index.lifecycle.rollover_alias": "my_data"
  },
  "aliases": {
    "my_data": {
      "is_write_index": true
    }
  }
}
```

##### Options

&emsp;&emsp;你必须至少指定一个rollover条件。没有条件的rollover动作是非法的。

###### max_age

&emsp;&emsp;（可选项，[time units](####Time units)）达到在创建索引后开始流逝的时间（elapsed time）最大值后触发转存动作。总是从索引的创建时间开始计算流逝的时间，即使索引的原先的数据配置为自定义的数据。比如设置了[index.lifecycle.parse_origination_date](######index.lifecycle.parse_origination_date) 或者 [index.lifecycle.origination_date ](######index.lifecycle.origination_date)。

###### max_docs

&emsp;&emsp;（可选值，整数）当达到指定的文档数量最大值时触发转存。上一次refresh后新增的文档不在文档计数中。副本分片中的文档不在文档计数中。

###### max_size

&emsp;&emsp;（可选值，[byte units](####Byte size units)）当索引达到一定的大小时触发转存。指的是索引中所有主分片的大小总量。副本分片的数量不会参与统计。

> TIP：可以通过[\_cat indices API](####cat indices API)查看当前索引的大小。`pri.store.size`值显示了所有主分片的大小总量。

###### max_primary_shard_size

&emsp;&emsp;（可选值，[byte units](####Byte size units)）当索引中的最大的主分片的大小达到某个值就触发转存。它是索引中主分片大小的最大值。跟`max_size`一样，副本分片则忽略这个参数。

> TIP：可以通过[\_cat shard API](####cat shards API)查看当前分片的大小。`store`值显示了每一个分片的大小，`prirep`值指示了一个分片是主分片还是副本分片。

###### max_primary_shard_docs

&emsp;&emsp;（可选值，整数）当索引中最大的主分片的文档数量达到某个值就触发转存。这是索引中主分片中的文档数量最大值。跟`max_doc`一样，副本分片则忽略这个参数。

> TIP：可以通过[\_cat shard API](####cat shards API)查看当前分片的大小。`doc`值显示了每个分片中的文档数量。

##### Example

###### Roll over based on largest primary shard size

&emsp;&emsp;下面的例子中，当最大的主分片的大小达到50gb则转存这个索引。

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover" : {
            "max_primary_shard_size": "50GB"
          }
        }
      }
    }
  }
}
```

###### Roll over based on index size

&emsp;&emsp;下面的例子中，当索引大小至少100g则转存这个索引。

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover" : {
            "max_size": "100GB"
          }
        }
      }
    }
  }
}
```

###### Roll over based on document count

&emsp;&emsp;下面的例子中，当索引中包含了至少了100000000篇文档后转存这个索引。

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover" : {
            "max_docs": 100000000
          }
        }
      }
    }
  }
}
```

###### Roll over based on document count of the largest primary shard

&emsp;&emsp;在这个例子中，当这个索引中最大的主分片中的文档数量至少有一千万时转存这个索引。

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover" : {
            "max_primary_shard_docs": 10000000
          }
        }
      }
    }
  }
}
```

###### Roll over based on index age

&emsp;&emsp;在这个例子中，索引在创建后已经至少过了7天后转存这个索引。

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover" : {
            "max_age": "7d"
          }
        }
      }
    }
  }
}
```

###### Roll over using multiple conditions

&emsp;&emsp;当你指定了多个转存条件时，任何一个条件满足都会触发转存。这个例子中，要么索引在创建后已经至少过了7天后或者当索引大小至少100g时触发转存。

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover" : {
            "max_age": "7d",
            "max_size": "100GB"
          }
        }
      }
    }
  }
}
```

###### Rollover condition blocks phase transition

&emsp;&emsp;只有至少一个转存条件满足才会完成rollover动作，这意味着任何接下来的阶段在转存成功前都会被阻塞住。

&emsp;&emsp;例如，下面的策略在索引转存后就删除索引。在索引创建后的一天内不会去删除这个索引。

```text
PUT /_ilm/policy/rollover_policy
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover": {
            "max_size": "50G"
          }
        }
      },
      "delete": {
        "min_age": "1d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}
```


#### Searchable snapshot
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-searchable-snapshot.html)

&emsp;&emsp;可以在hot、cold、frozen阶段使用该动作。

&emsp;&emsp;在配置好的仓库中对被管理的索引（managed index）生成一个快照并且挂载它作为一个[searchable snapshot](###Searchable snapshots)。如果这个索引是[data stream](###Data Streams)的一部分，被挂载的索引将替换流中的原来的索引（original index）。

&emsp;&emsp;`searchable_snapshot`动作需要数据层（[data tiers](###Data tiers)），这个动作使用[index.routing.allocation.include.\_tier\_preference](######index.routing.allocation.include.\_tier\_preference)参数来挂载不同阶段对应的数据层（data tiers）的索引。在frozen阶段，这个动作会挂载到frozen层中前缀为`partial-`的[partially mounted index](######Partially mounted index)。在其他阶段，这个动作会挂载到对应数据层中前缀为`restored-`的[fully mounted index](######Fully mounted index)。

> WARNING：不要同时在hot跟cold阶段包含`searchable_snapshot`动作。这样会导致在cold阶段索引无法自动迁移（migrate）到cold tier。

&emsp;&emsp;如果在hot阶段使用了`searchable_snapshot`动作，那么接下来的阶段中不能包含`shrink`或者`forcemerge`动作。

&emsp;&emsp;这个动作不能在数据流的write index上执行。尝试这种操作会导致失败。为了可以将数据流中的索引转化为searchable snapshot，可以先[manually roll over](####Manually roll over a data stream)数据流，这将创建一个新的write index。因为这个索引不再是流的write index，所以当前动作可以将其转化为searchable snapshot。使用一个策略，在这个策略中的hot阶段使用[rollover](####Rollover)动作可以避免这种情况以及不需要为未来被管理的索引作手动转存。

> IMPORTANT：挂载并且重分配searchable snapshot的分片涉及到从snapshot仓库中拷贝分片内容。This may incur different costs from the copying between nodes that happens with regular indices。这些开销通常很低，但是在有些环境可能会很高。见[Reduce costs with searchable snapshots](####Reduce costs with searchable snapshots)了解更多内容。

&emsp;&emsp;默认情况下，snapshot会在delete阶段被[delete](####Delete)动作删除，可以通过在delete动作中设置`delete_searchable_snapshot`为`false`的方式来保留这个snapshot。

##### Options

##### snapshot_repository

&emsp;&emsp;（必须，字符串）存储snapshot的[repository](###Register a snapshot repository)。

##### force_merge_index

&emsp;&emsp;（可选值，布尔）将被管理的索引（managed index）强制合并到一个段。默认值为true。如果被管理的索引已经使用前面的[force merge](####Force merge)动作强制合并过了，那么`searchable snapshot`中的强制合并操作将不会执行。

>NOTE：forcemerge这个动作属于best effort。这个动作有可能在一些分片正在分配时执行，在这种情况下这些分片不会进行合并。如果不是所有的分片都执行了forcemerge，那么searchable_snapshot动作会继续执行。

&emsp;&emsp;合并操作在`searchable_snapshot`动作之前执行。如果在`hot`阶段使用`searchable_snapshot`动作，force merge将在hot节点上执行。如果在`cold`阶段使用`searchable_snapshot`动作，force merge将在`hot`或者`warm`阶段执行。

##### Example

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "cold": {
        "actions": {
          "searchable_snapshot" : {
            "snapshot_repository" : "backing_repo"
          }
        }
      }
    }
  }
}
```

#### Set priority
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-set-priority.html#ilm-set-priority)

&emsp;&emsp;可以在hot、warm、cold阶段使用该动作。

&emsp;&emsp;一旦策略进入hot、warm或cold阶段，就设置索引的[priority](####Index recovery prioritization)。节点重启后，优先级高的索引会在优先级低的索引之前恢复。

&emsp;&emsp;通常来说在hot阶段的索引应该有最高的优先级并且在cold阶段的索引应该有最低的优先级。例如：hot阶段的值为100，warm阶段的值为50，cold阶段的值为0。没有设置的话则优先级的默认值为1。

##### Options

###### priority

&emsp;&emsp;（必选，整数）索引的优先值。该值必须不小于0.设置为`null`则移除优先值。

##### Example

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "warm": {
        "actions": {
          "set_priority" : {
            "priority": 50
          }
        }
      }
    }
  }
}
```

#### Shrink
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-shrink.html)

&emsp;&emsp;可以在hot、warm阶段使用该动作。

&emsp;&emsp;将源索引（source index）设置为[read-only](#####index.blocks.read_only)并且收缩（shrink）到一个新的索引中，这个索引有很少的主分片（fewer primary shards）。生成的索引名字为`shrink-<random-uuid>-<original-index-name>`。这个动作对应于 [shrink API](####Shrink index API)。

&emsp;&emsp;在`shrink`动作执行后，那些指向源索引的aliases会指向收缩后的索引（shrunken index）。如果ILM在一个数据流（data stream）的[backing index](###Backing indices)上执行收缩操作时，收缩后的索引会替代流中的源索引。你不能在一个写索引（write index）上执行`shrink`动作。

&emsp;&emsp;如果要在`hot`阶段使用`shrink`动作，必须同时配置`rollover`动作。如果没有配置rollover动作，ILM会reject这个策略。

&emsp;&emsp;shrink动作会移除索引的`index.routing.allocation.total_shards_per_node`设置，意味着将取消限制。这个操作能保证索引的所有分片都被拷贝到同一个节点上。This setting change will persist on the index even after the step completes。

> IMPORTANT：如果收缩动作在[follower index](####Create follower API)上使用，执行策略前需要等待leader index转存结束（或者[otherwise marked complete](###Skip rollover)），然后在执行shrink动作前先通过[unfollow](####Unfollow)动作将follower index转化为一个普通索引（regular index）。

##### Shrink options

###### number_of_shards

&emsp;&emsp;（可选值，整数）收缩后分片的数量。必须是源索引的分片数量的因子值（factor）。这个参数跟下文的`max_primary_shard_size`冲突，只能设置一个值。

###### max_primary_shard_size

&emsp;&emsp;（可选值，[byte units](####Byte size units)）目标索引（target index）的主分片大小的最大值。用来找到目标索引的最适宜的（optimum）分片大小。当设置了这个参数后，目标索引中每一个分片大小（shard's storage）不会超过这个参数值。目标索引的分片数量将仍然是源索引（source index）的因子值（factor）。如果这个参数值比源索引中的分片大小还要小，那么目标索引中的分片数量将和源索引中的分片数量相等。比如说参数设置为50gb，如果源索引有60个主分片，一共100gb，那么目标索引将有2个主分片，每个分片的大小为50gb；如果源索引有60个主分片，一共1000gb，那么目标索引将有20个主分片，每个分片的大小为50gb，如果源索引有60个主分片，一共4000gb，那么目标索引仍然有60个主分片。这个参数跟上文中的`number_of_shards`存在冲突，只能选择一个对其设置。

##### Example

###### Set the number of shards of the new shrunken index explicitly

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "warm": {
        "actions": {
          "shrink" : {
            "number_of_shards": 1
          }
        }
      }
    }
  }
}
```

###### Calculate the optimal number of primary shards for a shrunken index

&emsp;&emsp;下面的策略使用`max_primary_shard_size`参数基于源索引中的存储大小自动的计算新的收缩后（shrunken）的索引的主分片的数量。

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "warm": {
        "actions": {
          "shrink" : {
            "max_primary_shard_size": "50gb"
          }
        }
      }
    }
  }
}
```

###### Shard allocation for shrink

&emsp;&emsp;在`shrink`动作执行期间，ILM将源索引的主分片分配到一个节点。在收缩完索引后，ILM基于你的分配规则将收缩后的索引分片分配给合适的节点。

&emsp;&emsp;这些分配步骤会因为下面的一些原因而导致失败，包括：

- 在`shrink`动作执行期间，一个节点被移除了
- 没有节点有足够的磁盘空间来存放源索引的分片
- Elasticsearch因为分配规则发生冲突而无法重新分配收缩后的索引

&emsp;&emsp;当分配步骤的其中一步失败后，ILM会等待[index.lifecycle.step.wait_time_threshold](######index.lifecycle.step.wait_time_threshold)，默认值为12小时。这个阈值会周期性让集群去解决任何导致分配失败的问题。

&emsp;&emsp;如果过了这个周期性的域值时间并且ILM还没有收缩完索引，ILM会尝试将源索引的主分片分配到其他节点。如果ILM收缩完索引但是不能在这个周期性的域值时间内重新分配收缩后的索引，ILM会删除收缩后的索引并且重新尝试整个`shink`动作。

#### Unfollow
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-unfollow.html#ilm-unfollow)

&emsp;&emsp;可以在hot、warm、cold、frozen阶段使用该动作。

&emsp;&emsp;将一个[CCR](###Cross-cluster replication APIs) follower index转变为普通索引（regular index），使得shrink、rollover、以及searchable snapshot动作可以在follower index上安全的执行。你可以在生命周期中移动follower index时直接使用unfollow。该动作不会对非follower index产生影响，阶段中执行该动作只是继续做下一个动作。

> NOTE：如果是作用在follower index上，这个动作会被[rollover](####Rollover)、[shrink](####Shrink)以及[searchable snapshot](####Searchable snapshot)动作自动触发。

&emsp;&emsp;这个动作会一直等到安全的将一个follower index转化为普通索引才会执行，必须满足下面的条件：

- lead index必须已经将`index.lifecycle.indexing_complete`设置为`true`。如果leader index使用[rollover](####Rollover)动作转存结束，这个设置会自动完成。并且也可以使用[index settings API](####Update index settings API)来手动设置。
- leader index上的所有操作都已经在follower index上执行。这个保证了当索引转变结束后不会丢失任何的操作。

&emsp;&emsp;一旦满足条件，unfollow动作将执行下面的操作：

- Pauses indexing following for the follower index.
- Closes the follower index.
- Unfollows the leader index.
- Opens the follower index (which is at this point is a regular index).

##### Options

&emsp;&emsp;无

##### Example

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "unfollow" : {}
        }
      }
    }
  }
}
```

#### Wait for snapshot
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-wait-for-snapshot.html)

&emsp;&emsp;可以在delete阶段使用该动作。

&emsp;&emsp;在移除索引前等待指定的[SLM](####Automate snapshots with SLM)策略执行结束。使得被删除的索引的snapshot是可见的。

##### Options

###### policy

&emsp;&emsp;（必选，字符串）SLM策略的名字，delete动作执行前需要等待执行这个策略。

##### Example

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "delete": {
        "actions": {
          "wait_for_snapshot" : {
            "policy": "slm-policy-name"
          }
        }
      }
    }
  }
}
```

### Configure a lifecycle policy
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/set-up-lifecycle-policy.html)

&emsp;&emsp;为了让ILM能够管理一个索引，必须在索引设置 `index.lifecycle.name`中指定一个合法的策略。

&emsp;&emsp;若要为[rolling indices](####Rollover(concept))配置一个生命周期策略，你需要创建一个策略然后添加到[index template](##Index templates)中。

&emsp;&emsp;若要使用策略来管理一个不进行roll over的索引，你可以在创建索引的时候指定一个生命周期策略，或者将一个策略应用到已存在的索引上。

&emsp;&emsp;ILM 策略存储在全局cluster state中，当你[take the snapshot](###Create a snapshot)时，你可以将`include_global_state`设置为`true`，使得在snapshot中包含策略。当存储snapshot后，会存储全局state中的所有策略并且任意相同名字的本地策略会被覆盖。

> IMPORTANT：
When you enable index lifecycle management for Beats or the Logstash Elasticsearch output plugin, the necessary policies and configuration changes are applied automatically. You can modify the default policies, but you do not need to explicitly configure a policy or bootstrap an initial index.

#### Create lifecycle policy

&emsp;&emsp;若要从Kibana中创建一个生命周期策略，打开菜单然后跳转到**Stack Management > Index Lifecycle Policies**。点击 **Create policy**。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/create-policy.png">

&emsp;&emsp;为策略指定生命周期策略的阶段以及每一个阶段中的动作（action）。

&emsp;&emsp;[create or update policy](####Create or update lifecycle policy API)被调用后，策略被添加到Elasticsearch 集群中。

```text
PUT _ilm/policy/my_policy
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover": {
            "max_primary_shard_size": "25GB" 
          }
        }
      },
      "delete": {
        "min_age": "30d",
        "actions": {
          "delete": {} 
        }
      }
    }
  }
}
```

&emsp;&emsp;第8行，当索引大小达到25G后进行转存（roll over）

&emsp;&emsp;第16行，在转存后的30天后删除索引

#### Apply lifecycle policy with an index template

&emsp;&emsp;若要使用策略来触发rollover动作，你需要在index template中配置策略用于创建每一个新的索引。你指定策略的名字和alias用于引用rolling indices。

&emsp;&emsp;你可以使用Kibana创建模板向导程序来创建一个模板。若要访问向导程序，打开菜单并且进入**Stack Management > Index Management**。在**Index Template**页面，点击**Create template**

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/create-template-wizard-my_template.png">

&emsp;&emsp;向导程序调用了[create or update index template API](####Create or update index template API)将模板添加到集群。

```text
PUT _index_template/my_template
{
  "index_patterns": ["test-*"], 
  "template": {
    "settings": {
      "number_of_shards": 1,
      "number_of_replicas": 1,
      "index.lifecycle.name": "my_policy", 
      "index.lifecycle.rollover_alias": "test-alias" 
    }
  }
}
```

&emsp;&emsp;第3行，使用这个模板用于所有索引名以`test-`开头的新的索引

&emsp;&emsp;第8行，应用`my_policy`到使用这个模板创建的索引上

&emsp;&emsp;第9行，定义一个index alias用于引用被`my_policy`管理的索引

#### Create an initial managed index

&emsp;&emsp;当为你自己的rolling indices设置策略时，你需要手动创建由策略管理的第一个索引，并且授权（designate）这个索引为write index。

> IMPORTANT：
When you enable index lifecycle management for Beats or the Logstash Elasticsearch output plugin, the necessary policies and configuration changes are applied automatically. You can modify the default policies, but you do not need to explicitly configure a policy or bootstrap an initial index.

&emsp;&emsp;索引的名字必须匹配定义在index template中的pattern并且以数字结尾。递增这个数字来生成由rollover动作创建的索引的名字。

&emsp;&emsp;例如，下面的请求创建了一个名为`test-00001`的索引。因为它匹配到了`my_template`中的index pattern，Elasticsearch自动的从这个应用这个模板中的设置。

```text
PUT test-000001
{
  "aliases": {
    "test-alias":{
      "is_write_index": true 
    }
  }
}
```

&emsp;&emsp;第5行，为这个alias设置最初的索引作为write index

&emsp;&emsp;现在你可以开始将数据索引到生命周期策略中指定的rollover alias中。对于这个样例策略`my_policy`，rollover的动作是在最初的索引大小超过25GB后就触发动作。ILM随后为`test-alias`创建一个新的索引并作为write index。

#### Apply lifecycle policy manually

&emsp;&emsp;你可以通过Kibana或者[update settings API](####Update index settings API)在创建索引时指定一个策略或者将某个策略应用到现有的索引上。在你应用某个策略后，ILM会马上开始管理索引。

> IMPORTANT：不要手动应用一个使用rollover动作的策略。使用rollover的策略必须由[index template](####Apply lifecycle policy with an index template)应用。否则当rollover动作闯将一个新的索引时不能carry forward这个策略。

&emsp;&emsp;`index.lifecycle.name`这个设置用于指定索引的一个策略。

```text
PUT test-index
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 1,
    "index.lifecycle.name": "my_policy" 
  }
}
```

&emsp;&emsp;第6行，为索引指定生命周期策略

#### Apply a policy to multiple indices

&emsp;&emsp;当你使用[update settings API](####Update index settings API)时，你可以使用索引名的通配符将某个策略应用到多个索引中。

> WARNING：当心无意中（inadvertent）匹配到了你不想修改的索引。

```text
PUT mylogs-pre-ilm*/_settings 
{
  "index": {
    "lifecycle": {
      "name": "mylogs_policy_existing"
    }
  }
}
```

&emsp;&emsp;第1行，更新所有以`mylogs-pre-ilm`开头的索引。

#### Switch lifecycle policies

&emsp;&emsp;若要切换某个索引的生命周期策略，可以按照下面的步骤：

1. 使用[remove policy API](####Remove policy from index API)移除现有的策略。目标是一个data stream或者alias，移除它的所有索引的策略。

```text
POST logs-my_app-default/_ilm/remove
```

2. 移除策略的API会移除索引中所有的ILM metadata并且不会考虑索引的生命周期状态。这会让索引处于一个不希望（undesired）的状态。

&emsp;&emsp;例如，[forcemerge](####Force merge)动作会在重新打开一个index前先关闭它。在`forcemerge`期间移除一个索引的ILM的策略会让索引永久的（indefinite）关闭。

&emsp;&emsp;移除策略后，使用[get index API ](####Get index API)检查索引的状态。目标是一个data stream或者alias来获得所有它的索引的状态。

```text
GET logs-my_app-default
```

&emsp;&emsp;随后你可以按需来变更索引。比如你可以使用[open index API](####Open index API)重新打开任何被关闭的索引。

```text
POST logs-my_app-default/_open
```

3. 使用[update settings API](####Update index settings API)分配（assign）一个新的策略。目标是一个data stream或者alias的所有索引

> WARNING：首先移除现有的策略再分配一个新的策略。否则会导致[phase execution](####Phase execution)出现故障。

```text
PUT logs-my_app-default/_settings
{
  "index": {
    "lifecycle": {
      "name": "new-lifecycle-policy"
    }
  }
}
```

### Migrate index allocation filters to node roles
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/migrate-index-allocation-filters.html)

&emsp;&emsp;如果你正在一个[hot-warm-cold](https://www.elastic.co/blog/implementing-hot-warm-cold-in-elasticsearch-with-index-lifecycle-management)的架构中使用自定的节点属性（custom node attribution）和[attribute-based allocation filters](####Index-level shard allocation filtering)将索引移动到[data tiers](###Data tiers)。我们建议你转而使用内置的节点属性并且自动的[data tier allocation](####Data tier index allocation)。使用节点角色能让ILM自动的将索引在data tiers间移动。

> NOTE：尽管我们建议在hot-warm-cold架构中使用自动化的data tier allocation来管理你的数据，你依然可以出于其他的目的使用 attribute-based allocation filters来控制分片的分配。

&emsp;&emsp;Elasticsearch Service和Elastic Cloud Enterprise能自动的执行迁移（migration）。对于self-managed 的部署，你需要手动的更新你的配置，ILM策略，以及索引来切换到节点角色。

#### Automatically migrate to node roles on Elasticsearch Service or Elastic Cloud Enterprise

&emsp;&emsp;在Elasticsearch Service和Elastic Cloud Enterprise中，如果你使用默认部署模板中的节点属性，you will be prompted to switch to node roles when you：

- 更新到Elasticsearch 7.10或者更高
- 部署一个warm，cold，或者frozen data tier
- [Enable autoscaling](https://www.elastic.co/guide/en/cloud/current/ec-autoscaling.html)

&emsp;&emsp;这些动作自动的更新你的集群配置和ILM 策略以使用节点角色。另外，更新到7.14或者更高版本后，每当部署应用任何配置更改时，都会自动更新ILM策略。

&emsp;&emsp;如果你使用自定义的index template，在自动迁移完成后检查这个模板并移除每一个[attribute-based allocation filters](####Index-level shard allocation filtering)。

> NOTE：自动迁移后你不需要再执行任何的步骤。下文手动的步骤只有在你不允许自动迁移或者自己管理部署时才需要。

#### Migrate to node roles on self-managed deployments

&emsp;&emsp;若要切换到（switch to）使用节点角色：

1. 分配（assign）[data node](#####Assign data nodes to a data tier)到合适的data tier
2. 从你的ILM中[Remove the attribute-based allocation settings ](#####Remove custom allocation settings from existing ILM policies)
3. 在新的索引上[Stop setting the custom hot attribute ](#####Stop setting the custom hot attribute on new indices)
4. 更新现有的索引用于[set a tier preference](#####Troubleshooting index lifecycle management errors)

##### Assign data nodes to a data tier

&emsp;&emsp;为每一个data node配置一个合理的角色，分配给这些节点一个或者多个data tiers：`data_hot`, `data_content`, `data_warm`, `data_cold`, 或者 `data_frozen`。节点也可以有其他的[role](#####Node roles)。默认情况下，新的节点配置为有所有的角色。

&emsp;&emsp;当你添加一个data tier到一个Elasticsearch Service deployment，一个或多个节点会被自动的配置对应的角色。你可以显示的通过[Update deployment API](https://www.elastic.co/guide/en/cloud/current/ec-api-deployment-crud.html#ec_update_a_deployment)修改Elasticsearch Service deployment中的节点的角色。用合适的`node_roles`代替节点的`node_type`配置。例如，下面的配置将节点添加到hot和content tiers，并让这个节点作为一个ingest node，remote 和transform node。

```text
"node_roles": [
  "data_hot",
  "data_content",
  "ingest",
  "remote_cluster_client",
  "transform"
],
```

&emsp;&emsp;如果你直接管理自己的集群，在每个节点的`elasticsearch.yml`中配置合适的角色。例如下面的设置将节点配置为一个位于hot和content tier的data-only的节点。

```text
node.roles [ data_hot, data_content ]
```

##### Remove custom allocation settings from existing ILM policies

&emsp;&emsp;更新每一个生命周期中[allocate](####Allocate)动作来移除attribute-base allocation settings。ILM会在每一个阶段中inject一个[migrate](####Migrate)动作来自动的在data tiers上转移（transition）索引。

&emsp;&emsp;如果allocate动作不设置副本分片（replica）的数量，则移除整个allocate动作。（空的allocate动作是非法的）

> IMPORTANT：
The policy must specify the corresponding phase for each data tier in your architecture. Each phase must be present so ILM can inject the migrate action to move indices through the data tiers. If you don’t need to perform any other actions, the phase can be empty. For example, if you enable the warm and cold data tiers for a deployment, your policy must include the hot, warm, and cold phases.

##### Stop setting the custom hot attribute on new indices

&emsp;&emsp;当你创建了一个data stream，它的第一个backing index自动的被分配到`data_hot`节点。同样的，当你直接创建了一个索引，自动的分配到`data_content`节点。

&emsp;&emsp;在[Elasticsearch Service deployments](https://www.elastic.co/cloud/elasticsearch-service/signup?baymax=docs-body&elektra=docs)，移除index template `cloud-hot-warm-allocation-0`，它会在所有节点上设置hot shard allocation attribution。

```text
DELETE _template/.cloud-hot-warm-allocation-0
```

&emsp;&emsp;如果你正在使用一个自定义的模板，更新这个模板来移除用于分配新的索引到hot tier的[attribute-based allocation filters ](####Index-level shard allocation filtering)。

&emsp;&emsp;To completely avoid the issues that raise when mixing the tier preference and custom attribute routing setting we also recommend updating all the legacy, composable, and component templates to remove the [attribute-based allocation filters](####Index-level shard allocation filtering) from the settings they configure.

##### Set a tier preference for existing indices

&emsp;&emsp;ILM通过在每一个阶段中inject一个[migrate](####Migrate)动作来自动的将管理的索引转移到可用的data 

&emsp;&emsp;为了能让ILM将现有的索引移动到data tier，更新下面的index settings：

- 通过将设置置为`null`的方式来移除自定义的allocation filter
- 设置[tier preference](######index.routing.allocation.include.\_tier\_preference)

&emsp;&emsp;例如，如果在你的旧模板中设置属性`data`的值为`hot`将分片分配到hot tier，那么将属性`data`设置为`null`并且设置`_tier_preference`为`data_hot`。

```text
PUT my-index/_settings
{
  "index.routing.allocation.require.data": null,
  "index.routing.allocation.include._tier_preference": "data_hot"
}
```

&emsp;&emsp;对于已经从hot阶段转移出去的索引，tier preference必须包含了一个合适的回滚tier（fallback tier）使得期望用于分配的（prefer）tier如果不可见的话也能分配它。例如为已经在warm阶段中的索引指定一个回滚tier。

```text
PUT my-index/_settings
{
  "index.routing.allocation.require.data": null,
  "index.routing.allocation.include._tier_preference": "data_warm,data_hot"
}
```

&emsp;&emsp;如果索引已经在cold阶段，回滚tier可以是cold，warm和hot阶段。

&emsp;&emsp;如果索引同时配置了`_tier_preference`和`require.data`，但是`_tier_preference` 是outdated（比如说，节点属性的配置比配置的`_tier_preference`更"colder"）。那migration必须要移除`require.data`属性并且将`_tier_preference`更新为正确的tier。

&emsp;&emsp;例如，有以下路由配置的索引：

```text
{
  "index.routing.allocation.require.data": "warm",
  "index.routing.allocation.include._tier_preference": "data_hot"
}
```

&emsp;&emsp;路由配置应该修复为：

```text
PUT my-index/_settings
{
  "index.routing.allocation.require.data": null,
  "index.routing.allocation.include._tier_preference": "data_warm,data_hot"
}
```

&emsp;&emsp;这种情况有可能发生在默认是data tiers的系统中。例如ILM使用了存储的节点属性并将管理的索引从hot阶段转移到warm阶段，在这种情况下，节点属性的配置指出了索引应该被分配到的正确的tier。

### Troubleshooting index lifecycle management errors
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-lifecycle-error-handling.html)

&emsp;&emsp;当ILM执行一个生命周期策略时，有可能在某个步骤中执行必要的索引操作时候发生错误。发生错误后，ILM将索引移动到`ERROR`。如果ILM不能自动的解决错误，执行过程将被暂停直到你解决了策略，索引，或者集群的问题才能继续。

&emsp;&emsp;例如你可能有一个`shink-index`的策略：一旦索引的寿命达到5天，其分片数量收缩到4个：

```text
PUT _ilm/policy/shrink-index
{
  "policy": {
    "phases": {
      "warm": {
        "min_age": "5d",
        "actions": {
          "shrink": {
            "number_of_shards": 4
          }
        }
      }
    }
  }
}
```

&emsp;&emsp;不会有任何的障碍阻止你应用`shrink-index`策略到一个新的索引上，尽管这个索引只有两个分片：

```text
PUT /my-index-000001
{
  "settings": {
    "index.number_of_shards": 2,
    "index.lifecycle.name": "shrink-index"
  }
}
```

&emsp;&emsp;五天后，ILM试图将索引`my-index-000001`的分片数从两个分片收缩到四个分片。因为[shrink](####Shrink)动作不会增加分片的数量，所以这个操作会失败，ILM将`my-index-000001`移动到`ERROR`。

&emsp;&emsp;你可以使用[ILM Explain API](####Explain lifecycle API)来获取发生错误的相关信息：

```text
GET /my-index-000001/_ilm/explain
```

&emsp;&emsp;上述请求返回如下的信息：

```text
{
  "indices" : {
    "my-index-000001" : {
      "index" : "my-index-000001",
      "managed" : true,
      "index_creation_date_millis" : 1541717265865,
      "time_since_index_creation": "5.1d",
      "policy" : "shrink-index",                
      "lifecycle_date_millis" : 1541717265865,
      "age": "5.1d",                            
      "phase" : "warm",                         
      "phase_time_millis" : 1541717272601,
      "action" : "shrink",                      
      "action_time_millis" : 1541717272601,
      "step" : "ERROR",                         
      "step_time_millis" : 1541717272688,
      "failed_step" : "shrink",                 
      "step_info" : {
        "type" : "illegal_argument_exception",  
        "reason" : "the number of target shards [4] must be less that the number of source shards [2]"
      },
      "phase_execution" : {
        "policy" : "shrink-index",
        "phase_definition" : {                  
          "min_age" : "5d",
          "actions" : {
            "shrink" : {
              "number_of_shards" : 4
            }
          }
        },
        "version" : 1,
        "modified_date_in_millis" : 1541717264230
      }
    }
  }
}
```

&emsp;&emsp;第8行，用于管理索引的策略：`shrink-index`

&emsp;&emsp;第10行，索引寿命：5.1天

&emsp;&emsp;第11行，索引当前位于`warm`阶段

&emsp;&emsp;第13行，当前的动作：`shrink`

&emsp;&emsp;第15行，索引当前位于`ERROR`步骤

&emsp;&emsp;第17行，执行失败时位于`shrink`步骤

&emsp;&emsp;第19行，错误类型和错误的描述

&emsp;&emsp;第24行，`shrink-index`策略中当前阶段的定义

&emsp;&emsp;若要解决这个问题，你可以更新策略：在5天后将索引收缩为单个分片：

```text
PUT _ilm/policy/shrink-index
{
  "policy": {
    "phases": {
      "warm": {
        "min_age": "5d",
        "actions": {
          "shrink": {
            "number_of_shards": 1
          }
        }
      }
    }
  }
}
```

#### Retrying failed lifecycle policy steps

&emsp;&emsp;一旦你解决位于`ERROR`步骤的索引的问题，你可能需要显示的告知ILM进行重试：

```text
POST /my-index-000001/_ilm/retry
```

&emsp;&emsp;ILM接下来尝试重新运行刚刚失败的步骤。你可以使用[ILM Explain API](####Explain lifecycle API)查看处理进程。

#### Common ILM errors

&emsp;&emsp;以下是一些常见在`ERROR`步骤中报出的错误以及解决方案。

> TIP：
Problems with rollover aliases are a common cause of errors. Consider using [data streams](##Data streams) instead of managing rollover with aliases.

##### Rollover alias [x] can point to multiple indices, found duplicated alias [x] in index template [z]

&emsp;&emsp;index template的`index.lifecycle.rollover_alias`指定了目标rollover alias。你需要在[bootstrap the initial index](####Bootstrap the initial time series index with a write index alias)时显示的配置一次这个alias。rollover动作才能管理设置并更新alias来对接下来的索引进行[roll over](####Rollover API)。

##### index.lifecycle.rollover_alias [x] does not point to index [y]

&emsp;&emsp;要么索引使用了错误的alias或者alias不存在。

&emsp;&emsp;检查[index setting](####Get index settings API)的`index.lifecycle.rollover_alias`。若要查看alias配置的内容，可以使用[\_cat/aliases](####cat aliases API)。

##### Setting [index.lifecycle.rollover_alias] for index [y] is empty or not defined

&emsp;&emsp;必须为rollover动作配置`index.lifecycle.rollover_alias`。

&emsp;&emsp;更新index settings来设置`index.lifecycle.rollover_alias`。

##### Alias [x] has more than one write index [y,z]

&emsp;&emsp;对于某一个alias，只能有一个索引可以被指派（designate）为write index。

&emsp;&emsp;使用[aliases ](####Aliases API) API，将除了某一个索引以外的其他索引都设置`is_write_index:false`

##### index name [x] does not match pattern ^\.\*-\\d+

&emsp;&emsp;为了rollover动作可以工作，索引名必须匹配regex pattern "^.\*-\d+"。最常见的问题是索引名不包含尾随数字（trailing digits）。例如，`my-index`不匹配pattern要求。

&emsp;&emsp;索引名字尾部追加一个数值，例如`my-index-000001`。

##### CircuitBreakingException: [x] data too large, data for [y]

&emsp;&emsp;说明集群达到了资源限制。

&emsp;&emsp;在继续设置ILM前，你需要采取方式来缓和（alleviate）资源问题。见[Circuit breaker errors](####Circuit breaker errors)了解更多信息。

##### High disk watermark [x] exceeded on [y]

&emsp;&emsp;说明cluster的磁盘空间快满了。当你的ILM中没有设置从hot节点转存到warm节点时可能会发生。

&emsp;&emsp;考虑添加节点，更新你的硬件，或者删除不要的索引。

##### security_exception: action [\<action-name>] is unauthorized for user [\<user-name>] with roles [\<role-name>], this action is granted by the index privileges [manage_follow_index,manage,all]

&emsp;&emsp;说明ILM动作不能执行的原因是因为用户没有合适的privilege。在更新完ILM策略后用户的privilege可能被drop。ILM动作是以最后一个修改策略的用户运行的。创建或者修改策略的用户应该有这个策略中所有操作的权限。

### Start and stop index lifecycle management
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/start-stop-ilm.html)

&emsp;&emsp;默认情况下，ILM服务处于`RUNNING`的状态并且管理所有有生命周期策略的索引。你可以停止index lifecycle management为所有的索引暂停管理操作。例如，当执行定期维护或者对集群作变更且会对ILM动作的执行造成影响时关闭ILM。

> IMPORTANT：When you stop ILM, SLM operations are also suspended. No snapshots will be taken as scheduled until you restart ILM. In-progress snapshots are not affected

#### Get ILM status

&emsp;&emsp;使用[Get Status API](####Get index lifecycle management status API)查看当前ILM服务的状态：

```text
GET _ilm/status
```

&emsp;&emsp;正常条件下，响应会显示ILM处于`RUNNING`：

```text
{
  "operation_mode": "RUNNING"
}
```

#### Stop ILM

&emsp;&emsp;若要停止ILM服务并且暂停所有生命周期策略的执行，使用[Stop API](####Stop index lifecycle management API)：

```text
POST _ilm/stop
```

&emsp;&emsp;ILM服务将所有的策略运行到某个点使得可以安全的停止。当ILM服务正在关闭中，状态API会显示ILM处于`STOPPING`模式：

```text
{
  "operation_mode": "STOPPING"
}
```

&emsp;&emsp;一旦所有的策略处于一个安全的停止点，ILM就进入`STOPPED`模式：

```text
{
  "operation_mode": "STOPPED"
}
```

#### Start ILM

&emsp;&emsp;若要启动ILM并且恢复执行策略，使用[Start API](####Start index lifecycle management API)。这将ILM服务置为`RUNNING`的状态，ILM开始从离开的地方执行策略。

```text
POST _ilm/start
```

### Manage existing indices
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-with-existing-indices.html)

&emsp;&emsp;如果你使用了Curator或者其他机制来管理周期性（periodic）的索引，那么迁移到ILM时有两个选项：

- 设置index template来使用ILM策略管理你的新索引。一旦ILM正在管理你当前的write index，你可以应用一个合适的策略到你的旧索引
- reindex到一个ILM-managed index

> NOTE：Starting in Curator version 5.7, Curator ignores ILM managed indices.

#### Apply policies to existing time series indices

&emsp;&emsp;转移（transition）管理你周期性的索引的最简单的方式就是使用ILM ，[configure an index template](####Apply lifecycle policy with an index template)，将生命周期策略应用到新的索引上。一旦你正在写入的索引由ILM管理，你可以对你的旧索引[manually apply a policy](#####Apply a policy to multiple indices)。

&emsp;&emsp;为你的旧索引定义一个不同的策略，策略中omit rollover动作。Rollover用于管理新的数据，所以不适用旧数据。

&emsp;&emsp;注意的是对现有的索引应用策略后，每一个阶段的`min_age`会跟索引的创建时间作比较，所以有可能会立即处理多个阶段。如果你的策略属于资源密集型的操作比如force merge，当切换到ILM时你不会想要让很多的索引马上一下子都执行这些操作。

&emsp;&emsp;你可以为现有的索引指定不同的`min_age`，或者设置[index.lifecycle.origination_date](######index.lifecycle.origination_date)来控制索引寿命（age）的计算。

&emsp;&emsp;Once all pre-ILM indices have been aged out and removed, you can delete the policy you used to manage them。

> NOTE：If you are using Beats or Logstash, enabling ILM in version 7.0 and onward sets up ILM to manage new indices automatically. If you are using Beats through Logstash, you might need to change your Logstash output configuration and invoke the Beats setup to use ILM for new data.

#### Reindex into a managed index

&emsp;&emsp;另一种[applying policies to existing indices](###Manage existing periodic indices with ILM)就是将你的数据reindex到一个ILM-managed index中。如果创建了数据很小导致过多的（excessive）分片数量的周期性索引或者一直往相同的索引中写入导致大分片以及性能问题，那么你可能就想要reindex。

&emsp;&emsp;首先，你需要设置新的ILM-managed 索引：

1. 更新index template来包含必要的ILM 设置
2. 引导（bootstrap）一个最初的索引作为write index
3. 停止往旧索引中写入数据并且使用指向引导索引的alias来索引新的文档

&emsp;&emsp;若要reindex到ILM管理的索引中：

1. 如果你不想要将新旧索引混合到ILM-managed index中，那么先停止索引新的文档。将新旧索引混合到一个索引是安全，但是混合的索引需要被保留直到你准备删除新的数据。
2. 降低ILM的拉取间隔时间（poll interval）来保证索引的大小在等待rollover的检查时不会增长的太大。默认情况下，ILM每十分钟会检查要执行哪些动作：

```text
PUT _cluster/settings
{
  "persistent": {
    "indices.lifecycle.poll_interval": "1m" 
  }
}
```
&emsp;&emsp;第4行，每隔一分钟就检查下例如rollover的ILM动作是否要执行。

3. 使用[reindex API](####Reindex API)来reindex你的数据。如果你想要根据写入的时间有序的拆分数据，你可以运行多个reindex请求。

> IMPORTANT：Documents retain their original IDs. If you don’t use automatically generated document IDs, and are reindexing from multiple source indices, you might need to do additional processing to ensure that document IDs don’t conflict. One way to do this is to use a [script](#####Modify documents during reindexing) in the reindex call to append the original index name to the document ID.

```text
POST _reindex
{
  "source": {
    "index": "mylogs-*" 
  },
  "dest": {
    "index": "mylogs", 
    "op_type": "create" 
  }
}
```

&emsp;&emsp;第4行，匹配现有的索引。为新的索引使用前缀使得index pattern更加简单

&emsp;&emsp;第7行，alias指向了你的bootstrapped index

&emsp;&emsp;第8行，如果多个文档有相同的ID就暂停reindex。这种做法是推荐的，能防止在不同的源索引中出现ID相同的文档时覆盖文档的问题


4. reindex完成后，将ILM的拉取时间间隔（poll interval）设置回默认的值，避免master node上没必要的负载：

```text
PUT _cluster/settings
{
  "persistent": {
    "indices.lifecycle.poll_interval": null
  }
}
```

5. 使用相同的alias恢复索引新的数据。

&emsp;&emsp;使用这个alias进行查询时会检索你的新数据跟所有的reindex的数据。

6. 一旦你已经验证了在新的managed index中所有的reindex数据都是可用的，你就可以安全的移除旧的索引。

### Skip rollover
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/skipping-rollover.html)

&emsp;&emsp;当`index.lifecycle.indexing_complete`设置为`true`，ILM不会在这个索引上执行rollover动作，即使满足了rollover的标准。当rollover成功完成后，ILM会自动的设置该参数。

&emsp;&emsp;如果你需要在正常的生命周期策略中有一个例外以及更新alias来强制rollover，但是想要ILM能继续的管理索引，那你可以手动的设置该参数来跳过rollover。如果你使用了rollover API，那就不需要手动的配置这个设置。

&emsp;&emsp;如果索引的生命周期策略被移除了，这个设置也会被移除。

> IMPORTANT：当`index.lifecycle.indexing_complete`设置为`true`，ILM会核实（verify）这个索引不再是`index.lifecycle.rollover_alias`中指定的write index。如果索引仍然是write index或者rollover alias没有设置，这个索引就被移动到[ERROR step](###Troubleshooting index lifecycle management errors)。

&emsp;&emsp;例如，如果你需要在一个series中更改新索引的名字同时还要保留根据配置的策略生成的之前的索引的数据，你可以：

1. 为新的index pattern创建一个新的模板并且使用新的策略
2. 引导最初的索引（bootstrap the initial index）
3. 使用[aliases API](####Aliases API)为alias修改引导索引为write index
4. 在旧的索引上将`index.lifecycle.indexing_complete`设置为`ture`告知它不需要被rollover

&emsp;&emsp;ILM继续使用你现有的策略来管理旧的索引。新的索引根据新的模板进行命名并且根据相同的策略进行管理without interruption。

### Restore a managed data stream or index
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-lifecycle-and-snapshots.html)

### Index lifecycle management APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-lifecycle-management-api.html)


### Data tiers
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/data-tiers.html)

&emsp;&emsp;数据层（data tier）是具有相同数据角色（[data role](#####Data node)），通常享有相同硬件配置（hardware profile ）的节点集合。

- [Content tier](####Content tier)节点处理例如产品目录内容的索引和查询负载
- [Hot tier](####Hot tier)节点处理例如logs或者metrics这些时序（time series）数据的索引负载，并且保存（hold）你最近最常访问的数据
- [Warm tier](####Warm tier)节点保存最近less-frequently的访问并且很少（rarely）需要更新的时序数据
- [Cold tier](####Cold tier)节点保留infrequent的访问并且一般不更新的时序数据。为了节省空间，你可以在cold tier上保留[fully mounted indices](######Fully mounted index)的[searchable snapshots](###Searchable snapshots)。这些fully mounted indices会消除（eliminate）对副本分片的需求，相比较普通索引（regular index）能降低50%的磁盘空间
- [Frozen tier](####Frozen tier)节点保留很少（rarely）访问并且从不更新的时序数据。 Fronze tier只存储 [partially mounted indices](######Partially mounted index)的[searchable snapshots](###Searchable snapshots)。This extends the storage capacity even further — by up to 20 times compared to the warm tier.

&emsp;&emsp;当你直接往指定索引中写入文档，这些文档将无期限（indefinitely）的一直保留（remain on）在content ties节点上。

&emsp;&emsp;当你往[data stream](###Data streams)中写入文档，这些文档最开始会常驻（reside on）在hot tier节点上。你可以根据性能、弹性（resiliency）、数据保留（data retention）的要求，通过配置[index lifecycle management](###ILM: Manage the index lifecycle) 策略自动的将文档转移到hot ties、warm ties以及cold ties。

#### Content tier

&emsp;&emsp;存储在content tier上的数据通常是iterm的集合比如说产品目录（product catalog）或者文章归档（article archive）。跟时序数据不同的是，这些内容的价值随着时间的流逝相对保持不变的，所以根据这些数据的寿命（age）将它们移到性能不同的数据层是不合理的。Content data通常有长时间保留（retention）的要求，并且也希望无论这些数据的寿命的长短，总是能很快的检索到。

&emsp;&emsp;Content tier是必须要有的（required）。系统索引以及其他不是data stream的索引都会被自动分配到content tier。

#### Hot tier

&emsp;&emsp;hot tier是Elasticsearch中时序数据的入口点（entry point），并且保留最近，最频繁搜索的时序数据。hot tier节点上的读写速度都需要很快，要求更多的硬件资源和更快的存储（SSDs）。出于弹性目的，hot tier上的索引应该配置一个或多个副本分片。

&emsp;&emsp;hot tier是必须要有的。[data stream](##Data streams)中新的索引会被自动分配到hot tier。

#### Warm tier

&emsp;&emsp;一旦时序数据的访问频率比最近索引的数据（recently-indexed data）低了，这些数据就可以移到warm tier。warm tier通常保留最近几周的数据。允许更新数据，但是infrequent。warm tier节点不需要像hot tier一样的快。出于弹性目的，warm tier上的索引应该配置一个或多个副本分片。

#### Cold tier

&emsp;&emsp;当你不再经常（regular）搜索时序数据了，那可以将它们从warm tier移到cold tier。数据仍然可以被搜索到，在这一层的通常会被优化成较低的存储开销而不是查询速度。

&emsp;&emsp;为了更好的节省存储（storage saveing），你可以在cold tier保留[fully mounted indices](######Fully mounted index)的[searchable snapshots](###Searchable snapshots)。跟普通索引（regular index）不同的是，这些fully mounted indices不需要副本分片来满足可靠性（reliability），一旦出现失败事件，可以从底层（underlying）snapshot中恢复。这样可以潜在的减少一般的本地数据存储开销。snapshot仓库要求在cold tier使用fully mounted indices。Fully mounted indices只允许读取，不能修改。

&emsp;&emsp;另外你可以使用cold tier存储普通索引并且使用副本分片的方式，而不是使用searchable snapshot，这样会帮你在较低成本的硬件上存储较老的索引，但是相比较warm tier不会降低磁盘空间。

#### Frozen tier

&emsp;&emsp;一旦数据不需要或者很少（rare）被查询，也许就可以将数据从cold tier移到frozen tier，where it stays for the rest of its life。

&emsp;&emsp;frozen tier需要用到snapshot repository。frozen tier使用[partially mounted indices](######Partially mounted index)的方式存储以及从snapshot repository中载入数据。这样仍然可以让你搜索frozen数据并且可以减少本地储存（local storage）和操作开销（operation cost）。因为Elasticsearch必须有时从snapshot repository中提取（fetch）数据，在frozen tier的查询速度通常比cold  tier慢。

#### Configure data tiers on Elasticsearch Service or Elastic Cloud Enterprise

&emsp;&emsp;Elastic Cloud部署中默认配置了包含hot、content data的一个共享层（shared tier）。这层是必须要有的并且不能被移除。

&emsp;&emsp;要增加一个warm。cold。或者frozen tier，你可以创建这么一个部署（deployment）：

1. 在**Create deployment**页面，点击 **Advanced Settings**
2. 点击**Add capacity**增加任意数据层
3. 页面底部点击**Create deployment**保存你的更改

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ess-advanced-config-data-tiers.png">

&emsp;&emsp;如果要移除一个数据层，见[Disable a data tier](https://www.elastic.co/guide/en/cloud/current/ec-disable-data-tier.html)。

#### Configure data tiers for self-managed deployments

&emsp;&emsp;对于自己管理的部署，每个节点的[data role](#####Data node)配置在`elasticsearch.yml`中。例如，集群中性能最高的节点应该同时分配hot和content tier：

```text
node.roles: ["data_hot", "data_content"]
```

> NOTE：我们强烈建议你在frozen tier中使用[ dedicated nodes](#####Frozen data node)。


#### Data tier index allocation

&emsp;&emsp;当你创建一个索引时，Elasticsearch默认设置[index.routing.allocation.include.\_tier\_preference](######index.routing.allocation.include.\_tier\_preference)为`data_content`来自动的在content tier上分配索引分片。

&emsp;&emsp;当Elasticsearch创建一个索引，该索引作为 data stream的一部分时，Elasticsearch默认设置[index.routing.allocation.include.\_tier\_preference](######index.routing.allocation.include.\_tier\_preference)为`data_hot`来自动的在hot tier上分配索引分片。

&emsp;&emsp;你可以显示指定[index.routing.allocation.include.\_tier\_preference](######index.routing.allocation.include.\_tier\_preference)的值，不使用（opt out of）默认的tier-based的分配方式。

#### Automatic data tier migration

&emsp;&emsp;ILM使用[migrate](####Migrate)动作自动的在可见的数据层间进行索引的转变，默认情况下，每一层都会自动注入这个动作。你可以通过 `"enabled"`:`false`显示指定关闭自动迁移。例如你正在使用[allocate](####Allocate)动作手动的指定分配规则。

## Monitor a cluster
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/monitor-elasticsearch-cluster.html)

&emsp;&emsp;Elastic Stack monitoring功能提供了一种可以随时了解（keep a pulse）Elasticsearch集群健康跟性能的方法。

- [Overview](###Monitoring overview)
- [How it works](###How monitoring works)
- [Monitoring in a production environment](###Monitoring in a production environment)
- [Collecting monitoring data with Metricbeat](###Collecting Elasticsearch monitoring data with Metricbeat)
- [Collecting log data with Filebeat](###Collecting Elasticsearch log data with Filebeat)
- [Configuring indices for monitoring](######Configuring indices for monitoring)
- [Legacy collection methods](###Collecting monitoring data using legacy collectors)
- [Troubleshooting](###Troubleshooting monitoring)

### Monitoring overview
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/monitoring-overview.html)

&emsp;&emsp;当你监控一个集群时，你会从集群中的Elasticsearch nodes，Logstash nodes，Kibana和Beats中收集数据。你也可以[use Filebeat to collect Elasticsearch logs](###Collecting Elasticsearch log data with Filebeat)。

&emsp;&emsp;所有的检测指标（monitoring metrics）都存储在Elasticsearch中，使得你可以很容易的在Kibana中可视化这些数据。默认情况下，监控指标存储在本地索引中。

> TIP: 在生产中，我们强烈建议使用分开的监控集群（a separate monitoring cluster）。防止发生因生产集群（production cluster）中断而影响访问监控数据的问题。同样的也是防止因为监控活动对生产集群的性能产生影响。基于同样的原因，我们也建议使用一个分开的Kibana实例来观察监控数据（monitoring data）

&emsp;&emsp;你可以使用Metricbeatt将收集到的Elasticsearch，Kibana，Logstash和Beats的数据直接发送给你的监控集群，而不是通过生产集群进行路由。下图中展示的是一个经典的生产集群和监控集群分离的监控架构：

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/architecture.png">

&emsp;&emsp;如果你有特定的许可证，你可以从多个生产集群中将数据路由到一个监控集群中。见https://www.elastic.co/subscriptions 了解了解更多关于订阅级别的差异。

> IMPORTANT： 通常来说，监控集群和被监控的集群应该使用相同的版本。监控集群不能监控高版本的生产集群。If necessary, the monitoring cluster can monitor production clusters running the latest release of the previous major version.。

### How monitoring works
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/how-monitoring-works.html)

&emsp;&emsp;基于它们的持久的UUID（persistent UUID），每一个Elasticsearch node，Logstash node，Kibana实例和Beat实例都被认为是唯一的。在节点或者实例启动时，这个值会被写入到`path.data`中。

&emsp;&emsp;Monitoring document只是普通的JSON格式的document，通过每一个ELastic Stack 组件在一个指定的收集间隔（collection interval）构建。如果你想要更改这些索引的模板，见[Configuring indices for monitoring](###Configuring indices for monitoring)。

&emsp;&emsp;MetricBeat用于收集监控数据并且发送给监控集群。

&emsp;&emsp;见下面的内容学习如何收集监控数据：

- [Legacy collection methods](###Collecting monitoring data using legacy collectors)
- [Collecting monitoring data with Metricbeat](###Collecting Elasticsearch monitoring data with Metricbeat)
- [Monitoring Kibana](https://www.elastic.co/guide/en/kibana/8.2/xpack-monitoring.html)
- [Monitoring Logstash](https://www.elastic.co/guide/en/logstash/8.2/configuring-logstash.html)
- Monitoring Beats:
  - [Auditbeat](https://www.elastic.co/guide/en/beats/auditbeat/8.2/monitoring.html)
  - [Filebeat](https://www.elastic.co/guide/en/beats/filebeat/8.2/monitoring.html)
  - [Functionbeat](https://www.elastic.co/guide/en/beats/functionbeat/8.2/monitoring.html)
  - [Heartbeat](https://www.elastic.co/guide/en/beats/heartbeat/8.2/monitoring.html)
  - [Metricbeat](https://www.elastic.co/guide/en/beats/metricbeat/8.2/monitoring.html)
  - [Packetbeat](https://www.elastic.co/guide/en/beats/packetbeat/8.2/monitoring.html)
  - [Winlogbeat](https://www.elastic.co/guide/en/beats/winlogbeat/8.2/monitoring.html)

### Monitoring in a production environment
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/monitoring-production.html)

&emsp;&emsp;在生产中，你应该将监控数据发送到到一个分开的监控集群（a separate monitoring cluster）中，使得即使有些你监控的节点不在了也能对历史的数据可见。例如，你可以使用Metricbeat将Kibana，Elasticsearch，Logstash，和Beats的监控数据发送到监控集群中。

> IMPORTANT：Metricbeat是一种收集和发送监控数据到监控集群的一种方法。
> 如果你之前配置了legacy collection method，你应该迁移到使用Metricbeat collection。要么使用Metricbeat collection，要么使用legacy collection method，不要同时使用。
> 见[Collecting monitoring data with Metricbeat](###Collecting Elasticsearch monitoring data with Metricbeat)了解更多信息

&emsp;&emsp;如果你至少有一个Gold Subscription，可以使用一个专门的监控集群让你从一个central location中监控多个集群。

&emsp;&emsp;在一个分开的集群中存储监控数据，你需要：

1. 建立（set up）你想要用于作为监控集群的Elasticsearch集群。例如，你可以建立两个节点分别名为`es-mon-1`和`es-mon-2`的集群。

> IMPORTANT：最理想的情况是监控集群跟生产集群的运行在相同版本的Elastic Stack上。然而8.x的最新版本的监控集群也能跟主版本号（major version）相同的生产集群工作正常。8.x版本的监控集群也能跟7.x的最新版本的生产集群工作正常。
> 

&emsp;&emsp;a. （Optional）核实好在监控集群上的监控数据的收集功能是关闭的。默认情况下，`xpack.monitoring.collection.enabled`的设置为`false`。

&emsp;&emsp;例如，你可以使用下面的API查看以及更改设置：

```text
GET _cluster/settings

PUT _cluster/settings
{
  "persistent": {
    "xpack.monitoring.collection.enabled": false
  }
}
```

&emsp;&emsp;b. 如果监控集群上开启了Elasticsearch Security功能，创建好有权限发送以及查询监控数据的用户。

> NOTE: 如果你计划使用Kibana查看监控数据。在Kibana服务和监控集群上的用户名密码都必须要合法。

- 如果你计划使用Metricbeat收集Elasticsearch或者Kibana的数据，创建有`remote_monitoring_collector`内建角色（built-in）的用户和有[remote_monitoring_agent](#####remote_monitoring_agent)内建角色的用户。或者是`remote_monitoring_user`的内建用户（[built-in user](####Built-in users)）
- 如果你计划使用HTTP exproters通过你的生产集群来路由数据，创建一个用户并拥有[remote_monitoring_agent](#####remote_monitoring_agent)的内建角色。

&emsp;&emsp;例如，下面的请求创建一个`remote_monitor`用户，它拥有`remote_monitoring_agent`的角色：

```text
POST /_security/user/remote_monitor
{
  "password" : "changeme",
  "roles" : [ "remote_monitoring_agent"],
  "full_name" : "Internal Agent For Remote Monitoring"
}
```

&emsp;&emsp;或者，使用`remote_monitoring_user`[ built-in user](####Built-in users)。

2. 配置你的生产集群来收集数据并且发送到监控集群：
   - [Metricbeat collection methods](###Collecting Elasticsearch monitoring data with Metricbeat)
   - [Legacy collection methods](###Collecting monitoring data using legacy collectors). 
3. （Optional）[Configure Logstash to collect data and send it to the monitoring cluster](##Monitoring Logstash)。
4. （Optional）配置Beats来收集数据并发送到监控集群
   - [Auditbeat](https://www.elastic.co/guide/en/beats/auditbeat/8.2/monitoring.html)
   - [Filebeat](https://www.elastic.co/guide/en/beats/filebeat/8.2/monitoring.html)
   - [Heartbeat](https://www.elastic.co/guide/en/beats/heartbeat/8.2/monitoring.html)
   - [Metricbeat](https://www.elastic.co/guide/en/beats/metricbeat/8.2/monitoring.html)
   - [Packetbeat](https://www.elastic.co/guide/en/beats/packetbeat/8.2/monitoring.html)
   - [Winlogbeat](https://www.elastic.co/guide/en/beats/winlogbeat/8.2/monitoring.html)

5. （Optional）配置Kivana来收集数据并发送到监控集群
   - [Metricbeat collection methods](https://www.elastic.co/guide/en/kibana/8.2/monitoring-metricbeat.html)
   - [Legacy collection methods](https://www.elastic.co/guide/en/kibana/8.2/monitoring-kibana.html)

6. （Optional）创建一个专用的Kibana实例用于监控。而不是使用单个即要访问你的生产集群也要访问你的监控集群的Kibana。

> NOTE: 如果你使用SAML, Kerberos, PKI, OpenID Connect, or token authentication providers登陆Kibana，那么必须使用专用的Kibana。security token是cluster-specific的，因此你不能使用单个Kibana实例同时连接生产和监控集群。

- （Optional）关闭Kibana中监控数据的收集功能，在`kibana.yml`文件中，将`xpack.monitoring.kibana.collection.enabled`设置为`false`。见[Monitoring settings in Kibana](https://www.elastic.co/guide/en/kibana/8.2/monitoring-settings-kb.html)了解更多设置信息。

7. [Configure Kibana to retrieve and display the monitoring data](https://www.elastic.co/guide/en/kibana/8.2/monitoring-data.html)。


### Collecting Elasticsearch monitoring data with Metricbeat
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/configuring-metricbeat.html)

### Collecting Elasticsearch log data with Filebeat
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/configuring-filebeat.html)

### Configuring indices for monitoring
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/config-monitoring-indices.html)

### Collecting monitoring data using legacy collectors
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/collecting-monitoring-data.html)

### Troubleshooting monitoring
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/monitoring-troubleshooting.html)


## Roll up or transform your data
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/data-rollup-transform.html)

&emsp;&emsp;Elasticsearch提供了下面的方法来处理（manipulate）你的数据：

- [Rolling up your historical data](###Rolling up historical data)
- [Transforming data](###Transforming data)

### Rolling up historical data
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/xpack-rollup.html)

>WARNING：这个功能是属于技术预览，可能在未来的版本中移除或者更改。Elastic会尽力修复任何的问题，but features in technical preview are not subject to the support SLA of official GA features。

&emsp;&emsp;保留历史数据用于分析是非常实用的，但由于归档大量的数据带来的财政开销（financial cost）而不会保留。因此保留时间由财政现实（financial realities）决定而不是根据历史数据的用处。

&emsp;&emsp;Elastic Stack的rollup功能提供一个汇总（summarize ）和存储历史数据的方法，使得历史数据仍然可以用于分析，但只有原始数据（raw data）所需的存储开销的一小部分（a fraction of the storage cost）。

- [Overview](####Rollup overview)
- [Getting started](####Getting started with rollups)
- [API quick reference](####Rollup API quick reference)
- [Understanding rollup grouping](####Understanding groups)
- [Rollup aggregation limitations](####Rollup aggregation limitations)
- [Rollup search limitations](####Rollup search limitations)

#### Rollup overview
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rollup-overview.html)

> WARNING：这个功能是属于技术预览，可能在未来的版本中移除或者更改。Elastic会尽力修复任何的问题，but features in technical preview are not subject to the support SLA of official GA features。

&emsp;&emsp;基于时间（time-based）（主要由时间戳标识的文档）的数据经常有相关的保留策略（retention policy）来管理数据的成长。比如说，你的系统可能生成每秒生成500篇文档，那么一天会有4300万篇文章，一年有接近16亿篇文档。

&emsp;&emsp; 然而分析师和数据科学家可能希望你能存储无限量的数据。时间是无穷无尽的，所以你的存储需求将持续增长并且没有尽头。因此保留策略通常由随时间推移的存储成本的简单计算决定以及公司愿意为保留历史数据而花的钱。通常这些策略会在几个月或者几年后删除数据。

&emsp;&emsp; 存储成本是一个固定数量（Storage cost is a fixed quantity）。It takes X money to store Y data。但是一段数据的效用（utility）会随着时间发生变化。在当前时间内毫秒颗粒度的传感器的数据是非常有用，在几个星期前的数据相对有点用，但是几个月前就几乎没什么用了。

&emsp;&emsp; 虽然存储十年前的一毫秒传感器数据的成本是固定的，但传感器读数的价值会随着时间而减小。这些数据并不是没有用，它可以很容易地为实用的分析（useful analysis）做出贡献，但是价值的降低通常会导致被删除而不是使用固定的存储开销来保留它们。

##### Rollup stores historical data at reduced granularity

&emsp;&emsp;That’s where Rollup comes into play。Rollup 功能将旧的高粒度（high-granularity）汇总为降低粒度的格式，以便长期存储。通过将数据roll up到单个summary document中，相比较原始数据（raw data），历史数据可以更好的被压缩。

&emsp;&emsp;比如说一个每天生成4300万文档的系统，每一秒的数据对实时分析是很实用的，但是查看超过 10 年数据的历史分析可能只在更大的时间间隔内起作用，例如每小时或每天的趋势。

&emsp;&emsp;如果我们把4300篇文档按小时进行汇总，我们就可以节省大量的空间。Rollup功能会对历史数据进行自动的汇总。

&emsp;&emsp;见[Create Job API](####Create rollup jobs API)详细了解Rollup的设置跟配置。

##### Rollup uses standard Query DSL

&emsp;&emsp;Rollup功能提供了新的search endpoint（`_rollup_search` vs 标准的`/_search`），这个endpoint知道如何查询rolled-up的数据。重要的是，这个endpoint接受100%普通的（normal）Elasticsearch Query DSL。你的应用不需要学习新的DSL来inspect历史数据，很容易重新使用现有的查询跟dashboard。

&emsp;&emsp;这个功能也是有一些限制。不是所有的查询和聚合都支持的，一些查询功能（高亮）被禁用了并且可以使用的域（available fields）取决于Rollup的配置。更多的限制见[Rollup Search limitations](####Rollup search limitations)。

&emsp;&emsp;But if your queries, aggregations and dashboards only use the available functionality, redirecting them to historical data is trivial。

##### Rollup merges "live" and "rolled" data

&emsp;&emsp;Rollup的另一个实用的功能是可以在同一个查询中同时查询"live"实时数据和历史的"rollup"数据。

&emsp;&emsp;比如说你的系统保留了一个月的原始数据（raw data）。一个月后，这些数据被rollup到历史汇总（historical summarizes）数据中并且原始数据会被删除。

&emsp;&emsp;如果你想查询原始数据，你只能查看最近一个月的数据，如果你想查看rollup数据，你只能查看一个月前的数据。RollupSearch endpoint支持在同一时间同时进行查询。这个查询会从两个数据源获取并且将结果合并到一起。如果"live"和"rollup"的数据发生重叠，那么选择"live"数据，因为它更准确。

##### Rollup is multi-interval aware

&emsp;&emsp;最后，Rollup能够智能地利用可用的最佳间隔。如果你使用过其他产品的汇总功能（summarizing Feature），你会发现其局限性。比如如果配置了按天为间隔的rollup，那么你只能基于按天来进行查询或者出图表。如果你需要每月间隔，则必须显示的（explicit）创建另一个存储每月平均值的汇总。

&emsp;&emsp;Rollup功能可以通过按照最小的可用的间隔进行存储，并且依次来进行处理。如果你rollup了按天的数据，那么可以执行按天或者更长间隔的查询（按周、按月、按年等等）而不用显示的去配置一个新的rollup任务。这有助于缓解（alleviate）汇总系统的主要缺点之一：相对于原始数据的灵活性的降低。

#### Rollup API quick reference
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rollup-api-quickref.html#rollup-api-quickref)

> WARNING：这个功能是属于技术预览，可能在未来的版本中移除或者更改。Elastic会尽力修复任何的问题，but features in technical preview are not subject to the support SLA of official GA features。

&emsp;&emsp;大多数rollup endpoint都有下面的前缀：

```text
/_rollup/
```

##### /job/

- [PUT /\_rollup/job/](####Create rollup jobs API): Create a rollup job
- [GET /\_rollup/job](####Get rollup jobs API): List rollup jobs
- [GET /\_rollup/job/](####Get rollup jobs API): Get rollup job details
- [POST /\_rollup/job//\_start](####Start rollup jobs API): Start a rollup job
- [POST /\_rollup/job//\_stop](####Stop rollup jobs API): Stop a rollup job
- [DELETE /\_rollup/job/](####Delete rollup jobs API): Delete a rollup job

##### /data/

- [GET /\_rollup/data//\_rollup\_caps](####Get rollup job capabilities API): Get Rollup Capabilities
- [GET //\_rollup/data/](####Get rollup index capabilities API): Get Rollup Index Capabilities

##### /<index\_name>/

- [GET //\_rollup\_search](####Rollup search): Search rollup data

#### Getting started with rollups
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rollup-getting-started.html)

> WARNING：这个功能是属于技术预览，可能在未来的版本中移除或者更改。Elastic会尽力修复任何的问题，but features in technical preview are not subject to the support SLA of official GA features。

&emsp;&emsp;你需要创建一个或多个"Rollup jobs"来使用rollup功能。这些job会在后台持续不断的运行，将你指定的index或者indices进行rollup操作。那些被rolled的文档会被放到二级索引（secondary index）中。

&emsp;&emsp;比如你有一些按天记录传感器数据的索引（daily indices）（比如说`sensor-2017-01-01`, `sensor-2017-01-02`）。示例文档如下所示：

```text
{
  "timestamp": 1516729294000,
  "temperature": 200,
  "voltage": 5.2,
  "node": "a"
}
```

##### Creating a rollup job

&emsp;&emsp;我们想要将文档rollup到按小时汇总（hourly summarize）。这将允许我们生成间隔为1个小时或者更大间隔的报告和dashboard。rollup job如下所示：

```text
PUT _rollup/job/sensor
{
  "index_pattern": "sensor-*",
  "rollup_index": "sensor_rollup",
  "cron": "*/30 * * * * ?",
  "page_size": 1000,
  "groups": {
    "date_histogram": {
      "field": "timestamp",
      "fixed_interval": "60m"
    },
    "terms": {
      "fields": [ "node" ]
    }
  },
  "metrics": [
    {
      "field": "temperature",
      "metrics": [ "min", "max", "sum" ]
    },
    {
      "field": "voltage",
      "metrics": [ "avg" ]
    }
  ]
}
```

&emsp;&emsp;我们给这个job命名为"sensor"（在url中: `PUT _rollup/job/sensor`），并且告诉这个job我们要对 `sensor-*`进行rollup。这个job会找到匹配`sensor-*` pattern的所有索引。Rollup会进行汇总并存储到名为`sensor_rollup`的索引中。

&emsp;&emsp;`cron`参数用来控制什么时候以及什么频率来激活这个job。当一个rollup job的cron计划激活后，这个job将对上一次job结束任务后产生的新的数据（new worth of data）执行rollup。所以你如果配置了一个每30秒就执行一次的cron，该作业将处理最近 30 秒的数据，这些数据被索引到 sensor\-* 索引中。

&emsp;&emsp;如果你配置了每天在午夜时执行rollup的任务，这个job将会处理过去24小时的数据。选择主要取决于偏好，具体取决于你希望汇总的“实时”程度，以及你是否希望连续处理或将其移至非高峰时间。

&emsp;&emsp;接下来是`groups`的设置。Essentially, we are defining the dimensions that we wish to pivot on at a later date when querying the data。这个job中的grouping允许我们在`timestamp`域上执行`date_histogram`操作，按照一小时的间隔进行rollup。同样允许我们在`node`域上进行terms aggregation。

> Date histogram interval vs cron schedule
> 你可能注意到了job中的cron配置为每30秒执行一次，但是date_histogram配置为按照60分钟的间隔执行rollup，他们之间是什么关系呢？
> date_histogram控制保存的数据（saved data）的颗粒度。数据会按照小时的间隔（hourly interval）执行rollup，并且你无法执行更细的颗粒度（finer granularity）的查询。cron只是简单的查找是不是有新的数据可以用于rollup。每隔30秒，cron会看下有没有new hour’s worth of data可以用于rollup，如果没有，这个job则go back to sleep。
> 通常来说，在一个较大的间隔（1h）中定义一个这么小的cron（30s）是不合理的，因为大部分cron激活后会马上go back to sleep。但这也不会有什么问题，job会正确处理这种情况。

&emsp;&emsp;在定义好了这些数据中会生成哪些groups后，你下一步的配置是应该收集哪些metric。默认情况下，只会收集每一个group的`doc_counts`。为了使rollup更加具有实用性，你一般经常会收集例如averages, mins, maxes等一些metric信息。在这个例子中需要收集的metric是相对简单的（fairly straightforward）：我们想要保留`temperature`域的min/max/sum，以及`voltage`域的average。

> Averages aren’t composable?!
> 如果你之前使用过rollup，那么使用Average的时候要注意了。如果average用于计算10分钟的间隔，通常来说average就不能用于更大间隔的计算。你不能用简单的10分钟的average * 6 来计算以一个小时为间隔的average。the average of averages is not equal to the total average。
> 由于这个原因，其他的系统会试图omit计算average的能力或者存储多个不同的时间间隔来支持更多的灵活的查询。
> 然而在rollup功能中会根据定义的时间间隔来保存对应的`count`和`sum`。使得当时间间隔大于或等于定义的时间间隔后我们可以重新来计算。这就给予了使用最小的存储开销来实现最大化的灵活性。所以你不用担心average的精确度（no average of averages here!）。

&emsp;&emsp;见[Create rollup jobs](####Create rollup jobs API)了解更多关于job语法的信息。

&emsp;&emsp;在你执行上文中的命令，并创建了job后，你会收到下面的响应：

```text
{
  "acknowledged": true
}
```

##### Starting the job

&emsp;&emsp;job创建后，它会处于未激活状态（inactive state），在开始处理数据（这样使得随后你可以临时的暂停这个job而不需要删除它）前，job需要被启动。

&emsp;&emsp;执行这个命令来启动job：

```text
POST _rollup/job/sensor/_start
```

##### Searching the rolled results

&emsp;&emsp;在job运行并处理了一些数据后，我们就可以通过[Rollup search](####Rollup search) endpoint来做一些查询了。Rollup功能被设计为你可以使用你习惯了的Query DSL进行查询，只不过是在rolled up的数据上进行查询。

&emsp;&emsp;例如执行这个查询：

```text
GET /sensor_rollup/_rollup_search
{
  "size": 0,
  "aggregations": {
    "max_temperature": {
      "max": {
        "field": "temperature"
      }
    }
  }
}
```

&emsp;&emsp;这是一个计算`temperature`域最大值的简单聚合查询。但需要你注意的是，这是在`sensor_rollup`索引上进行查询而不是原始的`sensor-*`索引。还有一个你要注意的是使用了`_rollup_search`这个endpoint。Otherwise the syntax is exactly as you’d expect。

&emsp;&emsp;如果你执行了这个查询，那么会收到一个跟普通的聚合（normal aggregation）一样的响应：

```text
{
  "took" : 102,
  "timed_out" : false,
  "terminated_early" : false,
  "_shards" : ... ,
  "hits" : {
    "total" : {
        "value": 0,
        "relation": "eq"
    },
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "max_temperature" : {
      "value" : 202.0
    }
  }
}
```

&emsp;&emsp;这里唯一需要注意的是Rollup的查询结果中是没有`hits`结果的，因为我们不再从原始的，live data中进行查询。其他部分都是相同的语法结构。

&emsp;&emsp;There are a few interesting takeaways here。首先，尽管数据按小时的间隔（hourly interval）并按照node的名字进行分组，但是这个query我们只计算了所有文档中temperature的最大值。job中配置的`groups`在查询不是必要的元素（mandatory element），他们只是你可以用于分组的额外的维度。其次，这个查询和响应跟普通的DSL是一模一样的，使得更容易集成与dashboard和应用中。

&emsp;&emsp;最后，我们可以使用分组的字段来构造一个更加复杂的查询：

```text
GET /sensor_rollup/_rollup_search
{
  "size": 0,
  "aggregations": {
    "timeline": {
      "date_histogram": {
        "field": "timestamp",
        "fixed_interval": "7d"
      },
      "aggs": {
        "nodes": {
          "terms": {
            "field": "node"
          },
          "aggs": {
            "max_temperature": {
              "max": {
                "field": "temperature"
              }
            },
            "avg_voltage": {
              "avg": {
                "field": "voltage"
              }
            }
          }
        }
      }
    }
  }
}
```

&emsp;&emsp;这个查询对应的响应是：

```text
{
   "took" : 93,
   "timed_out" : false,
   "terminated_early" : false,
   "_shards" : ... ,
   "hits" : {
     "total" : {
        "value": 0,
        "relation": "eq"
     },
     "max_score" : 0.0,
     "hits" : [ ]
   },
   "aggregations" : {
     "timeline" : {
       "meta" : { },
       "buckets" : [
         {
           "key_as_string" : "2018-01-18T00:00:00.000Z",
           "key" : 1516233600000,
           "doc_count" : 6,
           "nodes" : {
             "doc_count_error_upper_bound" : 0,
             "sum_other_doc_count" : 0,
             "buckets" : [
               {
                 "key" : "a",
                 "doc_count" : 2,
                 "max_temperature" : {
                   "value" : 202.0
                 },
                 "avg_voltage" : {
                   "value" : 5.1499998569488525
                 }
               },
               {
                 "key" : "b",
                 "doc_count" : 2,
                 "max_temperature" : {
                   "value" : 201.0
                 },
                 "avg_voltage" : {
                   "value" : 5.700000047683716
                 }
               },
               {
                 "key" : "c",
                 "doc_count" : 2,
                 "max_temperature" : {
                   "value" : 202.0
                 },
                 "avg_voltage" : {
                   "value" : 4.099999904632568
                 }
               }
             ]
           }
         }
       ]
     }
   }
}
```

&emsp;&emsp;除了使用更加复杂的查询（(date histogram and a terms aggregation, plus an additional average metric），值得你注意的是这里的date_histogram使用了`7d`作为时间间隔而不是配置中的`60m`。

##### Conclusion

&emsp;&emsp;这一章的内容提供了简明扼要的关于Rollup所能提供的核心功能。在剩余的章节中你可以学习到更多在设置Rollup时的tips和内容。你可以通过[REST API](####Rollup API quick reference)概览下目前可用的功能。 

#### Understanding groups
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rollup-understanding-groups.html)

> WARNING：这个功能是属于技术预览，可能在未来的版本中移除或者更改。Elastic会尽力修复任何的问题，but features in technical preview are not subject to the support SLA of official GA features。

&emsp;&emsp;为了能提供灵活性（flexibility），Rollup的定义会基于以后的数据查询对应的query。通常来说，系统会要求管理员在配置rollup时要决定好metric以及时间间隔。比如说按小时的间隔（hourly interval）并且是`cpu_time`的average。这种方式存在局限性。在以后的日子里，管理员可能想要查询按小时的间隔并且是`cpu_time`的average同时还要根据`host_name`分组，这时候就做不到了。

&emsp;&emsp;当然管理员可以在按小时的间隔的基础上对`[hour, host]`这么一对来配置rollup，但是随着分组字段数量的增加，其需要更多的配置。另外`[hour, host]`的配置只能在按小时的间隔上才有用，按天、按周、按月的rollup都需要新的配置。

&emsp;&emsp;相比较要求管理员提前为rollup做好决策，Elasticsearch中的Rollup job的配置则是基于哪些groups可能会在将来被用于查询。例如下面的这个配置：

```text
"groups" : {
  "date_histogram": {
    "field": "timestamp",
    "fixed_interval": "1h",
    "delay": "7d"
  },
  "terms": {
    "fields": ["hostname", "datacenter"]
  },
  "histogram": {
    "fields": ["load", "net_in", "net_out"],
    "interval": 5
  }
}
```

&emsp;&emsp;允许在`timestamp`域上使用`date_histogram`。在`hostname`和`datacenter`域上使用`terms aggregation`，并且可以在`load`、`net_in`、`net_out`的任意一个域上使用`histograms`。

&emsp;&emsp;更重要的是，这些 agg/fields可以任何的组合：

```text
"aggs" : {
  "hourly": {
    "date_histogram": {
      "field": "timestamp",
      "fixed_interval": "1h"
    },
    "aggs": {
      "host_names": {
        "terms": {
          "field": "hostname"
        }
      }
    }
  }
}
```

&emsp;&emsp;跟下面的这个聚合都是合法的：

```text
"aggs" : {
  "hourly": {
    "date_histogram": {
      "field": "timestamp",
      "fixed_interval": "1h"
    },
    "aggs": {
      "data_center": {
        "terms": {
          "field": "datacenter"
        }
      },
      "aggs": {
        "host_names": {
          "terms": {
            "field": "hostname"
          }
        },
        "aggs": {
          "load_values": {
            "histogram": {
              "field": "load",
              "interval": 5
            }
          }
        }
      }
    }
  }
}
```

&emsp;&emsp;需要你注意的是第二个aggregation is not only substantially larger, it also swapped the position of the terms aggregation on "hostname"，说明了聚合的先后顺序对rollup没有影响。在对数据进行rollup时`date_histogram`是必须的，但是在查询阶段不是必须的（尽管经常会被使用）。例如下面是个可以用于Rollup Search的合法聚合查询：

```text
"aggs" : {
  "host_names": {
    "terms": {
      "field": "hostname"
    }
  }
}
```

&emsp;&emsp;最后，在对一个job配置`groups`时，考虑好在以后的查询会用到哪些字段用于分组，那么将这些域都添加到配置中就可以了。因为Rollup Search允许任意的先后顺序或者分组的字段的组合。你只需要决定好某个域是否会在以后用于聚合，以及你可能希望使用某一种聚合方式（terms、histogram等等）。

##### Calendar vs fixed time intervals

&emsp;&emsp;每一个rollup-job必须有一个date histogram并且定义一个间隔。Elasticsearch可以解析（understand） [calendar and fixed time intervals](####Date histogram aggregation)，fix time intervals解析（understand）起来相对简单；`60s`意味着60秒。但是`1M`是什么意思呢？一个月代表的时间取决于不同的月份，一些月份可能大于或者小于某些月份。这个calendar time的例子的持续的时间取决于上下文（ This is an example of calendar time and the duration of that unit depends on context）。Calendar unitis同样受到润秒（leap-seconds）、闰年（leap-years）等等的影响。

&emsp;&emsp;rollup生成的bucket可以使用calendar或者fixed intervals，所以上文中提到的影响是重要。并且这个问题会限制后续的查询。见[Requests must be multiples of the config](####Rollup search limitations)。

&emsp;&emsp;我们建议坚持（sticking with）使用fixed time intervals，因为它更容易解析（understand）并且在查询时更具弹性。它会在闰事件（leap-event）期间在你的数据中引入一些drift，你将不得不考虑固定数量（30 天）的月份，而不是实际的日历长度。However, it is often easier than dealing with calendar units at query time。

&emsp;&emsp;units的倍数总是fixed（Multiples of units are always "fixed"）。比如说`2h`也是一个固定数值（fixed quantity）`7200`秒。单个units是fixed还是calendar取决于unit：

|    Unit     | Calendar |       Fixed        |
| :---------: | :------: | :----------------: |
| millisecond |    NA    | 1ms, 10ms, etc |
|   second    |    NA    |  1s, 10s, etc  |
|   minute    |    1m    |  2m, 10m, etc  |
|    hour     |    1h    |  2h, 10h, etc  |
|     day     |    1d    |  2d, 10d, etc  |
|    week     |    1w    |         NA         |
|    month    |    1M    |         NA         |
|   quarter   |    1q    |         NA         |
|    year     |    1y    |         NA         |


&emsp;&emsp;对于一些单位（units）可是同时是fixed和calendar，你最好使用更小的单位来描述数量（quantity）。比如说你想要一个fixed day（not a calendar day），你应该指定为`24h`而不是`1d`。同样的，如果你想要一个fixed hours，应该指定为`60m`而不是`1h`。因为单个数量（single quantity）意味着calendar time，并且会在以后限制通过calendar time进行查询。

##### Grouping limitations with heterogeneous indices

&emsp;&emsp;以前在 Rollup 如何处理具有heterogeneous mappings（multiple, unrelated/non-overlapping mappings）的索引时存在限制。 当时的建议是为每个数据“类型”配置一个单独的作业。 例如，你可以为已启用的每个 Beats module配置一个单独的作业（一个用于`process`，另一个用于`filesystem`等）

&emsp;&emsp;由于在内部实现细节中。如果单个"merge" job被使用后会导致文档的统计可能会不正确，因此给出了上文中的建议。

&emsp;&emsp;这个局限性已经被缓和（alleviate）了，从 6.4.0 开始，现在将所有rollup配置组合到一个job中被认为是最佳实践。

&emsp;&emsp;例如，如果你的索引中有这两类的文档：

```text
{
  "timestamp": 1516729294000,
  "temperature": 200,
  "voltage": 5.2,
  "node": "a"
}
```

&emsp;&emsp;以及：

```text
{
  "timestamp": 1516729294000,
  "price": 123,
  "title": "Foo"
}
```

&emsp;&emsp;最佳实践是将它们组合成一个涵盖这两种文档类型的rollup job，如下所示：

```text
PUT _rollup/job/combined
{
  "index_pattern": "data-*",
  "rollup_index": "data_rollup",
  "cron": "*/30 * * * * ?",
  "page_size": 1000,
  "groups": {
    "date_histogram": {
      "field": "timestamp",
      "fixed_interval": "1h",
      "delay": "7d"
    },
    "terms": {
      "fields": [ "node", "title" ]
    }
  },
  "metrics": [
    {
      "field": "temperature",
      "metrics": [ "min", "max", "sum" ]
    },
    {
      "field": "price",
      "metrics": [ "avg" ]
    }
  ]
}
```

##### Doc counts and overlapping jobs

&emsp;&emsp;以前在“overlapping”的job配置上存在文档计数问题，这同样是由于内部实现细节导致的。 如果有两个rollup job保存到同一个索引，其中一个job是另一个job的“subset”，则对于某些aggregation arrangements，文档计数可能不正确。

&emsp;&emsp;此问题也已在 6.4.0 中消除。

#### Rollup aggregation limitations
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rollup-agg-limitations.html)

> WARNING：这个功能是属于技术预览，可能在未来的版本中移除或者更改。Elastic会尽力修复任何的问题，but features in technical preview are not subject to the support SLA of official GA features。

&emsp;&emsp;对域进行rollup up/aggregated存在一些限制。这一章强调了你需要注意的主要的限制（major limitation）。

##### Limited aggregation components

&emsp;&emsp;Rollup功能允许使用对域使用下面的aggregation进行分组：

- Date Histogram aggregation
- Histogram aggregation
- Terms aggregation

&emsp;&emsp;下列是numeric Field可以指定的metric：

- Min aggregation
- Max aggregation
- Sum aggregation
- Average aggregation
- Value Count aggregation

#### Rollup search limitations
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rollup-search-limitations.html)

> WARNING：这个功能是属于技术预览，可能在未来的版本中移除或者更改。Elastic会尽力修复任何的问题，but features in technical preview are not subject to the support SLA of official GA features。

&emsp;&emsp;当我们觉得Rollup功能非常的灵活（flexible）时，汇总数据（summarize data）的本质是有局限的。一旦live data（raw data）被丢弃后，你总是会丢失一些灵活性。

##### Only one rollup index per search

&emsp;&emsp;在使用[Rollup search](####Rollup search)时，`index`参数可以接受一个或者多个索引，可以是普通的（regular）、non-rollup、rollup的混合索引。然而只能指定一个rollup索引。使用`index`参数的规则如下所示：

- 至少要指定一个index/index-pattern。可以是一个rollup或者non-rollup索引。不允许omit index参数或者使用`\_all`
- 可以指定多个 non-rollup索引
- 只能指定一个rollup索引。如果指定了多个则会抛出异常
- 可以使用index-pattern，但是如果匹配到了超过一个rollup索引则会抛出异常

&emsp;&emsp;导致这些局限的原因是内部逻辑会根据给定的query决策出一个"best" job。如果在单个索引中存储了10个job。这个索引将覆盖不同的完整度（degree of completeness）和时间间隔的原数据（source data），这个query需要判断使用哪个job来进行查询。错误的决策可能会导致不准确的聚合（inaccurate aggregation）结果（比如over-counting doc counts, or bad metrics）。不管怎么样（needless to say），在技术上这是对编码的一种挑战。

&emsp;&emsp;为了简化这个问题，我们限制为每次只能对一个rollup index进行查询（which may contain multiple jobs）。In the future we may be able to open this up to multiple rollup jobs。

##### Can only aggregate what’s been stored

&emsp;&emsp;一个也许是非常明显的限制就是只能对存储在rollup中的数据进行聚合操作。如果你没有在rollup job中配置`price`域的metric，那么你不能在任何query或者aggregation中使用`price`域。

&emsp;&emsp;比如说在下面的query中，`temperature`域已经存储在一个rollup job中，但是没有存储它的`avg`这个metric信息。意味着不能使用`avg`这个功能：

```text
GET sensor_rollup/_rollup_search
{
  "size": 0,
  "aggregations": {
    "avg_temperature": {
      "avg": {
        "field": "temperature"
      }
    }
  }
}
```

&emsp;&emsp;在响应中会告诉你不能对这个域做这次聚合操作，因为没有一个rollup job中包含这个域的`avg`的metric信息：

```text
{
  "error": {
    "root_cause": [
      {
        "type": "illegal_argument_exception",
        "reason": "There is not a rollup job that has a [avg] agg with name [avg_temperature] which also satisfies all requirements of query.",
        "stack_trace": ...
      }
    ],
    "type": "illegal_argument_exception",
    "reason": "There is not a rollup job that has a [avg] agg with name [avg_temperature] which also satisfies all requirements of query.",
    "stack_trace": ...
  },
  "status": 400
}
```

##### Interval granularity

&emsp;&emsp;Rollup会以某个粒度进行存储，即`date_histogram`在配置中的定义。这意味着你在query/aggregation rollup data时，只能使用比rollup配置中相等或者更大的间隔（interval）。

&emsp;&emsp;比如说如果数据按小时间隔（hourly interval）进行rollup。[Rollup search](####Rollup search)可以在按任意小时的间隔或者更大的间隔进行聚合。小于一个小时间隔的查询都将抛出异常，因为不存在粒度更细（finer granularity）的数据。

> Requests must be multiples of the config
Perhaps not immediately apparent，在aggregation请求中指定的间隔必须是配置中的间隔的倍数。如果一个job中rollup配置了`3d`时间间隔。那么你只能query/aggregation 3的倍数的时间间隔（`3d`、`6d`、`9d`等等）。
> 不是倍数的时间间隔无法正常工作。因为rollup data无法与aggregation生成的桶完全“overlap”，从而导致不正确的结果。
> 基于这个原因，如果没有找到配置中的时间间隔的倍数将会抛出异常。

&emsp;&emsp;由于RollupSearch endpoint可以使用配置中的时间间隔的倍数（ "upsample" intervals） ，所以不需要根据不同倍数的间隔（hourly、daily等等）来配置多个job。建议按需配置最小粒度的时间间隔，使得RollupSearch可以根据需要来向上调整时间间隔（upsample）。

&emsp;&emsp;也就是说，如果在单个索引中配置了多个不同时间间隔的job，search endpoint会识别（identify）并且使用最大间隔的job来进行查询。

##### Limited querying components

&emsp;&emsp;Rollup功能允许在查询请求中带有query，但局限于只能使用一部分query，目前可用的query如下所示：

- Term Query
- Terms Query
- Range Query
- MatchAll Query
- Any compound query (Boolean, Boosting, ConstantScore, etc)

&emsp;&emsp;这些query同样只能使用在rollup job的`group`中配置的域。如果你想要过滤出keyword类型的`hostname`域，那么这个域必须在rollup job中配置`term` grouping。

&emsp;&emsp;如果你尝试使用一个不支持的query，或者query引用了一个没有在rollup job中配置的域，那么会抛出一个异常。我们预计随着时间推移会增加更多的query支持。

##### Timezones

&emsp;&emsp;Rollup documents存储在`date_histogram`中的时区里。如果没有指定时区，那么默认rollup的timestamp是UTC。


### Transforming data
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/transforms.html)

&emsp;&emsp;Transforms可以让你将现有的（existing）Elasticsearch索引转换（Convert）到汇总索引中（summarized indice），使得能提供一个新的insight和analytics。比如说你可以使用transforms将你的数据pivot到entity-centric indices中，它汇总了用户的行为（he behavior of users）或者是sessions，或者是其他实体（other entities）。或者你可以使用transforms在具有某个唯一键的所有文档中查找最新文档。

- [Overview](####Transform overview)
- [Setup](####Set up transforms)
- [When to use transforms](####When to use transforms)
- [Generating alerts for transforms](####Generating alerts for transforms)
- [Transforms at scale](####Working with transforms at scale)
- [How checkpoints work](####How transform checkpoints work)
- [API quick reference](####API quick reference)
- [Tutorial: Transforming the eCommerce sample data](####Tutorial: Transforming the eCommerce sample data)
- [Examples](####Transform examples)
- [Painless examples](####Painless examples for transforms)
- [Troubleshooting](####Troubleshooting transforms)
- [Limitations](####Transform limitations)

#### Transform overview
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/transform-overview.html)

&emsp;&emsp;你可以使用下面两个方法中的一种来transform你的数据：[pivot](#####Pivot transforms)或者[latest](#####Latest transforms)。

> IMPORTANT：transforms不会破坏你的源索引（leave your  source index intact）。transformed data会创建一个新的专用的索引。
> 相比较Kibana中的可用参数，Transforms可以通过APIs来提供更多的配置选项。参考[API documentation](###Transform APIs)了解transform所有的配置选项。

&emsp;&emsp;Transforms是持久性（persistent）的任务，存储在cluster state使得节点发生故障也具有弹性（resilient）。参考[How checkpoints work](####How transform checkpoints work)和[Error handling](#####Error handling)了解更多关于transforms背后的机制（machinery）。

##### Pivot transforms

&emsp;&emsp;你可以使用transforms将你的数据pivot到entity-centric indices中。通过transforming和汇总（summarizing）你的数据，可以以另一种有趣的方式对其进行可视化和分析。

&emsp;&emsp;许多的Elasticsearch索引的组织方式是事件流（stream of events），每一个事件是一个独立的文档，比如说单个商品的购买信息。Transforms能让你对这些数据进行汇总，汇总成一个组织化的（organized）、更友好的用于分析（analysis-friendly）格式。比如说你可以将某个用户购买的所有商品进行汇总。

&emsp;&emsp;Transforms让你定义一个pivot，它是一个特征集合（a set of features）将索引transform到一个不同的，更加易于提取（digestible）的格式。Pivoting会将汇总后的数据输出到一个新的索引。

&emsp;&emsp;要定义一个pivot，你首先要选择一个或者多个域，你将使用这些域对你的数据进行分组。你可以选择可以用于分类的域（categorical field）以及numerical fields用于分组。如果你使用了numerical fields，则使用你指定的间隔对这些域的域值进行分桶。

&emsp;&emsp;第二步就是决定好你想要如何对分组的数据进行聚合。When using aggregations, you practically ask questions about the index。聚合的类型很有多，每一种都有其作用和输出。见[Create transform](####Create transform API)了解更多关于支持的聚合和分组依据字段的内容。

&emsp;&emsp;作为一个可选的步骤，你也可以增加一个query来进一步限制聚合的范围。

&emsp;&emsp;transform会分页处理source index query对应的数据并且对此执行一个composite aggregation。聚合的输出会存储到一个destination index中。transform每次查询source index时，都会创建一个checkpoint。你可以决定是否希望transform执行一次还是连续执行。一个batch transform属于单个操作并且只有一个checkpoint。连续的transforms会不断的增加以及处理（increment and process）新提取的（ingest）source data的checkpoint。

&emsp;&emsp;比如你正在运行一个售卖衣服的网上商城（webshop）。每个订单会创建一个文档，文档中包含一个唯一的订单ID、订单名字和订单中产品的分类、价格、数量，订单的准确时间和一些客户的信息（名字，性别，地址等等）。你的数据集中包含了过去一年的交易。

&emsp;&emsp;如果你想查看上一财年不同类别的销售额，那么定义一个transform，这个transform根据产品分类（女鞋、男鞋等等）和订单时间进行分组。使用过去一年的时间作为订单时间的时间间隔（interval），然后根据订单数量增加一个sum aggregation。输入结果是一个entity-centric index，这个index中显示了过去一年每一类产品卖出的商品的数量。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/pivot-preview.png">

##### Latest transforms

&emsp;&emsp;你可以使用 latest transform将最近的文档拷贝到新的索引中（You can use the latest type of transform to copy the most recent documents into a new index）。你必须指定一个或者多个域作为一个唯一键用于对数据进行分组，同时还要一个时间域（date field）来进行时间排序（sort the data chronologicallly）。比如说你可以使用这种类型的transform来跟踪每一个用户最新付款的时间或者每一台服务器最新的事件（the latest event for each host）。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/pivot-preview.png">

&emsp;&emsp;跟pivot transforms一样，latest transform也可以允许一次或者持续运行。它会在source index的data上执行一个composite aggregation并将输出存储到destination index。如果transform持续运行，新的唯一键会自动的添加到destination index上，现有键（existed key）的最新的文档会在每一个checkpoint时自动的更新。

##### Performance considerations

&emsp;&emsp;Transforms在source index上执行聚合并将结果存储到destination index中。因此它所需要的时间跟资源不会比aggregation和index这两个操作花费的时间少。

&emsp;&emsp;如果你一定要对大量的历史数据执行Transform，那么它最初会占用大量的资源—特别是在第一个检查点期间。

&emsp;&emsp;为了有更好的性能，你需要保证对search aggregation和queries进行了优化并且只对必要的进行Transform处理。考虑好你是否能将一个source query对应的数据进行Transform操作，降低处理的数据范围。同样要考虑好集群是否有足够的资源同时处理composite aggregation的查询和索引Transform输出的结果。

&emsp;&emsp;如果你想要分散集群的压力（spread out the impact on your cluster）（slower Transform带来的压力）。你可以调整执行查询跟索引请求的速率。在你[create](####Create transform API)或者[update ](####Update transform API)你的Transform时设置`docs_per_second`进行限制。如果想要计算当前的速率，根据[get transform stats API](####Get transform statistics API)中使用下面的信息：

&emsp;&emsp;`documents_processed / search_time_in_ms * 1000`

#### Set up transforms
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/transform-setup.html)

##### Requirements overview

&emsp;&emsp;若要使用Transform，你必须：

- 至少要有一个[transform node](#####Transform node)
- management features visible in the Kibana space, and
- security privileges that:
  - grant use of transforms, and
  - grant access to source and destination indices

##### Security_privileges

&emsp;&emsp;用户的安全权限的分配对用户使用transform会有影响。考虑下面的两个类别：

- [Elasticsearch API user](######Elasticsearch API user)：通过Elasticsearch APIs使用Elasticsearch client、cURL、或者kibana Dev Tools访问transforms。这个场景要求Elasticsearch安全权限（security privileges）
- [Kibana user](######Kibana user)：在Kibana中使用transforms。这种场景要求Kibana feature privileges和Elasticsearch安全权限（security privileges）

###### Elasticsearch API user

&emsp;&emsp;为了能管理transform，你必须满足下面的所有要求：

- `transform_admin` built-in role或者`manage_transform` cluster privileges
- 源索引（source index）上`read`和`view_index_metadata`的索引权限（index privilege）
- 目前索引（destination index）上的`create_index`、`index`、`manage`和`read`的索引权限。一旦配置了`retention_policy`，也需要有目标索引上的`delete`索引权限。

&emsp;&emsp;如果是仅仅要读取下transforms的配置跟状态，你必须要有：

- `transform_user` built-in role or `monitor_transform` cluster privileges

&emsp;&emsp;可以参考[Built-in roles](####Built-in roles) and [Security privileges](####Security privileges)了解更多关于Elasticsearch roles and privileges的信息。

###### Kibana user

##### Kibana spaces

#### When to use transforms
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/transform-usage.html)

&emsp;&emsp;Elasticsearch的aggregation是一个强有力并且非常灵活的功能，可以让你汇总（summarize）并且检索出你的数据中的complex insight。你可以在一个busy website上汇总每天请求网页的数量这类复杂的事情，并可以按地理位置和浏览器类型细分（break down）。 如果你使用相同的数据集来尝试计算一些东西比如说计算页面访问的会话（visitor web session）的平均时间，然而，这样很快会出现OOM。

&emsp;&emsp;为什么会发生OOM呢？网页会话（web session）的持续时间是behavior attribute的一个例子，它不被记录于任何一条日志中。只有从weblogs中找到每一个session的第一条跟最后一条才能衍生出来。这种衍生（derivation）数据要求复杂的查询表达式以及很多的内存来连接（connect）所有指向的数据。如果你有一个持续处理的后台程序将相关的事件融合（fuse）到entity-centric并汇总到另一个索引，你就可以得到一个更实用、连贯的图（joined-up picture）。这个新索引有时被称为数据帧（data frame）。

&emsp;&emsp;当出现下面的需求时，你应该要使用transforms而不是aggregation：

- 你需要一个复杂的特征索引（feature index）而不是top-N的集合
  - 在机器学习中，你通常需要一个复杂的behavioral feature的集合而不是top-N。 如果你要预测客户流失（customer churn），你可能需要观察例如上一周网页访问数量、销售量、或者邮件发出的数量（the number of emails sent）。Elastic Stack的机器学习功能（machine learning feature）会创建基于多维特性空间（multi-dimensional feature space）的模型，就可以受益于由transforms创建的全特征索引（full feature index）
  - 这个场景同样可以应用于跨一个或多个聚合结果进行查询。聚合结果可以被排序或者过滤，但是会受到返回的分桶数量上限的约束（constraint），见[limitations to ordering](####Terms aggregation)和[filtering by bucket selector](####Bucket selector aggregation)。如果你想要查询所有的聚合结果，你需要创建完整的数据帧（data frame）。如果你需要根据多个域对聚合结果进行排序或者过滤，那么transforms就特别的有用。
- 你需要通过pipeline aggregation对聚合结果进行排序
  - [Pipeline aggregations](###Pipeline aggregations)不能用于排序。技术上来说这是因为pipeline aggregation是在所有的聚合已经完成后的reduce phase期间运行的。如果你创建了一个transforms，你可以有效的对多个数据进行传递。
- 你想要创建summary tables来优化查询
  - 如果你有一个high level dashboard并且会被大量的用户访问并且在一个很大的数据集上使用复杂的聚合查询，这时候创建一个transforms来缓存结果可能会比较会更有效。因此，每个用户都不需要运行聚合查询

#### Generating alerts for transforms
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/transform-alerts.html)

>WARNING：该功能处于beta测试阶段，可能会发生变化。设计跟代码相比较官方的GA（general availability）功能是不成熟的，原样的提供了这个功能但没有任何的保证（warranty）。Beta功能不受官方GA功能的支持SLA的限制。

&emsp;&emsp;Kibana的告警功能（alerting feature）中支持transforms的规则，它会基于某些条件来检查连续的transforms的运行状况（the healthy of continuous transforms）。如果满足规则中的条件，会创建一条规则并且触发相关的action。您可以创建一个规则来检查连续的transforms是否已经启动，如果没有启动，则通过电子邮件通知你。参考[Alerting](https://www.elastic.co/guide/en/kibana/8.2/alerting-getting-started.html#alerting-getting-started)了解更多关于Kibana alerting feature的内容。

&emsp;&emsp;目前可用的transforms规则如下所示：

###### Transform health

&emsp;&emsp;监控transforms的运行状况并在发生操作问题（operational issue）时发出警报。

##### Creating a rule

&emsp;&emsp;你可以在**Stack Management > Rules and Connectors**下创建transforms的规则。

&emsp;&emsp;在**Create rule**的窗口中，给规则起一个名字并且提供一个可选的tag。指定检查transforms运行状态是否发生变化的时间间隔。你也通过选择`Notify`来指定一个通知选项。只要在检查间隔期间满足配置的条件，alert就会保持活动状态。当在下一个interval中没有满足条件，`Recovered` action group会被调用并且告警状态改为`OK`。参考[general rule details](https://www.elastic.co/guide/en/kibana/8.2/create-and-manage-rules.html#defining-rules-general-details)了解更多的内容细节。

&emsp;&emsp;在Stack Monitoring下选择Transform health rule type：

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/transform-rule.png">

###### Transform health

&emsp;&emsp;选择一个或者多个transforms。你可以使用特殊的符号（`*`）将规则应用到所有的transforms。Transforms created after the rule are automatically included。

&emsp;&emsp;下文中的transform运行状态检查是可用的并且是默认开启的：

- Transform is not started

&emsp;&emsp;相关的transforms如果没有启动或者它没有索引任何的数据时会发出通知。建议在通知的消息中包含必要的action来解决这个错误。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/transform-check-config.png">

&emsp;&emsp;在创建规则的最后一步中，[define the actions](#####Defining actions)来执行满足条件后的后续动作。

##### Defining actions

&emsp;&emsp;通过选择一个connector type将你的规则和内建集成（built-in integrations）的action联系起来。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/transform-alert-actions.png">

&emsp;&emsp;比如说你可以选择`Slack`作为一个connector type，配置为将消息发送到一个你选择频道中。你可以选择一个index connector type，配置为往你指定的索引中写入JSON对象（JSON object）。当然也可以自定义通知消息。消息中可以有一系列的变量可选，例如 transform ID, description, transform state等等。

&emsp;&emsp;在保存完配置后，你可以在`Rules and Connectors`列表中看到这条规则，你可以检查其状态并且概览其配置信息。

&emsp;&emsp;告警的名字总是跟关联的触发的transforms的ID是一样的。你可以在列出各个警报的规则页面上关闭特定的transforms的通知。你可以在**Rules and Connectors**中根据规则的名字打开通知。

#### Working with transforms at scale
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/transform-scale.html)

&emsp;&emsp;Transforms将现有的Elasticsearch索引转化到汇总索引中（summarized indices），使得有机会用于new insight和数据分析。transforms中的查询和索引操作都使用了标准的Elasticsearch功能，所以在大规模（at scale）使用transforms时需要考虑的事项跟使用标准的Elasticsearch查询是相似的（similar）。如果你遇到了性能问题（performance issue），可以先从明确好瓶颈区域（bottleneck areas）（查询、索引、运行或者存储），然后再看下本章节中提高的需要考虑的相关事项来提高性能。本章节的内容还有助于理解transforms是如何工作的，因为根据transforms是在连续模式下运行还是在批处理模式下（in continuous mode or in batch）运行，应用了不同的考虑事项。

&emsp;&emsp;在这一章节中，你可以学习到：

- 理解配置选项对transforms性能的影响

###### Prerequisites:

&emsp;&emsp;阅读这些指导内容的前提是你有一个想要进行调整（tune）的transforms，并且你对熟悉了下面的内容：

- [How transforms work](####Transform overview)
- [How to set up transforms](####Set up transforms)
- [How transform checkpoints work in continuous mode](####How transform checkpoints work)

&emsp;&emsp;下面的考虑事项（consideration）没有先后顺序（not sequential），每一条的编号只是作为navigation而已。你可以任意的采纳（take action）一个或者多个考虑事项。大多数的建议同时适用于continuous和batch transforms。如果只适用于一种transforms类型，会在描述中高亮说明。

&emsp;&emsp;在每一条建议末尾的圆括号中的关键字说的是当遇到这类瓶颈区域（bottleneck area）可能可以通过这条建议来提高性能。

##### Measure transforms performance

&emsp;&emsp;为了可以优化transforms的性能，首先从明确主要工作的地方开始（start by identifying the areas where most work is being done）。在Kibana 页面中，`Transforms`中的`Stats`里包含了下面三个主要的信息：Indexing、searching、和processing time（或者你可以使用[transforms stats API](####Get transform statistics API)）。例如，如果结果中显示在查询时花费的时间比例最高（highest proportion of time），那么优先优化transforms中的查询。Transforms同样有[Rally support](https://esrally.readthedocs.io/en/stable/)，使得在必要时通过transforms进行配置来检查运行时的性能。如果你已经优化了关键的部分（crucial factor），但是仍然有性能问题，那么你可能需要考虑是否要提高硬件的性能。

##### 1. Optimize frequency (index)

&emsp;&emsp;在一个continuous transform中，配置项`frequency`用来设置周期检查source index变更的时间间隔。如果检测到了变更，source index会被搜索，并将变更应用（apply）到destination index。基于于你的用例，你可能会降低应用变更的频率。通过将`frequency`设置为一个较高的值（最大值是一个小时），工作负载（workload）可以随着时间推移被分散，代价是数据更新频率降低了（less up-to-date）。

##### 2. Increase the number of shards of the destination index (index)

&emsp;&emsp;基于destination index的大小，你可能需要提高分片的数量。transforms在创建destination index时默认只使用一个分片。可以在启动transforms前先创建好索引来覆盖索引设置。参考[Scalability and resilience](###Scalability and resilience: clusters, nodes, and shards)了解分片的数量会如何影响扩展性跟弹性（scalability 和 resilience）。

>TIP：使用[Preview transform](####Preview transform API)来查看transforms创建destination index时使用的设置（settings）。你可以拷贝并且调整索引配置并且在transforms启动前创建好索引。

##### 3. Profile and optimize your search queries (search)

&emsp;&emsp;如果你定义了一个查询source index的query，确保这个query是高效的。使用Kibana中的`Search Profiler`和`Dev Tools`来获取每一个独立的组件在查询请求中详细的时间开销信息（timing information）。或者你可以使用[Profile](####Profile API)。其结果可以给你一个insight，让你了解查询请求在low level是如何执行的，并理解为什么一些请求很慢。接着你就可以采取操作进行优化。

&emsp;&emsp;Transforms执行的是标准的Elasticsearch查询请求。有很多的方法来编写Elasticsearch query，并且有些query比其他的query更加的高效。参考[Tune for search speed](###Tune for search speed)了解更多关于调节（tune）Elasticsearch性能的内容。

#### How transform checkpoints work
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/transform-checkpoints.html)

&emsp;&emsp;transformas每次检查完（examine）source index会创建或者更新destination index，并生成一个checkpoint。

&emsp;&emsp;如果transforms只运行一次，逻辑上只会生成一个checkpoint。如果transforms持续运行，它会在提取（ingest）和转化（transform）source data时创建checkpoint。ransform的`sync`属性通过指定一个时间类型的域（time fields）来配置checkpoint。

&emsp;&emsp;要创建一个checkpoint，continuous transform会：

1. 检查source index的变更
   - transform使用一个周期性的定时器（periodic timer） 对source index进行检查。基于transforms中定义的`frequency`这个时间间隔属性来完成检查。
   - If the source indices remain unchanged or if a checkpoint is already in progress then it waits for the next timer
   - 如果发生了变更则创建一个checkpoint。
2. 明确哪个entities and/or time buckets发生了变更
   - transforms在查询后会检查在上一次和最新的检查点之间哪些entities or time buckets发生了变更。transform会用变更的值，使用较少的操作来同步source和destination index，而不是full  re-run。
3. 将变更更新到destination index（the data frame）中
   - transform将变更应用到destination index中相关的新的或者发生变更的entities or time buckets中。The set of changes can be paginated。transform执行一个composite aggregation，类似batch transform operation。然而它会基于之前的步骤添加query filters来减少工作量（amount of work）。应用（apply）后所有的变更后，checkpoint就完成了。

&emsp;&emsp;checkpoint的处理会涉及到集群上的Indexing和search动作（activity）。在开发transforms这个功能时，我们已经尝试控制其性能。我们决定让transform花更长的时间来完成，而不是快速完成并优先（take  precedence in）消耗资源。也就是说，集群仍然需要足够的资源来支持composite aggregation搜索以及索引其结果。

>TIP：如果集群因为transform导致性能下降（unsuitable performance degradation），可以停止使用transform，并且参考[Performance considerations](#####Performance considerations)。

##### Using the ingest timestamp for syncing the transform

&emsp;&emsp;在大多数的情况下，强烈建议提取（ingest）source index中的时间戳用于同步transform。这是一种能让transform区分发生变更最好的方法（the optimal way）。如果你的数据源（data source）遵循[ECS standard](https://www.elastic.co/guide/en/ecs/8.2/ecs-reference.html)，那你已经有一个[event.ingested](https://www.elastic.co/guide/en/ecs/8.2/ecs-event.html#field-event-ingested)域，这时候就可以将`event.ingested`作为transform的`sync.time.field`属性。

&emsp;&emsp;如果你没有`event.ingested`或者没有生成（populated）。你可以通过一个ingest pipeline进行设置。创建一个ingest pipeline可以使用[ingest pipeline API](####Create or update pipeline API)或者通过Kibana中的`Stack Management > Ingest Pipelines`。使用[set processor](####Set processor)来设置这个域并关联提取的timestamp的值。

```text
PUT _ingest/pipeline/set_ingest_time
{
  "description": "Set ingest timestamp.",
  "processors": [
    {
      "set": {
        "field": "event.ingested",
        "value": "{{{_ingest.timestamp}}}"
      }
    }
  ]
}
```

&emsp;&emsp;你在创建ingest pipeline之后，将其应用（apply）到transform的source index中。这个pipeline会将提取出的时间戳的添加到每篇文档的`event.ingested`域中。在通过[create transform API](####Create transform API)创建一个新的transform或者通过[update transform API](####Update transform API)更新现有的transform时来配置transform的`sync.time.field`属性。

&emsp;&emsp;参考[Add a pipeline to an indexing request](####Add a pipeline to an indexing request)和[Ingest pipelines](##Ingest pipelines)了解更多关于如何使用ingest pipeline的内容。

##### Change detection heuristics

&emsp;&emsp;当transform以continuous mode运行时，它不会的更新新的数据到destination index中。transform使用一组称为change detection的启发式方法以更少的操作更新destination index。

&emsp;&emsp;在这个例子中，根据host name对数据进行了分组。Change detection会检测哪些host name发生了变更。host A、C和G，只更新与这些host有关的文档，而不更新存储关于hostB、D或任何其他未更改的主机的信息的文档。

&emsp;&emsp;`date_histogram`这种通过时间分桶的场景也可以使用这种启发式方法。Change detection会检测哪些时间分桶发生了变更并且只更新发生变更的时间分桶。

##### Error handling

&emsp;&emsp;transform中发生故障往往是在Indexing或者searching时。To increase the resiliency of transforms, the cursor positions of the aggregated search and the changed entities search are tracked in memory and persisted periodically。

&emsp;&emsp;Checkpoint的故障可以根据下面的方式分类：

- Temporary failures：checkpoint会进行重试。如果连续10次出现错误（10 consecutive failure occur），transform会有一个失败的状态。比如说当分片发生故障并且请求只能返回部分数据时。
- Irrecoverable failures：transform会马上失败。比如说source index没有找到时。
- Adjustment failures：transform基于adjusted settings进行重试。比如说如果在composite aggregation时发生了 parent circuit breaker memory errors 。transform会收到部分的结果（partial result）。aggregated search在重试时会进行更少数量的分桶。重试会使用在transform中定义的`frequency`这个时间间隔。如果重试的查询达到了要使用最小数量分桶的地步，则会发生一个不可恢复的错误。

&emsp;&emsp;如果在一个节点上transform发生故障。transform会从最近的一次 persisted cursor position重启恢复。恢复的操作可能执行一些已经完成过的工作，但是能保证数据一致性。

#### API quick reference
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/transform-api-quickref.html)

&emsp;&emsp;所有transform的endpoint都有这个前缀：

```text
_transform/
```

- [Create transforms](####Create transform API)
- [Delete transforms](####Delete transform API)
- [Get transforms](####Get transform statistics API)
- [Get transforms statistics](####Get transform statistics API)
- [Preview transforms](####Preview transform API)
- [Reset transforms](####Reset transform API)
- [Start transforms](####Start transform API)
- [Stop transforms](####Stop transforms API)
- [Update transforms](####Update transform API)

&emsp;&emsp;见[Transform APIs](###Transform APIs)查看所有的接口。

#### Tutorial: Transforming the eCommerce sample data
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ecommerce-transforms.html)

&emsp;&emsp;[Transforms](###Transforming data)能让你检索Elasticsearch的索引，然后进行转化（transform）并存放到另一个索引中。我们将使用[Kibana sample data](https://www.elastic.co/guide/en/kibana/8.2/add-sample-data.html)来演示如何使用transform来pivot以及summarize。

1. 先验证下你的环境是否设置正确以便使用transform。如果开启了Elasticsearch Security Feature，你需要一个有预览和创建transform权限的用户来完成此次教程。你同样需要有source index和destination index这些指定的索引权限。见[Setup](####Set up transforms)。
2. 选择source index
   -  在这个例子中， 我们将使用电子商务订单的样例数据(eCommerce orders sample data)。如果你不熟悉`kibana_sample_data_ecommerce`这个索引，使用Kibana中的**Revenue**了解下。考虑下你能从这些电子商务数据中获得哪些insight
3. 选出transform的pivot类型，并使用各种用于分组和聚合的选项
   - 目前有两种类型的transform，但首先我们先要`Pivoting`你的数据，也就是至少使用一个域进行分组（apply）并应用一个聚合。你可以先预览转化后的数据（transformed data）， so go ahead and play with it! 你也可以开启histogram chart来更好的了解你的数据分布。
   - 比如说你可能想要根据产品ID进行分组并且统计每一个产品的销售额和平均价格。或者你可能想要查看每一个客户的行为，计算每一个客户的总消费以及他们为多少种产品付费。或者你可能想要将货币和地区考虑进去。What are the most interesting ways you can transform and interpret this data?

&emsp;&emsp;进入Kibana中的**Management > Stack Management > Data > Transforms**，使用向导（use the wizard）来创建一个transform：

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ecommerce-pivot1.png">

&emsp;&emsp;根据客户ID进行分组并添加一个或多个aggregation来了解每一个客户的订单。比如说让我们计算下购买的产品数量，购买花费总额、最贵以及最便宜的两个订单、订单的总数。通过在`total_quantity`和`taxless_total_price`上使用[sum aggregation](####Sum aggregation)，在`total_quantity`上使用[max aggregation](####Max aggregation)，在`order_id`上使用[cardinality aggregation](####Cardinality aggregation)。 

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ecommerce-pivot2.png">

> TIP: 如果你只对数据的部分子集感兴趣，你可以使用[query](######query)。在这个例子中，我们对数据进行了过滤，使得只关心货币类型为欧元的数据。或者你可以根据货币进行分组。如果你想要使用更加复杂的查询，你可以从[saved search](https://www.elastic.co/guide/en/kibana/8.2/save-open-search.html)中创建你的数据帧（data frame）。

&emsp;&emsp;你也可以使用[preview transforms API](####Preview transform API)。

```text
POST _transform/_preview
{
  "source": {
    "index": "kibana_sample_data_ecommerce",
    "query": {
      "bool": {
        "filter": {
          "term": {"currency": "EUR"}
        }
      }
    }
  },
  "pivot": {
    "group_by": {
      "customer_id": {
        "terms": {
          "field": "customer_id"
        }
      }
    },
    "aggregations": {
      "total_quantity.sum": {
        "sum": {
          "field": "total_quantity"
        }
      },
      "taxless_total_price.sum": {
        "sum": {
          "field": "taxless_total_price"
        }
      },
      "total_quantity.max": {
        "max": {
          "field": "total_quantity"
        }
      },
      "order_id.cardinality": {
        "cardinality": {
          "field": "order_id"
        }
      }
    }
  }
}
```

4. 如果你满意这个预览数据，那么就可以创建这个transform了。
   a. 提供一个transform ID，destination index的名字以及描述信息（可选）。如果destination index不存在，则会在transform启动的时候创建。
   b. 先确定好transform要运行一次还是一直运行（once or continuous）。由于样例数据是不会发生变更，那就让transform运行一次。If you want to try it out, however, go ahead and click on **Continuous mode**。你必须选择一个域，transform就可以根据这个域检查是否发生了变更。一般情况下，使用ingest timestamp域是个不错的主意。在这个例子中，你可以使用`order_filed`域。
   c. 获取你可以配置一个retention policy来应用到你的transform中。选择一个date域用于明确destination index中的旧文档并且提供一个最大的寿命（age）。destination index中超过这个寿命的文档会被移除。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ecommerce-pivot3.png">

&emsp;&emsp;在Kibana中，在你完成创建transform前，你可以复制preview transform API request 到你的粘贴板。这些信息对你之后想要手动创建destination index会有一定的帮助。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ecommerce-pivot4.png">

&emsp;&emsp;你也可以使用[create transforms API.](####Create transform API)。

```text
PUT _transform/ecommerce-customer-transform
{
  "source": {
    "index": [
      "kibana_sample_data_ecommerce"
    ],
    "query": {
      "bool": {
        "filter": {
          "term": {
            "currency": "EUR"
          }
        }
      }
    }
  },
  "pivot": {
    "group_by": {
      "customer_id": {
        "terms": {
          "field": "customer_id"
        }
      }
    },
    "aggregations": {
      "total_quantity.sum": {
        "sum": {
          "field": "total_quantity"
        }
      },
      "taxless_total_price.sum": {
        "sum": {
          "field": "taxless_total_price"
        }
      },
      "total_quantity.max": {
        "max": {
          "field": "total_quantity"
        }
      },
      "order_id.cardinality": {
        "cardinality": {
          "field": "order_id"
        }
      }
    }
  },
  "dest": {
    "index": "ecommerce-customers"
  },
  "retention_policy": {
    "time": {
      "field": "order_date",
      "max_age": "60d"
    }
  }
}
```

5. 可选：创建destination index

   - 如果destination index不存在，会在transform启动时候创建。pivot transform会根据source index和transform aggregation为destination index推测出mapping。如果destination index中的一些域是从脚本中衍生出来的（比如说你使用了[scripted_metrics](####Scripted metric aggregation)或者[bucket_scripts](####Bucket script aggregation)），它们是通过[dynamic mappings](###Dynamic mapping)创建的。你可以使用preview transform API预览下destination index中的mapping。在Kibana中，如果你拷贝了API请求，粘贴到控制台，然后参考下API响应中的`generated_dest_index`。

>NOTE：相比较Kibana中可用的选项，通过APIs能够为transform提供更多的用于配置的选项。例如你可以通过调用[Create transform](####Create transform API)来为`dest`设置一个ingest pipeline。参考[documentation](###Transform APIs)了解更多关于transform配置信息 。

&emsp;&emsp;API example:

```text
{
  "preview" : [
    {
      "total_quantity" : {
        "max" : 2,
        "sum" : 118.0
      },
      "taxless_total_price" : {
        "sum" : 3946.9765625
      },
      "customer_id" : "10",
      "order_id" : {
        "cardinality" : 59
      }
    },
    ...
  ],
  "generated_dest_index" : {
    "mappings" : {
      "_meta" : {
        "_transform" : {
          "transform" : "transform-preview",
          "version" : {
            "created" : "8.0.0"
          },
          "creation_date_in_millis" : 1621991264061
        },
        "created_by" : "transform"
      },
      "properties" : {
        "total_quantity.sum" : {
          "type" : "double"
        },
        "total_quantity" : {
          "type" : "object"
        },
        "taxless_total_price" : {
          "type" : "object"
        },
        "taxless_total_price.sum" : {
          "type" : "double"
        },
        "order_id.cardinality" : {
          "type" : "long"
        },
        "customer_id" : {
          "type" : "keyword"
        },
        "total_quantity.max" : {
          "type" : "integer"
        },
        "order_id" : {
          "type" : "object"
        }
      }
    },
    "settings" : {
      "index" : {
        "number_of_shards" : "1",
        "auto_expand_replicas" : "0-1"
      }
    },
    "aliases" : { }
  }
}
```

6. 启动transform

>TIP：尽管资源的使用会基于集群负载进行调整，在transform启动后会增加集群中Indexing和search的负载。如果你遇到了过多的负载，那你可以停止transform。

&emsp;&emsp;你可以在Kibana中启动、停止、重置以及管理transform：

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/manage-transforms.png">

&emsp;&emsp;或者你也可以使用[start transforms](####Start transform API), [stop transforms](####Stop transforms API) 和 [reset transforms](####Reset transform API)这些APIs。

&emsp;&emsp;如果你reset了transform，所有的checkpoint，states和destination index（如果是通过transform创建的）都会被删除。如果transform创建好了那么它就可以开始运行了。

&emsp;&emsp;API example:

```text
POST _transform/ecommerce-customer-transform/_start
```

>TIP：如果你选择了Batch transform，这是一个single operation并且只有一个checkpoint。当这个transform完成后你不能重启它。 Continuous transforms的不同点在于，当提取了新的source 数据后，它持续的增加和处理checkpoint。

7. 查看新的索引中的数据

&emsp;&emsp;例如在Kibana中使用`Discover`这个应用：

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ecommerce-results.png">

8. 可选：使用`latest`方法创建另一个transform

&emsp;&emsp;这个方法在destination index中产生最新的文档的每一个唯一键的值。例如你可能想找到每一个客户或者每一个国家和地区的最新排序（根据`order_date`进行排序）。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ecommerce-latest1.png">

API example：

```text
POST _transform/_preview
{
  "source": {
    "index": "kibana_sample_data_ecommerce",
    "query": {
      "bool": {
        "filter": {
          "term": {"currency": "EUR"}
        }
      }
    }
  },
  "latest": {
    "unique_key": ["geoip.country_iso_code", "geoip.region_name"],
    "sort": "order_date"
  }
}
```

>TIP：如果destination index不存在，则会在第一次启动transform时创建。跟pivot transforms不同的是，latest transform不会在创建index的时推测mapping定义。它使用的是动态索引（dynamic index）。如果要使用显示的（explicit）mapping。在你启动transform前创建好索引。

9. 如果你不想要保留某个transform，你可以在Kibana中删除它或者使用使用[delete transform API](####Delete transform API)进行删除。默认情况下，当你删除了一个transform，它的destination index和Kibana index pattern会保留。

&emsp;&emsp;现在你对Kibana样例数据创建了简单的transform，并且考虑了你的数据中可能的用例。见[When to use transforms](####When to use transforms)和[Examples](####Transform examples)了解更多的想法。

#### Transform examples
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/transform-examples.html)

&emsp;&emsp;这些例子展示的是如何使用transform从你的数据中衍生（derive）出有用的insight。所有的例子都使用了[Kibana sample datasets](https://www.elastic.co/guide/en/kibana/8.2/add-sample-data.html)。至于更详细的，分步骤的例子，见[Tutorial: Transforming the eCommerce sample data](####Tutorial: Transforming the eCommerce sample data)。

- [Finding your best customers](#####Finding your best customers)
- [Finding air carriers with the most delays](#####Finding air carriers with the most delays)
- [Finding suspicious client IPs](#####Finding suspicious client IPs)
- [Finding the last log event for each IP address](#####Finding the last log event for each IP address)
- [Finding client IPs that sent the most bytes to the server](#####Finding client IPs that sent the most bytes to the server)
- [Getting customer name and email address by customer ID](#####Getting customer name and email address by customer ID)

##### Finding your best customers

&emsp;&emsp;这个例子中使用了电子商务订单的样例数据集来找出在一个假想的网上商城中消费最多的客户。让我们使用`pivot`类型的transform，这样我们的destination index中会包含订单的数量，订单的总价，每一个商品的总数，每一个订单的平均价格以及每一个客户购买的产品数量。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/transform-ex1-1.jpg">

&emsp;&emsp;或者你可以使用[preview transform](####Preview transform API)和[create transform API](####Create transform API)。

&emsp;&emsp;API example

```text
POST _transform/_preview
{
  "source": {
    "index": "kibana_sample_data_ecommerce"
  },
  "dest" : { 
    "index" : "sample_ecommerce_orders_by_customer"
  },
  "pivot": {
    "group_by": { 
      "user": { "terms": { "field": "user" }},
      "customer_id": { "terms": { "field": "customer_id" }}
    },
    "aggregations": {
      "order_count": { "value_count": { "field": "order_id" }},
      "total_order_amt": { "sum": { "field": "taxful_total_price" }},
      "avg_amt_per_order": { "avg": { "field": "taxful_total_price" }},
      "avg_unique_products_per_order": { "avg": { "field": "total_unique_products" }},
      "total_unique_products": { "cardinality": { "field": "products.product_id" }}
    }
  }
}
```

&emsp;&emsp;第6行，transform中的destination index，在`_preview`中可以忽略

&emsp;&emsp;第10行，使用了两个`group_by`。这意味着transform包含了由`user`和`customer_id`组成的列。在此数据集中，这两个字段都是唯一的。通过将两者都包含在transform中，它为最终结果提供了更多的context。

> NOTE: 在上面的例子中，简练的 （condense）JSON 格式对pivot对象有更好的可读性

&emsp;&emsp;preview transform API能让你提前（in advance）的看到transform生成出的一些样例结果布局：

```text
{
  "preview" : [
    {
      "total_order_amt" : 3946.9765625,
      "order_count" : 59.0,
      "total_unique_products" : 116.0,
      "avg_unique_products_per_order" : 2.0,
      "customer_id" : "10",
      "user" : "recip",
      "avg_amt_per_order" : 66.89790783898304
    },
    ...
    ]
  }
```

&emsp;&emsp;这个transform使得更容易的回答下面的问题：

- 哪个客户消费最多
- 在每个订单中，哪个客户消费最多
- 哪个客户经常下单购买
- 哪个客户购买的不同产品的数量是最少的

&emsp;&emsp;当然也可以只使用聚合来回答上面的问题，但是transform可以让我们持久化这些数据作为一个customer centric index。这个索引使我们能够用于大规模（at scale）分析数据，并提供更大的灵活性，从以客户为中心的角度探索和navigate 数据。在某些情况下，它甚至可以使创建可视化变得非常简单。

##### Finding air carriers with the most delays

&emsp;&emsp;这个例子使用了航空样例数据来找出哪个航空公司延误次数最多。首先通过一个query filter从source data中筛选掉所有被取消的航班。然后将数据转化（transform）为包含了根据航空公司对航班编号分组去重，延误时间求和，飞行时间求和。最终使用了一个[bucket_script](####Bucket script aggregation)确定实际延误的飞行时间百分比。

```text
POST _transform/_preview
{
  "source": {
    "index": "kibana_sample_data_flights",
    "query": { 
      "bool": {
        "filter": [
          { "term":  { "Cancelled": false } }
        ]
      }
    }
  },
  "dest" : { 
    "index" : "sample_flight_delays_by_carrier"
  },
  "pivot": {
    "group_by": { 
      "carrier": { "terms": { "field": "Carrier" }}
    },
    "aggregations": {
      "flights_count": { "value_count": { "field": "FlightNum" }},
      "delay_mins_total": { "sum": { "field": "FlightDelayMin" }},
      "flight_mins_total": { "sum": { "field": "FlightTimeMin" }},
      "delay_time_percentage": { 
        "bucket_script": {
          "buckets_path": {
            "delay_time": "delay_mins_total.value",
            "flight_time": "flight_mins_total.value"
          },
          "script": "(params.delay_time / params.flight_time) * 100"
        }
      }
    }
  }
}
```
&emsp;&emsp;第5行，从数据源中筛选出没有被取消的航班

&emsp;&emsp;第13行，transform中的destination index，在`_preview`中可以忽略

&emsp;&emsp;第17行，根据`Carrier`域进行分组，这个域的域值是航空公司的名字

&emsp;&emsp;第24行，这个`bucket_script`会对聚合返回的结果进行计算。在这个特定的例子中，它会计算出延误占飞行时间的比例。

&emsp;&emsp;预览可以让你看到在新的索引中将含有每家航空公司的这样的数据：

```text
{
  "preview" : [
    {
      "carrier" : "ES-Air",
      "flights_count" : 2802.0,
      "flight_mins_total" : 1436927.5130677223,
      "delay_time_percentage" : 9.335543983955839,
      "delay_mins_total" : 134145.0
    },
    ...
  ]
}
```

&emsp;&emsp;这个transform使得更容易的回答下面的问题：

- 哪家航空公司的延误时间占飞行时间的比例最大？

> NOTE: 此数据是虚构的（fictional），并不反映任何特色目的地或出发地机场的实际延误或航班统计数据

##### Finding suspicious client IPs

&emsp;&emsp;这个例子使用网页样例数据集来识别出有问题的（suspicious）client ip。转化（transform）后的数据所在的索引中包含了流量的总和（sum of bytes）以及每个客户机IP的不同url、代理、按位置和地理目的地传入的请求的数量。还使用了filter aggregation来统计每一个client IP收到的不同HTTP类型响应的数量。最终下面的例子将网页日志数据转化为一个entity centric index，这个entity就是`cientip`。

```text
PUT _transform/suspicious_client_ips
{
  "source": {
    "index": "kibana_sample_data_logs"
  },
  "dest" : { 
    "index" : "sample_weblogs_by_clientip"
  },
  "sync" : { 
    "time": {
      "field": "timestamp",
      "delay": "60s"
    }
  },
  "pivot": {
    "group_by": {  
      "clientip": { "terms": { "field": "clientip" } }
      },
    "aggregations": {
      "url_dc": { "cardinality": { "field": "url.keyword" }},
      "bytes_sum": { "sum": { "field": "bytes" }},
      "geo.src_dc": { "cardinality": { "field": "geo.src" }},
      "agent_dc": { "cardinality": { "field": "agent.keyword" }},
      "geo.dest_dc": { "cardinality": { "field": "geo.dest" }},
      "responses.total": { "value_count": { "field": "timestamp" }},
      "success" : { 
         "filter": {
            "term": { "response" : "200"}}
        },
      "error404" : {
         "filter": {
            "term": { "response" : "404"}}
        },
      "error5xx" : {
         "filter": {
            "range": { "response" : { "gte": 500, "lt": 600}}}
        },
      "timestamp.min": { "min": { "field": "timestamp" }},
      "timestamp.max": { "max": { "field": "timestamp" }},
      "timestamp.duration_ms": { 
        "bucket_script": {
          "buckets_path": {
            "min_time": "timestamp.min.value",
            "max_time": "timestamp.max.value"
          },
          "script": "(params.max_time - params.min_time)"
        }
      }
    }
  }
}
```

&emsp;&emsp;第6行，transform中的destination index

&emsp;&emsp;第9行，将transform配置为持续运行。它使用`timestamp`字段来同步source index和destination index。最晚60秒进行一次同步

&emsp;&emsp;第16行，根据`clientip`对数据进行分组

&emsp;&emsp;第26行，Filter aggregation统计了`response`域中成功响应（200）的数量。接下来的两个aggregation根据错误码、某个范围内的响应码（response code）统计了错误的响应。

&emsp;&emsp;第40行，`bucket_script`基于聚合结果统计了`clientip`访问的持续时间

&emsp;&emsp;在你创建transform之后，你必须启动它：

```text
POST _transform/suspicious_client_ips/_start
```

&emsp;&emsp;此后不久（Shortly thereafter），第一个结果会出现在destination index中：

```text
GET sample_weblogs_by_clientip/_search
```

&emsp;&emsp;下面展示的是你的数据中某一个client ip的结果：

```text
  "hits" : [
      {
        "_index" : "sample_weblogs_by_clientip",
        "_id" : "MOeHH_cUL5urmartKj-b5UQAAAAAAAAA",
        "_score" : 1.0,
        "_source" : {
          "geo" : {
            "src_dc" : 2.0,
            "dest_dc" : 2.0
          },
          "success" : 2,
          "error404" : 0,
          "error503" : 0,
          "clientip" : "0.72.176.46",
          "agent_dc" : 2.0,
          "bytes_sum" : 4422.0,
          "responses" : {
            "total" : 2.0
          },
          "url_dc" : 2.0,
          "timestamp" : {
            "duration_ms" : 5.2191698E8,
            "min" : "2020-03-16T07:51:57.333Z",
            "max" : "2020-03-22T08:50:34.313Z"
          }
        }
      }
    ]
```

> NOTE: 跟其他的Kibana数据集一样，网页日志样例数据集的时间戳跟你加载时的时间有关，包括有些数据的时间是未来时间。一旦这些时间超前的数据成为以前的时间，continuous transform在一定的时间点就会处理它们。如果你是在一段时间之前加载的网页日志样例数据集，你可以卸载并重新加载，时间戳就会发生变更。

&emsp;&emsp;这个transform使得更容易的回答下面的问题：

- 哪个client IP传输最多的数据？
- 哪个client IP跟最多不同数量的URl进行了交互
- 哪个client IP有high error state
- 哪个client IP跟最多不同数量的国家进行了交互

##### Finding the last log event for each IP address

&emsp;&emsp;这个例子使用网页日志样例数据集来找出一个IP地址最后的那条日志。我们使用continuous模式下的`latest`类型的transform。它从最新的source data中拷贝出每一个唯一的key对应的最新文档信息到destination index中，并且在新的数据进入到source data后，将其更新到destination index中。

&emsp;&emsp;选择`clientip`作为唯一的key。根据这个域对数据进行分组。选择`timestamp`域作为时间类型的域用来对数据进行排序。对于continuous mode，指定一个时间类型的域用于识别在source index中新的文档，以及用于周期性检查是否发生变更的间隔时间。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/transform-ex4-1.jpg">

&emsp;&emsp;让我们假设下，我们对只在最近的log中出现的IP地址对应的文档感兴趣。你可以定义一个保留策略（retention policy）并且指定一个date域用于计算文档的寿命（age）。这个例子使用了相同的date域用于排序。然后设置一个文档的最大寿命，超过你设定的值的文档会从destination index中移除。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/transform-ex4-2.jpg">

&emsp;&emsp;这个transform创建的destination index中包含了每一个client ip最新的登陆时间。因为transform是continuous模式，所以当新的数据进入到source index后会更新destination index的值。最终每一篇超过30天（基于保留策略）的文档都会从destination index中移除

```text
PUT _transform/last-log-from-clientip
{
  "source": {
    "index": [
      "kibana_sample_data_logs"
    ]
  },
  "latest": {
    "unique_key": [ 
      "clientip"
    ],
    "sort": "timestamp" 
  },
  "frequency": "1m", 
  "dest": {
    "index": "last-log-from-clientip"
  },
  "sync": { 
    "time": {
      "field": "timestamp",
      "delay": "60s"
    }
  },
  "retention_policy": { 
    "time": {
      "field": "timestamp",
      "max_age": "30d"
    }
  },
  "settings": {
    "max_page_search_size": 500
  }
}
```

&emsp;&emsp;第9行，指定用于对数据分组的域

&emsp;&emsp;第12行，指定用于对数据排序的date域

&emsp;&emsp;第14行，设置时间间隔用于周期性检查source index中是否发生了变化

&emsp;&emsp;第18行，这里面的date域和delay的设置用于同步source index跟destination index

&emsp;&emsp;第24行，为transform指定保留策略，超过配置的值的文档会从destination index中移除

&emsp;&emsp;创建transform之后并启动：

```text
POST _transform/last-log-from-clientip/_start
```

&emsp;&emsp;transform处理了数据后，可以从destination index中进行查询：

```text
GET last-log-from-clientip/_search
```

&emsp;&emsp;下面展示的是你的数据中某一个client ip的结果：

```text
{
  "_index" : "last-log-from-clientip",
  "_id" : "MOeHH_cUL5urmartKj-b5UQAAAAAAAAA",
  "_score" : 1.0,
  "_source" : {
    "referer" : "http://twitter.com/error/don-lind",
    "request" : "/elasticsearch",
    "agent" : "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; .NET CLR 1.1.4322)",
    "extension" : "",
    "memory" : null,
    "ip" : "0.72.176.46",
    "index" : "kibana_sample_data_logs",
    "message" : "0.72.176.46 - - [2018-09-18T06:31:00.572Z] \"GET /elasticsearch HTTP/1.1\" 200 7065 \"-\" \"Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; .NET CLR 1.1.4322)\"",
    "url" : "https://www.elastic.co/downloads/elasticsearch",
    "tags" : [
      "success",
      "info"
    ],
    "geo" : {
      "srcdest" : "IN:PH",
      "src" : "IN",
      "coordinates" : {
        "lon" : -124.1127917,
        "lat" : 40.80338889
      },
      "dest" : "PH"
    },
    "utc_time" : "2021-05-04T06:31:00.572Z",
    "bytes" : 7065,
    "machine" : {
      "os" : "ios",
      "ram" : 12884901888
    },
    "response" : 200,
    "clientip" : "0.72.176.46",
    "host" : "www.elastic.co",
    "event" : {
      "dataset" : "sample_web_logs"
    },
    "phpmemory" : null,
    "timestamp" : "2021-05-04T06:31:00.572Z"
  }
}
```

&emsp;&emsp;这个transform使得更容易的回答下面的问题：

- 与特定IP地址相关的最近的日志事件是什么?

##### Finding client IPs that sent the most bytes to the server

&emsp;&emsp;这个例子使用了网页日志样例数据集来找出每小时往服务器发送最多字节的client Ip。这个例子使用了带有[top_metrics aggregation](####Top metrics aggregation) 的`pivot`类型的transform。

&emsp;&emsp;在date域上使用[date histogram](#####\_Date histogram)并指定一个小时作为时间间隔对数据进行分组。在`bytes`域上使用[max aggregation](####Max aggregation)获取发送到服务的最大数值总和。如果不用`max aggregation`，可以返回发送字节最多的client ip，但是获取不到字节的数值总和。在`top_metrics`属性中，指定`clientip`和`geo.src`，然后根据`byte`字段进行降序排序。这个transform返回发送数据最多的client ip和用对应位置的2个字母的ISO代码（2-letter ISO code of the corresponding location）。

```text
POST _transform/_preview
{
  "source": {
    "index": "kibana_sample_data_logs"
  },
  "pivot": {
    "group_by": { 
      "timestamp": {
        "date_histogram": {
          "field": "timestamp",
          "fixed_interval": "1h"
        }
      }
    },
    "aggregations": {
      "bytes.max": { 
        "max": {
          "field": "bytes"
        }
      },
      "top": {
        "top_metrics": { 
          "metrics": [
            {
              "field": "clientip"
            },
            {
              "field": "geo.src"
            }
          ],
          "sort": {
            "bytes": "desc"
          }
        }
      }
    }
  }
}
```

&emsp;&emsp;第7行，使用时间间隔为1个小时的date histogram对数据进行分组

&emsp;&emsp;第16行，计算`bytes`字段的最大值

&emsp;&emsp;第22行，指定top document中的`clientip`和`geo.src`以及排序方法（`bytes`的值最大的文档）

&emsp;&emsp;上面的例子返回的结果大致是：

```text
{
  "preview" : [
    {
      "top" : {
        "clientip" : "223.87.60.27",
        "geo.src" : "IN"
      },
      "bytes" : {
        "max" : 6219
      },
      "timestamp" : "2021-04-25T00:00:00.000Z"
    },
    {
      "top" : {
        "clientip" : "99.74.118.237",
        "geo.src" : "LK"
      },
      "bytes" : {
        "max" : 14113
      },
      "timestamp" : "2021-04-25T03:00:00.000Z"
    },
    {
      "top" : {
        "clientip" : "218.148.135.12",
        "geo.src" : "BR"
      },
      "bytes" : {
        "max" : 4531
      },
      "timestamp" : "2021-04-25T04:00:00.000Z"
    },
    ...
  ]
}
```

##### Getting customer name and email address by customer ID

&emsp;&emsp;这个例子使用电子商务样例数据集，基于客户ID创建一个entity-centric index，并通过`top_metrics aggregation`获取客户名字、email地址。

&emsp;&emsp;根据`customer_id`进行分组，在`top_metrics aggregation`中添加`metirc`，包括`email`，`ustomer_first_name.keyword`，以及`customer_last_name.keyword`。用`order_date`域对`top_metrics`进行降序排序。API大致如下：

```text
POST _transform/_preview
{
  "source": {
    "index": "kibana_sample_data_ecommerce"
  },
  "pivot": {
    "group_by": { 
      "customer_id": {
        "terms": {
          "field": "customer_id"
        }
      }
    },
    "aggregations": {
      "last": {
        "top_metrics": { 
          "metrics": [
            {
              "field": "email"
            },
            {
              "field": "customer_first_name.keyword"
            },
            {
              "field": "customer_last_name.keyword"
            }
          ],
          "sort": {
            "order_date": "desc"
          }
        }
      }
    }
  }
}
```

&emsp;&emsp;第7行，在`customer_id`域上使用`terms aggregation`对数据进行分组

&emsp;&emsp;第16行，根据时间降序排序返回指定的域（email和名字）

&emsp;&emsp;API返回结果大致如下：

```text
 {
  "preview" : [
    {
      "last" : {
        "customer_last_name.keyword" : "Long",
        "customer_first_name.keyword" : "Recip",
        "email" : "recip@long-family.zzz"
      },
      "customer_id" : "10"
    },
    {
      "last" : {
        "customer_last_name.keyword" : "Jackson",
        "customer_first_name.keyword" : "Fitzgerald",
        "email" : "fitzgerald@jackson-family.zzz"
      },
      "customer_id" : "11"
    },
    {
      "last" : {
        "customer_last_name.keyword" : "Cross",
        "customer_first_name.keyword" : "Brigitte",
        "email" : "brigitte@cross-family.zzz"
      },
      "customer_id" : "12"
    },
    ...
  ]
}
```

#### Painless examples for transforms
（8,2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/transform-painless-examples.html)

&emsp;&emsp;这些例子展示的是如何在transform中使用painless。你可以在[Painless guide](https://www.elastic.co/guide/en/elasticsearch/painless/8.2/painless-guide.html)中了解更多关于Painless scripting language的内容。

- [Getting top hits by using scripted metric aggregation](#####Getting top hits by using scripted metric aggregation)
- [Getting time features by using aggregations](#####Getting time features by using aggregations)
- [Getting duration by using bucket script](#####Getting duration by using bucket script)
- [Counting HTTP responses by using scripted metric aggregation](#####Counting HTTP responses by using scripted metric aggregation)
- [Comparing indices by using scripted metric aggregations](#####Comparing indices by using scripted metric aggregations)
- [Getting web session details by using scripted metric aggregation](#####Getting web session details by using scripted metric aggregation)

>NOTE: 
>
>尽管下面的例子中的上下文是transform的用例，但是它内部的painless脚本可以在Elasticsearch的其他查询聚合中使用。
>
>下面的所有的例子都使用了脚本，但是transform无法推测出脚本生成的用于mapping的域。Transform不会在destination index中创建这些域的mapping，意味着是动态映射的（dynamic mapped）。如果你要显示的mapping，你可以在启动Transform前创建好destination index。

##### Getting top hits by using scripted metric aggregation

&emsp;&emsp;下面的脚本片段（snippet）说的是如何找到最新的文档，换句话说就是找到timestamp是最新的文档。从技术视角看，在Transform中使用[Scripted metric aggregation](####Scripted metric aggregation)也能达到[Top hits](####Top hits aggregation)的功能。

```text
"aggregations": {
  "latest_doc": {
    "scripted_metric": {
      "init_script": "state.timestamp_latest = 0L; state.last_doc = ''", 
      "map_script": """ 
        def current_date = doc['@timestamp'].getValue().toInstant().toEpochMilli();
        if (current_date > state.timestamp_latest)
        {state.timestamp_latest = current_date;
        state.last_doc = new HashMap(params['_source']);}
      """,
      "combine_script": "return state", 
      "reduce_script": """ 
        def last_doc = '';
        def timestamp_latest = 0L;
        for (s in states) {if (s.timestamp_latest > (timestamp_latest))
        {timestamp_latest = s.timestamp_latest; last_doc = s.last_doc;}}
        return last_doc
      """
    }
  }
}
```

&emsp;&emsp;第4行，`init_script`在`state`对象中创建了一个long类型的`timestamp_latest`和string类型的`last_doc`

&emsp;&emsp;第5行，`map_script`中基于文档中的`timestamp`定义了`current_date`，并且跟`state.timestamp_latest`作比较。最终从shard中返回`state.last_doc`。通过使用`new HashMap(...)`复制了source document，这个操作对于将完整的source object从一个阶段传递给下一个阶段是非常重要的

&emsp;&emsp;第11行，`combine_script`从每一个shard中返回`state`

&emsp;&emsp;第12行，`reduce_script`遍历每一个shard返回的`s.timestamp_latest`的值并且返回带有最新timestamp（last_doc）的文档。在响应中，top hit（也就是`latest_doc`）嵌套在`latest_doc`下

&emsp;&emsp;见[scope of scripts](#####Scope of scripts)了解更多的脚本信息

&emsp;&emsp;你可以使用类似的方式来获取last value：

```text
"aggregations": {
  "latest_value": {
    "scripted_metric": {
      "init_script": "state.timestamp_latest = 0L; state.last_value = ''",
      "map_script": """
        def current_date = doc['@timestamp'].getValue().toInstant().toEpochMilli();
        if (current_date > state.timestamp_latest)
        {state.timestamp_latest = current_date;
        state.last_value = params['_source']['value'];}
      """,
      "combine_script": "return state",
      "reduce_script": """
        def last_value = '';
        def timestamp_latest = 0L;
        for (s in states) {if (s.timestamp_latest > (timestamp_latest))
        {timestamp_latest = s.timestamp_latest; last_value = s.last_value;}}
        return last_value
      """
    }
  }
}
```

##### Getting time features by using aggregations

&emsp;&emsp;下面的这个片段（snippet）用来展示如何在transform中使用Painless提取time的功能。片段中使用的`@timestamp`域被定义为了date类型。

```java
"aggregations": {
  "avg_hour_of_day": { 
    "avg":{
      "script": { 
        "source": """
          ZonedDateTime date =  doc['@timestamp'].value; 
          return date.getHour(); 
        """
      }
    }
  },
  "avg_month_of_year": { 
    "avg":{
      "script": { 
        "source": """
          ZonedDateTime date =  doc['@timestamp'].value; 
          return date.getMonthValue(); 
        """
      }
    }
  },
 ...
}
```

&emsp;&emsp;第2行，aggregation的名字

&emsp;&emsp;第4行，包含了一个painless脚本，该脚本将返回小时

&emsp;&emsp;第6行，基于文档中的timestamp字段设置一个`date`

&emsp;&emsp;第7行，从`date`中返回小时

&emsp;&emsp;第12行，aggregation的名字

&emsp;&emsp;第14行，包含了一个painless脚本，该脚本将返回月份

&emsp;&emsp;第16行，基于文档中的timestamp字段设置一个`date`

&emsp;&emsp;第17行，从`date`中返回月份


##### Getting duration by using bucket script

&emsp;&emsp;这个例子展示的是如何从一个数据日志中通过使用[bucket script](#####Bucket script aggregation)并根据client ip进行分组获取session的持续时间。这个例子使用了 Kibana中的样例网页日志集。

```text
PUT _transform/data_log
{
  "source": {
    "index": "kibana_sample_data_logs"
  },
  "dest": {
    "index": "data-logs-by-client"
  },
  "pivot": {
    "group_by": {
      "machine.os": {"terms": {"field": "machine.os.keyword"}},
      "machine.ip": {"terms": {"field": "clientip"}}
    },
    "aggregations": {
      "time_frame.lte": {
        "max": {
          "field": "timestamp"
        }
      },
      "time_frame.gte": {
        "min": {
          "field": "timestamp"
        }
      },
      "time_length": { 
        "bucket_script": {
          "buckets_path": { 
            "min": "time_frame.gte.value",
            "max": "time_frame.lte.value"
          },
          "script": "params.max - params.min" 
        }
      }
    }
  }
}
```

&emsp;&emsp;第25行，为了定义session的时长，我们使用 bucket script

&emsp;&emsp;第27行， bucket script是脚本变量的一个映射（map）并且关联的路径是你在脚本中会使用的变量。在这个例子中，`min`跟`max`这两个变量跟`time_frame.gte.value`和`time_frame.lte.value`形成映射关系

&emsp;&emsp;第31行，最终脚本对session的开始跟结束时间执行减法操作并返回了session的持续时间

##### Counting HTTP responses by using scripted metric aggregation

&emsp;&emsp;你可以在transform中使用scripted metric aggregation来计算网页日志数据中不同的HTTP响应类型。你也可以使用 filter aggregations来达到类似的功能，见[Finding suspicious client IPs](#####Finding suspicious client IPs)了解更多详细的例子。

&emsp;&emsp;下面的例子中假设HTTP响应码是文档中keyword类型的`response`域的域值。

```text
"aggregations": { 
  "responses.counts": { 
    "scripted_metric": { 
      "init_script": "state.responses = ['error':0L,'success':0L,'other':0L]", 
      "map_script": """ 
        def code = doc['response.keyword'].value;
        if (code.startsWith('5') || code.startsWith('4')) {
          state.responses.error += 1 ;
        } else if(code.startsWith('2')) {
          state.responses.success += 1;
        } else {
          state.responses.other += 1;
        }
        """,
      "combine_script": "state.responses", 
      "reduce_script": """ 
        def counts = ['error': 0L, 'success': 0L, 'other': 0L];
        for (responses in states) {
          counts.error += responses['error'];
          counts.success += responses['success'];
          counts.other += responses['other'];
        }
        return counts;
        """
      }
    },
  ...
}
```

&emsp;&emsp;第1行，这个transform的`aggregations`对象包含了所有的aggregation

&emsp;&emsp;第2行，`scripted_metric`  aggregation对象

&emsp;&emsp;第3行，`scripted_metric`在网页日志数据上执行了分布式操作来统计指定的HTTP响应码类型（error，success，其他）

&emsp;&emsp;第4行，`init_scriptc`在`state`对象中创建了带有三个long类型属性（`error`, `success`, `other`）的`responses`数组。

&emsp;&emsp;第5行，`map_script`基于文档中的`response.keyword`定义了`code`。然后基于response中的第一个数字来统计errors, successes, 和其他responses类型

&emsp;&emsp;第15行，`combine_script`从每一个shard中返回`state.responses`

&emsp;&emsp;第16行，`reduce_script`创建了一个`counts`数组，带有`error`,，`success`，`other`三个属性。然后遍历从每一个shard返回的`response`，将不同的相应类型分配给`counts`的对应属性。error responses to the error counts, success responses to the success counts, and other responses to the other counts。最终返回`counts`数组。

##### Comparing indices by using scripted metric aggregations

&emsp;&emsp;这个例子展示的是如何在transform中使用一个scripted metric aggregation来比较两个索引的内容

```text
POST _transform/_preview
{
  "id" : "index_compare",
  "source" : { 
    "index" : [
      "index1",
      "index2"
    ],
    "query" : {
      "match_all" : { }
    }
  },
  "dest" : { 
    "index" : "compare"
  },
  "pivot" : {
    "group_by" : {
      "unique-id" : {
        "terms" : {
          "field" : "<unique-id-field>" 
        }
      }
    },
    "aggregations" : {
      "compare" : { 
        "scripted_metric" : {
          "map_script" : "state.doc = new HashMap(params['_source'])", 
          "combine_script" : "return state", 
          "reduce_script" : """ 
            if (states.size() != 2) {
              return "count_mismatch"
            }
            if (states.get(0).equals(states.get(1))) {
              return "match"
            } else {
              return "mismatch"
            }
            """
        }
      }
    }
  }
}
```

&emsp;&emsp;第4行，在`source`对象中引用的索引，他们将会进行相互比较

&emsp;&emsp;第13行，`dest`索引中包含了比较的结果

&emsp;&emsp;第20行，`group_by`字段必须是文档中的唯一键

&emsp;&emsp;第25行，`scripted_metric` aggregation对象

&emsp;&emsp;第27行，`map_script`在`state`对象中定义了一个`doc`，通过`new HashMap(...)`拷贝了原文档，这个操作对于将完整的source object从一个阶段传递给下一个阶段是非常重要的

&emsp;&emsp;第28行，`combine_script`从每一个分片中返回`state`

&emsp;&emsp;第29行，`reduce_script`检查索引的大小是否一样，如果不一样，那么返回`count_mismatch`。然后遍历两个索引的所有值，如果值相等，返回`match`，否则返回`mismatch`

##### Getting web session details by using scripted metric aggregation

&emsp;&emsp;这个例子展示的是如何从一个transform中衍生（derive）出多个功能。让我们先看下原数据：

```text
{
  "_index":"apache-sessions",
  "_type":"_doc",
  "_id":"KvzSeGoB4bgw0KGbE3wP",
  "_score":1.0,
  "_source":{
    "@timestamp":1484053499256,
    "apache":{
      "access":{
        "sessionid":"571604f2b2b0c7b346dc685eeb0e2306774a63c2",
        "url":"http://www.leroymerlin.fr/v3/search/search.do?keyword=Carrelage%20salle%20de%20bain",
        "path":"/v3/search/search.do",
        "query":"keyword=Carrelage%20salle%20de%20bain",
        "referrer":"http://www.leroymerlin.fr/v3/p/produits/carrelage-parquet-sol-souple/carrelage-sol-et-mur/decor-listel-et-accessoires-carrelage-mural-l1308217717?resultOffset=0&resultLimit=51&resultListShape=MOSAIC&priceStyle=SALEUNIT_PRICE",
        "user_agent":{
          "original":"Mobile Safari 10.0 Mac OS X (iPad) Apple Inc.",
          "os_name":"Mac OS X (iPad)"
        },
        "remote_ip":"0337b1fa-5ed4-af81-9ef4-0ec53be0f45d",
        "geoip":{
          "country_iso_code":"FR",
          "location":{
            "lat":48.86,
            "lon":2.35
          }
        },
        "response_code":200,
        "method":"GET"
      }
    }
  }
}
```

&emsp;&emsp;通过使用`sessionid`域对数据进行分组，你可以通过session列举事件并且使用scripted metric aggregation获得session的更多详细内容。

```text
POST _transform/_preview
{
  "source": {
    "index": "apache-sessions"
  },
  "pivot": {
    "group_by": {
      "sessionid": { 
        "terms": {
          "field": "apache.access.sessionid"
        }
      }
    },
    "aggregations": { 
      "distinct_paths": {
        "cardinality": {
          "field": "apache.access.path"
        }
      },
      "num_pages_viewed": {
        "value_count": {
          "field": "apache.access.url"
        }
      },
      "session_details": {
        "scripted_metric": {
          "init_script": "state.docs = []", 
          "map_script": """ 
            Map span = [
              '@timestamp':doc['@timestamp'].value,
              'url':doc['apache.access.url'].value,
              'referrer':doc['apache.access.referrer'].value
            ];
            state.docs.add(span)
          """,
          "combine_script": "return state.docs;", 
          "reduce_script": """ 
            def all_docs = [];
            for (s in states) {
              for (span in s) {
                all_docs.add(span);
              }
            }
            all_docs.sort((HashMap o1, HashMap o2)->o1['@timestamp'].toEpochMilli()compareTo(o2['@timestamp']-toEpochMilli()));
            def size = all_docs.size();
            def min_time = all_docs[0]['@timestamp'];
            def max_time = all_docs[size-1]['@timestamp'];
            def duration = max_time.toEpochMilli() - min_time.toEpochMilli();
            def entry_page = all_docs[0]['url'];
            def exit_path = all_docs[size-1]['url'];
            def first_referrer = all_docs[0]['referrer'];
            def ret = new HashMap();
            ret['first_time'] = min_time;
            ret['last_time'] = max_time;
            ret['duration'] = duration;
            ret['entry_page'] = entry_page;
            ret['exit_path'] = exit_path;
            ret['first_referrer'] = first_referrer;
            return ret;
          """
        }
      }
    }
  }
}
```

&emsp;&emsp;第8行，使用`sessionid`对数据进行分组

&emsp;&emsp;第14行，aggregation统计了每一个session中`apache.access.path`的种类以及`apache.access.url`的数量。

&emsp;&emsp;第27行，`init_script`在`state`对象中创建了数组类型的`doc`

&emsp;&emsp;第28行，`map_script`使用文档中的timestamp定义了一个`span`数组，以及根据文档中对应的字段定义了`referrer`、`url`。然后将`span`数组的值添加到`doc`对象中。

&emsp;&emsp;第36行，`combine_script`从每一个分片中返回`state.docs`

&emsp;&emsp;第37行，`reduce_script`定义的多个对象例如`min_time`，`max_time`，`duration`都是基于文档中的字段。然后声明一个`ret` 对象，使用`new HashMap ()`将source document拷贝到这个对象中。接着脚本在`ret`中定义了`first_time`，`last_time`，`duration`，这些字段的值基于脚本中的之前的一些值，最终返回`ret`

&emsp;&emsp;最终返回的结果大致如下：

```text
{
  "num_pages_viewed" : 2.0,
  "session_details" : {
    "duration" : 100300001,
    "first_referrer" : "https://www.bing.com/",
    "entry_page" : "http://www.leroymerlin.fr/v3/p/produits/materiaux-menuiserie/porte-coulissante-porte-interieure-escalier-et-rambarde/barriere-de-securite-l1308218463",
    "first_time" : "2017-01-10T21:22:52.982Z",
    "last_time" : "2017-01-10T21:25:04.356Z",
    "exit_path" : "http://www.leroymerlin.fr/v3/p/produits/materiaux-menuiserie/porte-coulissante-porte-interieure-escalier-et-rambarde/barriere-de-securite-l1308218463?__result-wrapper?pageTemplate=Famille%2FMat%C3%A9riaux+et+menuiserie&resultOffset=0&resultLimit=50&resultListShape=PLAIN&nomenclatureId=17942&priceStyle=SALEUNIT_PRICE&fcr=1&*4294718806=4294718806&*14072=14072&*4294718593=4294718593&*17942=17942"
  },
  "distinct_paths" : 1.0,
  "sessionid" : "000046f8154a80fd89849369c984b8cc9d795814"
},
{
  "num_pages_viewed" : 10.0,
  "session_details" : {
    "duration" : 343100405,
    "first_referrer" : "https://www.google.fr/",
    "entry_page" : "http://www.leroymerlin.fr/",
    "first_time" : "2017-01-10T16:57:39.937Z",
    "last_time" : "2017-01-10T17:03:23.049Z",
    "exit_path" : "http://www.leroymerlin.fr/v3/p/produits/porte-de-douche-coulissante-adena-e168578"
  },
  "distinct_paths" : 8.0,
  "sessionid" : "000087e825da1d87a332b8f15fa76116c7467da6"
}
```

#### Troubleshooting transforms
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/transform-troubleshooting.html)

&emsp;&emsp;使用这个章节中的信息来解决一些常用的问题。

&emsp;&emsp;For issues that you cannot fix yourself … we’re here to help。如果你是有support contract的现有用户，可以在[Elastic Support portal](https://support.elastic.co/customers/s/login/)中创建一个ticket，或者在[Elastic forum](https://discuss.elastic.co/)发帖。

&emsp;&emsp;如果你遇到了transform的问题，你可以从下面的文件和API中获取更多的信息。

- 在`.transform-notifications-read`存储了轻量级的审计信息，基于`transform_id`进行查询。
- [get transform statistics API](####Get transform statistics API)提供了transform状态和失败的信息
- 如果transform作为一个任务存在，你可以使用[task management API](####Task management API)来收集任务信息（task information）。例如`GET _tasks?actions=data_frame/transforms*&detailed`。通常来说，在transform启动后或者处于失败状态时会存在这个任务。
- 正在运行transform的节点上的Elasticsearch日志可能也含有有用的信息。你可以从通知消息中确认是哪个节点。或者如果task存在，你可以通过`get transform statistics API`获取信息。更多信息见[Logging](####Logging)。

#### Transform limitations
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/transform-limitations.html)

&emsp;&emsp;下文中的局限性和已知问题适用于8.2.3版本的Elastic transform功能。局限性按下面几种进行分类：

- [Configuration limitations](#####Configuration limitations)适用于transform的配置过程
- [Operational limitations](#####Operational limitations)适用于影响运行中的transform的行为
- [Limitations in Kibana](#####Limitations in Kibana)适用于通过用户接口（user interface）配置的transform

##### Configuration limitations

###### Field names prefixed with underscores are omitted from latest transforms

&emsp;&emsp;如果你使用了`latest`类型的transform并且source index中有已下划线开头的域名，它们会被认为是内部域（internal fileds）。在destination index中这些域会被omit。

###### Transforms support cross-cluster search if the remote cluster is configured properly

&emsp;&emsp;如果你使用[cross-cluster search](###Search across clusters)，远端的集群（remote cluster）必须支持你transform中的查询和聚合。Transform会验证他们的配置。如果你使用了cross-cluster search并且验证失败，确保远端的集群支持你使用的查询和聚合。

###### Using scripts in transforms

 只要是聚合中支持的脚本，那么在任意case下，在transform中使用也是支持的。然而在transform中使用脚本需要考虑下面的一些因素（factor）：

- 当域是由脚本生成的时，Transform不能推测其index mapping。在这种情况下，你可以在创建 transform前先在destination index中创建好索引
- Scripted fields可能会增加transform的运行时间
- 当你使用脚本中定义的`group_by`进行分组时，transform无法为此query进行优化，如果在脚本中使用了，会收到警告消息（warning message）

###### Deprecation warnings for Painless scripts in transforms

&emsp;&emsp;如果transform使用了Painless脚本，并且使用弃用的语法（deprecated syntax），transform的预览或启动时不会显示warning。然而 由于运行中的查询可能是资源密集型过程，所以不可能在转换期间为了弃用警告进行检查。因此任何因为使用了弃用的语法而引起的弃用警告都不会在Upgrade Assistant中出现。

###### Transforms perform better on indexed fields

&emsp;&emsp;transform使用用户定义的时间域（time filed）进行排序，这个域会被经常访问。如果时间域是一个[runtime filed](###Runtime fields)，在查询期间对于计算域值的性能影响会显著的降低transform。在使用transform时使用一个indexed field作为时间域。

###### Continuous transform scheduling limitations

&emsp;&emsp;continuous transform会周期的检查source data是否发生变化。这个定时功能目前局限于一个basic periodic timer，`frequency`可以是1s到1h范围的值。默认值是1m。它被设计为run litter and often。当基于你的提取率（ingest rate）为计时器选择了一个`frequency`时，同时也要考虑在集群中使用transform search/index操作对其他用户的影响。Also note that retries occur at `frequency ` interval。

##### Operational limitations

###### Aggregation responses may be incompatible with destination index mappings

&emsp;&emsp;当pivot transform首先启动后，它会为destination index推测出mapping。这种处理基于source index中的域的类型以及使用的aggregation。如果域是从[scripted_metrics](####Scripted metric aggregation)或者[bucket_scripts](####Bucket script aggregation)中衍生（derive）出来的，会使用[dynamic mappings](###Dynamic mapping)。在有些情况下（in some instances），推测出来的mapping（deduced mapping）可能跟真正的数据不兼容。比如说可能会发生numeric overflow或者动态映射出的字段同时包含numbers和strings。如果你觉得发生了上述的问题，可以检查Elasticsearch的日志。

&emsp;&emsp;你可以使用[preview transform API预览推测出的mapping。见API response中`generated_dest_index`对象。

&emsp;&emsp;如果有必要的话，你可以在transform启动前，使用[create index API](####Create index API)先创建一个自定义的destination index使得可以自定义mapping。由于推测的mapping不能被index template overwrite，所以就使用create index API来自定义mapping。index template只能作用于（apply to）脚本中使用dynamic mapping衍生出的域。

###### Batch transforms may not account for changed documents

&emsp;&emsp;Batch transform使用了[composite aggregation](####Composite aggregation)使得所有的分桶可以有高效的分页（efficient pagination）。composite aggregation不支持search context，因此如果source data发生了变更（删除，更新，添加）并且同时Batch data frame正在处理中，那么结果中可能不会包括这些变更。

###### Continuous transform consistency does not account for deleted or updated documents

&emsp;&emsp;尽管在提取（ingest）到new data后，transform中的处理允许重新转化（transform）计算，但它同样有一些局限。

&emsp;&emsp;发生变更的entities只有在它们的时间域同时发生变化并且处于更改检查的操作范围内时才能被识别到（identified）。这是因为从设计原则上来讲，是根据new data提供的时间戳进行提取的。

&emsp;&emsp;如果满足source index pattern的索引被删除了，例如当删除了历史的基于时间的索引（historical time-based），那么composite aggregation在持续的checkpoint处理中会搜索到不同的source data，entities只存在于被删除的索引中，并且不会从destination index中移除。

&emsp;&emsp;取决于你的使用用例，你可能希望在有删除（deletion）后重新创建transform。或者你的使用用例能容忍historical archiving，你可能希望在你的聚合中包含一个max ingest timestamp。This will allow you to exclude results that have not been recently updated when viewing the destination index。

###### Deleting a transform does not delete the destination index or Kibana index pattern

&emsp;&emsp;当使用`DELETE _transform/index`删除一个transform时，destination index和Kibana index pattern都不会删除，这些都需要各自进行删除。

###### Handling dynamic adjustment of aggregation page size

&emsp;&emsp;在开发transform时，更倾向于control而不是性能。在设计方面的考虑中，更愿意让transform在后台花费更长的时间进行处理而不是快速完成并且优先资源消费（take precedence in resource consumption）。

&emsp;&emsp;Composite aggregations are well suited for high cardinality data enabling pagination through results。当composite aggregation查询时发生了[circuit breaker](####Circuit breaker settings)的内存异常时，我们会降低请求中分桶的数量并重试。这个circuit breaker是基于集群中的所有活动（activity）计算的，而不仅仅是来自transform的活动，因此它可能只是一个临时的资源可用性问题（resource availability issue）。

&emsp;&emsp;For a batch transform, the number of buckets requested is only ever adjusted downwards. The lowering of value may result in a longer duration for the transform checkpoint to complete. For continuous transforms, the number of buckets requested is reset back to its default at the start of every checkpoint and it is possible for circuit breaker exceptions to occur repeatedly in the Elasticsearch logs.

&emsp;&emsp;The transform retrieves data in batches which means it calculates several buckets at once. Per default this is 500 buckets per search/index operation. The default can be changed using max_page_search_size and the minimum value is 10. If failures still occur once the number of buckets requested has been reduced to its minimum, then the transform will be set to a failed state.

###### Handling dynamic adjustments for many terms

&emsp;&emsp;对于每个checkpoint，会检查entities自上一次检查后发生的变化。发生变更的entities作为一个[terms query](####Terms query)提供给transform的composite aggregation，one page at a time。然后更新被应用到destination index中。

&emsp;&emsp;`max_page_search_size`定义了页的`size`，它同样用于定义composite aggregation查询返回的分桶数量。默认值是500，最小值是10.

&emsp;&emsp;index setting中的`index.max_terms_count`定义了在一个terms query中term的最大数量。默认值是65536。如果`max_page_search_size`超过了`index.max_terms_count`，transform将失败。

&emsp;&emsp;使用更小的`max_page_search_size`的值会导致transform的checkpoint完成的时间花费更长。

###### Handling of failed transforms

&emsp;&emsp;失败的transform仍然会作为一个persistent task并且应该要做适当的处理。要么删除它或者在解决导致失败的原因后重启。

&emsp;&emsp;当使用API删除一个失败的transform时，先使用`_stop?force=true`停止transform，然后再删除它。

###### Continuous transforms may give incorrect results if documents are not yet available to search

&emsp;&emsp;一篇文档在被索引后，在它对搜索可见前有一段很小的延迟。

&emsp;&emsp;一个continuous transform 会周期性的检查entities是否发生变化，其检查的时间范围是上一次检查的时间到 （`now` 减去 `sync.time.delay`）。时间窗口的移动不会有重叠的。如果一个最近索引的文档的时间戳在检查时间窗口内，但是这个文档对搜索不可见，那么这个entity不会被更新。

&emsp;&emsp;如果使用了一个`sync.time.field` 域来表示数据的提取时间（data ingest time）并且使用了一个 zero second或者一个非常小的`sync.time.delay`。那么更有可能会发生这个问题。

###### Support for date nanoseconds data type

&emsp;&emsp;如果你的数据使用了[date nanosecond data type](####Date nanoseconds field type)，aggregation仍然是millionsecond的粒度。这个局限同样会影响transform中的聚合。

###### Data streams as destination indices are not supported

&emsp;&emsp;transform会在destination index中更新数据，要求能够往destination index写入。[Data Streams](##Data Streams)被设计为append-only，意味着你不能直接往data stream中发送更新或者删除请求。因为这个原因，data streams不支持作为transform的destination index。

###### ILM as destination index may cause duplicated documents

&emsp;&emsp;不建议[ILM](###ILM: Manage the index lifecycle)用于作为transform 的destination index。Transform在当前destination index中更新文档，不能删除ILM之前使用过的索引中的文档。在rollover中，使用transform结合ILM可能会导致重复的文档。

&emsp;&emsp;如果你使用ILM来获得time-based索引，请考虑使用[Date index name](####Date index name processor)。如果你的transform包含基于`date_hisotram`的`group_by`，这个处理工作不会有重复的文档。

##### Limitations in Kibana

###### Transforms are visible in all Kibana spaces

&emsp;&emsp;[Spaces](https://www.elastic.co/guide/en/kibana/8.2/xpack-spaces.html)允许你组织你的source、destination index 以及Kibana中其他保存的对象（saved object）并且只能看到属于你的空间的objects。然而transform是一个长期运行的任务并且是在集群层进行管理的，因此不会将其可见范围限制到某些空间（certain spaces）。Space awareness can be implemented for a data view under **Stack Management > Kibana** which allows privileges to the transform destination index。

###### Up to 1,000 transforms are listed in Kibana

&emsp;&emsp;transform的管理页面最多只能显示1000个transform。

###### Kibana might not support every transform configuration option

&emsp;&emsp;可能有些配置选项在transform API中可见但是在Kibana中不支持。参考[documentation](###Transform APIs)浏览所有的可配置的选项。


## Set up a cluster for high availability
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/high-availability.html)

&emsp;&emsp;你的数据对你来说很重要。让数据保持安全以及可见性对Elasticsearch来说很重要。有时候你的集群可能会经历硬件错误或者断电。为了帮助你对付这类事件，Elasticsearch提供了一些功能实现高可用。

- 通过适当的规划，可以对一个集群[design for resilience](###Designing for resilience)以应对许多通常出错的事情，从单个节点或网络连接的丢失到区域范围的中断（outage），比如断电。
- 你可以使用[cross-cluster replication](###Cross-cluster replication)让远程的follower cluster拥有副本数据（replicate data）。follower cluster可能是一个不同的数据中心或者相对于leader cluster在不同的陆地上（continent）。follower cluster一方面扮演热备（hot standby）的角色，时刻为leader cluster发生灾难事件时做好故障转移（fail over），另一个方面也扮演了geo-replica来为附近的客户服务。
- 对付数据丢失的最后防线就是对你的集群[take regular snapshots](###Create a snapshot
) ，这样你可以在任何地方有需要时恢复完整的数据。

### Designing for resilience
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/high-availability-cluster-design.html)

&emsp;&emsp;分布式系统比如Elasticsearch都会设计为即使它们中的一些组件失效了也能工作。只要有足够多连接正常的节点能担负起它们的任务职责，Elasticsearch集群仍能正常的操作，即使一些节点不可用或者丢失连接。

&emsp;&emsp;再小的具有弹性的集群也要有以下的限制。所有的Elasticsearch集群要求：

- 一个[elected master node](####Quorum-based decision making)节点
- 每个[role](####Node)至少有一个节点
- 每一个[shard](###Scalability and resilience: clusters, nodes, and shards)至少要有一个副本

&emsp;&emsp;弹性集群需要为每个必需的集群组件提供冗余。这意味着弹性集群要求：

- 至少有三个具有master资格的节点
- 每一个角色至少有两个节点
- 每一个分片至少有2个拷贝（一个主分片和一个或多个副本，除非索引是[searchable snapshot index](##Searchable snapshots)）

&emsp;&emsp;一个有弹性的集群需要三个具有master节点的资格，这样如果其中一个节点失败，那么剩下的两个仍然形成多数（form a majority），并且可以成功举行选举。

&emsp;&emsp;同样的，每一个节点角色的冗余意味着如果某个有特定角色的节点发生故障后，其他的节点能承担起对应的职责。

&emsp;&emsp;最终，一个弹性的集群中至少包每一个分片有两个拷贝。如果一个拷贝发生故障，另一个拷贝能接管。在发生故障后，Elasticsearch会自动的在剩余的节点上重建分片拷贝来恢复集群的健康。

&emsp;&emsp;故障会暂时减少集群的总容量。 此外，在发生故障后，集群必须执行额外的后台活动才能将自身恢复到健康状态。 即使某些节点发生故障，你也应该确保您的集群有能力处理您的工作负载。

&emsp;&emsp;基于你的需求以及预算，一个Elasticsearch集群可以由一个或者上百个或者这个范围内的节点组成。当你设计一个较小的集群时，你通过应该集中考虑单节点故障的问题。对于较大集群的设计，需要考虑同一个时间多个节点发生故障的问题。下面的链接介绍了在不同规模的集群上构建弹性（build resilience）的一些建议：

- [Resilience in small clusters](####Resilience in small clusters)
- [Resilience in larger clusters](####Resilience in larger clusters)

#### Resilience in small clusters
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/high-availability-cluster-small-clusters.html)

&emsp;&emsp;在较小的集群上，最重要的是单节点故障对应的弹性。这个章节给出了一些指导：对于单节点故障如何尽可能保证集群的弹性。

##### One-node clusters

&emsp;&emsp;如果你的集群由一个节点组成，这个节点必须做所有的事情。对这种场景的推荐是，Elasticsearch默认将所有的角色给这个节点。

&emsp;&emsp;单个节点的集群是不具备弹性的。如果这个节点发生了故障，集群将停止工作。因为在这个单节点的集群中没有replica，你无法存储冗余数据。然而默认情况下`green` [cluster health](####Cluster health API)状态至少需要一个replica。为了你的集群能显示一个`green`状态，可以对每一个索引通过[index.number_of_replicas](#####index.number_of_replicas)设置为`0`。

&emsp;&emsp;如果节点发生故障，你可能需要从[snapshot](###Snapshot module)中恢复会丢失索引数据的较老的拷贝。

&emsp;&emsp;由于对于任何的故障不具备弹性，我们不建议在生产中使用单节点的集群。

##### Two-node clusters

&emsp;&emsp;如果你有两个节点，我们建议这两个节点都是数据节点。你应该保证在两个节点上都保留着冗余数据，通过在每一个索引上设置[index.number_of_replicas](#####index.number_of_replicas)为`1`，这些索引不是一个[ searchable snapshot index](##Searchable snapshots)，以上都是默认的行为但是可以通过[index template](##Index templates
)覆盖。[Auto-expand relicas](#####index.auto_expand_replicas)能达到同样的目的，但是不建议在这种规模的集群上使用这个功能。

&emsp;&emsp;我们建议在这两个节点的其中一个节点上设置`node.master: false`使得这个节点不是[master-eligible](##### Master-eligible node)。这样你可以知道哪个节点会被选为集群的master节点。这个集群容忍不是master-eligible的节点的丢失。如果你不在其中一个节点上设置`node.master:false`，两个节点都是master-eligible。这意味着这两个节点要求master的选举。由于如果任一节点不可用，选举将失败，因此你的集群无法可靠地容忍任一节点的丢失。

&emsp;&emsp;默认情况下，每一个节点会有一个角色。我们建议你除了master-eligible外给这两个节点赋予所有的角色。如果一个节点发生了故障，另一个节点可以接管任务。

&emsp;&emsp;你应该避免把客户请求只发送给其中一个节点。如果你这么做了并且这个节点发生了故障，这个请求将不会收到响应即使剩余的节点本身是一个健康的集群。理想情况下你应该在这两个节点间做好请求的平衡。一个好方法是在客户端配置所有节点的地址，这样你可以弹性的负载均衡的请求集群中的节点。

&emsp;&emsp;由于对于任何的故障不具备弹性，我们不建议在生产中使用只有两个节点的集群。

##### Two-node clusters with a tiebreaker

&emsp;&emsp;因为master节点的选举基于多数（majority-based）。上文中的两个节点的集群容忍某一个节点可以发生故障而不是另一个。你不可能配置一个两个节点的集群，并且可以容忍任意一个节点可以发生故障，因为理论上是不可能。你可能期望任意一个节点发生故障后，Elasticsearch可以选出剩余的节点作为master，因为没法区分出远程节点发生故障还是仅仅发生了连接问题。如果两个节点都可以进行各自的选举，连接的丢失会导致[split-brain](https://en.wikipedia.org/wiki/Split-brain_(computing))问题从而丢失数据。Elasticsearch会避免这种情况并且通过不让任何节点作为master节点来保护你的数据，直到节点保证拥有最新的集群状态并且集群中没有其他的master节点。这种方式会导致集群中没有master节点直到恢复连接。

&emsp;&emsp;你可以通过添加第三个节点并且让这个三个节点都是master-eligible来解决这个问题。一次[master election](####Quorum-based decision making)只要求三个master-eligible中的两个节点。这意味着集群可以容忍任意单个节点的丢失。原先的两个节点相互之间丢失连接后，这个第三个节点扮演了一个tiebreaker。你可以将这个额外的节点作为一个 [dedicated voting-only master-eligible node](######Voting-only master-eligible node)来降低这个节点的资源配置，即dedicated tiebreaker。因为这个节点没有其他的角色，一个dedicated tiebreaker 节点不需要像其他两个节点一样的powerful。这个节点不会用于执行任何搜索或者客户端的请求，并且不能选举为集群的master节点。

&emsp;&emsp;原先的两个节点不能是[voting-only master-eligible nodes](######Voting-only master-eligible node)因为一个弹性的集群至少需要3个master-eligible 节点，至少有两个节点不能是 voting-only master-eligible nodes。如果三个节点中的两个是 voting-only master-eligible nodes那么选出的master节点必须是第三个节点，但这个节点会成为单点故障（single point of failure）。

&emsp;&emsp;我们建议给两个non-tiebreaker的节点赋予所有的角色。通过创建冗余信息保证集群中的任何任务都有节点接管。

&emsp;&emsp;你应该避免把客户请求发送到dedicated tiebreaker节点。你也应该避免把客户请求只发送给其他两个节点中的一个节点。如果你这么做了并且这个节点发生了故障，这个请求将不会收到响应，及时剩余的节点本身是一个健康的集群。理想情况下你应该在这两个节点间做好请求的平衡。一个好方法是在客户端配置所有节点的地址，这样你可以弹性的负载均衡的请求集群中的节点。[Elastic Cloud](https://www.elastic.co/cloud/elasticsearch-service/signup?baymax=docs-body&elektra=docs) 服务提供这种负载均衡。

&emsp;&emsp;两个节点加上一个额外的tiebreaker节点的集群是可以在生产中使用的最小的集群。

##### Three-node clusters

&emsp;&emsp;如果你有三个节点，我们建议这三个节点都是[data nodes](#####Data node)，不是[ searchable snapshot index](##Searchable snapshots)的索引都应该至少有一个replica。默认情况下节点是data node。你可以让一些索引拥有两个replica，这样每一个节点都有一个分片的拷贝。你应该将每一个节点配置为[master-eligible](#####Master-eligible node)，这样任意两个节点都可以选择出一个master并且不需要第三个节点的参与。默认情况下所有的节点都是master-eligible。在丢失单个节点的情况这个集群仍然是弹性的。

&emsp;&emsp;你应该避免把客户请求只发送给其中一个节点。如果你这么做了并且这个节点发生了故障，这个请求将不会收到响应即使剩余的节点本身是一个健康的集群。理想情况下你应该在这两个节点间做好请求的平衡。一个好方法是在客户端配置所有节点的地址，这样你可以弹性的负载均衡的请求集群中的节点。[Elastic Cloud](https://www.elastic.co/cloud/elasticsearch-service/signup?baymax=docs-body&elektra=docs) 服务提供这种负载均衡。

##### Clusters with more than three nodes

&emsp;&emsp;一旦你的集群增长超过3个节点，你可以指定节点给予特定的责任，根据需要独立的衡量节点需要的资源。你可以有许多例如 [data nodes](#####Data node), [ingest nodes](##Ingest pipelines), [machine learning nodes](#####Machine learning node)等等的节点。根据你的需求来支持你的工作量。当你的集群增长到更大后，我们建议你为每一个角色指定专用的节点。这样可以为每一个任务来独立的衡量对应的资源。

&emsp;&emsp;然而，一个好的实践就是限制master-eligible的节点数量到三个。Master节点不需要像其他类型的节点一样去衡量，因为总是从他们三个中选取一个作为master节点。如果有太多的master-eligible节点，那么完成master的选举需要较长的时间才能完成。在大型的集群中，我们建议你配置一些节点为dedicated master-eligible节点，并且避免往这些专用的节点中发送请求。如果master-eligible节点因为额外的工作可能会导致你的集群可能变得不稳定，这些额外的工作可以交给其他节点处理。

&emsp;&emsp;你可以将master-eligible节点中的一个节点为[voting-only node](######Voting-only master-eligible node)，这样它永远不会被选为master节点。比如说你有两个专用的master 节点以及一个第三个节点，这个节点同时是data note以及voting-only master-eligible节点。第三个节点将会用于在master选举中扮演tiebreaker并且自身不会成为master。

##### Summary

&emsp;&emsp;只要满足下面的条件，集群在丢失任何节点后依然是弹性的：

- [cluster health status](####Cluster health API)是`green`
- 至少有两个数据节点
- 每一个不是[searchable snapshot index](##Searchable snapshots)的索引至少每一个分片都有一个replica，in addition to the primary
- 集群中至少有三个master-eligible节点，只要这些节点中至少有两个不是voting-only master-eligible nodes
- 客户端配置为将其请求发送到多个节点，或者配置为使用负载均衡器在一组适当的节点之间平衡请求，[Elastic Cloud](https://www.elastic.co/cloud/elasticsearch-service/signup?baymax=docs-body&elektra=docs) 服务提供这种负载均衡

#### Resilience in larger clusters
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/high-availability-cluster-design-large-clusters.html)

&emsp;&emsp;节点间共享一些基础设施（infrastructure），比如网络互联（network interconnect）、电源供应（power supply）并不罕见。如果是这样的话，你应该计划好为这些基础设施发生故障做好准备并且保证发生故障后不会影响太多的节点。常见的实践是将共享一些基础设施的节点分组为不同的区域（zone）并且为整个zone发生故障做好计划。

&emsp;&emsp;Elasticsearch期望节点之间的连接是可靠的、低延迟以及有足够的带宽。许多Elasticsearch任务要求在节点间多次往返（multiple round-trips）。缓慢或者不可靠的互联（interconnect）对集群的性能和稳定有着很明显的影响。

&emsp;&emsp;比如说，每次往返增加几毫秒的延迟会迅速累积成明显的性能损失（performance penalty）。一个不可靠的网络可能会有频繁的网络分区（network partition）。Elasticsearch 将尽快从网络分区自动恢复，但你的集群可能在分区期间部分不可用，并且需要花费时间和资源来重新同步任何丢失的数据并在分区恢复后重新平衡自身。从故障中恢复可能会设计节点之间拷贝大量的数据，所以恢复的时间往往取决于可用的带宽。

&emsp;&emsp;如果将你的集群按照区域（zone）进行划分，区域内（within）的节点网络通常比区域间（between）的更高。保证区域间的网络连接质量足够的高。将所有的区域放在一个数据中心（data center）并且每一个区域有自己独立的电源供应和其他基础设置是最好的方式。你也可以把集群扩张（stretch）到附近的数据中心，只要数据中心间的网络互联足够好。

&emsp;&emsp;没法指定一个健康运行的Elasticsearch所需要的最低网络性能。理论上，即使节点间的往返延迟有上百毫秒的延迟，集群也能正常工作。在实践中，缓慢的网络会导致很差的集群性能。另外缓慢的网络通常是不可靠的并且会发生网络分区导致间断性的不可用。

&emsp;&emsp;如果多个数据中心间相隔很远（further apart）或者连接不好并且还要希望数据可见，可以在每一个数据中心内部署一个额外的集群，使用 [cross-cluster search](###Search across clusters)或者 [cross-cluster replication](###Cross-cluster replication)来连接集群。这些功能使得，相比较集群内，即使集群间有更低的可靠性以及性能也能较好的运行。

&emsp;&emsp;在丢失了一个区域内所有的节点后，一个设计合理的集群可能会正常运行（functional），只是大大的降低了capacity（significantly reduced capacity）。当出现这个故障后你需要提供（provision）额外的节点恢复到可接受的性能。

&emsp;&emsp;对于整个区域发生后故障的弹性问题，在一个或多个区域中有每一个分片的拷贝是很重要，可以通过将[data node](#####Data node)放置在多个区域中并且配置[shard allocation awareness](#####Shard allocation awareness) 实现弹性。你也应该保证客户端的请求会发送给一个或多个区域。

&emsp;&emsp;你需要考虑到所有的节点角色并且保证每一个角色冗余的分配到两个或多个区域中。例如，如果你使用[ingest pipelines](##Ingest pipelines)或者machine learning，你应该在两个或者多个区域中有ingest node或者machine learning node。然而master-eligible node的位置更需要关注因为一个弹性的集群需要三个master-eligible node中的两个才能工作正常。下面的章节展开介绍了多个区域中如何放置master-eligible node。

##### Two-zone clusters

&emsp;&emsp;如果你有两个区域，在每一个区域中应该有不同数量的master-eligible node，这样拥有更多节点的区域中将包含大多数（majority）并且在丢失其他区域后也能够survive。比如说如果你有三个master-eligible node，那么可以将所有的master-eligible node 放在一个区域中或者你把其中两个master-eligible node放到一个区域，第三个放到另一个区域中。你不能在每一个区域中放置相同数量的master-eligible node。每一个区域没有拥有它自己区域内的大多数。因此，在丢失任意一方的区域后，另一方的区域可能没法survive。

##### Two-zone clusters with a tiebreaker

&emsp;&emsp;因为master节点的选举基于多数（majority-based）。上文中的two-zones可以容忍某个某一个节点可以发生故障而不是另一个。你不可能配置一个two-zone cluster，并且可以容忍任意一个区域可以发生故障，因为理论上是不可能。你可能期望任意一个区域发生故障后，Elasticsearch可以从剩余的区域中选出一个节点作为master，因为没法区分出远程区域（remote zone）发生故障还是仅仅发生了连接问题。如果两个区域都可以进行各自的选举，连接的丢失会导致[split-brain](https://en.wikipedia.org/wiki/Split-brain_(computing))问题从而丢失数据。Elasticsearch会避免这种情况并且通过不让任意区域中的节点作为master节点来保护你的数据，直到节点保证拥有最新的集群状态并且集群中没有其他的master节点。这种方式会导致集群中没有master节点直到恢复连接。

&emsp;&emsp;你可以在每一个区域中添加一个master-eligible node并且在一个独立的第三个区域中额外单独添加一个master-eligible node。原先的两个区域相互之间丢失连接后，这个第三个节点扮演了一个tiebreaker。你可以将这个额外的节点作为一个 [dedicated voting-only master-eligible node](######Voting-only master-eligible node)，即dedicated tiebreaker。因为这个节点没有其他的角色，一个dedicated tiebreaker 节点不需要像其他两个节点一样的powerful。这个节点不会用于执行任何搜索或者客户端的请求，并且不能选举为集群的master节点。

&emsp;&emsp;你应该使用[shard allocation awareness](#####Shard allocation awareness) 来保证每个区域中的每一个分片都有一份拷贝。这意味着任意一个区域发生故障后所有的数据仍然是可见的。

&emsp;&emsp;所有的master-eligible node，包括voting-only node，相比较集群中的其他节点，它们需要相当快速的持久存储以及一个可靠的低延迟的网络连接，因为它们处于[publishing cluster state updates](####Publishing the cluster state)的关键路径（critical path）上。

##### Clusters with three or more zones

&emsp;&emsp;如果你有三个区域，那么你应该在每一个区域都放置一个 master-eligible node。如果你有超过三个区域，那么从中选出三个区域并且在这三个区域都分别放置一个 master-eligible node。这意味着其中一个区域发生故障后仍然可以选出一个master node。

&emsp;&emsp;你的索引应该至少有一个副本来防止节点发生故障，除非这些索引是[searchable snapshot indices](###Searchable snapshots)。你也应该使用[shard allocation awareness](#####Shard allocation awareness) 来限制每一个区域中每一个分片的副本数量。比如说你配置了一个索引有一个或者两个副本，那么allocation awareness会保证主分片的副本在不同的区域中。这意味着即使一个区域发生了故障，分片的副本仍然是可见的，分片的可见性不会受到这种故障的影响。

##### Summary

&emsp;&emsp;只要满足下面的条件，集群在丢失任何区域后依然是弹性的：

- [cluster health status](####Cluster health API)是`green`
- 至少有两个区域有数据节点
- 每一个不是[searchable snapshot index](##Searchable snapshots)的索引至少每一个分片都有一个replica，in addition to the primary
- 配置Shard allocation awareness使得不让一个分片的所有副本都集中在一个区域中
- 集群中至少有三个master-eligible节点，只要这些节点中至少有两个不是voting-only master-eligible nodes，并且他们分在至少三个不同的区域中
- 客户端配置为将其请求发送到多个节点，或者配置为使用负载均衡器在一组适当的节点之间平衡请求，[Elastic Cloud](https://www.elastic.co/cloud/elasticsearch-service/signup?baymax=docs-body&elektra=docs) 服务提供这种负载均衡

### Cross-cluster replication
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/xpack-ccr.html)

&emsp;&emsp;通过cross-cluster replication，你可以跨集群复制索引使得：

- 数据中心断电后仍可以继续处理查询请求
- 防止搜索量（search volume）影响索引吞吐量
- 根据用户的地理位置（geo-proximity）来降低查询的延迟

&emsp;&emsp;Cross-cluster replication使用主动-被动模式（active-passive mode），往leader index写入数据，然后数据被复制到只读的follower index中。在集群中创建一个follower index之前，需要先配置一个包含leader index的远程集群（remote cluster）。

&emsp;&emsp;当leader index收到写入请求时，follower index会从远程的leader index中拉取（pull）变更。你可以手动创建follower index，也可以配置auto-follow 模式为时序索引（time series indices）自动创建follower index。

&emsp;&emsp;你可以配置双向（bi-directional）或者单向的（uni-directional）cross-cluster replication集群：

- 在uni-directional配置中，一个集群只包含leader index，其他集群只包含follower index
- 在bi-directional配置中，每一个集群同时包含leader index和follower index

&emsp;&emsp;在uni-directional中，包含follower index的集群中的Elasticsearch版本必须跟远端集群的Elasticsearch版本相同或者更新。如果版本更新，那么参考下面表格中的兼容性。

&emsp;&emsp;版本兼容性：


|                    | Local cluster |      |         |      |      |      |          |      |         |
| :----------------: | :-----------: | :--: | :-----: | :--: | :--: | :--: | :------: | :--: | ------- |
| **Remote cluster** |    5.0–5.5    | 5.6  | 6.0–6.6 | 6.7  | 6.8  | 7.0  | 7.1–7.16 | 7.17 | 8.0–8.2 |
|      5.0–5.5       |       √       |  √   |    ×    |  ×   |  ×   |  ×   |    ×     |  ×   | ×       |
|        5.6         |       √       |  √   |    √    |  √   |  √   |  ×   |    ×     |  ×   | ×       |
|      6.0–6.6       |       ×       |  √   |    √    |  √   |  √   |  ×   |    ×     |  ×   | ×       |
|        6.7         |       ×       |  √   |    √    |  √   |  √   |  √   |    ×     |  ×   | ×       |
|        6.8         |       ×       |  √   |    √    |  √   |  √   |  √   |    √     |  √   | ×       |
|        7.0         |       ×       |  ×   |    ×    |  √   |  √   |  √   |    √     |  √   | ×       |
|      7.1–7.16      |       ×       |  ×   |    ×    |  ×   |  √   |  √   |    √     |  √   | ×       |
|        7.17        |       ×       |  ×   |    ×    |  ×   |  √   |  √   |    √     |  √   | √       |
|      8.0–8.2       |       ×       |  ×   |    ×    |  ×   |  ×   |  ×   |    ×     |  √   | √       |


##### Multi-cluster architectures

&emsp;&emsp;在Elastic Stack中使用cross-cluster replication来构造多个多集群架构：

- [Disaster recovery](######Disaster recovery and high availability)说的是一旦主集群（primary cluster）发生了故障，辅助集群（secondary cluster）可以作为一个热备（hot backup）提供服务。
- [Data locality](######Data locality)用于在靠近应用程序服务器（和用户）的地方维护数据集的多个副本，并减少代价高昂的延迟
- [Centralized reporting](######Centralized reporting)用于最大限度地减少查询多个地理分布式（geo-distribution） Elasticsearch 集群时的网络流量（network traffic）和延迟，或防止辅助集群（secondary cluster）上的查询负载影响该集群上的索引写入性能。

&emsp;&emsp;观看[cross-cluster replication webinar](https://www.elastic.co/cn/webinars/replicate-elasticsearch-data-with-cross-cluster-replication-ccr)来了解更多关于下文中用例的内容，然后在你的电脑上[set up cross-cluster replication]来允许webinar中的demo。

###### Disaster recovery and high availability

&emsp;&emsp;Disaster recovery为你的任务关键型（mission-critical ）应用程序提供承受数据中心或区域中断的容错能力。这个用例是最常见的基于cross-cluster replication的部署方式。你可以通过不同的架构来提供集群的disaster recovery和高可用：

- Single disaster recovery datacenter
- Multiple disaster recovery datacenters
- Chained replication
- Bi-directional replication

- Single disaster recovery datacenter

&emsp;&emsp;在这个配置中，生产中心（production datacenter）中的数据拷贝到灾难恢复中心（disaster recovery datacenter）或者说灾备中心。由于follower index复制了leader index，所以如果生产中心不可用时候可以使用灾难恢复中心的数据。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ccr-arch-disaster-recovery.png">

- Multiple disaster recovery datacenters

&emsp;&emsp;你可以将一个数据中心的数据复制到多个数据中心。在复制到两个数据中心后，当主数据中心（primary datacenter）发生故障后，这种配置可以提供disaster recovery和高可用。

&emsp;&emsp;下图中，数据中心A的数据复制到了数据中心B和数据中心C，这两个数据中心拥有从数据中心A上的leader index复制的索引，并且是只读的。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ccr-arch-multiple-dcs.png">

- Chained replication

&emsp;&emsp;你可以跨多个数据中心进行数据复制并形成一个复制链（replication chain）。下图中，数据中心A包含了leader index，数据中心B复制了数据中心A的数据，数据中心C复制了数据中心B上的follower index。这些数据中心间的连接形成了一个链式的复制模式（chained replication pattern）。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ccr-arch-chain-dcs.png">

- Bi-directional replication

&emsp;&emsp;在[bi-directional replication](https://www.elastic.co/blog/bi-directional-replication-with-elasticsearch-cross-cluster-replication-ccr)中，所有集群有权（access）访问所有的数据，所有集群拥有用于写入的索引并且不用手动实现故障转移（failover）。应用程序可以写入到每一个数据中心的本地索引中并且可以跨多个索引（across multiple indices）来读取全局的所有的信息（a global view of all information）。

&emsp;&emsp;当一个集群或者数据中心不可用时，这种配置不需要手动的干预。在下面图中，当数据中心A发生不可用后，你可以继续使用数据中心B并且不需要手动的进行故障转移。当数据中心A重新上线后（come online），集群间就会恢复复制。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ccr-arch-bi-directional.png">

&emsp;&emsp;这种配置对于index-only，不会对文档进行更新的场景十分有用。在这个配置中，通过Elasticsearch索引的数据是不会变更的（immutable）。客户端只跟所在数据中心的集群通信，不要跟其他数据中心的集群有联系（Clients are located in each datacenter alongside the Elasticsearch cluster, and do not communicate with clusters in different datacenters）。

###### Data locality

&emsp;&emsp;把数据放在靠近应用程序或者用户的地方能降低延迟以及响应时间。这种方法在Elasticsearch中进行数据复制时同样适用。比如你可以将产品目录（product catalog）和reference dataset复制到全世界20个或者更多的数据中心中，使得最小化数据和应用服务之间的距离。

&emsp;&emsp;下图中，一个数据中心的数据复制到了三个额外的数据中心。每个数据中心位于各自的区域（region）。Central DC中包含了leader index，其他三个数据中心在各自的区域中包含follower index。这个配置将数据放在了靠近应用的地方。

###### Centralized reporting

&emsp;&emsp;当查询需要跨一个很大的网络的时候其效率很低下的，这时候使用一个中心化的报告集群（centralized reporting cluster）是非常有用的。在这个配置中，你将许多较小的集群中的数据拷贝到centralized reporting cluster。这个配置使你能够将数据从区域中心（regional hubs）复制到中央集群（central cluster），使得你可以在本地运行所有报告。

<img src="http://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ccr-arch-central-reporting.png">

##### Replication mechanics

&emsp;&emsp;尽管你可以在索引层（index level）来设置[cross-cluster replication](####Tutorial Set up cross-cluster replication)，而Elasticsearch是在分片级别（shard level）实现复制。当创建了一个follower index，这个索引中的每一个分片会从对应的leader index中的分片拉取变更。这意味着follower index跟leader index有相同的分片数量。在leader index上的所有操作都会被follower index复制，比如创建、更新、删除一篇文档的操作。这些请求会通过leader shard（primary或者replica）发送。

&emsp;&emsp;当一个follower shard发送一个读请求，leader shard会根据在创建follower index时候配置的read parameter来响应每一个新的操作。如果没有新的操作，leader shard会根据配置的超时时间等待新的操作的发生，等待超时后，leader shard会响应follower shard没有新的操作。follower shard在更新shard statistics后会马上再发一个读请求给leader shard。这种通信模型确保远程集群和本地集群之间的网络连接持续使用，避免被防火墙等外部源强行终止。

&emsp;&emsp;如果读请求失败了，则会检查（inspect）失败的原因。如果失败的原因认为是可以恢复的（比如说网络问题），那么 follower shard会进行循环重试。否则follower shard会暂停[until you resume it](#####Pause and resume replication（manage）)。

###### Processing updates

&emsp;&emsp;你不能手动的更改follower index的mappings或者aliases。如果进行变更，需要去更改leader index。因为follower index是只读的，所有配置中对follower index的写操作都会被reject。

> NOTE：Although changes to aliases on the leader index are replicated to follower indices, write indices are ignored. Follower indices can’t accept direct writes, so if any leader aliases have `is_write_index` set to `true`, that value is forced to `false`.

&emsp;&emsp;例如你在数据中心A中添加了一篇名为`doc_1`的文档并且复制到了数据中心B。如果客户连接到了数据中心B并且尝试更新`doc_1`，那这次请求会失败。要想更新`doc_1`，必须连接数据中心A并且在leader index上进行更新。

&emsp;&emsp;当follower shard从leader shard收到操作请求时，follow shard会将这些操作放到一个writer buffer中，follower shard会基于这个writer buffer执行bulk write请求。当writer buffer超过配置的上限后，则不会发送读请求。这个配置提供back-pressure机制，使得writer buffer不再full以后就恢复读请求。

&emsp;&emsp;你可以在[creating the follower index](####Tutorial Set up cross-cluster replication)时进行配置来管理如何复制leader index的操作。

&emsp;&emsp;leader index上的index mapping的变更会马上复制到follower index上。这种行为同样发生在index settings上。除了一些leader index的本地设置，比如说修改leader index的分片数量是不会复制到follower index的，follower index不会收到（retrieve）这种设置。

&emsp;&emsp;如果在leader index上应用了一个non-dynamic的设置（settings）并且follower index也需要这个设置，follower index会先关闭，然后应用这个设置，最后重新打开。在这个期间，follower index无法被读取并且不能复制写操作。

##### Initializing followers using remote recovery

&emsp;&emsp;当创建了一个follower index，你只有等这个follower index完全的初始化结束后才能使用它。`remote recovery process`会在follower node上创建一个新的分片并且将leader node上的主分片数据拷贝到这个新的分片中。

&emsp;&emsp;Elasticsearch使用这个`remote recovery process`来引导（bootstrap）follower index使用来自leader index的数据。`remote recovery process`将leader index当前状态的副本提供给follower index，即使由于Lucene中合并中的段使得leader上完整的历史更改还不可用。

&emsp;&emsp;remote recovery是网络密集型（network intensive）的处理过程：它将所有的Lucene的段文件从leader集群传到follower集群。follower在leader集群中的主分片上初始化一个recovery session，随后并发的向leader请求文件块（file chunks）。默认情况下，并发的请求five 1MB file chunks。默认的行为设计为：支持leader和follower集群之间具有高网络延迟（high network latency）。

> TIP：你可以修改[dynamic remote recovery settings](#####Remote recovery settings)来限制数据传输速率，并通过remoterecovery管理资源消费。

&emsp;&emsp;使用集群中的[recovery API](####cat recovery API)以及follower index来获取处理中的remote recovery信息。由于Elasticsearch使用[snapshot and restore](##Snapshot and restore)来实现remote recovery，运行中的remote recovery在recovery API被标记为`snapshot`类型。

##### Replicating a leader requires soft deletes

&emsp;&emsp;Cross-cluster replication的工作方式是通过replay leader index的主分片上执行的各个写操作的历史记录。Elasticsearch需要保留[history of these operations](###History retention)，这样就可以让follower shard任务去拉取这些操作。保留这些操作的底层机制是`soft deletes`。

&emsp;&emsp;当删除/更新现有的文档时会发生soft delete。保留的soft delete有一定的限制，这个限制是可配置的。操作历史保留在leader shard上并且对follower shard task可见，用于replay历史操作。

&emsp;&emsp;[index.soft_deletes.retention_lease.period](#####index.soft_deletes.retention_lease.period)这个设置定义了分片历史的驻留最大时间，这个设置决定了你的follower index可以离线的最长时间，默认12个小时。如果分片拷贝在其保留租约（retention lease）到期后恢复，但是缺失的操作仍然在leader index上可用，那Elasticsearch会建立一个新的租约并拷贝缺失的操作。然而Elasticsearch不能保证retain unleased operations，所以有可能一些缺失的操作被leader丢弃了并且现在完全不可用了。如果发生了这种情况，follower就不能自动恢复，那你必须[recreate it](#####Recreate a follower index)。

&emsp;&emsp;如果要作为leader index，它必须开启soft delete。在Elasticsearch 7.0.0之后，新创建的索引默认开启soft delete。

> IMPORTANT： Cross-cluster replication不能用于Elasticsearch 7.0.0或者更早版本中现有的索引，因为soft delete是关闭的。你必须[reindex](####Reindex API)你的数据并且开启soft delete。

##### Use cross-cluster replication

&emsp;&emsp;下面的内容提供了关于如何配置以及使用cross-cluster replication的更多信息：

- [Set up cross-cluster replication](####Tutorial: Set up cross-cluster replication)
- [Manage cross-cluster replication](####Manage cross-cluster replication)
- [Manage auto-follow patterns](####Manage auto-follow patterns)
- [Upgrading clusters](####Upgrading clusters using cross-cluster replication)

#### Tutorial Set up cross-cluster replication
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ccr-getting-started-tutorial.html)

&emsp;&emsp;使用本章节的指南来设置两个数据中心集群的cross-cluster replication（CCR）。跨数据中心复制（replicate）你的数据能提供以下的好处：

- 将数据靠近用户或者应用服务来降低延迟很响应时间
- 为你的mission-critical application提供可承受数据中心或区域中断的容忍度

&emsp;&emsp;在这一章节，你将会学习到：

- 如何用leader index配置一个[remote cluster](###Remote clusters)
- 如何在一个本地集群中创建一个follower index
- 如何创建一个auto-follow pattern来自动的follow时序索引，remote cluster会周期性的创建这些索引

&emsp;&emsp;你可以手动创建follower index来复制（replicate）remote cluster上的指定的索引，或者配置auto-follow patterns来复制 rolling time series indices。

> TIP：如果你要在cloud上跨集群复制数据，你可以[configure remote clusters on Elasticsearch Service](https://www.elastic.co/guide/en/cloud/current/ec-enable-ccs.html)，然后你就可以[search across clusters](###Search across clusters)以及设置CCR。

##### Prerequisites

&emsp;&emsp;若要完成这个教程，你需要：

- 本地集群上的`manage` 集群特权（cluster privilege）
-  集群的许可证以及CCR。[Activate a free 30-day trial](https://www.elastic.co/guide/en/kibana/8.2/managing-licenses.html)
-  remote cluster上有你想要复制的数据。这个教程使用了电子商务订单的样例数据。[Load sample data](https://www.elastic.co/guide/en/kibana/8.2/get-started.html#gs-get-data-into-kibana)
-  本地集群中，拥有`master` [node role](####Node)的所有节点必须还要有[remote_cluster_client](####Node)角色。本地的集群至少还要一个有`data` 和`remote_cluster_client`角色的节点。个别用于coordinating replication的任务的规模（scale）取决于本地集群中拥有`data` 和`remote_cluster_client`角色的节点数量

##### Connect to a remote cluster

&emsp;&emsp;若要将remote cluster（Cluster A）上的索引复制到本地集群（Cluster B），你需要将Cluster A配置为Cluster B的remote 。

<img src="https://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ccr-tutorial-clusters.png">

&emsp;&emsp;若要在Kibana中的Stack Management中配置一个remote cluster：

1. 从侧边导航栏选择**Remote Clusters**
2. 指定remote cluster（Cluster A）的Elasticsearch endpoint URL，或者IP地址，hostname以及transport端口（默认值`9300`）。例如`cluster.es.eastus2.staging.azure.foundit.no:9400`或者`192.168.1.1:9300`

&emsp;&emsp;你也可以使用[cluster update settings API ](####Cluster update settings API)来添加一个remote cluster：

```text
PUT /_cluster/settings
{
  "persistent" : {
    "cluster" : {
      "remote" : {
        "leader" : {
          "seeds" : [
            "127.0.0.1:9300" 
          ]
        }
      }
    }
  }
}
```

&emsp;&emsp;第8行，指定remote cluster的hostname以及transport 端口。

&emsp;&emsp;你可以通过下面的方式验证本地集群是否成功连接了remote cluster。

```text
GET /_remote/info
```

&emsp;&emsp;API响应显示本地集群连接了一个名为`leader`的remote cluster。

```text
{
  "leader" : {
    "seeds" : [
      "127.0.0.1:9300"
    ],
    "connected" : true,
    "num_nodes_connected" : 1, 
    "max_connections_per_cluster" : 3,
    "initial_connect_timeout" : "30s",
    "skip_unavailable" : false,
    "mode" : "sniff"
  }
}
```

&emsp;&emsp;第7行，本地集群连接的remote cluster中节点的数量

##### Enable soft deletes on leader indices

&emsp;&emsp;index若要能被follow，在创建时必须开启[soft deletes](#####Replicating a leader requires soft deletes)。如果索引没有开启soft deletes，你必须reindex并且使用新的索引作为leader index。Elasticsearch 7.0.0及以后的版本默认在新的索引上开启soft deletes。

##### Configure privileges for cross-cluster replication

&emsp;&emsp;CCR在remote cluster和本地集群（local cluster）上要求有不同的cluster和index privilege。使用下面的请求创建remote cluster和本地集群（local cluster）上不同的角色，然后创建一个拥有这些角色的用户。

###### Remote cluster

&emsp;&emsp;拥有leader index的remote cluster上，CCR role要求`read_ccr` cluster privilege，以及leader index上的`monitor`和`read` privilege。

> NOTE：如果请求出现了[on behalf of other users](####Submitting requests on behalf of other users)的问题，用户需要有remote cluster上的`run_as` privilege。

&emsp;&emsp;下面的请求在remote cluster上创建一个`remote-replication` 角色：

```text
POST /_security/role/remote-replication
{
  "cluster": [
    "read_ccr"
  ],
  "indices": [
    {
      "names": [
        "leader-index-name"
      ],
      "privileges": [
        "monitor",
        "read"
      ]
    }
  ]
}
```

###### Local cluster

&emsp;&emsp;拥有follower index的本地集群，`remote-replication` 角色要求`manage_ccr` cluster privilege，以及follower index上的`monitor`，`read`，`write`，以及`manage_follow_index` privilege。

&emsp;&emsp;下面的请求在本地集群上创建了一个`remote-replication`角色：

```text
POST /_security/role/remote-replication
{
  "cluster": [
    "manage_ccr"
  ],
  "indices": [
    {
      "names": [
        "follower-index-name"
      ],
      "privileges": [
        "monitor",
        "read",
        "write",
        "manage_follow_index"
      ]
    }
  ]
}
```

&emsp;&emsp;在一个集群上创建`remote-replication`角色后，在本地集群上使用[create or update users API](####Create or update users API)上创建一个用户并且赋予`remote-replication`角色。例如，下面的请求赋予名为`cross-cluster-user`的用户一个`remote-replication`角色：

```text
POST /_security/user/cross-cluster-user
{
  "password" : "l0ng-r4nd0m-p@ssw0rd",
  "roles" : [ "remote-replication" ]
}
```

> NOTE：你只需要在本地集群上创建这个用户

##### Create a follower index to replicate a specific index

&emsp;&emsp;当你创建了一个follower index，要将remote cluster和leader index引用到你的remote cluster中。

&emsp;&emsp;若要从Kibana中的Stack Management中创建一个follower index：

1. 侧边导航栏选择**Cross-Cluster Replication**，然后选择**Follower Indices**
2. 选择你想要复制（replicate）的包含leader index的cluster（Cluster A）
3. 输入leader index的名字，如果你按照的是以下的教程，那么就填入`kibana_sample_data_ecommerce`
4. 为你的follower index输入一个名字，比如`follower-kibana-sample-data`

&emsp;&emsp;Elasticsearch使用[remote recovery process](#####Initializing followers using remote recovery)来初始化follower，即将leader index中的Lucene段文件传输到follower index中。索引状态被更改为**Paused**。在remote recovery process结束，follower index开始使用并且状态更改为**Active**。

&emsp;&emsp;当你在leader index中索引文档时，Elasticsearch会将这些文档复制到follower index中。

<img src="https://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/ccr-follower-index.png">

&emsp;&emsp;你也可以使用[create follower API](####Create follower API)来创建follower index。在你创建一个follower index后，你必须引用remote cluster以及leader index。

&emsp;&emsp;在初始化follower请求时，请求响应在[remote recovery process](#####Initializing followers using remote recovery)完成前就会返回，若要等待完成后再返回，那么在你的请求中添加参数`wait_for_active_shards`。

```text
PUT /server-metrics-follower/_ccr/follow?wait_for_active_shards=1
{
  "remote_cluster" : "leader",
  "leader_index" : "server-metrics"
}
```

&emsp;&emsp;使用[get follower stats API](####Get follower stats API)来观察复制状态（status of replication）。

##### Create an auto-follow pattern to replicate time series indices

&emsp;&emsp;你可以使用[auto-follow patterns](####Manage auto-follow patterns)自动的为rolling time series indices创建新的follower。只要remote cluster上的新的索引名字匹配到了auto-follow pattern，对应的follower index就会添加到local cluster中。

&emsp;&emsp;auto-follow pattern用于指定你想要从remote cluster中复制的索引，可以指定一个或者多个。

&emsp;&emsp;若要在Kibana的Stack Management中创建一个auto-follow pattern：

1. 侧边导航栏选择**Cross Cluster Replication **，然后选择**Auto-follow patterns**
2. 为auto-follow pattern输入一个名字，比如`beats`
3. 选择你想要复制（replicate）的包含leader index的remote cluster，例子中的场景是Cluster A
4. 输入一个或者多个index pattern来确认你想要从remote cluster中复制的索引。例如输入`metricbeat-* packetbeat-*`会为Metricbeat和Packetbeat自动创建follower
5. 输入以**Follower**作为follower index名字的前缀，这样能让你更容易区分出复制的索引

&emsp;&emsp;匹配pattern的新索引在remote cluster中创建，Elasticsearch会自动的将它们复制到本地follower index中。

<img src="https://www.amazingkoala.com.cn/uploads/Elasticsearch/8.2/auto-follow-patterns.png">

&emsp;&emsp;你可以使用[create auto-follow pattern API](####Create auto-follow pattern API)来配置auto-follow patterns。

```text
PUT /_ccr/auto_follow/beats
{
  "remote_cluster" : "leader",
  "leader_index_patterns" :
  [
    "metricbeat-*", 
    "packetbeat-*" 
  ],
  "follow_index_pattern" : "{{leader_index}}-copy" 
}
```

&emsp;&emsp;第6行，自动follow新的Metricbeat索引

&emsp;&emsp;第7行，自动follow新的Packetbeat索引

&emsp;&emsp;第9行，follower index的名字继承于leader index 并且添加了`copy`后缀

#### Manage cross-cluster replication
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ccr-managing.html#ccr-pause-replication)

&emsp;&emsp;使用下面的内容来管理cross-cluster replication任务，例如inspecting replication progress、pausing and resuming replication, recreating a follower index, and terminating replication。

&emsp;&emsp;若要开始使用CCR，访问Kibana然后进入**Management > Stack Management**。在侧边导航栏选择**Cross-Cluster Replication**。

##### Inspect replication statistics

&emsp;&emsp;若要查看某个follower index的复制过程以及详细的分片统计数据（shard statistics）。根据上文中的内容进入**Cross-Cluster Replication**然后选择**Follower indices**。

&emsp;&emsp;选择你想要查看的follower index详情的名字，滑出（slide out）的面板会先显示follower index的设置和replication statistics，包括follower shard的读写操作。

&emsp;&emsp;若要显示更多的详细的统计数据，点击**View in Index Management**，在index management中选择follower index的名字，查看follower index的详细统计数据。

&emsp;&emsp;API example：

- 使用[get follower stats API](####Get follower stats API)查看在分片级别的复制过程，这个API能提供follower shard管理的读写操作，还会报出可以重试的异常以及需要用户干预的异常。

##### Pause and resume replication（manage）

&emsp;&emsp;若要停止或者恢复leader index的复制（replication），根据上文中的内容进入**Cross-Cluster Replication**然后选择**Follower indices**

&emsp;&emsp;选择你想要停止的follower index然后选择**Manage > Pause Replication**，follower index的状态变更为Paused。

&emsp;&emsp;若要恢复复制，选择follower index然后选择**Resume replication**

&emsp;&emsp;API example：

&emsp;&emsp;你可以使用[pause follower API](####Pause follower API)停止复制并随后使用[resume follower API](####Resume follower API)恢复复制。一前一后（in tandem）使用这两个API可以让你调整follower shard task的读写参数，如果你最开始的配置不太适用于你的用例。

##### Recreate a follower index

&emsp;&emsp;当更新/删除一篇文档时，底层操作会在Lucene的索引中保留一段时间，这个时间通过参数[index.soft_deletes.retention_lease.period](#####index.soft_deletes.retention_lease.period)定义。你可以在[leader index](###Cross-cluster replication)上配置这个设置。

&emsp;&emsp;当follower index开始后，它会向leader index需求一个retention lease，这个lease要求leader不允许prune一个soft delete，直到follower告知它已经收到了该操作或者lease到期。

&emsp;&emsp;如果follower index远远落后于（fall sufficiently）leader并且不能复制操作，Elasticsearch会报出`indices[].fatal_exception`的错误，若要解决这个问题，需要重新创建一个follower index。当新的follower index创建后，[remote recovery process](###Cross-cluster replication)开始从leader中复制Lucene的段文件。

> IMPORTANT：重新创建follower index属于破坏性的行为（destructive action），集群中包含follower index的所有Lucene段文件都会被删除

&emsp;&emsp;若要重新创建follower index，根据上文中的内容进入**Cross-Cluster Replication**然后选择**Follower indices**。

&emsp;&emsp;选择follower index和pause replication。当follower index的状态更改为Paused，重新选择follower index，然后unfollower leader index。

&emsp;&emsp;follower index会被转化为standard index，并且不再Cross-Cluster Replication 页面上显示。

&emsp;&emsp;在侧边导航栏，选择**Index Management**，选择上一步的follower index然后关闭它们。

&emsp;&emsp;随后你就可以[recreate the follower index](#####Create a follower index to replicate a specific index)，重新进行复制。

&emsp;&emsp;API example：

&emsp;&emsp;使用[pause follow API](####Pause follower API)来停止复制，然后关闭follower index并重新创建：

```text
POST /follower_index/_ccr/pause_follow

POST /follower_index/_close

PUT /follower_index/_ccr/follow?wait_for_active_shards=1
{
  "remote_cluster" : "remote_cluster",
  "leader_index" : "leader_index"
}
```

##### Terminate replication

&emsp;&emsp;你可以unfollower leader index，然后将follower index转化为一个standard index。根据上文中的内容进入**Cross-Cluster Replication**然后选择**Follower indices**。

&emsp;&emsp;选择follower index和pause replication。当follower index的状态更改为Paused，重新选择follower index，然后unfollower leader index。

&emsp;&emsp;follower index会被转化为standard index，并且不再Cross-Cluster Replication 页面上显示。

&emsp;&emsp;选择**Index Management**，选择上一步的follower index然后关闭它们。

&emsp;&emsp;API example：

&emsp;&emsp;你可以使用[unfollow API](####Unfollow API)来停止复制，这个API会将follower index转化为standard index（non-follower）。

#### Manage auto-follow patterns
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ccr-auto-follow.html)

&emsp;&emsp;若要复制（replication）时序索引，你可以配置一个auto-follow pattern，使得新创建的索引被自动复制。只要remote cluster上新的索引名字匹配到auto-follow pattern，对应的follower index就会添加到本地集群（local cluster）中。

> NOTE：Auto-follow patterns只会匹配remote cluster上打开的索引（open index），并且索引的所有的主分片都已经启动（start）。Auto-follow patterns不会匹配[closed indices](####Open index API)和[searchable snapshots](###Searchable snapshots)来用于CCR。避免使用auto-follow pattern匹配带有[read or write block](####Index block settings)的索引，这些block会阻止follower index执行复制操作。

&emsp;&emsp;你也可以为data streams创建auto-follow pattern。当remote cluster上创建了一个新的backing index并且auto-follow pattern匹配到了data stream的名字，那index和data stream会被自动的follow。如果你在创建auto-follow pattern之后再创建data stream，那么所有的backing index都会被follow。

&emsp;&emsp;通过CCR从remote cluster复制过来的data streams受到local rollovers的保护，可以使用[promote data stream API](####Promote data stream API)来将这些data streams变成regular data streams。

&emsp;&emsp;Auto-follow patterns在[Index lifecycle management](###ILM: Manage the index lifecycle)中特别有用，因为它在包含leader index的集群上会不断的创建新的索引。

&emsp;&emsp;若要在Kibana的Stack Management中使用cross-cluster replication auto-follow patterns，从侧边导航栏选择**Cross-Cluster Replication**然后选择**Auto-follow patterns**。

##### Create auto-follow patterns

&emsp;&emsp;当你[create an auto-follow pattern](#####Create an auto-follow pattern to replicate time series indices)时，你可以对单个集群配置一个pattern集合。当在remote cluster上创建一个索引时，索引名会匹配pattern集合中的一个，随后就会在local cluster中配置一个follower index。follower index使用最新的索引作为leader index。

&emsp;&emsp;使用[create auto-follow pattern API](####Create auto-follow pattern API)来添加一个新的auto-follow pattern配置。

##### Retrieve auto-follow patterns

&emsp;&emsp;若要查看现有的auto-follow patterns并修改backing patterns，那就访问你的remote cluster上的Kibana。

&emsp;&emsp;选择你想要查看的auto-follow pattern，然后就可以对其做变更，你可以查看auto-follow pattern下的follower index。

&emsp;&emsp;你可以通过[get auto-follow pattern API](####Get auto-follow pattern API)查看所有配置的auto-follow pattern集合。

##### Pause and resume auto-follow patterns

&emsp;&emsp;若要停止或者恢复auto-follow pattern 集合，访问Kibana，然后选择auto-follow并且停止复制（replication）。

&emsp;&emsp;若要恢复复制，选择pattern并选择**Manage pattern > Resume replication**。

&emsp;&emsp;可以使用[pause auto-follow pattern API](####Pause auto-follow pattern API)来停止auto-follow pattern，并使用[pause auto-follow pattern API](####Resume auto-follow pattern API)来恢复auto-follow pattern。

##### Delete auto-follow patterns

&emsp;&emsp;若要删除auto-follow pattern集合，访问Kibana，选择auto-follow pattern，然后停止复制。

&emsp;&emsp;当pattern状态变成Paused，选择**Manage pattern > Delete pattern**。

&emsp;&emsp;也可以使用[delete auto-follow pattern API](####Delete auto-follow pattern API)来删除一个auto-follow pattern集合。

#### Upgrading clusters using cross-cluster replication
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ccr-upgrading.html)

&emsp;&emsp;对正在使用CCR的集群升级要谨慎对待，下面的情况可能会导致在rolling upgrades时发生索引follow出现失败：

- 尚未升级的集群将拒绝从升级后的集群复制的新的index setting或mapping
- 当follower index尝试回退到file-based的恢复时，未升级的集群中的节点将拒绝来自已升级集群中的节点的索引文件。这个限制是由于Lucene不向前兼容

&emsp;&emsp;基于uni-directional和bi-directional index following，在开启CCR的集群上运行rolling upgrade有所不同。

##### Uni-directional index following

&emsp;&emsp;在uni-directional配置中，只有一个集群包含leader index，并且其他集群只包含复制leader index的follower index。

&emsp;&emsp;这种策略中，follower index所在的集群应该先升级并且leader index所在的集群最后升级。按这种顺序处理能保证在升级过程中能继续index following并且不需要下线。

&emsp;&emsp;你也可以使用这个策略升级一个[replication chain](###Cross-cluster replication)，先升级链中最后的集群再升级包含leader index的集群。

&emsp;&emsp;例如，ClusterA包含了所有的索引，ClusterB follow ClusterA中的索引，ClusterC follow ClusterB中的索引。

```text
Cluster A
        ^--Cluster B
                   ^--Cluster C
```

&emsp;&emsp;在这种配置中，按照下面的顺序升级：

1. Cluster C
2. Cluster B
3. Cluster A

##### Bi-directional index following

&emsp;&emsp;在bi-directional的配置中，每一个集群包含leader和follower index。

&emsp;&emsp;在这种配置中升级时，在升级集群前先[pause all index following](#####Pause and resume replication)和[pause auto-follow patterns ](#####Pause and resume auto-follow patterns)。

&emsp;&emsp;在升级结束后，恢复index following以及恢复auto-follow pattern的复制。

##### Recreate a follower index

##### Pause and resume replication

## Snapshot and restore
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/snapshot-restore.html)

&emsp;&emsp;快照（snapshot）是运行中的Elasticsearch集群的备份。你可以使用快照用于：

- 定期备份集群，不停机
- 删除或者硬件故障后恢复数据
- 集群间传递数据
- 在cold和frozen data tiers中使用[searchable snapshots](###Searchable snapshots)降低存储开销

#### The snapshot workflow

&emsp;&emsp;Elasticsearch将快照存储在集群外的称为snapshot repository的存储位置中。在创建或恢复快照之前，你必须在集群中[register a snapshot repository](###Register a snapshot repository)。Elasticsearch支持一些带有云仓库选项的仓库类型，包括：

- AWS S3
- Google Cloud Storage（GCS）
- Microsoft Azure

&emsp;&emsp;注册一个快照仓库（snapshot repository）后，你可以使用[snapshot lifecycle management (SLM)](####Automate snapshots with SLM)自动的创建和管理快照。你可以[restore a snapshot](###Restore a snapshot)来恢复或传输数据。

#### Snapshot contents

&emsp;&emsp;默认情况下，集群的快照包含了集群状态（cluster state），所有的常规的（regular）data streams，所有常规的索引。集群状态包括：

- [Persistent cluster settings](####Cluster and node setting types)
- [Index templates](##Index templates)
- [Legacy index templates](####Create or update index template API)
- [Ingest pipelines](##Ingest pipelines)
- [ILM policies](###ILM: Manage the index lifecycle)
- For snapshots taken after 7.12.0, [feature states](#####Feature states)

&emsp;&emsp;你也可以在创建快照时只指定集群中的data stream或者索引。包含data stream或者索引的快照自动会包含它们的alias。当你恢复一个快照，你可以选择是否恢复这些alias。

&emsp;&emsp;快照中不包括或者说不备份：

- 临时的集群设置
- 注册的快照仓库
- 节点配置文件
- [Security configuration files](####Security files)

##### Feature states

&emsp;&emsp;Feature states包含索引和data streams，用于为一个Elastic feature存储配置、历史和其他数据，例如Elasticsearch security或者Kibana。

&emsp;&emsp;一个feature state通常包括一个或者多个[system indices or system data streams](#####System indices)。它也可能包括feature使用的常规索引和data stream。例如，某个feature state可能包括了一个常规索引，这个索引包含了feature的执行历史。在一个常规索引中存储这个历史让你更容易查询到。

&emsp;&emsp;在Elasticsearch 8.0及以后的版本中，feature states备份和恢复系统索引和系统data stream的唯一方法。

#### How snapshots work

&emsp;&emsp;快照会自动的删除重复数据（deduplicate）来节省存储空间并且能减少网络传输开销。快照通过拷贝索引的[segment](###Near real-time search)来备份一个索引并且将其存储在快照仓库中。由于segment是不可变的，快照只需要拷贝仓库中上一个快照后新的段。

&emsp;&emsp;每一个快照在逻辑上是独立的。当你删除一个快照，Elasticsearch仅仅（exclusively）删除那个快照使用的段。如果段被仓库中的其他快照使用，则不会删除。

##### Snapshots and shard allocation

&emsp;&emsp;快照对索引的主分片进行拷贝。当你开始一个快照，Elasticsearch会马上开始拷贝任意可见的（available）主分片的段。如果某个分片正在启动或者relocating，Elasticsearch会在拷贝分片的段（shard's segment）之前等待它们完成。如果一个或者多个分片不可见，the snapshot attempt fails。

&emsp;&emsp;一旦快照开始拷贝分片的段，Elasticsearch不会将这个分片移动到其他节点，即使rebalancing或者shard allocation settings会触发reallocation。Elasticsearch只会在快照完成复制分片数据后才移动分片。

##### Snapshot start and stop times

&emsp;&emsp;快照不代表某个精确时间点（a precise point in time）的集群。而是每个快照包含一个开始跟结束时间。快照代表的是两个时间之间每一个分片数据的视图。

#### Snapshot compatibility

&emsp;&emsp;若要往某个集群中恢复一个快照，快照、集群以及每一个恢复的索引版本必须兼容。

##### Snapshot version compatibility

| **Snapshot version** | 6.8  | 7.0–7.1 | 7.2–7.17 | 8.0–8.2 |
| :------------------: | :--: | :-----: | :------: | :-----: |
|       5.0–5.6        |  √   |    ×    |    ×     |    ×    |
|       6.0–6.7        |  √   |    √    |    √     |    ×    |
|         6.8          |  √   |    ×    |    √     |    ×    |
|       7.0–7.1        |  ×   |    √    |    √     |    √    |
|       7.2–7.17       |  ×   |    ×    |    √     |    √    |
|       8.0–8.2        |  ×   |    ×    |    ×     |    √    |

&emsp;&emsp;你不能将快照恢复到旧版本中。例如，你不能把在7.6.0中创建的快照恢复到运行7.5.0的集群中。

##### Index compatibility

&emsp;&emsp;恢复的每一个索引必须和当前集群的版本兼容。如果你尝试在一个不兼容的集群中恢复一个索引，尝试恢复会失败。

| **Index creation version** | 6.8  | 7.0–7.1 | 7.2–7.17 | 8.0–8.2 |
| :------------------: | :--: | :-----: | :------: | :-----: |
|       5.0–5.6        |  √   |    ×    |    ×     |    ×    |
|       6.0–6.7        |  √   |    √    |    √     |    ×    |
|         6.8          |  √   |    ×    |    √     |    ×    |
|       7.0–7.1        |  ×   |    √    |    √     |    √    |
|       7.2–7.17       |  ×   |    ×    |    √     |    √    |
|       8.0–8.2        |  ×   |    ×    |    ×     |    √    |

&emsp;&emsp;你不可以在旧版本中恢复索引。例如，你不能把在7.6.0中创建的索引恢复到运行7.5.0的集群中。

&emsp;&emsp;一个兼容的快照中可能会包含在不兼容的版本中创建的索引。例如，7.17集群的快照可以包含6.8中创建的索引。但你如果要在8.2的集群中恢复6.8的索引，尝试会失败。如果在升级之前创建快照请注意这个情况。

&emsp;&emsp;存在这么一种替代方法（workaround），你可以先将索引恢复到同时兼容你当前集群和索引的最新版本的集群上。你随后可以使用[reindex-from-remote](#####Reindex select fields with a source filter)在你当前集群上重新构建索引。只有索引开启[\_source](####\_source field)才可以从remote上reindex。

#### Warnings

##### Other backup methods

&emsp;&emsp;**Taking a snapshot is the only reliable and supported way to back up a cluster** 。你不能通过拷贝data目录中的节点数据进行备份。没有一种支持方式（supported way）从文件系统层面的备份来恢复数据。如果你想尝试从这种备份中恢复集群，可能会失败并且Report出corruption或者文件丢失或者其他数据一致性的问题，或者出现成功的悄无声息的丢失一些你的数据。

&emsp;&emsp;拷贝data目录中的节点数据不能用于备份是因为它不是在单个时间点上对其内容的一致表示（consistent representation）。你不能通过将节点下线，然后执行那些拷贝或者是采用文件系统层面的快照来修复刚才的问题。因为Elasticsearch有跨整个集群的一致性要求。你必须使用内置的快照功能来备份集群。

##### Repository contents

&emsp;&emsp;**Don’t modify anything within the repository or run processes that might interfere with its contents**。如果某个除了Elasticsearch以外的东西修改了仓库的内容，那以后快照或者恢复操作可能会失败，可能会出现轻微的数据丢失。

&emsp;&emsp;你可以安全的[ restore a repository from a backup ](####Back up a repository)，只要你：

- 在你恢复仓库的内容时，它没有注册在Elasticsearch
- 当你恢复完仓库，它的内容跟你创建它时的内容完全一样

&emsp;&emsp;另外，快照可能包含security-sensitive的信息，那你可能需要[store in a dedicated repository](####Dedicated cluster state snapshots)。

### Register a snapshot repository
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/snapshots-register-repository.html)

### Create a snapshot
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/snapshots-take-snapshot.html#automate-snapshots-slm)

&emsp;&emsp;这一章节介绍了如何对运行中的集群创建快照。随后你可以通过[restore a snapshot](###Restore a snapshot)来恢复或者传输数据。

&emsp;&emsp;你将学习到：

- 自动创建快照以及使用snapshot lifecycle management（SLM）保留快照
- 手动创建一个快照
- 监控快照的处理过程（snapshot's progress）
- 删除或取消一个快照
- 备份集群配置文件

&emsp;&emsp;这一章同样介绍了创建专用的集群状态快照的技巧以及在不同的时间间隔创建快照。

#### Prerequisites

- 若要使用Kibana的**Snapshot and Restore**功能，你必须要有以下的权限（permission）：
  - [Cluster  privilege](#####Cluster privileges)：`monitor`, `manage_slm`, `cluster:admin/snapshot` 以及 `cluster:admin/repository`
  - [Index privilege](#####Indices privileges)：`all` on the `monitor` 索引
- 你只可以在选举为[master](#####Master-eligible node)的节点上对运行中的集群创建快照
- 快照仓库必须[registered](###Register a snapshot repository) 并且对集群可见（available）
- 集群的global metadata必须可读。要在快照中包含索引，所以索引和它的metadata必须也是可读的。确保没有任何的[cluster blocks](######Metadata)或者[index blocks](###Index blocks)阻碍读取访问

#### Considerations

- 仓库中快照名字必须是唯一的。尝试创建一个仓库中同名的快照会失败
- 快照自动删除重复的数据（deduplicated）。你可以频繁创建快照，对存储开销只有很小的影响
- 每个快照逻辑上是独立的。你可以删除一个快照，不会影响其他的快照
- 创建一个快照会临时暂停分片的分配。见[Snapshots and shard allocation](#####Snapshots and shard allocation)
- 创建快照不会阻塞索引操作获取其他的请求。然而快照不会包含开始备份后的变更
- 你可以在同一时间创建多个快照。集群设置[snapshot.max_concurrent_operations](######snapshot.max_concurrent_operations)限制的快照操作的数量最大值并发量
- 如果快照中包含了一个data stream，那快照中同样包含了这个流的backing Index和metadata
  - 你可以在快照中包含指定的backing Index。然而就不会包含data stream的metadata或者其他的backing Index
- 快照可以包含一个data stream但是排除指定的backing Index。当你恢复这样的data stream，就只包含快照中的backing Index。如果流中原先的write Index不在快照中，那快照中最近（most recent）的backing index成为流的write index

#### Automate snapshots with SLM

&emsp;&emsp;Snapshot lifecycle management（SLM）是定期备份集群的最简单的方法。SLM策略根据设定好的定时计划自动的创建快照。该策略同样可以根据你定义的保留规则删除快照。

> TIP：Elasticsearch Service deployments automatically include the cloud-snapshot-policy SLM policy. Elasticsearch Service uses this policy to take periodic snapshots of your cluster. For more information, see the [Elasticsearch Service snapshot documentation](https://www.elastic.co/guide/en/cloud/current/ec-snapshot-restore.html).

##### SLM security

&emsp;&emsp;在开启Elasticsearch security feature后，下面的[cluster privilege](#####Cluster privileges)控制SLM动作的访问：

###### manage_slm

&emsp;&emsp;允许用户执行所有的SLM动作。包括创建/更新策略以及开始/停止SLM。

###### read_slm

&emsp;&emsp;允许用户执行所有的只读的SLM动作，例如获取策略以及检查SLM状态。

###### cluster:admin/snapshot/*

&emsp;&emsp;允许用户创建/删除任意的索引，无论是否有访问那个索引的权限

&emsp;&emsp;你可以通过Kibana创建/管理角色来分配这些privilege。

&emsp;&emsp;为了能保证必要的privilege来创建/管理SLM策略和快照，你可以设置一个拥有`manage_slm`和`cluster:admin/snapshot/*`privilege的角色以及访问SLM history index的全部访问权限。

&emsp;&emsp;下面的例子中，下面的请求创建了名为`slm-admin`的角色：

```text
POST _security/role/slm-admin
{
  "cluster": [ "manage_slm", "cluster:admin/snapshot/*" ],
  "indices": [
    {
      "names": [ ".slm-history-*" ],
      "privileges": [ "all" ]
    }
  ]
}
```

&emsp;&emsp;为了保证有SLM 策略和快照历史的只读访问权限，你可以设置一个拥有`read_slm`的集群privilege的角色以及SLM history index的读取访问权限。

&emsp;&emsp;下面的请求中创建了一个名为`slm-read-only`的角色：

```text
POST _security/role/slm-read-only
{
  "cluster": [ "read_slm" ],
  "indices": [
    {
      "names": [ ".slm-history-*" ],
      "privileges": [ "read" ]
    }
  ]
}
```

##### Create an SLM policy

&emsp;&emsp;若要在Kibana中管理SLM，进入主菜单并且点击**tack Management > Snapshot and Restore > Policies**，若要创建一个策略，则点击**Create Policy**。

&emsp;&emsp;你也可以使用[SLM APIs](###Snapshot lifecycle management APIs)管理SLM。使用[create SLM policy API](####Create or update snapshot lifecycle policy API)创建一个策略。

&emsp;&emsp;下面的请求创建了一个策略，该策略在**1:30 a.m.UTC**备份了集群状态、所有的data streams、以及所有的索引。

```text
PUT _slm/policy/nightly-snapshots
{
  "schedule": "0 30 1 * * ?",       
  "name": "<nightly-snap-{now/d}>", 
  "repository": "my_repository",    
  "config": {
    "indices": "*",                 
    "include_global_state": true    
  },
  "retention": {                    
    "expire_after": "30d",
    "min_count": 5,
    "max_count": 50
  }
}
```

&emsp;&emsp;第3行，使用[Cron syntax](#####Watcher cron schedule)定义创建快照的时间

&emsp;&emsp;第4行，快照的名字，支持[date math](####Date math support in index and index alias names)。为了防止命名冲突，这个策略在每个名字后面追加了一个UUID

&emsp;&emsp;第5行，[Registered snapshot repository](###Register a snapshot repository)用于存储这个策略的快照

&emsp;&emsp;第7行，这个策略的快照中包含了data stream和 index

&emsp;&emsp;第8行，如果为`true`，该策略的快照中包含集群状态（cluster state）。同时也默认包含所有的 feature state。若要只包含指定的feature states，见[Back up a specific feature state](####Back up a specific feature state)

&emsp;&emsp;第10行，保留规则（可选）。该配置将为快照保留30天，无论快照的寿命（age）是多少，至少保留5个以及最多50个快照。见[SLM retention](#####SLM retention)和[Snapshot retention limits](#####Snapshot retention limits)


##### Manually run an SLM policy

&emsp;&emsp;你可以手动运行一个SLM策略来立即创建一个快照。在升级前测试一个策略或者快照时很有用。手动运行一个策略不会影响它的快照定时计划（snapshot schedule）。

&emsp;&emsp;若要在Kibana中运行一个策略，计入**Policies**页面，在**Action**列下并且点击运行图标。你也可以使用[execute SLM policy API](####Execute snapshot lifecycle policy API)。

```text
POST _slm/policy/nightly-snapshots/_execute
```

&emsp;&emsp;快照程序在后台运行。若要监控其运行过程，见[Monitor snapshot](####Monitor a snapshot)。

##### SLM retention

&emsp;&emsp;SLM snapshot retention属于cluster-level的任务，独立于策略中的快照定时计划。若要控制SLM retention任务的运行时间，配置集群设置[slm.retention_schedule](######slm.retention_schedule)。

```text
PUT _cluster/settings
{
  "persistent" : {
    "slm.retention_schedule" : "0 30 1 * * ?"
  }
}
```

&emsp;&emsp;若要马上运行retention任务，使用[execute SLM retention policy API](####Execute snapshot retention policy API)。

```text
POST _slm/_execute_retention
```

&emsp;&emsp;SLM策略中的保留规则（retention rule）只会作用到使用这个策略创建的快照。其他快照不会受到（not count toward）这个策略的保留限制。

##### Snapshot retention limits

&emsp;&emsp;我们建议你在SLM策略中包含保留规则来删除你不再需要的快照。

&emsp;&emsp;快照仓库可以安全的扩展到上千个快照。然而，若要管理它的metadata，规模大的（large）仓库要求在master node上有更多的内存。保留策略能保证仓库的metadata一直增长而造成master node的不稳定。

#### Manually create a snapshot

&emsp;&emsp;若不使用SLM策略创建一个快照，则使用[create snapshot API](####Create snapshot API)，快照的名字支持[date math](####Date math support in index and index alias names)。

```text
# PUT _snapshot/my_repository/<my_snapshot_{now/d}>
PUT _snapshot/my_repository/%3Cmy_snapshot_%7Bnow%2Fd%7D%3E
```

&emsp;&emsp;快照需要花点时间时间完成，取决于它的大小。默认情况下，快照创建API用于初始化快照，快照的处理在后台运行。若要阻塞客户端直到快照完成，则将请求参数`wait_for_completion`设置为`true`。

```text
PUT _snapshot/my_repository/my_snapshot?wait_for_completion=true
```

&emsp;&emsp;你可以使用[clone snapshot API](####Clone snapshot API)克隆一个现有的快照。

#### Monitor a snapshot

&emsp;&emsp;若要监控当前正在运行的快照，使用参数`_current`的[get snapshot API](####Get snapshot API)。

```text
GET _snapshot/my_repository/_current
```

&emsp;&emsp;To get a complete breakdown of each shard participating in any currently running snapshots, use the [get snapshot status API](####Get snapshot API).

```text
GET _snapshot/_status
```

##### Check SLM history

&emsp;&emsp;若要获取某个集群SLM的执行历史（execution history），包括每一个SLM策略的统计，则使用[get SLM stats API](####Get snapshot lifecycle stats API)。这个API同样返回集群快照保留任务历史（retention task history）

```text
GET _slm/stats
```
&emsp;&emsp;若要获取指定的SLM策略的执行历史，则使用[get SLM policy API](####Get snapshot lifecycle policy API)。响应包括：

- 下一计划的策略执行
- 上一次成功开始进行快照的时间。开始进行快照不能保证快照的完成
- 上一次策略执行失败的时间，以及有相关的错误（if applicable）

```text
GET _slm/policy/nightly-snapshots
```

#### Delete or cancel a snapshot

&emsp;&emsp;若要在Kibana中删除一个快照，进入**Snapshots**页面并点击**Action**列下面的trash icon。你也可以使用[delete snapshot API](####Delete snapshot API)。

```text
DELETE _snapshot/my_repository/my_snapshot_2099.05.06
```

&emsp;&emsp;如果你删除一个正在处理中的快照，Elasticsearch会取消这个快照。快照的处理会被停止并删除为这个快照生成的文件。如果某些文件被其他快照使用，那么不会删除这些文件。

#### Back up configuration files

&emsp;&emsp;如果在你自己的硬件上运行Elasticsearch，我们建议作额外的备份，选择一个文件备份软件对每一个节点上的[$ES_PATH_CONF directory ](####Config files location)进行常规备份（regular backup）。快照不会备份这些文件。注意的是每一个节点上的这些文件可能会不同，所以节点上的文件需要各自备份。

> IMPORTANT：The elasticsearch.keystore, TLS keys, and [SAML](#####Realm settings), [OIDC](######OpenID Connect realm settings), and [Kerberos](######Kerberos realm settings) realms private key files contain sensitive information. Consider encrypting your backups of these files.

#### Back up a specific feature state

&emsp;&emsp;默认情况下，包含集群状态的快照也包含所有的[feature states](#####Feature states)。不包含集群状态的快照默认不包含所有的feature states。

&emsp;&emsp;你可以配置一个快照只包含指定的feature states，不用关心集群状态。

&emsp;&emsp;若要获取可用的feature信息，使用[get features API](####Get Features API)。

```text
GET _features
```

&emsp;&emsp;这个API的响应：

```text
{
  "features": [
    {
      "name": "tasks",
      "description": "Manages task results"
    },
    {
      "name": "kibana",
      "description": "Manages Kibana configuration and reports"
    },
    {
      "name": "security",
      "description": "Manages configuration for Security features, such as users and roles"
    },
    ...
  ]
}
```

&emsp;&emsp;若要在快照中包含指定的feature state，则在`feature_states`数组中指定feature的`name`。

&emsp;&emsp;例如，下面的SLM策略中包含Kibana和Elasticsearch security features的feature states。

```text
PUT _slm/policy/nightly-snapshots
{
  "schedule": "0 30 2 * * ?",
  "name": "<nightly-snap-{now/d}>",
  "repository": "my_repository",
  "config": {
    "indices": "*",
    "include_global_state": true,
    "feature_states": [
      "kibana",
      "security"
    ]
  },
  "retention": {
    "expire_after": "30d",
    "min_count": 5,
    "max_count": 50
  }
}
```

&emsp;&emsp;feature state对应的索引或者data stream都会在快照内容中展示出来。例如，如果你备份了`security` feature state，`security-*`这些系统索引会在[get snapshot API](####Get snapshot API)的`indices`和`feature_states`下展示。

#### Dedicated cluster state snapshots

&emsp;&emsp;一些feature states包含敏感数据。例如`security` feature state包含的系统索引包含了用户名字和加密后的密码。因为密码使用[cryptographic hashes](######User cache and password hash algorithms)存储，快照的泄露（disclosure）不会让第三方授权作为其中的一个用户或者使用API keys。然而，如果第三方可以更改快照，它们可以安装一个后门，则会丢失机密信息。

&emsp;&emsp;若要更好的保护数据，考虑为集群状态的快照创建一个专用的仓库SLM策略。这能让你严格限制以及审计仓库的访问。

&emsp;&emsp;例如，下面的SLM策略只备份集群状态。策略中使用了专用的仓库存储这些快照。

```text
PUT _slm/policy/nightly-cluster-state-snapshots
{
  "schedule": "0 30 2 * * ?",
  "name": "<nightly-cluster-state-snap-{now/d}>",
  "repository": "my_secure_repository",
  "config": {
    "include_global_state": true,                 
    "indices": "-*"                               
  },
  "retention": {
    "expire_after": "30d",
    "min_count": 5,
    "max_count": 50
  }
}
```

&emsp;&emsp;第7行，备份集群状态，同时包含所有的feature states

&emsp;&emsp;第8行，不包括常规的data stream和index

#### Create snapshots at different time intervals

&emsp;&emsp;如果你使用单个SLM策略，那就很难同时做到频繁创建快照并且长时间保留快照。

&emsp;&emsp;比如说，某个策略每30分钟创建一个快照同时快照数量最大值为100个，那么每个快照最多只能保留2天（(100 * 30) / 60 / 24 = 2.08天 ）的时间。这种设置对备份最新的改动是不错的，但是没法让你恢复一星期或者一个月之前的数据。

&emsp;&emsp;为了解决这个问题，你可以使用相同的仓库创建多个SLM策略，这些策略的执行计划间隔不一样。由于策略的保留规则只应用到这个策略生成的快照，某个策略不会删除其他策略创建的快照。

&emsp;&emsp;例如，下面的SLM策略每小时创建一次快照并最多保留24个快照。每个快照在这个策略中只保留一天。

```text
PUT _slm/policy/hourly-snapshots
{
  "name": "<hourly-snapshot-{now/d}>",
  "schedule": "0 0 * * * ?",
  "repository": "my_repository",
  "config": {
    "indices": "*",
    "include_global_state": true
  },
  "retention": {
    "expire_after": "1d",
    "min_count": 1,
    "max_count": 24
  }
}
```

&emsp;&emsp;下面的SLM策略每天创建一次快照并最多保留31个快照。每个快照在这个策略中只保留30天。

```text
PUT _slm/policy/daily-snapshots
{
  "name": "<daily-snapshot-{now/d}>",
  "schedule": "0 45 23 * * ?",          
  "repository": "my_repository",
  "config": {
    "indices": "*",
    "include_global_state": true
  },
  "retention": {
    "expire_after": "30d",
    "min_count": 1,
    "max_count": 31
  }
}
```

&emsp;&emsp;每天下午11点45 UTC运行。

&emsp;&emsp;下面的SLM策略每月创建一次快照并最多保留12个快照。每个快照在这个策略中只保留366天。

```text
PUT _slm/policy/monthly-snapshots
{
  "name": "<monthly-snapshot-{now/d}>",
  "schedule": "0 56 23 1 * ?",            
  "repository": "my_repository",
  "config": {
    "indices": "*",
    "include_global_state": true
  },
  "retention": {
    "expire_after": "366d",
    "min_count": 1,
    "max_count": 12
  }
}
```

&emsp;&emsp;每个月第一天的下午11点56分 UTC运行。


### Restore a snapshot
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/snapshots-restore-snapshot.html)

### Searchable snapshots
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/searchable-snapshots.html)

&emsp;&emsp;Searchable snapshots能让你使用[snapshots](##Snapshot and restore)以一种cost-effective的方式查询很少被访问并且是只读的数据。[cold](####Cold tier)和[frozen](####Frozen tier) data tiers使用Searchable snapshots来降低你的存储和操作开销。

&emsp;&emsp;Searchable snapshots消除了[replica shards](###Scalability and resilience: clusters, nodes, and shards)的需要，可能将搜索数据需要的本地空间减半（halve）。searchable snapshots依赖相同的快照机制，这个机制也用于备份并且对你的快照仓库存储开销有极小的影响。

#### Using searchable snapshots

&emsp;&emsp;查询一个searchable snapshot index跟查询其他索引是一样的。

&emsp;&emsp;默认情况下，searchable snapshot index没有副本分片（replica）。底层的快照提供弹性，由于对查询体量（query volume）的期望是很低的，所以单个分片就足够了。然而，如果你需要支持一个高查询体量，你可以通过调整索引设置`index.number_of_replicas`添加副本分片。

&emsp;&emsp;如果节点发生故障，searchable snapshot shards需要在其他地方恢复，这时候就会出一个短暂的（brief）的时间窗口，在这个时间窗口内，Elasticsearch将分片分配到其他节点，集群的颜色不再是`green`。在那个时间内查询这些分片可能会失败或者返回部分结果，直到将分片分配到健康的节点。

&emsp;&emsp;你通常会通过ILM来管理searchable snapshots。当常规（regular）索引到达`cold`或者`frozen`阶段， [searchable snapshots](####Searchable snapshot)动作自动将常规索引转化为searchable snapshot index。你也可以手动的通过使用[mount snapshot](####Mount snapshot API) API让现有的快照中的索引变成可以被搜索。

&emsp;&emsp;若要从快照中mount一个索引，但是快照中拥有很多的索引，我们建议你创建一个快照的[clone](####Clone snapshot API)，克隆后的快照中只包含你想要搜索的那个索引，并mount那个克隆。你不应该删除一个被mount的快照，所以创建一个克隆能你管理这个快照备份的生命周期，独立于其他任何的searchable snapshots。如果你使用ILM来管理你的searchable snapshots，ILM会在快照需要时自动的进行克隆。

&emsp;&emsp;你可以使用跟常规索引相同的机制来控制searchable snapshot index的分片的分配。例如，你可以使用[Index-level shard allocation filtering](####Index-level shard allocation filtering)来限制searchable snapshot shard分配到某些节点子集上。

&emsp;&emsp;searchable snapshot index的恢复速度受制于仓库设置`max_restore_bytes_per_sec`以及跟普通的恢复操作一样的节点设置`indices.recovery.max_bytes_per_sec`。默认情况下，`indices.recovery.max_bytes_per_sec`的是无限制的，但是`indices.recovery.max_bytes_per_sec`的默认值取决于节点的配置。见[Recovery settings](#####Recovery settings)。

&emsp;&emsp;我们建议在创建一个快照前，索引的每个分片都[force-merge](####Force merge API)到单个段中，这样快照将会被mount成一个searchable snapshot index。从快照仓库中的每一次读取都耗时耗钱，段的数量越少，恢复快照或者查询的响应时的读取次数越少。

> TIP：Searchable snapshot用来管理大型归档历史数据室是最理想的的办法。历史信息相比较最新的数据通常很少被查询，因此不需要副本分片来提高性能。
> 对于更多复杂或者耗时的查询，你可以在Searchable snapshots中使用[Async Searchable](####Async search)。

&emsp;&emsp;使用下面任意一个仓库类型来进行searchable snapshots：

- [AWS S3](####S3 repository)
- [Google Cloud Storage](####Google Cloud Storage repository)
- [Azure Blob Storage](####Azure repository)
- [Hadoop Distributed File Store (HDFS)](https://www.elastic.co/guide/en/elasticsearch/plugins/8.2/repository-hdfs.html)
- [Shared filesystems](####Shared file system repository) such as NFS
- [Read-only HTTP and HTTPS repositories](####Read-only URL repository)

&emsp;&emsp;You can also use alternative implementations of these repository types, for instance [MinIO](#####Client settings), as long as they are fully compatible. Use the [Repository analysis ](####Repository analysis API) API to analyze your repository’s suitability for use with searchable snapshots。

#### How searchable snapshots work

&emsp;&emsp;当从某个快照中mount了一个索引，Elasticsearch将它的分片分配到集群中的data node上。data node随后基于指定的[mount options](#####Mount options)，自动的从仓库中将相关的分片数据找回（retrieve）到本地储存。如果数据在本地不可见（available），Elasticsearch从快照仓库中下载需要的数据。

&emsp;&emsp;如果包含了分片的节点发生故障，Elasticsearch自动的将受到影响的分片分配到其他的节点，并且节点是通过仓库来恢复相关的分片数据。不需要副本分片（replica），不需要负载的监控或者orchestration用于恢复丢失的分片。尽管searchable snapshot index默认不需要副本分片，你也可以通过调整`index.number_of_replicas`来添加副本分片。searchable snapshot shard的副本分片是通过拷贝快照仓库中的数据获得，像是searchable snapshot primary shard。与此相反，常规索引的副本分片则是从主分片中拷贝而来。

##### Mount options

&emsp;&emsp;若要搜索一个快照，你必须首先mount快照到本地成为一个索引。通常ILM会自动完成。但你也可以自己调用[mount snapshot](####Mount snapshot API)。有两个选项从快照中mount出一个索引，它们都不同的性能特色（performance characteristic）和本地储存占用（local storage footprints）：

###### Fully mounted index

&emsp;&emsp;加载完整的被快照的索引分片（snapshotted index's shards）的拷贝到集群的本地节点存储上。ILM在`hot`和`cold`阶段使用这个选项。

&emsp;&emsp;fully mounted index的查询性能跟常规索引（regular index）相当，因为极小需要访问快照仓库。随着恢复的进行（while recover is ongoing），查询性能可能比常规索引要慢，因为某个查询所需要的数据还没有找回（retrieve）到本地。如果发生这种情况，Elasticsearch会急切的找回需要的数据来完成这次查询，并且是跟恢复过程并行处理。磁盘上的数据在重启后也会被保留，所以节点不需要在重启后重新下载已经存储在节点上的数据。

&emsp;&emsp;fully mount后，ILM管理的索引名都有`restored-`的前缀。

###### Partially mounted index

&emsp;&emsp;使用本地缓存，只包含最新查询的索引数据。该缓存有一个固定大小，其大小由frozen tier的所有节点共享。ILM在`frozen`阶段使用这个选项。

&emsp;&emsp;如果查询需要的数据不在缓存中，Elasticsearch会从快照仓库中获取丢失的数据。需要获取数据的查询会很慢，但是获取到的数据会存储在缓存中，以后类似的查询可以非常快。Elasticsearch会evict缓存中不常被使用的数据来释放空间。节点重启后，缓存会被清空。

&emsp;&emsp;尽管比fully mounted或者常规索引慢，partially mounted index仍然能很快的返回查询结果，即使是很大的数据集，因为仓库的数据层为查询进行了heavily optimization。许多的查询在返回结果前只需要检索分片数据总量的一个小的子集。

&emsp;&emsp;partiallly mounted后，ILM管理的索引名都有`partial-`的前缀。

&emsp;&emsp;若要partially mount an index，你必须要有一个或者多个节点上分片缓存可用 。默认情况下，专用的frozen data tier中的所有节点共享一个配置，使用磁盘总空间的90%和磁盘总空间与headroom（100GB）的差值。

&emsp;&emsp;强力推荐在生产使用中使用一个专用的frozen tier。如果没有，你必须配置`xpack.searchable.snapshot.shared_cache.size`为一个或者多个节点的缓存保留空间。Partially mounted indice只会分配到有分片缓存的节点。

- `xpack.searchable.snapshot.shared_cache.size`

&emsp;&emsp;（[Static](######Static（settings） )）为partially mounted index的分片缓存保留的磁盘空间大小。接收一个磁盘空间的百分比或者一个absolute [byte value](####Byte size units)。对于专用的frozen data tires中的节点，默认值是90%。其他默认是`0b`。

- `xpack.searchable.snapshot.shared_cache.size.max_headroom`

&emsp;&emsp;（[Static](######Static（settings） ) [byte value](####Byte size units)）对于专用frozen tier中的节点，headroom的最大值。如果`xpack.searchable.snapshot.shared_cache.size`没有显示设置， 则headroom设置为100GB。否则默认是`-1`（not set）。只有`xpack.searchable.snapshot.shared_cache.size`用百分比设置才能配置`xpack.searchable.snapshot.shared_cache.size.max_headroom`。

&emsp;&emsp;为了说明这些设置如何协同工作（work in concert）。下面两个例子是在一个专用的frozen node上使用了默认值：

- 4000GB的磁盘将产生3900GB的分片缓存。4000GB的90%是3600G，留下的空间是400G的headroom。由于默认的`max_headroom`是100GB，因此最终的结果是3900GB
- 400GB的磁盘会产生360GB的分片缓存

&emsp;&emsp;你可以在`elasticsearch.yml`中配置：

```text
xpack.searchable.snapshot.shared_cache.size: 4TB
```

> IMPORTANT：你只能在[data_frozen](#####Frozen data node)角色的节点上配置这些设置。另外，有分片缓存的节点只能有单个[data path](#####Path settings)。

&emsp;&emsp;Elasticsearch同样使用了一个名为`.snapshot-blob-cache`的专用系统索引来加速searchable snapshot的恢复。这个索引是位于partially or fully mounted data之上的缓存层，它包含了启动searchable snapshot shard的最少需要的数据。Elasticsearch自动删除索引中不再使用的文档。可以通过以下的设置来调节这个周期性的清除：

- searchable_snapshots.blob_cache.periodic_cleanup.interval

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）`.snapshot-blob-cache`索引周期性清除计划的间隔时间。默认值是每1小时 。

- searchable_snapshots.blob_cache.periodic_cleanup.retention_period

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）在`.snapshot-blob-cache`索引中保留过时文档的保留期。默认值是`100`。

- searchable_snapshots.blob_cache.periodic_cleanup.batch_size

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）`.snapshot-blob-cach`周期性清除时，每次为bulk-delete查询的文档数量。默认值是`10m`。

- searchable_snapshots.blob_cache.periodic_cleanup.pit_keep_alive

&emsp;&emsp;（[Dynamic](######Dynamic（settings）)）The value used for the <point-in-time-keep-alive,point-in-time keep alive>> requests executed during the periodic cleanup of the .snapshot-blob-cache index. Defaults to 10m.

#### Reduce costs with searchable snapshots

&emsp;&emsp;在大多数的情况下，searchable snapshot降低开销的方式是通过消除副本分片以及节点之间分片数据的拷贝。然而，如果从快照仓库中取回数据的开销是特别大的，那么searchable snapshot可能比常规索引有更多的开销。在使用之前，确保操作系统的cost structure和searchable snapshot是兼容的。

##### Replica costs

&emsp;&emsp;为了弹性（resiliency），常规索引要求在多个节点上有冗余的分片拷贝（shard copy）。如果节点发生故障，Elasticsearch使用冗余分片来重构丢失的分片拷贝。searchable snapshot不要求副本分片。如果包含searchable snapshot index的节点发生故障，Elasticsearch会从快照仓库中重建丢失的分片拷贝 。

&emsp;&emsp;没有副本分片后，很少被访问的searchable snapshot index要求非常少（far fewer）的资源。包含replica-free fully-mounted的searchable snapshot index要求的节点和磁盘空间只有常规索引的一半。只包含partially-mounted searchable snapshot index的frozen tier甚至只要更少的资源。

##### Data transfer costs

&emsp;&emsp;当常规索引的分片在节点间移动时，分片的内容从集群中的另一个节点拷贝。在许多环境中，在节点间移动数据的开销是很大的。特别是在节点位于不同区域的云环境中运行时。相反的是，当mount一个searchable snapshot index或者移动其中一个分片时，总是从快照仓库中拷贝。通常来说成本是很低的。

> WARNING：Most cloud providers charge significant fees for data transferred between regions and for data transferred out of their platforms. You should only mount snapshots into a cluster that is in the same region as the snapshot repository. If you wish to search data across multiple regions, configure multiple clusters and use [cross-cluster search](###Search across clusters) or [cross-cluster replication](###Cross-cluster replication) instead of searchable snapshots.

#### Back up and restore searchable snapshots

&emsp;&emsp;你可以使用[regular snapshots](###Create a snapshot)备份一个包含searchable snapshot index的集群。当你恢复一个包含searchable snapshots index的快照后，这些索引又再次恢复成searchable snapshot index。

&emsp;&emsp;在你恢复一个包含searchable snapshot index的快照之前，你必须首先[register ](###Register a snapshot repository)，它包含最初的索引快照（original index snapshot）。在恢复时，searchable snapshot index从最初的索引快照中挂载索引快照。你可以使用不同的仓库分别用于常规快照（regular snapshot）和searchable snapshot。

&emsp;&emsp;searchable snapshot index的快照中只包含了少量的metadata用来识别它的original index snapshot，它不包含原始索引中的任何数据。如果original index snapshot不可见，那么在恢复searchable snapshot indices时会失败。

&emsp;&emsp;由于searchable snapshot index不是常规索引，所以她不能使用[source-only repository](####Source-only repository)对searchable snapshot indices生成快照。

##### Reliability of searchable snapshots

&emsp;&emsp;searchable snapshot index的数据拷贝依赖的是仓库中的快照。如果仓库出现故障或者快照损坏会导致数据丢失。尽管Elasticsearch在本地储存中有这些数据的副本（copy），这些副本可能是不兼容的并且在仓库发生故障时不能用于恢复数据。你必须保证你的仓库是可靠的并且保护仓库里面的数据不受到破坏。

&emsp;&emsp;主流公共云提供商的blob storage都能很好的提供对数据丢失或者破坏的保护，如果你自己管理仓库，那你需要负责其可靠性。

## Secure the Elastic Stack
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/secure-cluster.html)

### Manually configure security
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/manually-configure-security.html)

#### Set up basic security for the Elastic Stack
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/security-basic-setup.html#encrypt-internode-communication)

##### Encrypt internode communications with TLS

#### Security files
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/security-files.html)

### Enable audit logging
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/enable-audit-logging.html)

#### Audit events
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/audit-event-types.html)

#### Logfile audit events ignore policies
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/audit-log-ignore-policy.html)

#### Auditing search queries
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/auditing-search-queries.html)

### User authentication
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/setting-up-authentication.html)

#### Built-in roles
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/built-in-roles.html)

##### remote_monitoring_agent

#### Native user authentication
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/native-realm.html)

#### Built-in users
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/built-in-users.html)

### User authorization
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/authorization.html)

#### Defining roles
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/defining-roles.html)

#### Security privileges
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/security-privileges.html#privileges-list-indices)

##### Cluster privileges

##### Indices privileges

#### Submitting requests on behalf of other users
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/run-as-privilege.html)

## Watcher
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/xpack-alerting.html)

### Watcher triggers
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/trigger.html)

#### Watcher schedule trigger
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/trigger-schedule.html#trigger-schedule)

## Command line tools
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/commands.html)

### elasticsearch-node
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/node-tool.html#node-tool-repurpose)

#### Changing the role of a node

### elasticsearch-reset-password
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/reset-password.html)

## How to
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/how-to.html)

&emsp;&emsp;Elasticsearch附带了默认的配置目的是想让用户有一个很好的开箱体验，用户应该在不需要做任何更改的情况下就可以使用全文搜索（full  text search），高亮、聚合和索引这些功能。

&emsp;&emsp;一旦你更好的理解掌握了Elasticsearch后，你可以使用一些优化根据你自己的用例做性能的提升。

&emsp;&emsp;这一章节会指导你哪些变更是应该做以及不应该做。

### General recommendations
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/general-recommendations.html)

#### Don’t return large result sets

&emsp;&emsp;Elasticsearch 被设计为一个搜索引擎，这使得它非常擅长取回与查询匹配的top documents。然而将Elasticsearch作为数据库领域的那种工作负载是不太好的，比如返回满足某个查询的所有文档。如果你真的需要这么做，确保使用[Scroll](####Scroll search results) API来实现。

#### Avoid large documents

&emsp;&emsp;默认的[http.max_content_length](######http.max_content_length)的值为100M。Elasticsearch会拒绝索引大于该值的文档。你可能想要增加这个特定的设置，但 Lucene 仍然有大约 2GB 的限制。

&emsp;&emsp;即使抛开这些硬性的限制，索引一个large document不是一个很好的实践。large document会对网络、内存使用量和硬盘造成更多的压力。即使查询请求中不要求查询`_source`，Elasticsearch在所有情况下都需要获取文档的`_id`，而获取large document的这个域的开销由于文件系统缓存的原因会更大。索引这个文档可能使用的内存量是文档原始大小的倍数。Proximity search（phrase queries for instance）和[highlighting](###Highlighting)同样会变得开销昂贵，因为它们的开销直接取决于原始文档的大小。

&emsp;&emsp;It is sometimes useful to reconsider what the unit of information should be。例如，如果你想要让一本书可以被搜索到，并不是意味着文档中包含这一整本书的内容。一种可能比较好的方式是使用章节（chapter）或者甚至是段落（paragraph）作为一篇文档，并且在这个文档中有其他的属性（property）来标识属于哪一本书。这种方式不仅仅会避免索引large document，同时也使得查询体验会更好。比如说用户想要查询两个单词`foo`和`bar`。跨章节的匹配结果可能较差，而段落中同时出现这两个单词的匹配结果则看起来是好的。的

### Recipes
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/recipes.html)

&emsp;&emsp;这一章节包含了一些用于解决常见问题的方法：

- [Mixing exact search with stemming](####Mixing exact search with stemming)
- [Getting consistent scores](####Getting consistent scoring)
- [Incorporating static relevance signals into the score](####Incorporating static relevance signals into the score)

#### Mixing exact search with stemming
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/mixing-exact-search-with-stemming.html)

&emsp;&emsp;在构建一个查询应用时，stem通常是必要的，因为在查询`skiing`时会渴望匹配到包含`ski`或者`skis`的文档。但如果用户就想专门查询`skiing`呢？通常的做法就是使用[multi-field](####fields)使得对相同的内容用两种不同的方式进行索引：

```text
PUT index
{
  "settings": {
    "analysis": {
      "analyzer": {
        "english_exact": {
          "tokenizer": "standard",
          "filter": [
            "lowercase"
          ]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "body": {
        "type": "text",
        "analyzer": "english",
        "fields": {
          "exact": {
            "type": "text",
            "analyzer": "english_exact"
          }
        }
      }
    }
  }
}

PUT index/_doc/1
{
  "body": "Ski resort"
}

PUT index/_doc/2
{
  "body": "A pair of skis"
}

POST index/_refresh
```

&emsp;&emsp;根据这个设置，在`body`上查询`ski`会同时返回这两篇文档。

```text
GET index/_search
{
  "query": {
    "simple_query_string": {
      "fields": [ "body" ],
      "query": "ski"
    }
  }
}
```

```text
{
  "took": 2,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped" : 0,
    "failed": 0
  },
  "hits": {
    "total" : {
        "value": 2,
        "relation": "eq"
    },
    "max_score": 0.18232156,
    "hits": [
      {
        "_index": "index",
        "_id": "1",
        "_score": 0.18232156,
        "_source": {
          "body": "Ski resort"
        }
      },
      {
        "_index": "index",
        "_id": "2",
        "_score": 0.18232156,
        "_source": {
          "body": "A pair of skis"
        }
      }
    ]
  }
}
```

&emsp;&emsp;另外，在`body.exact`上查询`ski`将只返回文档1，因为`body.exact`的analysis chain不会执行stemming。

```text
GET index/_search
{
  "query": {
    "simple_query_string": {
      "fields": [ "body.exact" ],
      "query": "ski"
    }
  }
}
```

```text
{
  "took": 1,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped" : 0,
    "failed": 0
  },
  "hits": {
    "total" : {
        "value": 1,
        "relation": "eq"
    },
    "max_score": 0.8025915,
    "hits": [
      {
        "_index": "index",
        "_id": "1",
        "_score": 0.8025915,
        "_source": {
          "body": "Ski resort"
        }
      }
    ]
  }
}
```

&emsp;&emsp;This is not something that is easy to expose to end users，我们需要找出一个方法来知道用户是想要查找一个精确匹配（exact match），如果不是的话我们就重定向到一个合适的域。还需要考虑的是如果查询中的一部分需要精确匹配而其他部分仍然要stemming？

&emsp;&emsp;幸运的是，`query_string`和`simple_query_string`有一个功能来解决这个exact problem: `quote_field_suffix`。这个参数会告诉Elasticsearch被双引号包起来的词需要重定向到另一个不同的域，如下所示：

```text
GET index/_search
{
  "query": {
    "simple_query_string": {
      "fields": [ "body" ],
      "quote_field_suffix": ".exact",
      "query": "\"ski\""
    }
  }
}
```

```text
{
  "took": 2,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped" : 0,
    "failed": 0
  },
  "hits": {
    "total" : {
        "value": 1,
        "relation": "eq"
    },
    "max_score": 0.8025915,
    "hits": [
      {
        "_index": "index",
        "_id": "1",
        "_score": 0.8025915,
        "_source": {
          "body": "Ski resort"
        }
      }
    ]
  }
}
```

&emsp;&emsp;在上面的例子中，`ski`使用了双引号（in-between quotes），根据`quote_field_suffix`参数将在`body.exact`域上查询。所以只有文档1返回。这允许用户基于自己喜好混合查询exact search和 stemming search。

> NOTE：如果`quote_field_suffix`中的字段不存在，那么查询就会回滚（fall back）到使用默认的域进行查询。

#### Getting consistent scoring
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/consistent-scoring.html)

&emsp;&emsp;Elasticsearch在执行跨分片和副本的文档打分操作时会增加一定的挑战。

##### Scores are not reproducible

&emsp;&emsp;比如说同一个用户执行两次相同的列和文档的查询（the same request twice in a row and documents），但是返回的结果是不一样的，这是一种很差的体验对吗？事实上如果你有多个副本时（[index.number_of_replicas](#####index.number_of_replicas)大于0）是有可能发生这种情况的。原因是Elasticsearch在选择分片时采用了一种循环（round-robin）的方式，所以两次相同的列和文档的查询会在同一个分片的不同的两个副本上执行。

&emsp;&emsp;那为什么这会产生问题呢？Index statistics是打分的重要的组成部分。由于被删除的文档（deleted documents）的存在使得同一个分片的不同副本上的index statistics可能是不相同的。也许你知道当文档被删除或者更新后，旧的的文档不会从索引中移除，这些旧的文档只是被标记为被删除的（deleted）并且只有在所属的段下次被合并后才会被移除。然而由于生产实践的原因（However for practical reasons），index statistics会包含这些被删除的文档。所以考虑这种情况，当主分片刚刚完成了一次合并并且移除了很多被删除的文档，使得它的index statistic跟分片有很大的不同（分片中依旧有很多被删除的文档）导致打分也是不同的。

&emsp;&emsp;一种推荐的方式是使用一个字符串（用户的id或者session）来确认登陆的用户的身份作为一个[preference](######preference)，使得给定的用户总是去命中相同的分片，最终多个查询的打分始终是一致的。

&emsp;&emsp;这种work around还有别的好处：当两篇文档的打分相同时，他们会默认的根据lucene内部id（跟`_id没有关系`）进行排序。然而这些lucene内部id在同一个分片的不同副本中可能是不同的。所以通过总是命中相同的分片，使得相同打分值的文档总是有一致的排序结果。

##### Relevancy looks wrong

&emsp;&emsp;如果你注意到两篇内容一样的文档会得到不同的分数或者一次exact match不会让文档的排序靠前，这个问题可能跟sharding相关。默认情况下，Elasticsearch会让每一个分片负责生成自己的分数。然而由于index statistic对打分有重要的贡献，所以只有分片间有相同的index statistics才能工作正常。这个假设说的是文档会默认被均匀的（evenly）的路由到不同的分片，这些分片的index statistic应该是非常相似的那么就会得到期望的分数。然而如果有下面的假设：

- 要么在索引期间使用路由（use routing at index time）
- 查询多个索引（query multiple indices）
- 或者你的索引中数据很少

&emsp;&emsp;不然很大的可能会让参与查询请求的所有分片有不相同的index statistics使得相关性会很差。

&emsp;&emsp;如果是一个较小的数据集，那么你可以把所有的东西都索引到只有一个副本的分片（[index.number_of_shards](####index.number_of_shards): 1）中来work around这个问题，这是默认的配置。所有的文档有相同的index statistics并且分数是将保持一致。

&emsp;&emsp;其他推荐的方式就是使用[dfs_query_then_fetch](######search_type)的查询类型来work around这个问题。这使得Elasticsearch会遍历所有涉及的分片，向这些分片索要跟这次请求相关的index statistics，然后coordinating node会合并这些index statistics，伴随着查询将合并后的index statistics发给查询的分片，这样分片就可以使用全局的index statistics而不是自身的index statistics进行打分。

&emsp;&emsp;大多数情况下，这个额外的round trip开销非常小。然而有些查询如果包含了数量很多的域或者term，由于所有的term都需要从[terms dictionary](https://www.amazingkoala.com.cn/Lucene/suoyinwenjian/2019/0401/43.html)中查找并收集统计值使得开销不会很小。

#### Incorporating static relevance signals into the score
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/static-scoring-signals.html)

&emsp;&emsp;有些领域具有static single并且它跟相关性有关。比如说[PageRank](https://en.wikipedia.org/wiki/PageRank)跟url长度都是用于web查询的两个通用的功能来调整对网页的打分，并且独立于查询条件。

&emsp;&emsp;目前有两种查询允许对静态分数跟文本相关性进行组合。比如跟BM25一起打分：

- [script_score](####Script score query) query 
- [rank_feature](####Rank feature query) query

&emsp;&emsp;比如说你有一个`pagerank`域，你希望跟BM25进行组合，并且最终的打分公式为：`score = bm25_score + pagerank / (10 + pagerank)`。

&emsp;&emsp;使用[script_score](####Script score query) query 的话如下所示：

```text
GET index/_search
{
  "query": {
    "script_score": {
      "query": {
        "match": { "body": "elasticsearch" }
      },
      "script": {
        "source": "_score * saturation(doc['pagerank'].value, 10)" 
      }
    }
  }
}
```

&emsp;&emsp;第9行，`pagerank`必须使用[Numeric](####Numeric field types)类型。

&emsp;&emsp;使用[rank_feature](####Rank feature query) query 的话如下所示：

```text
GET _search
{
  "query": {
    "bool": {
      "must": {
        "match": { "body": "elasticsearch" }
      },
      "should": {
        "rank_feature": {
          "field": "pagerank", 
          "saturation": {
            "pivot": 10
          }
        }
      }
    }
  }
}
```

&emsp;&emsp;第10行，`pagerank`必须是[rank_feature](####Rank feature field type)域。

&emsp;&emsp;两种方式都可以获得相同的分数，但是各有取舍（trade-off）：[script_score](####Script score query) query 提供了更多的灵活性，使得可以让你用文本相关性分数跟你想要的static singles进行组合。另外[rank_feature](####Rank feature query) query只暴露（expose）了一些方法将static singles纳入到分数中。然而它基于[rank_feature](####Rank feature field type)跟[rank_features](####Rank features field type)域，这两个域的域值通过了特殊的方式进行索引，使得[rank_feature](####Rank feature query) query可以跳过不具竞争力（non-competitive）的文档并快速的返回top match。


### Tune for indexing speed
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/tune-for-indexing-speed.html)

#### Use bulk requests

&emsp;&emsp;Bulk request能获得比每次只索引一篇文档更高的性能。为了能找出bulk request中包含最合适的文档数量，你应该在一个单节点的单个分片上做基准测试（benchmark）。首先一次性索引100篇文档，然后200、400篇。每次的基准测试都将索引的文档数量翻倍。当索引的速度趋于平缓（start to plateau）那么说明针对你的数据类型已经达到了bulk request包含的最合适的文档数量。In case of tie, it is better to err in the direction of too few rather than too many documents。需要注意的是当并发发送large bulk request时，可能会对集群造成内存的压力，所以建议避免每次请求发送几十兆（a couple tens of megabytes）的数据，即使这些请求看起来性能没有问题。

#### Use multiple workers/threads to send data to Elasticsearch

&emsp;&emsp;单线程发送bulk request不大可能最大化Elasticsearch集群的索引能力。为了能使用集群所有的资源，你应该使用多线程或者多进程来发送数据。另外，除了可以更好的利用集群的资源，也应该会有助于降低每个 fsync 的成本。

&emsp;&emsp;确保观察`TOO_MANY_REQUESTS (429)`的响应码（`EsRejectedExecutionException` with the Java client）。Elasticsearch通过这个响应码告诉你它处理的速度赶不上当前的索引速率。当这个情况发生后，你应该暂停下索引再进行重试，ideally with randomized exponential backoff。

&emsp;&emsp;跟量化bulk request中的文档数量一样，只有通过测试才能知道优化后的worker的数量。可以通过持续的增加worker的数量直到集群中I/O or CPU达到饱和（saturate）。

#### Unset or increase the refresh interval

&emsp;&emsp;让变更的内容对搜索可见的操作称为[refresh](####Refresh API)，这是一项开销很大的操作。频繁的refresh会对同时进行中的索引速率造成影响。

&emsp;&emsp;默认情况下，Elasticsearch周期性的每一秒执行refresh。但只有最近30秒收到一个或者请求的索引才会按这种方式进行refresh。

&emsp;&emsp;这是一个优化配置，如果没有或者有很少的搜索流量（search traffic，5分钟中有一次或者没有查询请求）就可以优化索引的速度。这个做法目的是在没有查询请求时能优化bulk Indexing。可以通过显示的设置refresh的间隔来opt out这种方式。

&emsp;&emsp;另外，如果你的索引用于常规的查询请求（if your index experiences regular search requests），默认的行为意味着Elasticsearch每隔一秒种就会执行一次refresh。如果你能够承受（afford to）文档被索引后和对搜索可见之间的时间间隔，提高[index.refresh_interval](#####index.refresh_interval)的值，比如说30s，以此来提高索引速度。

#### Disable replicas for initial loads

&emsp;&emsp;如果你想要马上将大量的数据写入到Elasticsearch中，可以将`index.number_of_replicas`设置为0来提高索引速度。没有副本（replica）意味着丢失单个节点后会遇到数据丢失，所以很重要的一点是数据在其他地方是live使得在最初的导入（initial load）在遇到这种情况时可以通过重试来解决这个问题。最初的导入完成后，你再把`index.number_of_replicas`的值设置为原来的值。

&emsp;&emsp;如果在index settings中设置了`index.refresh_interval`，可以更助于在最初导入时先unset然后在完成后重新设置为原来的值。

#### Disable swapping(how to)

&emsp;&emsp;You should make sure that the operating system is not swapping out the java process by [disabling swapping](####Disable swapping).

#### Give memory to the filesystem cache

&emsp;&emsp;文件系统缓存（filesystem cache）用于缓存I/O操作，你应该确保将运行 Elasticsearch 的机器的至少一半内存分配给文件系统缓存（You should make sure to give at least half the memory of the machine running Elasticsearch to the filesystem cache）。

#### Use auto-generated ids

&emsp;&emsp;当索引一个带有显示id（explicit id）的文档时，Elasticsearch会检查分片中是否有跟这个id一样的文档，这是一种开销大的操作并且随着索引量的增加开销会增加。通过自动生成的id，Elasticsearch会跳过检查来提高索引速度。

#### Use faster hardware

&emsp;&emsp;如果索引受 I/O 限制（I/O-bound），考虑增加文件系统缓存的大小（见上文）或使用更快的存储。Elasticsearch通常创建独立的文件并顺序写入（sequential write），然而索引过程中会并发写入多个文件，所以是随机和顺序写入的混合模式。因此SSD驱动器的性能往往比旋转磁盘（spinning disk）好。

#### Indexing buffer size

&emsp;&emsp;如果你的节点执行繁重的索引（heavy Indexing），确保[indices.memory.index_buffer_size](####Indexing buffer settings)设置的足够大，让每一个正在执行繁重索引的分片最多拥有512M Indexing buffer（超过这个值通常不会再有提升）。Elasticsearch会让一个节点上所有活动的分片（active shard）都分享这个设置（a percentage of the java heap or an absolute byte-size）。非常活跃的分片自然的会比执行轻量级索引（lightweight indexing）的分片更多地使用这个缓冲区。

&emsp;&emsp;默认值是10%，这个值通常是够用的：如果你分配给JVM 10G内存，那么1G的内存将会用于index buffer。这个值足够一个节点上的两个分片来执行繁重的索引。

#### Use cross-cluster replication to prevent searching from stealing resources from indexing

&emsp;&emsp;单个集群中，查询和搜索会为了资源进行竞争（compete for resources），通过设置两个集群并配置[cross-cluster replication](###Cross-cluster replication)，将副本数据从一个集群复制到另一个集群中。并将所有的查询请求路由到follower index所在的集群，查询行为（search activity）将不再跟leader index所在的集群的索引（Indexing）抢夺资源。

#### Additional optimizations

&emsp;&emsp;另外在[Tune for disk usage](###Tune for disk usage)中提供了许多的策略也能提高索引的速度。

### Tune for search speed
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/tune-for-search-speed.html)


##### Give memory to the filesystem cache

&emsp;&emsp;Elasticsearch非常依赖（heavily depend on）文件系统缓存来实现快速的查询。一般情况下你要让一半的内存用于文件系统缓存使得Elasticsearch让hot regions of the index保持在物理内存中。

##### Avoid page cache thrashing by using modest readahead values on Linux

&emsp;&emsp;查询会带来很多随机I/O访问。当底层的块设备（underlying block device）有大量的预读值（readhead value）时，会有很多不必要的I/O，特别是文件通过mmap（[memory mapping](#####File system storage types) ）方式访问时。

&emsp;&emsp;大多数的linux发行版会中的单个plain device会使用一个合理的`128KiB`的预读值。然而使用了[software raid](https://en.wikipedia.org/wiki/RAID#SOFTWARE)、[LVM](https://en.wikipedia.org/wiki/Logical_volume_management)或者[dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt)后可能会导致块设备（用于支持Elasticsearch的[path.data](#####Path settings)）使用一个很大的预读值（在几个 MiB 的范围内）。这通常会导致严重的页面（文件系统）缓存抖动（page cache thrashing adversely），从而对搜索（或[update](###Document APIs)）性能产生不利影响。

&emsp;&emsp;你可以使用`lsblk -o NAME,RA,MOUNTPOINT,TYPE,SIZE`检查当前以字节为单位的预读值。参考Linux发行版的文档来了解如何更改预读值（比如使用`udev`命令设置为一个常量，需要重启，或者通过[blockdev --setra](https://man7.org/linux/man-pages/man8/blockdev.8.html)设置为一个临时的值），我们建议你将该值设置为`128KiB`。

> NOTE：`blockdev`期望的是512个字节为一个扇区（section）的值然而`lsblk`的值是以`KiB`为单位的。如果要为`/dev/nvme0n1`的预读值临时设置为`128KiB`，可以这么指定：`blockdev --setra 256 /dev/nvme0n1`。

##### Use faster hardware

&emsp;&emsp;如果查询受 I/O 限制（I/O-bound），考虑增加文件系统缓存的大小（见上文）或使用更快的存储。每一个查询涉及到在多个文件上进行随机和顺序的读取，并且可能在每一个分片上并发的进行查询。因此SSD驱动器的性能往往比旋转磁盘（spinning disk）好。

&emsp;&emsp;Directly-attached类型的存储（local storage）通常比远端储存（remote storage）有更好的性能因为它配置更方便并且避免了communications overheads。通过精心的调整，远端储存也可以达到可接受的性能要求。使用真实的工作负载进行基准测试来检测你用于调整的参数。如果达不到你期望的性能要求，跟存储的供应商一起找出问题所在。

&emsp;&emsp;如果查询受CPU限制，考虑使用更多更快的cpu。

##### Document modeling

&emsp;&emsp;可以对文档进行建模（Documents should be modeled），使得search-time的操作的开销尽可能的少。

&emsp;&emsp;特别是尽量避免join，[nested](####Nested field type)会以好几倍（several times）的代价降低查询速度并且[parent-child relations](####Join field type)会降低几百倍（hundreds of times）的查询速度。所以如果能通过重新标准化文档（denormalizing document）而不是用join解决同样的问题，那么可以获得非常好的查询速度。

##### Search as few fields as possible

&emsp;&emsp;[query_string](####Query string query)或者[multi_match](####Multi-match query)查询的域越多，查询越慢。一个常用的用于提高查询速度的技术就是在索引阶段将多个域的值索引到单个域，然后只查询这个域。可以通过[copy_to](####copy_to)实现自动的映射并且不需要更改源文档。下面的例子是一个包含电影的索引，该例子通过将电影的名字跟情节索引到`name_and_plot`域来优化查询。

```text
PUT movies
{
  "mappings": {
    "properties": {
      "name_and_plot": {
        "type": "text"
      },
      "name": {
        "type": "text",
        "copy_to": "name_and_plot"
      },
      "plot": {
        "type": "text",
        "copy_to": "name_and_plot"
      }
    }
  }
}
```

##### Pre-index data

&emsp;&emsp;你应该利用查询中的模式（pattern）来优化索引的方式。如果你的文档中有`price`域并且大部分的查询是指定了多个范围的[range](####Range aggregation)聚合，你可以将范围信息写入到索引中，然后使用[term](####Terms aggregation)聚合来提高聚合的速度。

&emsp;&emsp;比如说文档长这个样子：

```text
PUT index/_doc/1
{
  "designation": "spoon",
  "price": 13
}
```

&emsp;&emsp;并且查询请求长这个样子：

```text
GET index/_search
{
  "aggs": {
    "price_ranges": {
      "range": {
        "field": "price",
        "ranges": [
          { "to": 10 },
          { "from": 10, "to": 100 },
          { "from": 100 }
        ]
      }
    }
  }
}
```

&emsp;&emsp;然后这篇文档可以在索引期间丰富一个`price_range`字段，并且应该设为[keyword](####Keyword type family)类型。

```text
PUT index
{
  "mappings": {
    "properties": {
      "price_range": {
        "type": "keyword"
      }
    }
  }
}

PUT index/_doc/1
{
  "designation": "spoon",
  "price": 13,
  "price_range": "10-100"
}
```

&emsp;&emsp;然后查询请求可以在新的域上进行聚合查询而不是在`price`上执行`range`聚合。

```text
GET index/_search
{
  "aggs": {
    "price_ranges": {
      "terms": {
        "field": "price_range"
      }
    }
  }
}
```

##### Consider mapping identifiers as keyword

&emsp;&emsp;不是所有的数值类型的数据都必须用[numeric](####Numeric field types)类型的。Elasticsearch为`integer`、`long`这些数值类型的域优化了[range](####Range query
)查询。然而[keyword](####Keyword type family)域更适合用于[term](####Term query)或者[term-level](####Terms query)的查询。

&emsp;&emsp;类似ISBN或者产品ID的唯一标示（identifier），它们很少用于范围查询，然而它们经常使用term-level的查询。

&emsp;&emsp;可以在下列的场景中考虑对数值类型的唯一标示使用keyword：

- 你不计划对唯一标示的数据使用range查询
- 快速检索是非常重要的。在`keyword`上执行`term`查询通常比在数值类型的域类型上更快

&emsp;&emsp;如果你不确定怎么使用，可以通过[multi-field](####fields) 将数据同时索引为`keyword`或者数值类型的域。

##### Avoid scripts

&emsp;&emsp;如果可以的话，避免在聚合中使用脚本、基于[脚本](##Scripting)的排序以及[script_score](####Script score query)查询。见[Scripts, caching, and search speed](####Scripts, caching, and search speed)。

##### Search rounded dates

&emsp;&emsp;在时间类型的域上使用`now`的查询通常是无法缓存的，因为匹配的时间范围一直在发生变更。然而在一些用户体验中对时间进行四舍五入（rounded）是可接受的，使得可以利用查询缓存的功能带来的好处。

&emsp;&emsp;比如说下面这个查询：

```text
PUT index/_doc/1
{
  "my_date": "2016-05-11T16:30:55.328Z"
}

GET index/_search
{
  "query": {
    "constant_score": {
      "filter": {
        "range": {
          "my_date": {
            "gte": "now-1h",
            "lte": "now"
          }
        }
      }
    }
  }
}
```

&emsp;&emsp;上面这个查询可以替换为下面这个：

```text
GET index/_search
{
  "query": {
    "constant_score": {
      "filter": {
        "range": {
          "my_date": {
            "gte": "now-1h/m",
            "lte": "now/m"
          }
        }
      }
    }
  }
}
```

&emsp;&emsp;在上面的例子中， 我们对`分钟`进行了四舍五入，如果当前时间是`16:31:29`，这个范围查询将会匹配`my_date`域上所有在`15:31:00` 和 `16:31:59`之间的文档。如果一些用户执行了在同一`分钟`内的查询，查询缓存（query cache）会帮助提升一点查询速度。The longer the interval that is used for rounding, the more the query cache can help，但是注意的是过于激进的（too aggressive）四舍五入会降低用户的体验。

> NOTE：也可以尝试将查询范围切分为一个范围大可以缓存的部分和范围小但是不可以缓存的部分，这样就可以利用好查询缓存：

```text
GET index/_search
{
  "query": {
    "constant_score": {
      "filter": {
        "bool": {
          "should": [
            {
              "range": {
                "my_date": {
                  "gte": "now-1h",
                  "lte": "now-1h/m"
                }
              }
            },
            {
              "range": {
                "my_date": {
                  "gt": "now-1h/m",
                  "lt": "now/m"
                }
              }
            },
            {
              "range": {
                "my_date": {
                  "gte": "now/m",
                  "lte": "now"
                }
              }
            }
          ]
        }
      }
    }
  }
}
```

&emsp;&emsp;然而这种方式在实践中可能会因为引入`bool`带来开销导致查询速度变慢，破坏了更好地利用查询缓存所节省的成本。

##### Force-merge read-only indices

&emsp;&emsp;那些只读的索引可以从[merged down to a single segment](####Force merge API)中受益，这类索引通常是基于时间的索引（time-base indices）：只有当前时间范围的索引会获取新文档，而旧索引是只读的。那些被force-merged到单个段的分片可以使用简单并且高效的数据结构来执行查询操作。

> IMPORTANT：不要force-merge索引到你正在写入的索引中，或者以后你会再次写入的索引中。而是依赖后台合并进程按需合并来保持索引平滑运行。如果你继续往一个合并后的索引中写数据会另性能变差。

##### Warm up global ordinals

&emsp;&emsp;[Global ordinals](####eager_global_ordinals)是一个用来优化聚合的数据结构。它会被延迟计算并且保存在JVM堆中作为[field data cache](####Field data cache settings)的一部分。用于频繁桶聚合（heavily used for bucket aggregation）的域，可以在查询这些域之前，让Elasticsearch对这些域构造并且缓存global ordinals。注意的是这种操作会增加堆内存的使用并且增加[refreshes](####Refresh API) 的时间。可以通过mapping参数eager global ordinals对现有的mapping进行动态的更新。

```text
PUT index
{
  "mappings": {
    "properties": {
      "foo": {
        "type": "keyword",
        "eager_global_ordinals": true
      }
    }
  }
}
```

##### Warm up the filesystem cache

&emsp;&emsp;如果运行Elasticsearch的服务器重启了，那文件系统缓存会被清空。 所以需要一定的时间让操作系统将hot regions of the index 载入到内存中，使得可以查询操作可以变快。你可以使用[ index.store.preload](####Preloading data into the file system cache) 设置明确地告诉操作系统哪些文件应该根据文件扩展名尽快的（eagerly）加载到内存中。

>WARNING：如果把太多的索引跟文件载入到内存中，并且文件系统缓存没有那么大来容下所有的数据，则会导致查询速度变慢。


##### Use index sorting to speed up conjunctions（how to）

&emsp;&emsp;[Index sorting](###Index Sorting)可以提高交集（conjunctions）查询的性能，代价是降低索引（Indexing）的速度。查看[index sorting documentation](####Use index sorting to speed up conjunctions)了解更多内容。

##### Use preference to optimize cache utilization

&emsp;&emsp;有多种类型的缓存可以用来提高查询性能，比如[filesystem cache](https://en.wikipedia.org/wiki/Page_cache)、[request cache](####Shard request cache settings)、以及 [query cache](####Node query cache settings)。目前所有的缓存在节点层进行维护。如果你连续（in a row）执行两次相同的查询并且存在一个副本分片以及使用默认路由算法的[round-robin](https://en.wikipedia.org/wiki/Round-robin_DNS)，那这两次的查询会请求不同的两个分片，就没法使用节点层的缓存优化了。

&emsp;&emsp;由于使用查询应用的用户先后执行相同的查询是很常见的。比如说为了分析索引的较窄子集（a narrower subset of the index），使用preference来标识当前用户或会话可以帮助优化缓存的使用。

##### Replicas might help with throughput, but not always

&emsp;&emsp;除了提高弹性（resiliency），副本分片可以用来提高吞吐量。比如说你有个单分片索引和三个节点，你可以将分片的数量设置为2来拥有一共三份分片的拷贝，这样所有的节点都可以用来查询。

&emsp;&emsp;现在你想象你有一个索引，它有两个分片。在第一个场景中，副本分片的数量为0，那么每个节点都有单个分片。在第二个场景中，副本分片的数量为1，那么每个节点都有两个分片。哪一种方式可以有更好的查询性能呢？通常有较少分片的节点有更好的性能。原因是每一个分片可以有更大的文件系统缓存，这个文件系统缓存的大小是所有分片共享的，并且文件系统缓存可能是Elasticsearch性能指标中最重要的影响因子（the filesystem cache is probably Elasticsearch’s number 1 performance factor）。同时注意的是没有副本的那个场景在单个节点发生故障后就不可用了。所以要考虑好吞吐量跟高可用之间的trade-off。

&emsp;&emsp;所以正确的分片数量是多少呢？如果集群中一共有`num_nodes`个节点，`num_primaries`个主分片。如果你想要最多能够承受`max_failures`个节点同时发生故障，那么正确的分片数量是：`max(max_failures, ceil(num_nodes / num_primaries) - 1)`。

####  Tune your queries with the Search Profiler
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/_tune_your_queries_with_the_search_profiler.html)

&emsp;&emsp;[Profile API](####Profile API)提供了这次查询处理的详细信息，包括每一个子查询和聚合花费的时间。

&emsp;&emsp;Kibana中的[Search Profiler](https://www.elastic.co/guide/en/kibana/8.2/xpack-profiler.html)能更方便的定位和分析profile results，并让你深入了解如何调整查询以提高性能并减少负载。

&emsp;&emsp;由于Profile API自身就会带来额外的开销，所以主要用来观察各个子查询之间相对的开销，不会真正的给出真实的处理花费的时间。

#### Faster phrase queries with index_phrases
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/faster-phrase-queries.html#faster-phrase-queries)

&emsp;&emsp;[text](####Text type family)类型的域有一个[index_phrases](####index_phrases)选项会索引2-shingles，可以自动的被query parser用来查询查询没有间隔（slop）的短语查询（phrase query）。如果你的用例中有很多短语查询，那么这个选项可以很好的提高查询速度。

#### Faster prefix queries with index_prefixes
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/faster-prefix-queries.html)

&emsp;&emsp;[text](####Text type family)类型的域有一个[index_prefixes](####index_prefixes)选项会将所有term的前缀索引，可以自动的被query parser用来前缀查询（prefix query）。如果你的用例中有很多的前缀查询，那么这个选项可以很好的提高查询速度。

#### Use constant_keyword to speed up filtering
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/faster-filtering-with-constant-keyword.html)

&emsp;&emsp;通常来说，filter的开销取决于匹配到的文档数量。比如你有一个包含车子（cycles）的索引，并且有大量的bicycles，然后很多的查询都是过滤出`cycle_type: bicycle`。这是一个很普通的过滤操作但是开销很大，因为匹配到了大量的文档。有一个简单的办法来避免执行这个过滤操作：将包含bicycles的文档索引到自己的索引中，并且通过查询这个索引来过滤bicycles，而不是作为一个[filter query](####Example of query and filter contexts)。

&emsp;&emsp;但是这样做会使得客户端的逻辑变得复杂（logic tricky），不过可以通过`constant_keyword`来帮忙。通过在包含bicycles的索引上将`cycle_type`的类型映射为`constant_keyword`并且赋值为`bicycles`，客户端可以使用相同的query查询整体的索引（monolithic index），Elasticsearch会在bicycles的索引上做出正确的方式，即忽略`cycle_type`为`bicycles`的过滤，否则不返回任何命中。

&emsp;&emsp;mapping看起来是这样的：

```text
PUT bicycles
{
  "mappings": {
    "properties": {
      "cycle_type": {
        "type": "constant_keyword",
        "value": "bicycle"
      },
      "name": {
        "type": "text"
      }
    }
  }
}

PUT other_cycles
{
  "mappings": {
    "properties": {
      "cycle_type": {
        "type": "keyword"
      },
      "name": {
        "type": "text"
      }
    }
  }
}
```

&emsp;&emsp;我们把索引切分成了两个：一个索引只包含bicycle，另一个索引包含其他类型的车子：独轮车（unicycle）、三轮车（tricycle）。然后在查询期间，我们需要对两个索引进行查询，并且不需要更改query。

```text
GET bicycles,other_cycles/_search
{
  "query": {
    "bool": {
      "must": {
        "match": {
          "description": "dutch"
        }
      },
      "filter": {
        "term": {
          "cycle_type": "bicycle"
        }
      }
    }
  }
}
```

&emsp;&emsp;在`bicycles`索引上，Elasticsearch会简单的忽略`cycle_type`的过滤并且将query重写为下面的样子：

```text
GET bicycles,other_cycles/_search
{
  "query": {
    "match": {
      "description": "dutch"
    }
  }
}
```

&emsp;&emsp;在`other_cycles`索引上，Elasticsearch会快速的判断出在`cycle_type`域中不存在bicycles然后不返回任何的命中。

&emsp;&emsp;将常用的值放在专用的索引上是一种非常有力的使得查询开销很小的一种方式。这个想法可以跨多个域进行组合：比如说，如果你要跟踪每一种车子的颜色，并且`bicycles`索引中有大量的黑色的bicycles，那么你可以继续将索引划分成`bicycles-black`和`bicycles-other-colors`的两个索引。

&emsp;&emsp;这种优化不是一定严格需要`constant_keyword`的：也可以在客户端的逻辑中基于不同的filter将查询路由到对应的索引上。然而`constant_keyword`能够让这个过程透明，并允许将搜索请求与索引拓扑分离，以换取非常少的开销。

### Tune for disk usage
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/tune-for-disk-usage.html)

#### Disable the features you do not need

&emsp;&emsp;默认情况下，Elasticsearch对大部分的域同时进行索引（倒排、bkd）和添加doc value（正排），使得这些域默认（out of box）可以用于查询和聚合。比如说你有一个名为`foo`的数值类型的域，并且你需要在这个域上进行histogram并且从来不会应用于filter，那你可以在[mappings](######Mappings)中安全的关闭索引（只使用bkd存储，不用倒排）：

```text
PUT index
{
  "mappings": {
    "properties": {
      "foo": {
        "type": "integer",
        "index": false
      }
    }
  }
}
```

&emsp;&emsp;[text](####Text type family)类型的域会存储标准化因子（normalization factor）用于（facilitate）文档打分。如果你只关心是否匹配`text`·域而不用生成分数，那么你可以使用[match_only_text](#####Match-only text field type)。这种类型的域会扔掉分数和位置信息来节省很大的空间。

#### Don’t use default dynamic string mappings

&emsp;&emsp;[dynamic string mappings](###Dynamic mapping)会默认将字符串的值用[text](####Text type family)和[keyword](####Keyword type family)进行索引，如果你只需要其中一种，那么显然这种默认的方式会有一些浪费。比如说`id`类型的值只需要用`keyword`域类型进行索引而`body`类型的值只要用`text`域类型进行索引。

&emsp;&emsp;可以通过显示的（explicit）在string field上或者在dynamic templates上进行配置，使得将string filed使用`keyword`或者`text`索引。

&emsp;&emsp;比如，下面的模板将string filed使用`keyword`索引：

```text
PUT index
{
  "mappings": {
    "dynamic_templates": [
      {
        "strings": {
          "match_mapping_type": "string",
          "mapping": {
            "type": "keyword"
          }
        }
      }
    ]
  }
}
```

#### Watch your shard size

&emsp;&emsp;更大的分片（larger shard）更能有效的存储数据。你可以在[creating indices](####Create index API)时设置较少的分片，生成较少的索引（比如说利用[Rollover API](####Rollover API)）或者使用[Shrink API](####Shrink index API)更改现有的索引来降低主分片的数量。

&emsp;&emsp;注意的（keep in mind）是大分片（large shard）也会带来缺点，比如说分片恢复的时间会很长。

#### Disable \_source

&emsp;&emsp;[\_source](####\_source field)会存储文档对应的原始的json内容。如果你不需要的话可以关闭这个配置，然而访问`_source`的API比如说更新或者reindex将无法正常工作。

#### Use best_compression

&emsp;&emsp;`_source`和stored filed很容易占用大量的磁盘空间（can easily take a non negligible amount of disk space）。可以使用`best_compression` [codec](#####index.codec)对这些数据进行更加激进的压缩。

#### Force merge

&emsp;&emsp;Elasticsearch中的索引会被存储在一个或者多个分片上。每一个分片是一个Lucene索引，这个索引由一个或者多个[段](https://www.amazingkoala.com.cn/Lucene/suoyinwenjian/2019/0610/65.html)组成。每一个段中的文件都是磁盘上对应的文件。更大的段（larger segment）更能有效的存储数据。

&emsp;&emsp;[force merge API](####Force merge API)可以用来降低每一个分片中段的数量。大部分情况下，可以通过设置`max_num_segments=1`将每一个分片中段的数量降至一个。

> WARNING：**Force merge should only be called against an index after you have finished writing to it**。Force merge会产生非常大（> 5G）的段，并且如果你持续往这个索引里面写数据，自动执行的合并策略以后也不会将新的段进行合并直到这些段中的文档都是标记为被删除的。这会导致索引中有很多的大段并且降低查询性能

#### Shrink index

&emsp;&emsp;[Shrink API](####Shrink index API)可以让你降低索引的分片数量。结合上文中Force merge可以很好的降低分片数量以及分片中段的数量。

#### Use the smallest numeric type that is sufficient

&emsp;&emsp;为[numeric data](####Numeric field types)选择不同的域会对磁盘使用有着很大的影响。特别是integer应该使用integer类型（比如说`byte`、`short`、`integer`、`long`）存储并且浮点型数据应该要么使用`scaled_float`（如果合适的话），要么使用最小的类型（比特位）：使用`float`而不是用`double`，使用`half_float`而不是用`float`也能帮助节省磁盘空间。

#### Use index sorting to colocate similar documents

&emsp;&emsp;当Elasticsearch存储`_source`时，会一次性的处理多个文档来提高压缩率。比如文档间包含相同的域是很常见的，并且这些域包含相同的值也是很常见的。especially on fields that have a low cardinality or a zipfian distribution。

&emsp;&emsp;默认情况下会按照添加到索引的顺序对文档进行压缩。如果你开启了[index sorting](###Index Sorting)后则会根据排序后的文档进行压缩。排序后的文档有着类似的结构、域、域值，使得可以提高压缩率。

#### Put fields in the same order in documents

&emsp;&emsp;由于是多篇文档被压缩到block中，所以如果每篇文档中`_source`中的域是有序的，那么更容易找到更长的相同的值（duplicate strings）。

#### Roll up historical data

&emsp;&emsp;保留旧的数据对以后进行数据分析是有帮助的。但由于磁盘开销经常就不会保留。你可以使用data rollups以原始数据存储成本的一小部分来汇总和存储历史数据。见[Rolling up historical data](###Rolling up historical data)了解更多内容。

### Fix common cluster issues
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/fix-common-cluster-issues.html#circuit-breaker-errors)

&emsp;&emsp;这一章节介绍如何处理Elasticsearch集群的一些常见错误和问题。

#### Error: disk usage exceeded flood-stage watermark, index has read-only-allow-delete block

&emsp;&emsp;这个错误指出数据节点的磁盘空间大小已经严重不足（critical low）了并且达到了[flood-stage disk usage watermark](######cluster.routing.allocation.disk.watermark.flood_stage)。为了防止出现full disk，当一个节点达到watermark，Elasticsearch会block在这个节点上写入索引。如果这个block影响到了系统的索引，Kibana和其他Elastic Stack feature可能会不可用。

&emsp;&emsp;当受到影响的节点的磁盘使用量降到[high disk watermark](######cluster.routing.allocation.disk.watermark.high)以下，Elasticsearch会自动移除write block。Elasticsearch会自动的将受到影响的节点的分配移动到有相同data tier的其他节点上。

&emsp;&emsp;若要验证分片已从受到影响的节点上移走，使用[cat shards API](####cat shards API)。

```text
GET _cat/shards?v=true
```

&emsp;&emsp;如果分片仍然在节点上，使用[cluster allocation explanation API ](####Cluster allocation explain API)获取分配状态的介绍。


```text
GET _cluster/allocation/explain
{
  "index": "my-index",
  "shard": 0,
  "primary": false,
  "current_node": "my-node"
}
```

&emsp;&emsp;如果要马上恢复写操作，你可以临时的提高disk watermark然后移除write block。

```text
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.disk.watermark.low": "90%",
    "cluster.routing.allocation.disk.watermark.high": "95%",
    "cluster.routing.allocation.disk.watermark.flood_stage": "97%"
  }
}

PUT */_settings?expand_wildcards=all
{
  "index.blocks.read_only_allow_delete": null
}
```

&emsp;&emsp;作为长期解决方案（long-term solution）。你应该在受到影响的data tier中添加节点或者更新现有的节点的磁盘空间。若要释放额外的磁盘空间，你可以使用[delete index API](####Delete index API)来删除不需要的索引。

```text
DELETE my-index
```

&emsp;&emsp;当一个长期解决方案落实后，重置或者重新配置disk watermark。

```text
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.disk.watermark.low": null,
    "cluster.routing.allocation.disk.watermark.high": null,
    "cluster.routing.allocation.disk.watermark.flood_stage": null
  }
}
```

#### Circuit breaker errors

&emsp;&emsp;Elasticsearch使用[circuit breakers](####Circuit breaker settings)防止节点发生OOM。如果Elasticsearch估算出一个操作会超出circuit breakers，它将停止这个操作并返回一个错误。

&emsp;&emsp;默认情况下，[parent circuit breaker](####Circuit breaker settings)在达到95%的JVM内存量时触发。为了防止错误的发生，我们建议使用量一旦超过85%时就采取行动来降低内存压力。

##### Diagnose circuit breaker errors

###### Error messages

&emsp;&emsp;如果一个请求触发了circuit breaker，Elasticsearch会返回一个`429`的错误码。

```text
{
  'error': {
    'type': 'circuit_breaking_exception',
    'reason': '[parent] Data too large, data for [<http_request>] would be [123848638/118.1mb], which is larger than the limit of [123273216/117.5mb], real usage: [120182112/114.6mb], new bytes reserved: [3666526/3.4mb]',
    'bytes_wanted': 123848638,
    'bytes_limit': 123273216,
    'durability': 'TRANSIENT'
  },
  'status': 429
}
```

&emsp;&emsp;Elasticsearch同样会将circuit breaker的错误写入到[elasticsearch.log](####Logging)中，这有助于记录一些自动处理，例如分配这种操作触发circuit breaker对应的错误。

```text
Caused by: org.elasticsearch.common.breaker.CircuitBreakingException: [parent] Data too large, data for [<transport_request>] would be [num/numGB], which is larger than the limit of [num/numGB], usages [request=0/0b, fielddata=num/numKB, in_flight_requests=num/numGB, accounting=num/numGB]
```

###### Check JVM memory usage

&emsp;&emsp;如果你开启了Stack Monitoring，你可以在Kibana中查看JVM的内存使用。在主菜单中，点击**Stack Monitoring**。在Stack Monitoring的**Overview**页面，点击**Nodes**，**JVM Heap**会列出每一个节点的当前内存使用情况。

&emsp;&emsp;你同样可以使用[cat nodes API ](####cat nodes API)来获取每一个节点当前的`heap.percent`。

```text
GET _cat/nodes?v=true&h=name,node*,heap*
```

&emsp;&emsp;若要获取每一个circuit breaker的JVM 内存使用量。使用[node stats API](####Nodes stats API)：

```text
GET _nodes/stats/breaker
```

##### Prevent circuit breaker errors

###### Reduce JVM memory pressure

&emsp;&emsp;高的JVM内存压力通常会导致circuit breaker error，见[ High JVM memory pressure](####High JVM memory pressure)。

- Avoid using fielddata on text fields
  - 对于high-cardinality的`text`域，fileddata会使用大量的内存。若要避免这个问题，Elasticsearch默认关闭`text`域的[fileddata](#####fielddata mapping parameter)。如果你开启了fielddata，并且触发了[fielddata circuit breaker](#####Field data circuit breaker)，考虑使用关闭它或者使用`keyword`。见[fielddata mapping parameter](#####fielddata mapping parameter)。
- Clear the fieldata cache
  - 如果你触发了fielddata circuit breaker并且不能关闭fielddata，使用[clear cache API](####Clear cache API)来清除fileddata cache。这可能会中断（disrupt）正在使用fielddata的查询。
  
```text
POST _cache/clear?fielddata=true
```

#### High CPU usage

&emsp;&emsp;Elasticsearch使用[thread pools](####Thread pools)来管理并发操作所需的CPU资源，High CPU usage通常说的是一个或者多个线程池运行不足（running low）。

&emsp;&emsp;如果一个线程池耗尽（depleted）了，Elasticsearch会[reject](####Rejected requests)跟线程池相关的[request](####Rejected requests)。例如如果`search` 线程池耗尽了，Elasticsearch会reject查询请求直到有可用线程。

##### Diagnose high CPU usage

###### Check CPU usage

- Elasticsearch Service
  - 从部署菜单中，点击**Performance**。页面中的**CPU Usage**章节显示了你部署的CPU使用百分比。
  - High CPU usage can also deplete your CPU credits. CPU credits let Elasticsearch Service provide smaller clusters with a performance boost when needed. The CPU credits chart shows your remaining CPU credits, measured in seconds of CPU time.
  - 你可以使用[cat nodes API](####cat nodes API)获取每一个节点当前的CPU使用量

```text
GET _cat/nodes?v=true&s=cpu:desc
```

&emsp;&emsp;响应中`cpu`这一列包含了当前CPU使用量的百分比，`node`这一列包含了节点的名字。

- Self-managed
  - 你可以使用[cat nodes API](####cat nodes API)获取每一个节点当前的CPU使用量

```text
GET _cat/nodes?v=true&s=cpu:desc
```

&emsp;&emsp;响应中`cpu`这一列包含了当前CPU使用量的百分比，`node`这一列包含了节点的名字。

###### Check hot threads

&emsp;&emsp;如果一个节点有很高的CPU使用量，使用[nodes hot threads API ](####Nodes hot threads API)检查正在节点上运行的资源密集（resource-intensive）的线程。

```text
GET _nodes/my-node,my-other-node/hot_threads
```

&emsp;&emsp;这个API会返回hot thread的plain text形式的breakdown。

##### Reduce CPU usage

&emsp;&emsp;下面的内容介绍常见的导致high cup usage以及解决方案。

###### Scale your cluster

&emsp;&emsp;繁重的Index和search负载会耗尽（deplete）smaller thread pool。若要能更好的处理繁重的负载，在你的集群中添更多的节点或者更新现有的节点来提高承载能力（capacity）。

###### Spread out bulk requests(1)

&emsp;&emsp;尽管[bulk indexing ](####Bulk API)和[multi-search](####Multi search API)的请求比单独的请求有更高的效率，但是它们要求更高的CPU usage。如果可以的话，提交数量较小的请求然后运行提交多次。

###### Cancel long-running searches

&emsp;&emsp;运行时间长的查询会阻塞住[search](######search) 线程池中的线程。使用[task management API](####Task management API)来检查这些运行时间长的查询。

```text
GET _tasks?actions=*search&detailed
```

&emsp;&emsp;response中的`description`描述了请求索引以及query信息。`running_time_in_nanos`描述了这个查询已经运行了的时间。

```text
{
  "nodes" : {
    "oTUltX4IQMOUUVeiohTt8A" : {
      "name" : "my-node",
      "transport_address" : "127.0.0.1:9300",
      "host" : "127.0.0.1",
      "ip" : "127.0.0.1:9300",
      "tasks" : {
        "oTUltX4IQMOUUVeiohTt8A:464" : {
          "node" : "oTUltX4IQMOUUVeiohTt8A",
          "id" : 464,
          "type" : "transport",
          "action" : "indices:data/read/search",
          "description" : "indices[my-index], search_type[QUERY_THEN_FETCH], source[{\"query\":...}]",
          "start_time_in_millis" : 4081771730000,
          "running_time_in_nanos" : 13991383,
          "cancellable" : true
        }
      }
    }
  }
}
```

&emsp;&emsp;若要取消一个查询并且释放资源，使用endpoint `_cancel`。

```text
POST _tasks/oTUltX4IQMOUUVeiohTt8A:464/_cancel
```

&emsp;&emsp;见[Avoid expensive searches](######Avoid expensive searches)了解更多关于跟踪以及避免资源密集型的查询的内容。

#### High JVM memory pressure

&emsp;&emsp;High JVM memory usage会降低集群的性能以及触发[circuit breaker errors](#####Circuit breaker errors)。为了防止发生这个情况，我们建议当节点的JVM内存使用量一旦超过85%时就要采取行动来降低内存压力。

##### Diagnose high JVM memory pressure

###### Check JVM memory pressure

- Elasticsearch Service
  - 在你的部署菜单中，点击**Elasticsearch**，在**Instances**下，每一个实例都显示了**JVM memory pressure**指示器（indicator）。当内存压力达到75%后，指示器就会变成红色。

&emsp;&emsp;你也可以使用[nodes stats API](####Nodes stats API)来计算每一个节点上的内存压力。

```text
GET _nodes/stats?filter_path=nodes.*.jvm.mem.pools.old
```

&emsp;&emsp;使用response中的内容按照下面的方式计算内存压力：

&emsp;&emsp;JVM Memory Pressure = `used_in_bytes / max_in_bytes`

- Self-managed

&emsp;&emsp;你也可以使用[nodes stats API](####Nodes stats API)来计算每一个节点上的内存压力。

```text
GET _nodes/stats?filter_path=nodes.*.jvm.mem.pools.old
```

&emsp;&emsp;使用response中的内容按照下面的方式计算内存压力：

&emsp;&emsp;JVM Memory Pressure = `used_in_bytes / max_in_bytes`

###### Check garbage collection logs

&emsp;&emsp;随着内存使用量的增加，垃圾回收器会变的更加频繁并消耗更长的时间。你可以在[elasticsearch.log](####Logging)中垃圾回收器事件的频率跟长度（the frequency and length of garbage collection events）。例如下面的例子中，Elasticsearch花费了超过一般的时间来执行垃圾回收。

```text
[timestamp_short_interval_from_last][INFO ][o.e.m.j.JvmGcMonitorService] [node_id] [gc][number] overhead, spent [21s] collecting in the last [40s]
```

##### Reduce JVM memory pressure

###### Reduce your shard count

&emsp;&emsp;每一个分片都会占用内存，大多数情况下，体积很大但是数量较小的分片比体积很小但是数量较大占用更少的资源。见[Size your shards](###Size your shards)来降低分片的数量。

###### Avoid expensive searches

&emsp;&emsp;Expensive searches会使用大量的内存。可以开启[show logs](###Slow Log)来更好的追踪集群上的expensive searches。

&emsp;&emsp;Expensive searches可能是因为使用了非常大的[size argument](###Paginate search results)，使用了大量分桶的聚合，或者包含了[expensive queries](####Allow expensive queries)。若要防止expensive searches，考虑进行更改下面的设置：

- 使用索引设置[index.max_result_window](#####index.max_result_window)来降低`size`的上限
- 使用集群设置[search.max_buckets](#####search.max_buckets)降低允许分桶的数量
- 使用集群设置[search.allow_expensive_queries](####Allow expensive queries)关闭expensive query

```text
PUT _settings
{
  "index.max_result_window": 5000
}

PUT _cluster/settings
{
  "persistent": {
    "search.max_buckets": 20000,
    "search.allow_expensive_queries": false
  }
}
```

###### Prevent mapping explosions

&emsp;&emsp;定义太多的域，或者嵌套的域太深会导致[mapping explosions](####Settings to prevent mapping explosion)，使得占用大量的内存。若要防止mapping explosions，使用[mapping limit settings ](###Mapping limit settings)来限制域的数量。

###### Spread out bulk requests

&emsp;&emsp;尽管[bulk indexing ](####Bulk API)和[multi-search](####Multi search API)的请求比单独的请求有更高的效率，但是它们造成更高的JVM memory pressure。如果可以的话，提交数量较小的请求然后运行提交多次。

###### Upgrade node memory

&emsp;&emsp;繁重的索引跟查询会带来较高的JVM memory pressure。为了能更好的处理高负载，升级你的节点，提高内存容量。

#### Red or yellow cluster status

&emsp;&emsp;`red`或者`yellow`的集群状态说明一个或者多个分片missing或者未分配（unallocated）。这些为unassign的分片会带来丢失数据的风险以及降低集群性能。

##### Diagnose your cluster status

###### Check your cluster status

&emsp;&emsp;使用[cluster health API](####Cluster health API)。

```text
GET _cluster/health?filter_path=status,*_shards
```

&emsp;&emsp;一个健康的集群的颜色是`green`，并且没有`unassigned_shards`。`yellow`意味着只有副本分片没有被assign。`red`意味着一个或者多个主分片没有被assign。

###### View unassigned shards

&emsp;&emsp;若要查看unassigned分片，使用[cat shards API](####cat shards API)。

```text
GET _cat/shards?v=true&h=index,shard,prirep,state,node,unassigned.reason&s=state
```

&emsp;&emsp;unassigned的分片有`UNASSIGNED`的状态。`prirep`的值为`p`时指的是主分片，值为`r`时指的是副本分片。

&emsp;&emsp;若要明白为什么unassigned 分片没有被assign以及你应该采取行动运行Elasticsearch去assign这个分片，使用[cluster allocation explanation API](####Cluster allocation explain API)。

```text
GET _cluster/allocation/explain?filter_path=index,node_allocation_decisions.node_name,node_allocation_decisions.deciders.*
{
  "index": "my-index",
  "shard": 0,
  "primary": false,
  "current_node": "my-node"
}
```

##### Fix a red or yellow cluster status

&emsp;&emsp;一个分片不能被assign有很多的原因。下面列出了最常见的原因以及解决方案。

###### Re-enable shard allocation

&emsp;&emsp;你通常会在[restart](###Full-cluster restart and rolling restart)节点或者维护节点时关闭分配（disable allocation）。如果你忘记了重新开启它，Elasticsearch将无法assign分片。若要开启，reset集群设置`cluster.routing.allocation.enable`。

```text
PUT _cluster/settings
{
  "persistent" : {
    "cluster.routing.allocation.enable" : null
  }
}
```

###### Recover lost nodes

&emsp;&emsp;当data node离开集群后分片会无法被assign。导致这个的原因很多，从连接问题到硬件问题。当你处理完这些问题并恢复节点后，它会重新加入到集群中。Elasticsearch将随后自动的分配（allocate）那些未assign的分片。

&emsp;&emsp;为了避免资源浪费以及temporary issue，Elasticsearch会默认[delays allocation](####Delaying allocation when a node leaves)一分钟。如果你想要恢复一个节点并且不想等待延迟周期（delay period）。你可以调用不携带参数[cluster reroute API](####Cluster reroute API)来启动分配处理（allocation progress）。这个处理在后台异步处理。

```text
POST _cluster/reroute
```

###### Fix allocation settings

&emsp;&emsp;错误的配置可能会导致一个unassgin的主分片。这些设置包括：

- [Shard allocation](####Index-level shard allocation filtering) index settings
- [Allocation filtering](#####Cluster-level shard allocation filtering) cluster settings
- [Allocation awareness](#####Shard allocation awareness) cluster settings


&emsp;&emsp;若要查看分配设置，可以使用[get index settings](####Get index settings API)和[cluster get settings](####Cluster get settings API)。

```text
GET my-index/_settings?flat_settings=true&include_defaults=true

GET _cluster/settings?flat_settings=true&include_defaults=true
```

&emsp;&emsp;你可以使用[update index settings](####Update index settings API)和[cluster update settings](####Cluster update settings API)来更改这些设置。


###### Allocate or reduce replicas

&emsp;&emsp;为了应对硬件故障，Elasticsearch不会将副本分片和主分片分配到相同的节点上。如果没有其他data node可以存放这个副本分片，它仍然是unassign的。若要修复这个问题，你可以：

- 在相同的节点层（data tier）上添加data node
- 修改索引设置`index.number_of_replicas`来降低每一个分片的副本数量。我们建议每一个主分片至少要有一个副本。

```text
PUT _settings
{
  "index.number_of_replicas": 1
}
```

###### Free up or increase disk space

&emsp;&emsp;Elasticsearch会使用[low disk watermark](#####Disk-based shard allocation settings)来保证data node 有足够的磁盘空间来处理即将生成的分片。默认情况下，Elasticsearch不会将分片分配到磁盘使用超过85%的节点上。

&emsp;&emsp;若要检查当前节点的磁盘使用情况，使用[cat allocation API](####cat allocation API)。

```text
GET _cat/allocation?v=true&h=node,shards,disk.*
```

&emsp;&emsp;如果节点的磁盘空间不足，你有下面的可选项：

- 增加节点上的磁盘空间
- 删除不需要的索引来释放磁盘空间。如果你使用了ILM，你可以更新策略，使用[searchable snapshots](####Searchable snapshot)或者添加一个delete phase。如果你不再需要搜索这些数据，你可以使用[snapshot](##Snapshot and restore)存储并脱离于集群。
- 如果你不再需要往索引中写入数据，使用[force merge API](####Force merge API)或者ILM的[force merge action](####Force merge)将段合并为大段。

```text
POST my-index/_forcemerge
```

- 如果索引是只读的，使用[shrink index API ](####Shrink index API)或者[shrink action ](####Shrink)来降低主分片的数量

```text
POST my-index/_shrink/my-shrunken-index
```

- 如果节点的磁盘空间很大，你可以提高low disk watermark或者设置一个显示的字节为单位的值。

```text
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.disk.watermark.low": "30gb"
  }
}
```

###### Reduce JVM memory pressure

&emsp;&emsp;分片的分配需要JVM heap memory。高的内存压力会导致[circuit breakers](####Circuit breaker settings)，它会停止分配使得分片无法被assign。见[High JVM memory pressure](####High JVM memory pressure)。

###### Recover data for a lost primary shard

&emsp;&emsp;如果某个节点上的主分片丢失了，Elasticsearch通常会使用另一个节点的副本分片来替代它。如果你不能恢复节点并且不存在副本分片或者它无法用于恢复。那你需要从[snapshot](##Snapshot and restore)中或者源数据（original data source）重新添加。

> WARNING：只有再也无法恢复节点的情况下才考虑这个选项。这个操作会分配一个空的主分片。如果节点随后重新加入到集群。Elasticsearch会用新的空的分片中的数据进行覆盖，导致数据丢失。

&emsp;&emsp;使用[cluster reroute API](####Cluster reroute API)手动的分配一个unassign的主分片到另一个相同node tier的data node中。设置`accept_data_loss`为`true`。

```text
POST _cluster/reroute
{
  "commands": [
    {
      "allocate_empty_primary": {
        "index": "my-index",
        "shard": 0,
        "node": "my-node",
        "accept_data_loss": "true"
      }
    }
  ]
}
```

&emsp;&emsp;如果你使用snapshot备份了丢失的索引数据，使用[ restore snapshot API](####Restore snapshot API)来恢复。或者你可以从源数据中重新索引一遍。

#### Rejected requests

&emsp;&emsp;当Elasticsearch reject一个请求，它会停止这个请求的操作并且返回`429`的响应码。请求被reject通常由下面的情况导致：

- [depleted thread pool](####High CPU usage)，已经耗尽的`search`或者`write` 线程池会返回`TOO_MANY_REQUESTS`的错误信息
- [circuit breaker error](####Circuit breaker errors)
- 高的[indexing pressure](###Indexing pressure)，超过了[indexing_pressure.memory.limit](####Memory limits)。

##### Check rejected tasks

&emsp;&emsp;可以使用[cat thread pool API](####cat thread pool API)检查每一个thread pool中reject的任务数量。特别是在`search`和`write` thread pool中`rejected` to `completed`的任务的比例很高，意味着Elasticsearch经常（regular）reject请求。

```text
GET /_cat/thread_pool?v=true&h=id,name,active,rejected,completed
```

##### Prevent rejected requests

###### Fix high CPU and memory usage

&emsp;&emsp;如果Elasticsearch经常reject请求或者其他任务，说明你的集群可能有很高的CPU或者JVM内存的使用压力。见[High CPU usage](####High CPU usage)和[High JVM memory pressure](####High JVM memory pressure)。

###### Prevent circuit breaker errors

&emsp;&emsp;如果你经常触发circuit breaker errors，见[Circuit breaker errors](####Circuit breaker errors)进行诊断并防止再次出现。

#### Task queue backlog

&emsp;&emsp;用于积压任务的队列会导致任务无法完成并让集群陷入不健康的状态。资源受限（resource constraint），触发大量的任务以及长时间运行的任务都会导致任务积压。

#####  Diagnose a task queue backlog

###### Check the thread pool status

&emsp;&emsp;[depleted thread pool](####High CPU usage)会导致[rejected requests](####Rejected requests)。

&emsp;&emsp;你可以使用[cat thread pool API](####cat thread pool API)查看每一个线程池中活跃的线程数量以及有多少个任务在排队，有多少被reject，以及有多少已经完成。

```text
GET /_cat/thread_pool?v&s=t,n&h=type,name,node_name,active,queue,rejected,completed
```

###### Inspect the hot threads on each node

&emsp;&emsp;如果某个线程发生了积压，你可以周期性的调用[Nodes hot threads](####Nodes hot threads API)来查看是否线程有足够的资源处理以及观察它处理的速度。

```text
GET /_nodes/hot_threads
```

###### Look for long running tasks

&emsp;&emsp;长时间运行的任务也会导致积压，你可以使用[task management](####Task management API) API 获取运行中的任务的信息。检查`running_time_in_nanos`来确认任务是否花费了特别长的时间。

```text
GET /_tasks?filter_path=nodes.*.tasks
```

##### Resolve a task queue backlog

###### Increase available resources

&emsp;&emsp;如果任务允许缓慢并且队列发生堆积，你可能需要采取行动来[Reduce CPU usage](#####Reduce CPU usage)。

&emsp;&emsp;在一些用例中，提高thread pool size可能会有帮助。例如`force_merge` thread pool默认只有一个线程，提高两个线程有助于降低合并线程的堆积。

###### Cancel stuck tasks

&emsp;&emsp;如果你发现活跃的线程没有在处理并且发生了堆积，可以考虑取消这个任务。

### Size your shards
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/size-your-shards.html)

&emsp;&emsp;Elasticsearch中的每一个索引会被划分成一个或多个分片，每一个分片有对应的副本分片（replica），这些副本分布在多个节点上，用于应对硬件故障。如果你正在使用[Data streams](##Data streams)，每一个data stream会通过a sequence of indices实现备份。单个节点上能存储的数据量是有限制的，所以你可以通过添加节点和提高索引以及分片的数量 的方式来提高集群的承载能力。然而每一个索引和分片都是有开销的，如果你将数据划分出太多的分片，那么其开销会很大（overwhelming）。有太多索引或者分片的集群会有`oversharding`的问题。一个oversharding 的集群在响应查询方面会变得低效，极端情况下（in extreme case）会导致集群不稳定。

#### Create a sharding strategy

&emsp;&emsp;防止oversharding以及其他分片相关（shard-related）问题最好的办法 就是创建一个分片策略（ sharding strategy）。sharding strategy通过限制分片的数量来帮助你明确以及维护集群中进行优化后的分片数量。

&emsp;&emsp;不幸的是，不存在 one-size-fits-all的sharding strategy。在某个环境中工作不错的策略可能不适用于其他环境。一个好的sharding strategy必须考虑 你的基础设施，用例以及性能预期（performance expectation）。

&emsp;&emsp;创建sharding strategy最好的方式就是使用与生产环境相同的硬件，对生产数据使用跟生产中相同的查询和索引负载进行基准测试。可以查看[quantitative cluster sizing video](https://www.elastic.co/cn/elasticon/conf/2016/sf/quantitative-cluster-sizing)了解我们的推荐方式。若要测试不同的分片配置，使用Kibana的[Elasticsearch monitoring tools](https://www.elastic.co/guide/en/kibana/8.2/elasticsearch-metrics.html)来跟踪集群的稳定性和性能。

&emsp;&emsp;下面的内容提供了一些提醒（reminder）以及指导你在创建你自己的sharding strategy时需要考虑的点。如果你的集群已经有oversharding的问题，见[Reduce a cluster’s shard count](####Reduce a cluster’s shard count)。

#### Sizing considerations

&emsp;&emsp;在构建你自己的sharding strategy时，要牢记下面的内容：

##### Searches run on a single thread per shard

&emsp;&emsp;大多数的查询会命中多个分片。每一个分片上运行查询时需要一个CPU线程。一个分片上运行多个并发的查询，在大量的分片上查询会消耗（deplete） [search thread pool](####Thread pools)。这会导致吞吐量和查询速度的下降。

##### Each index, shard, segment and field has overhead

&emsp;&emsp;每一个索引和每一个分片需要内存和CPU资源。大多数情况下，数量少但是体积大的分片比数量多体积小的分片使用更少的资源。

&emsp;&emsp;段（segment）在分片的资源使用中起着重要的作用。大多数的分片包含多个段，段中存放了索引数据。Elasticsearch会在内存中保留一些segment metadata用于快速的查询。随着分片的增长，分片中的段会[merged](###Merge)成数量更少，体积更大的段。合并可以降低段的数量，意味着在堆内存中保留更少的metadata。

&emsp;&emsp;mapping中的每一个域都会带有一些内存使用和磁盘空间的开销。默认情况下，Elasticsearch会自动创建被索引的文档中每一个域的mapping，但是你可以通过[take control of your mappings](###Explicit mapping)来关闭这个行为。

&emsp;&emsp;并且segment会为每一个mapped field分配少量的堆内存。这个 per-segment-per-field的内存开销包括copy of the field name，使用 ISO-8859-1或者UTF-16编码。尽管它们占用的内存量不是很显著（not noticeable），但如果你的分片有很多的段，相关的mapping中包含了很多的域或者非常长的域名，这时候你是需要考虑它们的开销的。

##### Elasticsearch automatically balances shards within a data tier

&emsp;&emsp;集群中的所有节点会被分组到[data tiers](###Data tiers)，在每一层，Elasticsearch会尝试将索引的分片尽可能的分布到许多节点上。当你添加了 一个新节点或者一个节点发生了故障，Elasticsearch会自动的在同一层中剩余节点上进行rebalance。

#### Best practices

&emsp;&emsp;如果适用的话，使用下面的最佳实践开始创建你的sharding strategy。

##### Delete indices, not documents

&emsp;&emsp;被删除的文档不会马上从Elasticsearch的文件系统中移除。而是在每一个相关的分片上将这些文档标记为被删除的。这些被标记的文档仍然会占用资源，直到在周期性的[segment merge](###Merge)中移除。

&emsp;&emsp;如果可以的话，删除整个索引。Elasticsearch会马上从文件系统中移除被删除的索引并释放资源。

##### Use data streams and ILM for time series data

&emsp;&emsp;[Data streams](##Data streams)可以让你在多个基于时间的backing索引中存储时序数据。你可以使用[index lifecycle management](###ILM: Manage the index lifecycle)来自动的管理这些backing 索引。

&emsp;&emsp;这个设置的其中一个好处就是[automatic rollover](###Tutorial: Automate rollover with ILM)，它会在满足定义的`max_primary_shard_size`、`max_age`、`max_docs`、`max_size`阈值后创建一个新的write index。当不再需要某个索引时，你可以使用ILM自动的删除它并释放资源。

&emsp;&emsp;ILM可以让你随时轻松更改你的sharding strategy：

- **想要为新的索引降低分片数量？**
  - 在data stream的[matching index template](###Change mappings and settings for a data stream)中修改[index.number_of_shards ](######index.number_of_shards)
- **想要更大的分片或者更少的backing索引?**
  - 提高你的ILM策略中的[rollover threshold](####Rollover)
- **需要让索引之间有更短的时间跨度**
  -  通过删除旧的的索引来抵消（offset）增加的索引。你可以在策略中的[delete phase](####Index lifecycle)中降低`min_age`的阈值

&emsp;&emsp;每一个新backing index都是进一步调整策略的机会。

##### Aim for shard sizes between 10GB and 50GB

&emsp;&emsp;体积很大的分片会在发生故障后进行恢复时花费更多的时间。当一个节点发生故障，Elasticsearch会在同一层中剩余节点上进行rebalance。恢复的过程通常包括通过网络拷贝分片的内容，所以一个100G的分片会比50GB的分片花费两倍的时间。相比之下，体积小的分片会有更多的开销并且更低的查询效率。查询50个1GB的分片会比查询单个50GB包含相同内容的分片花费更多的资源。

&emsp;&emsp;尽管在分片大小上没有严格的限制，但是从经验上来说，大小在10GB和50GB之间能很好的在日志以及时序数据上工作。你可能会基于你的网络以及用例使用体积更大的分片。较小的分片可能适用于[Enterprise Search](https://www.elastic.co/guide/en/enterprise-search/8.2/index.html)和类似用例。

&emsp;&emsp;如果你使用ILM，设置[rollover action](####Rollover)的`max_primary_shard_size`阈值到`50gb`来避免分片的大小超过50GB。

&emsp;&emsp;使用[cat shards API](####cat shards API)来查看分片当前的大小。

```text
GET _cat/shards?v=true&h=index,prirep,shard,store&s=prirep,store&bytes=gb
```

&emsp;&emsp;`pri.store.size`的值显示了这个索引的所有主分片的大小总和。

```text
index                                 prirep shard store
.ds-my-data-stream-2099.05.06-000001  p      0      50gb
...
```

##### Master-eligible nodes should have at least 1GB of heap per 3000 indices

&emsp;&emsp;master node能管理的索引数量跟它的堆大小成比例（proportional ）。每一个索引对应的准确的堆内存大小取决于多个因素，例如mapping的大小和每一个索引的分片数量。

&emsp;&emsp;作为一般的经验法则（As a general rule of thumb），master node上有1GB的内存就应该拥有不大于3000个索引。例如，如果你的集群中有一个拥有4GB的dedicated master node，那你应该拥有不大于12000个索引。如果你的master node不是dedicated master node：你应该让集群中每一个master-eligible node有1GB的内存就应该拥有不大于3000个索引。

&emsp;&emsp;注意的是这个规则定义的是一个master node能管理的绝对最大数量的索引，但是不能保证在这么多数量的索引下的查询或者索引的性能。你必须保证你的data node有足够的资源满足你的工作负载，并且你整体的sharding strategy能满足你所有的性能要求。见[Searches run on a single thread per shard](####Searches run on a single thread per shard)和[Each index, shard, segment and field has overhead](####Each index, shard, segment and field has overhead)。

&emsp;&emsp;使用[cat nodes API](####cat nodes API)检查每一个节点的内存配置。

```text
GET _cat/nodes?v=true&h=heap.max
```

&emsp;&emsp;你可以使用[cat shards API](####cat shards API)检查每一个节点的分片数量。

```text
GET _cat/shards?v=true
```

##### Data nodes should have at least 1kB of heap per field per index, plus overheads

&emsp;&emsp;每一个mapped field需要的准确的资源使用量取决于域的类型，但是一般的经验法则是允许每一个索引的每一个域要有接近1KB的内存。你必须还要允许有足够的堆内存用于Elasticsearch最基本的用于索引，查询或者聚合所需要的内存。0.5GB的内存对于大多数的合理的工作负载都是够用的。如果你的工作负载是轻量级的那么可以需要更少的内存，反之需要更多的内存。

&emsp;&emsp;例如，如果节点中有1000个索引，每一个索引包含了4000个mapped field，那么你应该允许为这些域分配接近1000 \* 4000 \* 1KB =  4GB的内存，以及额外的0.5GB用于工作负载和其他开销。因此这个节点需要至少4.5GB。

&emsp;&emsp;注意的是这个规则定义的是一个data node能管理的索引的绝对最大数量，但是不能保证在这么多数量的索引下的查询或者索引的性能。你必须保证你的data node有足够的资源满足你的工作负载，并且你整体的sharding strategy能满足你所有的性能要求。见[Searches run on a single thread per shard](####Searches run on a single thread per shard)和[Each index, shard, segment and field has overhead](####Each index, shard, segment and field has overhead)。

##### Avoid node hotspots

&emsp;&emsp;如果太多的分片分配到了一个指定的节点，这个节点会成为一个热点（Hotspot）节点。例如，如果单个节点包含一个索引并带有大规模的索引操作，这个索引会有太多的分片，这个节点则很有可能出现问题。

&emsp;&emsp;为了防止出现Hotspot。使用索引设置[index.routing.allocation.total_shards_per_node](#####index.routing.allocation.total_shards_per_node)来显示的（explicit）限制单个节点上分片的数量。你可以使用[update index settings API](####Update index settings API)配置`index.routing.allocation.total_shards_per_node`。

```text
PUT my-index-000001/_settings
{
  "index" : {
    "routing.allocation.total_shards_per_node" : 5
  }
}
```

##### Avoid unnecessary mapped fields

&emsp;&emsp;默认情况下，Elasticsearch会为文档中的每一个域[automatically creates a mapping](###Dynamic mapping)。每一个mapped field对应磁盘上的一些数据结构，使得用于高效的查询，检索以及聚合。Details about each mapped field are also held in memory。在很多用例中这种开销是不需要的因为有些域没有用于查询或者聚合。使用[Explicit mapping](###Explicit mapping)替换dynamic mapping来防止创建从不会被使用的域。如果有些域通常一起使用，考虑在索引阶段使用[copy_to](####copy_to)。如果一个域很少被使用，使用[Runtime field](###Runtime fields)可能更合适。

&emsp;&emsp;你可以通过[Field usage stats API](####Field usage stats API)获取哪些域正在被使用的信息，并且你可以通过[Analyze index disk usage API](####Analyze index disk usage API)分析mapped field的磁盘使用。注意的是，不必要的mapped field也会带来一些内存开销以及它们的磁盘使用。

#### Reduce a cluster’s shard count

&emsp;&emsp;如果集群中已经oversharded了，你可以使用下面的一个或者多个方法来减少分片数量。

##### Create indices that cover longer time periods

&emsp;&emsp;如果你使用了ILM并且retention policy允许的话，不使用`max_age`阈值用于rollover action，而是使用`max_primary_shard_size`来避免创建空索引或者许多小的分片。

&emsp;&emsp;如果你的retention policy要求`max_age`阈值，提高阈值来处理longer time interval。例如你可以按周或者按月创建索引而不是按天创建。

##### Delete empty or unneeded indices

&emsp;&emsp;如果你正在使用ILM并且基于`max_age`阈值对索引roll over，你可能会在不经意间（inadvertently）创建没有文档的索引。这些空的索引没有什么用但仍然会消耗资源。

&emsp;&emsp;你可以使用[ cat count API](####cat count API)找出空的索引。

```text
GET _cat/count/my-index-000001?v=true
```

&emsp;&emsp;一旦有了空的索引列表，你可以使用[delete index API](####Delete index API)删除他们。你也可以删除不再与需要的索引。

```text
DELETE my-index-000001
```

##### Force merge during off-peak hours

&emsp;&emsp;如果你不再往一个索引中写入数据，你可以使用[force merge API](####Force merge API)将小段[merge](###Merge)到大段中。 这样能降低分片开销并能提高查询速度。然而force merge是一种资源密集型（resource-intensive）的操作，如果可以的话，尽量在非高峰期（off-peak）运行。

```text
POST my-index-000001/_forcemerge
```

##### Shrink an existing index to fewer shards

&emsp;&emsp;如果你不再往一个索引中写入数据，你可以使用[shrink index API](####Shrink index API) 来降低分片数量。

&emsp;&emsp;ILM同样在warm阶段有[shrink action](####Shrink)。

##### Combine smaller indices

&emsp;&emsp;你也可以使用[reindex API ](####Reindex API)将mapping类似的索引合并到一个更大的索引中。对于时序数据，你可以将短时间周期（short time period）的索引重新索引到长时间周期的索引中。类，例如你可以基于索引名模板重新索引按天索引的数据，例如`my-index-2099.10.11`重新索引到按月索引的`my-index-2099.10`中。reindex之后，删除按月索引的索引。

```text
POST _reindex
{
  "source": {
    "index": "my-index-2099.10.*"
  },
  "dest": {
    "index": "my-index-2099.10"
  }
}
```

#### Troubleshoot shard-related errors

&emsp;&emsp;下面介绍的是常见的分片相关的错误的解决办法。

##### this action would add [x] total shards, but this cluster currently has [y]/[z] maximum shards open;

&emsp;&emsp; 集群的设置[cluster.max_shards_per_node](######cluster.max_shards_per_node)限制了集群中可以打开的分片的最大数量。这个错误说的是某个行为会超过这个限制。

&emsp;&emsp;如果你有信心保证你的更改不会降低集群的稳定性，你可以通过[cluster update settings API](####Cluster update settings API)临时的提高限制然后重新这个行为。.

```text
PUT _cluster/settings
{
  "persistent" : {
    "cluster.max_shards_per_node": 1200
  }
}
```

&emsp;&emsp;这种解决方式只能是临时解决方案。作为一个长期解决方案，我们建议在发生oversharded的数据层添加节点或者[reduce your cluster’s shard count](####Reduce a cluster’s shard count)。在做出变更后可以通过[ cluster stats API](####Cluster stats API)查看当前的分片数量。

```text
GET _cluster/stats?filter_path=indices.shards.total
```

&emsp;&emsp;当部署了一个长期解决方案后，我们建议你reset `cluster.max_shards_per_nod`。

```text
PUT _cluster/settings
{
  "persistent" : {
    "cluster.max_shards_per_node": null
  }
}
```

### Use Elasticsearch for time series data
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/use-elasticsearch-for-time-series-data.html#search-visualize-your-data)

&emsp;&emsp;Elasticsearch提供的功能能帮助你存储，管理，以及搜索时序数据，例如日志和指标。使用了Elasticsearch后，你可以使用Kibana和其他Elastic Stack来分析以及可视化你的数据。

#### Set up data tiers

&emsp;&emsp;Elasticsearch的[ILM](###ILM: Manage the index lifecycle)使用[data tiers](###Data tiers)根据索引的寿命（age）自动的将旧的数据移动到成本较低的硬件中。这有助于提高性能以及降低存储开销。

&emsp;&emsp;hot和content层是必须要有的，而warm，cold以及frozen 层是可选的。

&emsp;&emsp;在hot和warm层使用高性能的节点用于快速的索引和查询最近的数据。在cold和frozen层使用较慢，成本较低的节点来降低开销。

&emsp;&emsp;设置datatiers的步骤基于你的部署类型：

- Elasticsearch Service
  - 登陆到[Elasticsearch Service Console](https://www.elastic.co/cn/cloud/elasticsearch-service/signup?baymax=docs-body&elektra=docs)
  - 在Elasticsearch Service 主页或者部署页面添加或者选择你的部署
  - 从你的部署菜单中，选择**Edit deployment**
  - 若要开启一个data tier，点击**Add capacity**
  - Enable autoscaling
    - [Autoscaling](https://www.elastic.co/guide/en/cloud/current/ec-autoscaling.html)能自动的调整你的部署的capacity来满足你的存储需要。若要开启autoscaling，在**Edit deployment**页面选择**Autoscale this deployment**。Autoscaling只会Elasticsearch Service可见
  
- Self-managed
  - 在节点的`elasticsearch.ym`文件中添加对应的[node role](#####Node roles)将这个节点分配到某个data tier。修改节点现有的节点角色要求[rolling restart](####Rolling restart)

```text
# Content tier
node.roles: [ data_content ]

# Hot tier
node.roles: [ data_hot ]

# Warm tier
node.roles: [ data_warm ]

# Cold tier
node.roles: [ data_cold ]

# Frozen tier
node.roles: [ data_frozen ]
```

&emsp;&emsp;我们建议你在frozen tier使用专门的节点。如果有需要的话，你可以给一个节点赋予多个data tier。

```text
node.roles: [ data_content, data_hot, data_warm ]
```

&emsp;&emsp;为你集群中的节点分配其他的角色。例如，一个小规模的集群中一个节点可以有多个角色。

```text
node.roles: [ master, ingest, ml, data_hot, transform ]
```

#### Register a snapshot repository ()

&emsp;&emsp;cold和frozen层可以使用[searchable snapshots](###Searchable snapshots)来降低本地储存开销。

&emsp;&emsp;若要使用searchable snapshots，你必须要注册一个支持的snapshots repository。注册的步骤取决于你的部署和提供的存储而有所不同：

##### Elasticsearch Service
  - 当你创建一个集群，Elasticsearch Service自动的注册一个默认的[found-snapshots repository](https://www.elastic.co/guide/en/cloud/current/ec-snapshot-restore.html)。这个仓库支持searchable snapshots。
  - `found-snapshots`仓库特定于你的集群。见[Share a repository across clusters](https://www.elastic.co/guide/en/cloud/current/ec_share_a_repository_across_clusters.html)使用其他集群默认的仓库。
  - 你也可以下列自定义的仓库类型用于searchable repository：
    - [Google Cloud Storage (GCS)](https://www.elastic.co/guide/en/cloud/current/ec-gcs-snapshotting.html)
    - [Azure Blob Storage](https://www.elastic.co/guide/en/cloud/current/ec-azure-snapshotting.html)
    - [Amazon Web Services (AWS)](https://www.elastic.co/guide/en/cloud/current/ec-aws-custom-repository.html)

##### Self-managed
  - 使用下列任意的仓库用于searchable repository：
    - [AWS S3](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/repository-s3.html)
    - [Google Cloud Storage](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/repository-gcs.html)
    - [Azure Blob Storage](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/repository-azure.html)
    - [Hadoop Distributed File Store (HDFS)](https://www.elastic.co/guide/en/elasticsearch/plugins/8.2/repository-hdfs.html)
    - [Shared filesystems](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/snapshots-filesystem-repository.html) such as NFS
    - [Read-only HTTP and HTTPS repositories](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/snapshots-read-only-repository.html)

  - You can also use alternative implementations of these repository types, for instance MinIO, as long as they are fully compatible. Use the Repository analysis API to analyze your repository’s suitability for use with searchable snapshots

#### Create or edit an index lifecycle policy

&emsp;&emsp;[data stream](##Data streams)在多个backing索引中存储你的数据。ILM使用一个[index lifecycle policy](####Index lifecycle)自动的将这些索引移动到你的data tier。

&emsp;&emsp;如果你使用Fleet或者Elastic Agent，可以编辑Elasticsearch内置的lifecycle policy。如果你使用自定义的应用，则需要创建你自己的策略：

- 包括你配置的每一个data tier的阶段内容
- 从rollover中为阶段转换（phase transition）计算阈值，或`min_age`
- 如果需要的话，在cold和frozen阶段使用searchable snapshots
- 如果需要的话，包含一个delete 阶段

##### Fleet or Elastic Agent

&emsp;&emsp;Fleet和Elastic Agent使用下列内置的生命周期策略“

- logs
- metrics
- synthetics

&emsp;&emsp;你可以基于你自己的性能，弹性（resilience）以及保留需求（retention requirement）自定义策略。

&emsp;&emsp;若要在Kibana中编辑一个策略，打开主菜单然后跳转到**Stack Management > Index Lifecycle Policies**，点击你想要编辑的策略。

&emsp;&emsp;你可以使用[update lifecycle policy API](####Create or update lifecycle policy API)。

```text
PUT _ilm/policy/logs
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover": {
            "max_primary_shard_size": "50gb"
          }
        }
      },
      "warm": {
        "min_age": "30d",
        "actions": {
          "shrink": {
            "number_of_shards": 1
          },
          "forcemerge": {
            "max_num_segments": 1
          }
        }
      },
      "cold": {
        "min_age": "60d",
        "actions": {
          "searchable_snapshot": {
            "snapshot_repository": "found-snapshots"
          }
        }
      },
      "frozen": {
        "min_age": "90d",
        "actions": {
          "searchable_snapshot": {
            "snapshot_repository": "found-snapshots"
          }
        }
      },
      "delete": {
        "min_age": "735d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}
```

##### Custom application

&emsp;&emsp;若要在Kibana中创建一个策略，打开主菜单然后跳转到**StackManagement > Index Lifecycle policies**。点击 **Create policy**。

&emsp;&emsp;你可以使用[update lifecycle policy API](####Create or update lifecycle policy API)。

```text
PUT _ilm/policy/my-lifecycle-policy
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover": {
            "max_primary_shard_size": "50gb"
          }
        }
      },
      "warm": {
        "min_age": "30d",
        "actions": {
          "shrink": {
            "number_of_shards": 1
          },
          "forcemerge": {
            "max_num_segments": 1
          }
        }
      },
      "cold": {
        "min_age": "60d",
        "actions": {
          "searchable_snapshot": {
            "snapshot_repository": "found-snapshots"
          }
        }
      },
      "frozen": {
        "min_age": "90d",
        "actions": {
          "searchable_snapshot": {
            "snapshot_repository": "found-snapshots"
          }
        }
      },
      "delete": {
        "min_age": "735d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}
```

#### Create component templates

> TIP：如果你使用Fleet或者Elastic Agent，跳过[Search and visualize your data](###Search and visualize your data)。Fleet和Elastic Agent使用内置的模板为你创建data streams

&emsp;&emsp;如果你使用自定义的应用，你需要设置你自己的data stream。data stream要求用于匹配的index template。大多数情况下，你会使用一个或多个component template组合成index template。你通常为mapping和index setting使用不同的component template。这可以让你在多个index template中复用component template。

&emsp;&emsp;当你创建component template，包括：

- 为`@timestamp`域使用[date](####Date field type)或者[date_nanos](####Date nanoseconds field type)。如果你不指定mapping，Elasticsearch默认为`@timestamp`使用`date`域。
- 索引设置中`index.lifecycle.name` 设置生命周期策略

> TIP：当对域进行映射时，使用[Elastic Common Schema (ECS)](https://www.elastic.co/guide/en/ecs/8.2/index.html)。ECS fields integrate with several Elastic Stack features by default。
> 
> 如果你不确定如何映射域，使用[runtime fields](####Define runtime fields in a search request)在查询期间从[unstructured content ](#####Wildcard field type
)中提取域。例如你可以索引一条日志消息为`wildcard`，随后在查询中从这个域中提取出IP address和其他数据。

&emsp;&emsp;若要在Kibana中创建一个component template，那么打开主菜单然后跳转到**Stack Management > Index Management**。在**Index Templates**视图中，点击**Create component template**。

&emsp;&emsp;你也可以使用[create component template API](####Create or update component template API)。

```text
# Creates a component template for mappings
PUT _component_template/my-mappings
{
  "template": {
    "mappings": {
      "properties": {
        "@timestamp": {
          "type": "date",
          "format": "date_optional_time||epoch_millis"
        },
        "message": {
          "type": "wildcard"
        }
      }
    }
  },
  "_meta": {
    "description": "Mappings for @timestamp and message fields",
    "my-custom-meta-field": "More arbitrary metadata"
  }
}

# Creates a component template for index settings
PUT _component_template/my-settings
{
  "template": {
    "settings": {
      "index.lifecycle.name": "my-lifecycle-policy"
    }
  },
  "_meta": {
    "description": "Settings for ILM",
    "my-custom-meta-field": "More arbitrary metadata"
  }
}
```

#### Create an index template

&emsp;&emsp;使用你的component templates来创建一个index template：

- 一个或多个index pattern满足data stream的名字，我们建议你使用我们的[data stream naming scheme](https://www.elastic.co/guide/en/fleet/8.2/data-streams.html#data-streams-naming-scheme)
- That the template is data stream enabled
- 任何一个component template都包含你的mapping和索引设置
- A priority higher than 200 to avoid collisions with built-in templates. See[ Avoid index pattern collisions](#####Avoid index pattern collisions)

&emsp;&emsp;若要在Kibana中创建一个index template，那么打开主菜单然后跳转到**Stack Management > Index Management**。在**Index Templates**视图中，点击**Create template**。

&emsp;&emsp;你也可以使用[create index template API](####Create or update index template API)。包含`data_stream` object来开启data streams。

```text
PUT _index_template/my-index-template
{
  "index_patterns": ["my-data-stream*"],
  "data_stream": { },
  "composed_of": [ "my-mappings", "my-settings" ],
  "priority": 500,
  "_meta": {
    "description": "Template for my time series data",
    "my-custom-meta-field": "More arbitrary metadata"
  }
}
```

####  Add data to a data stream

&emsp;&emsp;[Indexing requests](####Add documents to a data stream)将添加文档到data stream中。这些请求必须是使用`create`的`op_type`。文档中必须包含一个`@timestamp`域。

&emsp;&emsp;若要自动的创建你的data stream，指定stream的名字并提交一个索引请求。这个名字必须匹配你的index template的index patterns中的一个。

```text
PUT my-data-stream/_bulk
{ "create":{ } }
{ "@timestamp": "2099-05-06T16:21:15.000Z", "message": "192.0.2.42 - - [06/May/2099:16:21:15 +0000] \"GET /images/bg.jpg HTTP/1.0\" 200 24736" }
{ "create":{ } }
{ "@timestamp": "2099-05-06T16:25:42.000Z", "message": "192.0.2.255 - - [06/May/2099:16:25:42 +0000] \"GET /favicon.ico HTTP/1.0\" 200 3638" }

POST my-data-stream/_doc
{
  "@timestamp": "2099-05-06T16:21:15.000Z",
  "message": "192.0.2.42 - - [06/May/2099:16:21:15 +0000] \"GET /images/bg.jpg HTTP/1.0\" 200 24736"
}
```

#### Search and visualize your data

&emsp;&emsp;若要在Kibana中展示并且查询你的数据。打开主菜单然后选择**Discover**。见Kibana的[Discover documentation](https://www.elastic.co/guide/en/kibana/8.2/discover.html)。

&emsp;&emsp;使用Kibana的**Dashboard**功能，在图表、表格、地图中可视化你的数据。见Kibana的[Discover documentation](https://www.elastic.co/guide/en/kibana/8.2/discover.html)。

&emsp;&emsp;你也可以使用[search API](####Search API)查询或者聚合你的数据。使用[runtime fields ](####Define runtime fields in a search request)和[grok patterns ](###Grok basics)自动的在查询阶段从日志消息和其他非结构化数据中提取数据。

```text
GET my-data-stream/_search
{
  "runtime_mappings": {
    "source.ip": {
      "type": "ip",
      "script": """
        String sourceip=grok('%{IPORHOST:sourceip} .*').extract(doc[ "message" ].value)?.sourceip;
        if (sourceip != null) emit(sourceip);
      """
    }
  },
  "query": {
    "bool": {
      "filter": [
        {
          "range": {
            "@timestamp": {
              "gte": "now-1d/d",
              "lt": "now/d"
            }
          }
        },
        {
          "range": {
            "source.ip": {
              "gte": "192.0.2.0",
              "lte": "192.0.2.255"
            }
          }
        }
      ]
    }
  },
  "fields": [
    "*"
  ],
  "_source": false,
  "sort": [
    {
      "@timestamp": "desc"
    },
    {
      "source.ip": "desc"
    }
  ]
}
```

&emsp;&emsp;Elasticsearch的查询默认是同步操作。跨frozen data，long time ranges，large datasets这些查询可能需要较长时间。可以使用[async search API ](#####Submit async search API)在后台允许这些查询。更多的查询选项见[Search your data](##Search your data)。

```text
POST my-data-stream/_async_search
{
  "runtime_mappings": {
    "source.ip": {
      "type": "ip",
      "script": """
        String sourceip=grok('%{IPORHOST:sourceip} .*').extract(doc[ "message" ].value)?.sourceip;
        if (sourceip != null) emit(sourceip);
      """
    }
  },
  "query": {
    "bool": {
      "filter": [
        {
          "range": {
            "@timestamp": {
              "gte": "now-2y/d",
              "lt": "now/d"
            }
          }
        },
        {
          "range": {
            "source.ip": {
              "gte": "192.0.2.0",
              "lte": "192.0.2.255"
            }
          }
        }
      ]
    }
  },
  "fields": [
    "*"
  ],
  "_source": false,
  "sort": [
    {
      "@timestamp": "desc"
    },
    {
      "source.ip": "desc"
    }
  ]
}
```

## REST APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rest-apis.html)

### API conventions
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/api-conventions.html#time-units)

#### Date math support in index and index alias names

#### Multi-target syntax

##### ignore_unavailable

##### allow_no_indices

#### Byte size units

#### Time units

### Common options
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/common-options.html#date-math)

#### Date Math

#### Response Filtering

### Compact and aligned text (CAT) APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rest-apis.html)

#### cat fielddata API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/cat-fielddata.html)

#### cat indices API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/cat-indices.html)

#### cat recovery API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/cat-recovery.html)

#### cat shards API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/cat-shards.html)

#### cat thread pool API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/cat-thread-pool.html)

### Cluster APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-update-settings.html)

#### Cluster allocation explain API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/cluster-allocation-explain.html)

#### Cluster get settings API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/cluster-get-settings.html)

#### Cluster health API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/cluster-health.html)

#### Cluster state API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/cluster-state.html)

#### Cluster update settings API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/cluster-update-settings.html)

#### Nodes reload secure settings API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/cluster-nodes-reload-secure-settings.html)

#### Nodes hot threads API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/cluster-nodes-hot-threads.html)

#### Nodes stats API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/cluster-nodes-stats.html)

#### Remote cluster info API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/cluster-remote-info.html)

#### Task management API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/tasks.html)

#### Voting configuration exclusions API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/voting-config-exclusions.html)


### Cross-cluster replication APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/cluster-nodes-stats.html)

#### Create follower API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ccr-put-follow.html)

#### Pause follower API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ccr-post-pause-follow.html)

#### Get auto-follow pattern API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ccr-get-auto-follow-pattern.html)

#### Resume follower API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ccr-post-resume-follow.html)

#### Unfollow API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ccr-post-unfollow.html)

#### Get follower stats API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ccr-get-follow-stats.html)

#### Delete auto-follow pattern API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ccr-delete-auto-follow-pattern.html)

#### Create auto-follow pattern API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ccr-put-auto-follow-pattern.html)

#### Pause auto-follow pattern API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ccr-pause-auto-follow-pattern.html)

#### Resume auto-follow pattern API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ccr-resume-auto-follow-pattern.html)

### Data stream APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/data-stream-apis.html)

#### Create data stream API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-create-data-stream.html)

#### Delete data stream API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-delete-data-stream.htm)

#### Get data stream API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-get-data-stream.html)

#### Promote data stream API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/promote-data-stream-api.html)

### Document APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs.html)

#### Reading and Writing documents
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/docs-replication.html#basic-write-model)

##### Introduction

&emsp;&emsp;Elasticsearch中的每一个索引会[divided into shards](###Scalability and resilience: clusters, nodes, and shards)并且每一个分片都有多个拷贝。这些拷贝就是所谓的replication group并且当文档添加或者删除后，每一个replica都要保持同步。如果无法做到同步，那么可能就会导致从不同的拷贝中读取的结果各不相同。保持副本分片间的同步以及读取副本分片的处理过程称为`data replication model`。

&emsp;&emsp;Elasticsearch的data replication model基于主备模式（primary-backup model），微软的一篇论文[PacificA paper](https://www.microsoft.com/en-us/research/publication/pacifica-replication-in-log-based-distributed-storage-systems/)很好的描述了这种模式。这个模型就是从replication group中拿出一个拷贝作为主分片（primary shard），其他的拷贝作为副本分片（replica shard）。主分片用于服务所有索引操作的入口点（entry point）。主分片负责非法检查并保证索引操作的正确性。一旦主分片接受了某个索引操作，主分片负责对其他的副本分片复制这次索引操作。

&emsp;&emsp;本章的内容从high level的视角来了解Elasticsearch的replication model并讨论读操作与写操作之间不同的交互（interaction）带来的影响（implication）。

##### Basic write model

&emsp;&emsp;Elasticsearch中的每一个索引操作首先基于文档编号（document ID）通过[routing](######Routing(REST APIs))解析（resolve）replication group。一旦检测到replication group，这个操作会在内部转发到组中当前的主分片中。这个索引阶段称为`coordinating stage`。

&emsp;&emsp;索引阶段的下一个阶段就是`primary stage`，在主分片上执行。主分片负责对索引操作进行验证并转发到其他副本分片。由于副本分片可能处于离线状态，主分片不要求一定要转发到所有的副本分片。Elasticsearch维护了副本分片的列表，这些副本会收到主分片的转发。这个列表称为`in-sync copies` 并且master node负责维护。正如in-sync这个名字一样，列表中的都是"good" 副本分片并且他们已经处理好了所有的索引和删除操作并且响应给了用户。主分片负责维护这个列表因此会复制操作给列表中的每一个副本分片。

&emsp;&emsp;主分片遵循下面基本的步骤：

- 检查索引操作，如果结构非法则reject它（例如期望值是数值类型的域值，但是期望值是数值）
- 本地执行操作。例如索引或者删除相关的文档。同样会检查域的内容，如果必要的话会reject。（例如Keyword类型的域值太长，Lucene中无法处理它）
- 将操作转发到`in-sync`列表中的副本分片。如果列表中有多个副本分片，则会并发处理
- 一旦`in-sync`列表中所有的副本分片都成功执行了操作并且响应了主分片，主分片会将成功完成请求的消息响应给客户端

&emsp;&emsp;`in-sync`中的副本分片在本地执行索引操作。这个索引阶段称为`replica stage`。

&emsp;&emsp;这些索引阶段（coordination，primary，replica）是有序执行的。为了能实现内部重试（to enable internal retries），每一个阶段的时间（lifetime）包括（encompass）了 它的下一个阶段的时间。例如，coordination stage直到每一个primary stage完成才算是完成，primary stage可能会分散（spread out）到不同的主分片，等待他们完成。每一个primary stage直到每一个replica stage完成才算是完成，replica stage需要等到每一个副本分配完成本地的文档索引操作并且能成功的响应。

###### Failure handling

&emsp;&emsp;在索引期间会有很多事情会导致发生错误。磁盘损坏（corruption）节点失去连接或者一些错误的配置导致某个操作能在主分片上成功执行但是在副本分片上发生错误。尽管这些问题不常见，但是主分片必须负责处理这些问题。

&emsp;&emsp;如果主分片自身发生了错误，主分片所在的节点会像master node发送消息。索引操作会等待（[默认](####Dynamic index settings)一分钟），直到master晋升（promote）一个副本分片成为主分片。这个索引操作会被转发到新的主分片进行处理。注意的是master node会监控节点的健康度，可能会主动（proactive）降级（demote）一个主分片。发生这种情况一般是拥有主分片的节点因为网络问题从集群中分离（isolate）出来了。

&emsp;&emsp;一旦在主分片上成功的完成操作，主分片必须要处理在执行复制操作给副本分片后，replica stage出现错误的问题。有可能在副本分片上真的出现了错误或者因为网络问题导致复制操作无法到达副本分片，或者副本分片的响应无法到达主分片。这样的问题最终都有相同的结局：a replica which is part of the in-sync replica set misses an operation that is about to be acknowledged。In order to avoid violating the invariant，主分片往master node发送请求要求这个副本分配从`in-sync`中移除。Only once removal of the shard has been acknowledged by the master does the primary acknowledge the operation. Note that the master will also instruct another node to start building a new shard copy in order to restore the system to a healthy state。

&emsp;&emsp;在转发操作到副本分片时，主分片也可以利用副本分配来检查自己是否仍然是active primary。如果主分片由于network partition（long GC）已经被隔离了。它在认识到自己被降级前仍然继续处理索引操作。副本分配会reject来自失效的（stale）的主分片的操作。当主分片接收到副本分配的reject响应后，由于它不再是主分片，它会联系（reach out ）master node然后了解到自己的主分片地位被替换了。然后这个操作会路由到新的主分片。

> 如果没有replica的话会发生什么
> 由于索引配置或者简单来说所有的副本分配都失效的场景是存在的。在这种场景下，主分片处理操作并不需要任何外部的验证，看起来是有问题的。另一方面，主分片不能自己让其他shard而是需要向master node代表它这样做。这意味着master node知道这个主分片唯一"good"的分片。我们因此保证了master node 不会晋升其他的shard成为新的主分片，因此在这个主分片上的任意的索引操作都不会丢失。当然了，由于我们只在一个数据副本上允许，物理的硬件问题会导致数据丢失。见[Active shards](######Active shards)了解一些mitigation选项。

##### Basic read model

&emsp;&emsp;通过查找ID在Elasticsearch中读取是非常轻量的（weightlight），而带有复杂聚合操作的heavy 查询请求则会要求non-trivial的CPU power。主备模式（primary-backup）模式的优点之一是它让所有的副本分片的数据保持一致（with the exception of in-flight operations）。因此单个`in-sync`的分片足够用于服务读取请求。

&emsp;&emsp;当一个节点接收到一个读请求，这个节点负责转发这个请求到拥有相关分片的节点上，整合（collate）结果并响应给客户端。我们称那个节点为`coordinating node`。基本的流程如下所示：

1. 解析（resolve）这个请求对应的分片。注意的是由于大部分的请求都会被发送一个或者多个索引，所以通常来说都需要从多个分片中读取，每一个分片返回的内容都是一个数据子集
2. 从replication group中选择一个相关的分片的active copy。可以是主分片或者是副本分片。默认情况下，Elasticsearch使用[adaptive replica selection](####Adaptive replica selection)来选择分片
3. 发送分片级别（shard level）的读请求到选择的分片上
4. 组合结果和响应。注意的是在通过ID查询的场景中，只有一个分片是相关的，因此这个步骤可以跳过

###### Shard failures

&emsp;&emsp;当一个分片错误的响应读请求后，coordinating node会将请求发送到同属replication group中的其他分片。重复的失败会导致没有可用的分片。

&emsp;&emsp;为了保证快速的响应，下面的APIs在出现一个或者多个失败后返回部分结果：

- [Search](####Search API)
- [Multi Search](####Multi search API)
- [Multi Get](####Multi get \(mget\) API)


&emsp;&emsp;包含部分结果的响应仍旧提供一个`200 OK`的HTTP 状态码。Shard failure会在响应头的`timed_out`和`_shards` 中指出。

##### A few simple implications

&emsp;&emsp;这些基本流程中的每一个都决定了Elasticsearch作为一个读写系统的行为。进一步的说，由于读写请求可以并发执行，它们相互影响。

###### Efficient reads 

&emsp;&emsp;正常情况下一次读操作只会读取相关的replication group中的分片一次。只有在出错的情况下才会对多个相同的分片执行相同的查询。

###### Read unacknowledged

&emsp;&emsp;由于主分片先在自己本地进行索引，然后再复制这个请求到其他分片。有可能出现一个并发的读操作会看到这个未确认（等待其他所有副本分片更改完成的确认）的更改。

###### Two copies by default

&emsp;&emsp;主备模型可以在在仅仅维护两个副本的情况下实现容错，而相比较quorum-based的系统中用于容错的最小的副本数量需要3个。

##### Failures

&emsp;&emsp;发生错误后，下面的情况仍然可能发生。

###### A single shard can slow down indexing

&emsp;&emsp;每一个操作中，由于主分片会等待`in-sync`中所有的副本分片完成。单个slow分片会导致整个replication group变慢。这个是上文Efficient reads带来的代价。当然一个slow分片也会降低路由到这个分片的查询。

###### Dirty reads

&emsp;&emsp;被隔离的（isolate）的主分片仍然会暴露未被确认的数据。这是由于这个分片不会意识到自己已经被隔离了直到它发送请求到副本分片或者通过master node得知。在这个时间点，索引操作已经索引到主分片本地并且可以被并发的读操作读取变更。Elasticsearch通过每一秒对master node进行ping来缓解（mitigation）这个风险，并且在不知道主节点时reject索引操作。

##### The Tip of the Iceberg

&emsp;&emsp;当前这篇文档提供了一个high level的角度介绍了Elasticsearch如何处理数据，当然在底层有着更多的内容。例如primary terms，cluster state publishing以及master election，所有这一切都是为了让这个系统有正确的行为。这篇文档同样没有覆盖已知的并且重要的bug( closed以及open)。我们认识到[GitHub is hard to keep up with](https://github.com/elastic/elasticsearch/issues?q=label%3Aresiliency)，所以我们在页面维护了一个专门的[resiliency page](https://www.elastic.co/guide/en/elasticsearch/resiliency/current/index.html)，强烈建议阅读这些内容。

#### Index API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html)

###### Create document IDs automatically

###### Routing(REST APIs)

&emsp;&emsp;

###### Active shards


#### Get API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/docs-get.html)

#### Delete API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/docs-delete.html)

#### Update API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/docs-update.html)

#### Update By Query API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/docs-update-by-query.html)

#### Bulk API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/docs-bulk.html)

#### Reindex API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/docs-reindex.html)

##### Reindex from remote

#### Term vectors API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/docs-termvectors.html)

#### ?refresh(api)
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/docs-refresh.html#docs-refresh)

### EQL APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/eql-apis.html)

#### EQL search API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/eql-search-api.html#eql-search-api)

### Features APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/features-apis.html)

#### Get Features API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/get-features-api.html)

#### Reset features API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/reset-features-api.html)

### Index APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices.html)

#### Aliases API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-aliases.html)

#### Analyze API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-analyze.html)

#### Analyze index disk usage API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-disk-usage.html)

#### Clear cache API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-clearcache.html)

#### Clone index API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-clone-index.html)

#### Close index API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-close.html)

#### Create index API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-create-index.html)

##### Examples

###### Mappings

#### Create or update component template API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-component-template.html)

#### Create or update index template API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-templates-v1.html)

>IMPORTANT：这篇文档介绍legacy index templates。在Elasticsearch7.8中legacy index templates已被弃用并且使用可组合的模板（composable  template）代替。更多关于composable templates的信息见[Index templates](##Index templates)。

&emsp;&emsp;创建或者更新一个index template

```json
PUT _template/template_1
{
  "index_patterns": ["te*", "bar*"],
  "settings": {
    "number_of_shards": 1
  },
  "mappings": {
    "_source": {
      "enabled": false
    },
    "properties": {
      "host_name": {
        "type": "keyword"
      },
      "created_at": {
        "type": "date",
        "format": "EEE MMM dd HH:mm:ss Z yyyy"
      }
    }
  }
}
```

##### Request

`PUT /_template/<index-template>`

##### Prerequisites

- 如果开启了Elasticsearch security功能，你必须有`manage_index_templates`或者`manage` [cluster privilege](#####Cluster privileges)来使用这个API。

##### Description

&emsp;&emsp;index template定义了[settings](####Index Settings)和[mappings](##Mapping)并且自动应用到新建的索引上。Elastic search基于index pattern匹配索引名字来应用到新的索引上。

>NOTE：可组合的索引（composable templates）总是优先于（take precedence）legacy templates。如果composable templates没有匹配到新的索引，则根据其顺序应用匹配的旧模板

&emsp;&emsp;只在索引创建期间应用index template。index template的更改不会影响现有的索引。[create index](####Create index API) API请求中指定的settings和mappings会覆盖模板中settings和mapping。

###### Comments in index templates

&emsp;&emsp;你可以在index template中使用 C 风格的 /* */ 块注释。除了开始的大括号之前，你可以在请求正文中的任何位置包含注释。

######  Getting templates

&emsp;&emsp;见[Get index template (legacy)](####Get index template API)。

##### Path parameters
`<index-template>`
（必选，字符串）用于创建index template的名字。

##### Query parameters

###### create
&emsp;&emsp;（可选项，布尔值）如果为`true`，这个请求不能替换或者更新现有的index template。默认值为`false`。

###### order
&emsp;&emsp;（可选项，整数）如果索引匹配到多个模板，Elasticsearch根据order的值来应用模板。

&emsp;&emsp;首先合并order值较低的模板。order值较高的模板稍后合并，覆盖order值较低的模板。

###### master_timeout
&emsp;&emsp;（可选项，[time units](####Time units)）等待连接master节点的周期值。如果超时前没有收到响应，这个请求会失败并且返回一个错误。默认值是`30s`。

&emsp;&emsp;（未完成）

##### Request body

###### index_patterns

###### aliases(1)

#### Delete index API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-delete-index.html)

#### Field usage stats API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/field-usage-stats.html)

#### Flush API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-flush.html)

#### Force merge API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-forcemerge.html)

#### Get field mapping API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-get-field-mapping.html)

#### Get index template API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-get-template-v1.html)

#### Get mapping API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-get-mapping.html)

#### Index segments API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-segments.html)

#### Index stats API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-segments.html)

#### Rollover API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-rollover-index.html)

#### Shrink index API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-shrink-index.html)

#### Simulate index API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-simulate-index.html)

> WARNING：这个功能是属于技术预览，可能在未来的版本中移除或者更改。Elastic会尽力修复任何的问题，but features in technical preview are not subject to the support SLA of official GA features。

#### Simulate index template API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-simulate-template.html)

#### Split index API

(8.2) [link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-split-index.html)

&emsp;&emsp;将现有索引切分为拥有更多主分片的新索引。

```text
POST /my-index-000001/_split/split-my-index-000001
{
  "settings": {
    "index.number_of_shards": 2
  }
}
```

##### Request

```text
POST /<index>/_split/<target-index>
PUT /<index>/_split/<target-index>
```

##### Prerequisites

&emsp;&emsp;如果开启了Elasticsearch security features，对于这个索引，你必须要有管理权限[manage index privilege](####Security privileges)。

&emsp;&emsp;在开始切分这个索引前需要满足下面的条件

- 索引必须是只读
- [cluster health](####Cluster health API)的状态必须是绿色的

&emsp;&emsp;你可以通过下面的方式将索引设置为只读:

```text
PUT /my_source_index/_settings
{
  "settings": {
    "index.blocks.write": true 
  }
}
```

&emsp;&emsp;不允许写入的索引跟deleting index一样仍然允许进行元数据metadata的更改。

&emsp;&emsp;当前处于数据流[data streams](##Data streams)的write index不能被切分，如果要切分当前的write index，那么数据流必须先要执行[roll over](##Data streams)，使得一个新的write index被创建并且之前的write index就可以被切分了。

##### Description

&emsp;&emsp;索引切分API允许你将现有的索引切分到新的索引中，原本的一个主分片会被切分到两个或者多个主分片中。

&emsp;&emsp;索引被切分的次数取决于`index.number_of_routing_shards`，该值用于在内部将文档使用一致性哈希分布到分片上。例如，一个拥有5个分片，并且`index.number_of_routing_shards`的值设置为30时，那么每个分片可以按照因子2或者3进行划分，换句话说，可以按照下面的方式进行划分：

- 5 -> 10 -> 30 （5个分片中的每个分片先分为2份，然后10个分片的每个分片再分为3份）
- 5 -> 15 -> 30（5个分片中的每个分片先分为3份，然后15个分片中的每个分片再分为2份）
- 5 -> 30（5个分片中的每个分片分为6份）

&emsp;&emsp;`index.number_of_routing_shards`是一个[static](######Static)索引设置，只能在索引创建时期或者[关闭](####Open index API)时设置。

###### Index creation example

&emsp;&emsp;下面[create index API](####Create index API)中创建了名为`my-index-000001`的索引，并且设置`index.number_of_routing_shards`的值为30。

```text
PUT /my-index-000001
{
  "settings": {
    "index": {
      "number_of_routing_shards": 30
    }
  }
}
```

&emsp;&emsp;`index.number_of_routing_shards`的默认值取决于原先的索引的主分片数量。默认情况下允许按照因子2进行切分直到最大切分数量为1024。然而原先的分片数量会被考虑进来。比如5个主分片的索引可以被切分为10, 20, 40, 80, 160, 320, 或者最大值640 (一次或者多次切分)。

&emsp;&emsp;如果原先的索引的主分片数量是1（或者多个分片的索引被收缩[shrunk](####Shrink index API)到一个主分片），那么它会被切分为比1大的一个任意数值。默认的 routing shards的数量会被应用（apply）到新的索引中。

##### How splitting works

&emsp;&emsp;切分操作：

1. 创建一个跟源索引（source index）定义相同的目标索引（target index），但是目标索引拥有更多主分片数量。
1. 建立源索引到目标索引的硬链接（hard-linking）（如果操作系统不支持硬链接，那么所有的段会被复制到新的索引中，这是一个开销较大的过程）。
1. 对所有的文档进行hash操作，在low level files都创建后，删除那些属于不同的分片的文档
1. 恢复目标索引，就像是一个关闭的索引刚刚被重新打开那样

##### Why doesn’t Elasticsearch support incremental resharding?

&emsp;&emsp;从N个分片到N+1分片，又名增量分片，确实是许多键值存储支持的功能。 添加新分片并仅向该新分片中推送新数据不是一种选择：这可能是一个索引瓶颈，鉴于文档的\_id属于哪个分片，这是获取、删除和更新请求所必需的，这将变得相当复杂。 这意味着我们需要使用不同的散列方案重新平衡现有数据。

&emsp;&emsp;键值存储最常用的高效方法是使用一致的散列。 当分片数量从N增加到N+1时，一致的哈希只需要迁移1/N的键。 然而，Elasticsearch的存储单位是Lucene的索引。 由于其面向搜索的数据结构，占用Lucene索引的很大一部分，无论是只有5%的文档，删除它们并在另一个碎片上索引通常比键值存储的成本高得多。 如上一节所述，当通过乘法因子增加分片数量时，这一成本是合理的：这允许Elasticsearch在本地执行拆分，这种允许在索引级别执行拆分，而不是对需要移动的文档进行重新索引，并使用硬链接进行高效的文件复制。

&emsp;&emsp;对于仅追加数据，可以通过创建新索引并将新数据推送到其中，同时添加涵盖读取操作的旧索引和新索引的别名，从而获得更大的灵活性。 假设新旧索引中分别有M和N个分片，与搜索具有M+N分片的索引相比，这没有开销。

##### Split an index

&emsp;&emsp;执行下面的请求将名为`my_source_index`的索引切分到名为`my_target_index`

```text
POST /my_source_index/_split/my_target_index
{
  "settings": {
    "index.number_of_shards": 2
  }
}
```

&emsp;&emsp;上述的请求在索引`my_target_index`被添加到集群后就会返回，它不需要等待开始切分操作。

>IMPORTANT：
>索引在满足下面的要求后才能被切分
>
>1. 目标索引必须不存在
>2. 源索引必须比目标索引的主分片数量少
>3. 目标索引的主分片数量必须是源索引的倍数
>4. 处理切分的节点必须有足够的磁盘空间，要能容纳现有索引以及一份拷贝的大小

&emsp;&emsp;对于目标索引，API`_split`跟[create index api](####Create index API)一样接收参数`settings`和`aliases`

```text
POST /my_source_index/_split/my_target_index
{
  "settings": {
    "index.number_of_shards": 5 
  },
  "aliases": {
    "my_search_indices": {}
  }
}
```

&emsp;&emsp;第4行中，目标索引的主分片数量一定要大于源索引的主分片数量。

> NOTE：Mappings may not be specified in the`_split` request.

##### Monitor the split process

&emsp;&emsp;切分过程可以通过[\_cat recovery API](####cat recovery API)进行监控，也可以通过调用[cluster health API](####Cluster health API) ，等待所有主分片被分配完成后，参数`wait_for_status`被设置为`yellow`。

&emsp;&emsp;`_split`接口在目标索引添加到集群后，所有分片被分配前就会马上返回。在这个时间点，所有的分片处于`unassigned`状态，如果目标索引因为任何原因导致无法被分配，主分片会一直`unassigned`，直到在这个节点上能被分配。

&emsp;&emsp;一旦主分片被分配好了，它的状态就会变为`initializing`，然后切分操作就会开始。当切分操作结束后，主分片的状态就会变为`active`，这个时候，Elasticsearch就会尝试对主分片进行副本分片的分配，并将主分片迁移到其他节点。

##### Wait for active shards

&emsp;&emsp;因为切分操作会创建一个新的索引用于对分片的切分，索引创建中的[`wait for active shards`](####Create index API)这个设置同样作用于索引的切分。

##### Path parameters

###### \<index\>

&emsp;&emsp;(Required, string)待切分的源索引名字。

###### \<target-index\>

&emsp;&emsp;(Required, string)目标索引的名字。

&emsp;&emsp;索引名字必须满足下面的规范：

- 只允许小写
- 不能包含\, /, \*, ?, ", <, >, |, \` \` (space character), `,` , \#
- 在7.0之前允许包含`:`，7.0之后不被支持
- 不能以`-`, `_`, `+`开头
- 不能有 `.` 或者`..`
- 不能超过255个字节，有些字符用多个字节表示，所以更容易超过255个字节的限制
- 以`.`开头的索引名被弃用了，除了 [hidden indices](##Index Modules) 以及被插件使用的内部的索引名

##### Query parameters

###### wait_for_active_shards

&emsp;&emsp;(Optional, string) 开始切分前处于active状态的主分片数量。设置成`all`或者一个正整数，默认值1. 见[Active shards](####Index API)。

###### master_timeout

&emsp;&emsp;(Optional, [time units](###API conventions)) 连接等待master节点一段时间，如果没有收到response并且超时了，这次请求视为失败并且返回一个错误，默认值`30s`。

###### timeout

&emsp;&emsp;(Optional, [time units](###API conventions)) 等待返回response，如果没有收到response并且超时了，这次请求视为失败并且返回一个错误，默认值`30s`。

##### Request body

###### aliases(1)

&emsp;&emsp;(Optional, object of objects) 该参数用于生成的索引。

&emsp;&emsp;\<alias\>

&emsp;&emsp;&emsp;&emsp;(Required, object) 这个字段用来描述索引别名，索引别名支持 [date math](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/date-math-index-names.html)。

&emsp;&emsp;\<alias\>的属性：

- **filter**: (Optional, [Query DSL object](##Query DSL)) 用来限制文档访问的DSL语句。
- **index_routing**（: (Optional, string) 用于索引阶段到指定的分片进行写入索引，这个值会覆盖用于写入索引操作的参数`routing`
- **is_hidden**: (Optional, Boolean) 如果为true，那么别名是 [hidden](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-split-index.html#split-index-api-path-params)，默认为false，所有这个别名的索引都要有相同的`is_hidden`值。
- **is_write_index**: (Optional, Boolean) 如果为true，这个索引是这个别名中的[write index](##Aliases)，默认为false。
- **routing**: (Optional, string) 用来索引阶段或查询阶段路由到指定分片
- **search_routing**: (Optional, string) 用于查询阶段到指定的分片进行查询,这个值会覆盖用于查询操作的参数`routing`

###### settings

&emsp;&emsp;(Optional, [index setting object](##Index Modules)) 为目标索引的一些配置信息，见 [Index Settings](##Index Modules)。

#### Open index API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-open-close.html)

#### Resolve index API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-resolve-index-api.html)

#### Refresh API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-refresh.html)

#### Update index settings API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-update-settings.html)

##### Bulk indexing usage

#### Update mapping API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/indices-put-mapping.html)

### Ingest APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ingest-apis.html)

#### Create or update pipeline API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/put-pipeline-api.html)

##### Query parameters(Create or update pipeline API)

#### Simulate pipeline API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/simulate-pipeline-api.html)

### Index lifecycle management APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/index-lifecycle-management-api.html)

#### Create or update lifecycle policy API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-put-lifecycle.html)

#### Execute snapshot lifecycle policy API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/slm-api-execute-lifecycle.html)

#### Execute snapshot retention policy API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/slm-api-execute-retention.html)

#### Remove policy from index API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-remove-policy.html)

#### Get index lifecycle management status API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-get-status.html)

#### Explain lifecycle API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-explain-lifecycle.html)

#### Start index lifecycle management API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-start.html)

#### Stop index lifecycle management API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-stop.html)

### Machine learning APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ml-apis.html)

#### Open anomaly detection jobs API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ml-open-job.html)

#### Start datafeeds API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ml-start-datafeed.html)

#### Set upgrade mode API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ml-set-upgrade-mode.html)

### Rollup APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rollup-apis.html)

#### Create rollup jobs API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rollup-put-job.html)

#### Delete rollup jobs API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rollup-delete-job.html)

#### Get rollup jobs API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rollup-get-job.html)

#### Get rollup job capabilities API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rollup-get-rollup-caps.html)

#### Get rollup index capabilities API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rollup-get-rollup-index-caps.html)

#### Rollup search
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rollup-search.html)

#### Start rollup jobs API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rollup-start-job.html)

#### Stop rollup jobs API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/rollup-stop-job.html)

### Script APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/script-apis.html)

#### Create or update stored script API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/create-stored-script-api.html#create-stored-script-api)

#### Delete stored script API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/delete-stored-script-api.html)

#### Get stored script API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/get-stored-script-api.html)

### Search APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search.html)

#### Search API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-search.html)

##### Query parameters
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-search.html#request-body-search-query)


###### allow_partial_search_results

###### ccs_minimize_roundtrips

###### preference

###### query

###### search_type


#### Async search
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/async-search.html#submit-async-search)

##### Submit async search API

##### Get async search

##### Delete async search

#### Point in time API
(8.2)[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/point-in-time-api.html)

&emsp;&emsp;默认在目标索引的最新的可见数据上执行查询请求称为point in time。Elasticsearch PIT是一个轻量级针对于数据状态the state of the data的视图view，在数据初始化后就存在了。在有些场景中，更偏向于使用PIT来执行多次查询。例如，如果[refreshes](####Refresh API)发生在多个search_after请求之间，那么这些请求的结果可能不一致，因为每一次都在最新的PIT上执行查询。

##### Prerequisites

- 如果Elasticsearch security features开启了，你必须要有目标数据流data streams、索引、别名的[read index privilege](####Security privileges)。
- To search a [point in time (PIT)](####Point in time API) for an [alias](##Aliases)，you must have the read index privilege for the alias’s data streams or indices。

##### Examples

&emsp;&emsp;PIT被用来搜索前必须显示的打开，参数`keep_alive`告诉Elasticsearch我们需要保留这个PIT的时间。比如?keep_alive=5m。

```text
POST /my-index-000001/_pit?keep_alive=1m
```

&emsp;&emsp;上述请求的结果会返回一个`_id`，用于设置请求中参数`pit`中的`id`。

```text
POST /_search 
{
    "size": 100,
    "query": {
        "match" : {
            "title" : "elasticsearch"
        }
    },
    "pit": {
	    "id":  "46ToAwMDaWR5BXV1aWQyKwZub2RlXzMAAAAAAAAAACoBYwADaWR4BXV1aWQxAgZub2RlXzEAAAAAAAAAAAEBYQADaWR5BXV1aWQyKgZub2RlXzIAAAAAAAAAAAwBYgACBXV1aWQyAAAFdXVpZDEAAQltYXRjaF9hbGw_gAAAAA==", 
	    "keep_alive": "1m"  
    }
}
```

1. 使用`pit`查询的请求不能指定`index` 、`routing`等[参数](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-request-body.html#request-body-search-preference)，因为这些参数都已经从PIT中复制过来了。
2. 参数`id`告诉Elasticsearch在哪一个PIT上执行查询
3. 参数`keep_alive`告诉Elasticsearch延长这个PIT的live时间

>IMPORTANT：对于打开PIT的请求以及每一个后续的请求都会收到不一样的id，因此下一次查询总是用这个最新的id

##### Keeping point in time alive

&emsp;&emsp;参数`keep_alive`用于打开PIT的请求以及后续的查询请求，用来延长expand对应的PIT的存活时间time to live，这个值表示需要在这个时间内执行下一次查询，而不是在这个时间内必须处理完所有的结果。

&emsp;&emsp;通常来说，后台的段的合并操作会将一些较小的段合并成一个更大的新的段。一旦这些较小的段（已经被合并了）不再被使用那么就会被删除。然后PIT会使得这些段防止被删除因为他们还在使用中。

>TIP：保留旧的段意味着要求更多的磁盘空间以及文件句柄，确保在你的节点上配置好充足的ample的文件句柄，见[File Descriptors](####File Descriptors)

&emsp;&emsp;此外，如果一个段包含已删除或更新的文档，那么PIT必须跟踪段中的每个文档在初始化搜索请求时是否是live的（没有被删除）。如果你的节点上在某个索引上有很多PIT并且不管的有删除或者更新操作，保证节点上有足够的磁盘空间。注意的是PIT不会阻止它关联的索引被删除。

&emsp;&emsp;你可以使用[nodes stats API](####Nodes stats API)目前打开了多少个PIT。

```text
GET /_nodes/stats/indices/search
```

##### Close point in time API

&emsp;&emsp;当`keep_alive`过期后，PIT会自动的关闭。基于上文中的介绍，保留PIT的开销是很大，当PIT不再需要用于查询时应该马上关闭它。

```text
DELETE /_pit
{
    "id" : "46ToAwMDaWR5BXV1aWQyKwZub2RlXzMAAAAAAAAAACoBYwADaWR4BXV1aWQxAgZub2RlXzEAAAAAAAAAAAEBYQADaWR5BXV1aWQyKgZub2RlXzIAAAAAAAAAAAwBYgACBXV1aWQyAAAFdXVpZDEAAQltYXRjaF9hbGw_gAAAAA=="
}
```

&emsp;&emsp;上述调用返回下面的内容：

```text
{
   "succeeded": true, 
   "num_freed": 3     
}
```

1. 如果是true，PIT关联的所有search contexts都被成功关闭了
2. 成功关闭的search contexts的数量

##### Search slicing

&emsp;&emsp;当分页查询一个较大数据集时，可以切分成多次查询来消费数据集切分后的数据集。

```text
GET /_search
{
  "slice": {
    "id": 0,                      
    "max": 2                      
  },
  "query": {
    "match": {
      "message": "foo"
    }
  },
  "pit": {
    "id": "46ToAwMDaWR5BXV1aWQyKwZub2RlXzMAAAAAAAAAACoBYwADaWR4BXV1aWQxAgZub2RlXzEAAAAAAAAAAAEBYQADaWR5BXV1aWQyKgZub2RlXzIAAAAAAAAAAAwBYgACBXV1aWQyAAAFdXVpZDEAAQltYXRjaF9hbGw_gAAAAA=="
  }
}

GET /_search
{
  "slice": {
    "id": 1,
    "max": 2
  },
  "pit": {
    "id": "46ToAwMDaWR5BXV1aWQyKwZub2RlXzMAAAAAAAAAACoBYwADaWR4BXV1aWQxAgZub2RlXzEAAAAAAAAAAAEBYQADaWR5BXV1aWQyKgZub2RlXzIAAAAAAAAAAAwBYgACBXV1aWQyAAAFdXVpZDEAAQltYXRjaF9hbGw_gAAAAA=="
  },
  "query": {
    "match": {
      "message": "foo"
    }
  }
}
```

1. id是切分编号
2. 切分后的数据子集数量

&emsp;&emsp;第一个请求的结果返回属于第一个片的文档(id: 0)，第二个请求的结果返回属于第二个片的文档。由于切片的最大数量被设置为2，所以这两个请求的结果的并集等价于没有切片的时间点搜索的结果。默认情况下，首先在分片上进行分割，然后在每个分片上进行本地分割。本地分割基于Lucene的文档id将分片划分为连续的范围。

&emsp;&emsp;例如，如果分片的数量等于2，用户请求了4个分片，那么切片0和切片2被分配给第一个分片，切片1和切片3被分配给第二个分片。

>IMPORTANT：应该对所有片使用相同的时间点ID。如果使用不同的PIT id，则片可能会重叠和遗漏文档。这是因为拆分标准是基于Lucene文档id的，这些文档id在索引更改时变的不稳定。

#### Search template API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-template-api.html)

#### Multi search template API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/multi-search-template.html)

#### Render search template API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/render-search-template-api.html)

#### Search shards API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-shards.html)

#### Suggesters
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-suggesters.html)

##### Completion Suggester

#### Multi search API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-multi-search.html)

#### Profile API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-field-caps.html)


#### Field capabilities API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-field-caps.html)

#### Vector tile search API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-vector-tile-api.html)

### Searchable snapshots APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/searchable-snapshots-apis.html)

#### Mount snapshot API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/searchable-snapshots-api-mount-snapshot.html)

#### Cache stats API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/searchable-snapshots-api-cache-stats.html)

#### Searchable snapshot statistics API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/searchable-snapshots-api-stats.html)

#### Clear cache API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/searchable-snapshots-api-clear-cache.html)

### Security APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/security-api.html)

#### Role mappings
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/security-api.html#security-role-mapping-apis)

### Snapshot and restore APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/snapshot-restore-apis.html)

#### Create snapshot API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/create-snapshot-api.html)

#### Get snapshot API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/get-snapshot-api.html)

#### Clone snapshot API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/clone-snapshot-api.html)

#### Repository analysis API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/repo-analysis-api.html)

#### Delete snapshot API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/delete-snapshot-api.html)

### Snapshot lifecycle management APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/snapshot-lifecycle-management-api.html)

#### Create or update snapshot lifecycle policy API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/slm-api-put-policy.html)

#### Get snapshot lifecycle stats API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/slm-api-get-stats.html)

#### Get snapshot lifecycle policy API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/slm-api-get-policy.html)

### SQL APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/sql-apis.html)

#### SQL search API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/sql-apis.html)


### Transform APIs
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/transform-apis.html)

#### Create transform API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/put-transform.html)

#### Delete transform API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/put-transform.html)

#### Get transforms API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/get-transform.html)

#### Create API key API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/security-api-create-api-key.html)

#### Create or update role mappings API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/security-api-put-role-mapping.html)

#### Create or update roles API 
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/security-api-put-role.html)

#### Get transform statistics API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/get-transform-stats.html)

#### Preview transform API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/preview-transform.html)

#### Reset transform API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/reset-transform.html)

#### Start transform API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/start-transform.html)

#### Stop transforms API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/stop-transform.html)

#### Update transform API
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/update-transform.html)

## Cross-cluster search, clients, and integrations
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/ccs-clients-integrations.html)

### Adding and removing nodes
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-discovery-adding-removing-nodes.html)

&emsp;&emsp;见[Add and remove nodes in your cluster](###Add and remove nodes in your cluster)。

### Avoid oversharding
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/avoid-oversharding.html)

&emsp;&emsp;见[Size your shards](###Size your shards)。

### Configure TLS
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/configuring-tls.html#tls-transport)

#### Encrypt internode communication-1

&emsp;&emsp;见[Encrypt internode communication](#####Encrypt internode communications with TLS)。

### Cluster-level shard allocation filtering
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/allocation-filtering.html)

&emsp;&emsp;见[Cluster-level shard allocation filtering](#####Cluster-level shard allocation settings)。

### Cluster-level shard allocation
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/shards-allocation.html)

&emsp;&emsp;见[Cluster-level shard allocation settings](#####Cluster-level shard allocation settings)。

### Dangling indices-1
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-gateway-dangling-indices.html)

&emsp;&emsp;见[Dangling indices](#####Dangling indices)。

### fielddata mapping parameter(1)
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-gateway-dangling-indices.html)

&emsp;&emsp;见[fielddata mapping parameter](#####fielddata mapping parameter)。

### Full cluster restart upgrade
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/restart-upgrade.html)

&emsp;&emsp;当Elasticsearch升级到8.0或者更高版本时，你必须先升级到7.17，即使你优先执行一个full-cluster restart而不是rolling upgrade。更多关于upgrade的信息见[Upgrading to Elastic 8.2.3](https://www.elastic.co/guide/en/elastic-stack/8.2/upgrading-elastic-stack.html)。

### Grok basics
（8.2）[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/grok-basics.html#grok-basics)

&emsp;&emsp;见[Grokking grok](####Grokking grok)。

#### Create users

&emsp;&emsp;参考[elasticsearch-reset-password ](###elasticsearch-reset-password)工具来为内置的用户重置密码。

### Manage existing periodic indices with ILM
（8.2）[link](hhttps://www.elastic.co/guide/en/elasticsearch/reference/8.2/ilm-with-existing-periodic-indices.html)

&emsp;&emsp;见[Apply policies to existing time series indices](####Apply policies to existing time series indices)。

### Request body search
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/search-request-body.html)

#### Stored fields-1

&emsp;&emsp;见[Stored fields](#####Stored fields)。

### Snapshot module
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/modules-snapshots.html)

### Shard allocation awareness-1
[link](https://www.elastic.co/guide/en/elasticsearch/reference/8.2/allocation-awareness.html)

&emsp;&emsp;见[Shard allocation awareness](#####Shard allocation awareness)。



