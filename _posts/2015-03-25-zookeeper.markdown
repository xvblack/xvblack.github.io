---
title: ZooKeeper简介
layout: post
date: March 25, 2015 – 14:48
category: distributed system

---

ZooKeeper 是 Yahoo 公司开发的分布式服务项目，可以提供配置服务，命名服务和注册服务。HBase 正是基于ZooKeeper进行服务的。

ZooKeeper 的数据模型十分简单。其提供类类似于文件系统的目录状结构，每个目录节点称为一个 znode，并且每个 znode 可以储存一些键值对作为 meta info。而对于操作方面，ZooKeeper提供了简单的创建节点，删除节点，原子化的修改节点的元信息等操作，同时允许客户端对特定节点或时间进行监视，并在事件触发时通知客户端。

ZooKeeper节点的一个重要概念，也是节点创建时的选项，便是此节点是永久节点还是临时节点。临时节点与创建此节点的客户端相关，一旦客户端被判定断开连接，此节点便会被删除。除此之外，ZooKeeper还允许客户端声明创建节点时由服务器指定一个此目录下唯一的单调增id附加在新节点信息之中。

ZooKeeper的所有读服务是各个服务器独立进行的。服务器会定时收到来自Leader服务器的更新，所以各个服务器可能处于不同的状态。但ZooKeeper保证这些状态是一致的，所有的写操作操作会有序的进行，而任意一个服务器只能处于某个写操作完成的状态，并且包含所有此操作之前的写操作。

ZooKeeper的所有写服务都通过唯一的Leader服务器进行，并且保证所有操作的原子性。Leader服务器会通过简单地类似于Two-phase Commit的协议进行操作。当其收到超过Quorum，亦即超过半数的服务的确认(Acknowledgement)之后，便会要求所有服务器对此信息Commit。当Commit完成后，便会通知对应的服务器，进而通知对应的客户端此写操作已完成。

每一个操作会被唯一的指定一个zxid。zxid由两部分组成：(epoch, counter)。epoch唯一的代表这个Leader，亦即当且仅当每次新的Leader被选出，此epoch会被更新（+1）。counter代表这个Leader处理的所有操作的一个唯一的id。

ZooKeeper的服务器通过Leader Selection避免Single Node Failure。每次Leader节点出现错误时，ZooKeeper会选择所有服务内版本最新的服务器作为新的Leader，并且在Leader同步所有Follower之前暂停服务。