---
title: Hadoop 学习笔记
comments: true
categories:
  - 学习
  - Hadoop
tags:
  - Hadoop
date: 2024-07-26 15:26:55
updated: 2024-07-26 15:26:55
---

# HDFS简介、设计目标和应用场景

## 简介

HDFS（Hadoop Distributed File System） 是 Apache Hadoop 的核心组件之一，作为大数据生态圈最底层的分布式存储服务而存在。

HDFS 主要是解决大数据如何存储问题的。其是一种能够在普通硬件上运行的分布式文件系统，它是高容错的，适应于具有大数据集的应用程序，非常适合存储大型数据（比如TB和PB）。

HDFD 使用多台计算机存储文件，并且提供了统一的访问接口，像访问一个普通文件系统一样使用分布式文件系统。

## HDFS设计目标

HDFS 可能有成千上百的服务器组成，每一个组件都有可能出现故障。因此故障检测和自动快速恢复是 HDFS 的核心架构目标。

HDFS 上的应用主要以流式读取数据（Streaming Data Access）。HDFS 因此被设计成用于批处理，而不是用户交互式的。相较于数据访问的反应时间，更注重数据访问的高吞吐量。

典型的 HDFS 文件大小是 GB 到 TB 的级别。所以 HDFS 被调整成支持大文件（Large Data Sets）。它应该提供很高的聚合数据带宽，一个集群中支持数百个节点，一个集群还应该支持千万级别的文件。

## 应用场景

### 适合场景

大文件、数据流式访问、一次写入多次读取、低成本部署，廉价PC、高容错。

### 不适合场景

小文件、数据交互式访问、频繁任意修改、低延迟处理。

# HDFS 重要特性解读

## 主从架构集群

一个 namenode 和一定数目的 datanode 组成。
namenode 是 HDFS 的主节点。管理元数据，记录每一个文件的信息，如何分块等信息。
datanode 是 HDFS 的从节点。存储 block 数据。

## 分块存储

HDFS 中的文件在物理上是分块（block）存储的，默认大小是 128M（134217728），不足 128M 的文件本身就是一块。

## 副本机制

文件的所有 block 都会有副本。副本系数可以在文件创建时可以指定，也可以在之后通过命令改变。
副本数由参数 dfs.replication 控制，默认值为 3，文件的所有 block 都会存储 3 份。

## 元数据记录

namenode 管理的元数据有两种类型：

1. 文件自身属性信息：文件名称、权限、修改时间、文件大小、复制因子、数据块大小。
2. 文件块位置映射信息：记录文件块 ID 等信息，及 datanode 之间的映射信息，即块位于哪个节点上。

## 抽象统一的目录树结构（namespace）

HDFS 支持传统的层次型文件组织结构。文件系统的名字空间的层次结构与大多现有的文件系统雷系：用户可以创建、删除、移动或重命名文件。
namenode 负责维护文件系统的 namespace 名称空间，任何对文件系统名称空间或属性的修改都将被 namenode 记录下来。
HDFS 给客户端提供一个统一的抽象目录树，客户端通过路径来访问文件，形如：hdfs://namenode:port/dir/file.txt

## 数据块存储

文件的各个 block 的具体存储管理由 datanode 节点承担。
每个 block 都可以在多个 datanode 上存储。

# HDFS 工作流程与机制——各角色职责

## 主角色 namenode

- NameNode 是 Hadoop 分布式文件系统的核心，架构中的主角色。
- NameNode 维护和管理文件系统元数据，包括名称空间目录树结构、文件和块的位置信息、访问权限等信息。
- 基于此，NameNode 成为了访问 HDFS 的唯一入口。
- NameNode 内部通过内存和磁盘文件两种方式管理元数据。
- 其中磁盘上的元数据文件包括 Fsimage 内存元数据镜像文件和 edits log(Journal) 编辑日志。

### namenode 职责

- NameNode 仅存储 HDFS 的元数据：文件系统中所有文件的目录树，并跟踪整个集群中的文件，不存储实际数据。
- NameNode 知道 HDFS 中任何给定文件的块列表及其位置。使用此信息 NameNode 知道如何从块中构建文件。
- NameNode 不持久化存储每个文件中各个块所在的 DataNode 的位置信息，这些信息会在系统启动时从 DataNode 重建。
- NameNode 是 Hadoop 集群中的单点故障。
- NameNode 所在机器通常会配置有大量内存（RAM）。

## 从角色 datanode

- DataNode 是 Hadoop HDFS 中的从角色，负责具体得数据快存储。
- DataNode 的数量决定了 HDFS 集群的整体数据存储能力。通过和 NameNode 配合维护着数据块。

### datanode 职责

- DataNode 负责最终数据块 block 的存储。是集群的从角色，也称为 Slave。
- DataNode 启动时，会将自己注册到 NameNode 并汇报自己负责持有的块列表。
- 当某个 DataNode 关闭时，不会影响数据的可用性。NameNode 将安排由其他 DataNode 管理的块进行副本复制。
- DataNode 所在的机器通常配置有大量的磁盘空间，因为实际数据存储在 DataNode 中。

## 主角色辅助角色 secondarynamenode

- Secondary NameNode 充当 NameNode 的辅助节点，但不能替代 NameNode。
- 主要帮助主角色进行元数据文件的合并动作。可以通俗的理解为主角色的“秘书”。

# HDFS 工作流程与机制——写数据流程——核心概念

## 写数据流程图

## 核心概念：Pipeline 管道

客户端将数据库写入第一个数据节点，第一个数据节点保存数据之后再将块复制到第二个数据节点，第二个数据节点保存后再将块复制到第三个数据节点。数据以管道方式，顺序的沿着一个方向传输，这样能够充分利用每个机器的贷款，避免网络瓶颈和高延时的连接，最小化推送所有数据的延时。

## 核心概念：ACK应答响应

ACK（Acknowledge character），在数据通信中，接收方发给发送方的一种传输类控制字符。表示发来的数据已确认接收无误。
在 HDFS pipeline 传输数据过程中，传输的反方向会进行 ACK 校验，确保数据传输安全。

## 核心概念：默认三副本存储策略

默认副本存储策略是由 BlockPlacementPolicyDefault 指定。
第一块副本：优先客户端本地，否则随机。
第二块副本：不同于第一块副本的不同机架。
第三块副本：第二块副本相同机架不同机器。

# HDFS 工作流程与机制——写数据流程——梳理

1. HDFS 客户端创建对象实例 DistributedFileSystem，该对象中封装了与 HDFS 文件系统操作的相关方法。

2. 调用 DistributedFileSystem 对象的 create() 方法，通过 RPC 请求 NameNode 创建文件。

   NameNode 执行各种检查判断：目标文件是否存在、父目录是否存在、客户端是否具有创建该文件的权限。检查通过 NameNode 就会为本次请求记录下一条记录，返回 FSDataOutputStream 输出流对象给客户端用于写数据。

3. 客户端通过 FSDataOutputStream 输出流开始写入数据。

4. 客户端写入数据时，将数据分成一个个数据包（packet 默认 64k），内部组件 DataStreamer 请求 NameNode 挑选出适合存储数据副本的一组 DataNode 地址，默认是 3 副本存储。

   DataStreamer 将数据包流式传输到 pipeline 的第一个 DataNode, 该 DataNode 存储数据包并将它发送到 pipeline 的第二个 DataNode。同样，第二个 DataNode 存储数据包并且发送给第三个（也就是最后一个） DataNode。

5. 传输的反方向上，会通过 ACK 机制校验数据包传输是否成功；

6. 客户端完成数据写入后，在 FSDataOutputStream 输出流商调用 close() 方法关闭。

7. DistributedFileSystem 联系 NameNode 告知其文件写入完成，等待 NameNode 确认。
   因为 NameNode 已经知道文件由哪些块组成（DataStream 请求分配数据块），因此仅需等待最小复制块即可返回成功。

   最小复制是由参数 dfs.namenode.replication.min 指定，默认是 1。

# MapReduce 的设计思想

## 如何对付大数据处理场景

1. 对相互不具计算依赖关系的大数据计算任务，实现并行最自然的办法就是采用 MapReduce 分而治之的策略。
2. 首先 Map 阶段进行拆分，把大数据拆分成若干份小数据，多个程序同时并行计算产生中间结果；然后是 Reduce 聚合阶段，通过程序对并行的结果进行最终的汇总计算，得出最终结果。

## 构建抽象编程模型

MapReduce 借鉴了函数式语言中的思想，用 Map 和 Reduce 两个函数提供了高层的并行编程抽象模型。

map：对一组数据元素进行某种重复是的处理；

reduce：对 Map 的中间结果进行某种进一步的结果整理。

MapReduce 中定义了 Map 和 Reduce 两个抽象的编程接口，由用户去编程实现：

map: (k1:v1) -> (k2:v2)

reduce: (k2:[v2]) -> (k3:v3)

通过以上两个编程接口，可以看出 MapReduce 处理的数据类型是 <key, value> 键值对。

## 统一架构、隐藏底层细节

如何提供统一的计算框架，如果没有统一封装底层细节，那么程序员则需要考虑诸如数据存储、划分、分发、结果收集、错误恢复等诸多细节；为此，MapReduce 设计并提供了统一的计算框架，为程序员隐藏了绝大多数系统层面的处理细节。

MapReduce 最大的亮点在于通过抽象模型和计算框架把需要做什么和具体怎么做分开了，为程序员提供了一个抽象和高层的编程接口和框架。

程序员仅需要关心其应用层的具体计算问题，仅需编写少量处理应用本身计算问题的业务程序代码。

至于如何具体完成这个并行计算任务所相关的诸多系统层细节被隐藏起来，交给框架去处理：从分布代码的执行，到大到数千小到单个节点集群的自动调度使用。

# MapReduce 介绍

MapReduce 是一个分布式计算框架，用于轻松编写分布式应用程序，这些应用程序以可靠、容错的方式并行处理大型硬件集群（数千个节点）上的大量数据（多TB数据集）。

MapReduce 是一种面向海量数据处理的一种指导思想，也是一种用于对大规模数据进行分布式计算的编程模型。

MapReduce 特点：易于编程；良好的扩展性；高容错性；适合海量数据的离线处理。

MapReduce 局限性：实时计算性能差；不能进行流式计算。

## MapReduce 实例进程

一个完整的 MapReduce 程序在分布式运行时有三类：

MRAppMaster：负责整个 MR 程序的过程调度及状态协调

MapTask：负责 map 阶段的整个数据处理流程

ReduceTask：负责 reduce 阶段的整个数据处理流程

## 阶段组成

一个 MapReduce 编程模型中只能包含一个 Map 阶段和一个 Reduce 阶段，或者只有 Map 阶段。
