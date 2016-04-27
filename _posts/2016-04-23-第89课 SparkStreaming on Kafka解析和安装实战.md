本课是由**DT大数据梦工厂**推出的IMF传奇行动**Spark**课程的第89讲，本课分两部分讲解：
（1）Kafka的概念、架构和用例场景；
（2）Kafka的安装和实战。
由于时间关系，本课程只讲到如何用官网的例子验证Kafka的安装是否成功。后续课程会接着讲解如何集成Spark Streaming和Kafka。

一、Kafka的概念、架构和用例场景
------------------

**1.Kafka的概念**

Apache Kafka是分布式发布-订阅消息系统。它最初由LinkedIn公司开发，之后成为Apache项目的一部分。Kafka是一种快速、可扩展的、设计内在就是分布式的，分区的和可复制的提交日志服务。

**什么是消息组件？**
以帅哥和美女聊天为例，帅哥如何和美女交流呢？这中间通常想到的是微信、QQ、电话、邮件等通信媒介，这些通信媒介就是消息组件，帅哥把聊天信息发送给消息组件、消息组件将消息推送给美女，这就是常说的生产者、消费者模型。而且在发送信息时可以将内容进行分类，即所谓的Topic主题。Kafka就是这样的通信组件，将不同对象组件粘合起来的纽带，且是解耦合方式传递数据。

**Apache Kafka与传统消息系统相比，有以下不同的特点：**
•	分布式系统，易于向外扩展；
•	在线低延迟，同时为发布和订阅提供高吞吐量；
•	将消息存储到磁盘，因此可以处理1天甚至1周前内容。

**2.Kafka的架构**

![这里写图片描述](http://img.blog.csdn.net/20160426180937393)

Kafka既然具备消息系统的基本功能，那么就必然会有组成消息系统的组件：Topic，Producer和Consumer。Kafka还有其特殊的Kafka Cluster组件。

-	**Topic(主题)**：代表一种数据的类别或类型，工作、娱乐、生活有不同的Topic，生产者需要说明把说明数据分别放在那些Topic中，里面就是一个个小对象，并将数据数据推到Kafka，消费者获取数据是pull的过程。一组相同类型的消息数据流。这些消息在Kafka会被分区存放，并且有多个副本，以防数据丢失。每个分区的消息是顺序写入的，并且不可改写。

![这里写图片描述](http://img.blog.csdn.net/20160426181101379)


-	**Producer（生产者）**：把数据推到Kafka系统的任何对象。

-	**Kafka Cluster（Kafka集群）**：把推到Kafka系统的消息保存起来的一组服务器，也叫Broker。因为Kafka集群用到了Zookeeper作为底层支持框架，所以由一个选出的服务器作为Leader来处理所有消息的读和写的请求，其他服务器作为Follower接受Leader的广播同步备份数据，以备灾难恢复时用。

-	**Consumer（消费者）**：从Kafka系统订阅消息的任何对象。
消费者可以有多个，并且某些消费者还可以组成Consumer Group。多个Consumer Group之间组成消息广播的关系，所以各个Group可以拉相同的消息数据。在Consumer Group内部，各消费者之间对Consumer Group拉出来的消息数据是队列先进先出的关系，某个消息数据只能给该Group的一个消费者使用。

![这里写图片描述](http://img.blog.csdn.net/20160426181210291)

数据传输基于kernel（内核）级别的（传输速度接近0拷贝-ZeroCopy）、没有用户空间的参与。Linux本身是软件，软件启动时第一个启动进程叫init，在init进程启动后会进入用户空间；例如：在分布式系统中，机器A上的应用程序需要读取机器B上的Java服务数据，由于Java程序对应的JVM是用户空间级别而且数据在磁盘上，A上应用程序读取数据时会首先进入机器B上的内核空间再进入机器B的用户空间，读取用户空间的数据后，数据再经过B机器上的内核空间分发到网络中，机器A网卡接收到传输过来的数据后再将数据写入A机器的内核空间，从而最终将数据传输给A的用户空间进行处理。如下图：

![这里写图片描述](http://img.blog.csdn.net/20160426181244460)

外部系统从Java程序中读取数据，传输给内核空间并依赖网卡将数据写入到网络中，从而把数据传输出去。其实Java本身是内核的一层外衣，Java Socket编程，操作的各种数据都是在JVM的用户空间中进行的。而Kafka操作数据是放在内核空间的，通常内核空间处理数据的速度比用户空间快上万倍，所以通过kafka可以实现高速读、写数据。

**3、Kafka的用例场景**

类似微信，手机和邮箱等等这样大家熟悉的消息组件，Kafka也可以：
•	支持文字/图片
•	可以存储内容
•	分门别类
从内容消费的角度，Kafka把邮箱中的邮件看成是Topic。


二、Kafka的安装和实战
-------------
下载地址为：http://kafka.apache.org/documentation.html#quickstart
**1、安装和配置Zookeeper**
Kafka集群模式需要提前安装好Zookeeper。
**提示**：Kafka单例模式不需要安装额外的Zookeeper，可以使用内置的Zookeeper。Kafka集群模式需要至少3台服务器。本课实战用到的服务器Hostname：master，slave1，slave2。本课中用到的Zookeeper版本是Zookeeper-3.4.6。

1)	下载Zookeeper

进入http://www.apache.org/dyn/closer.cgi/zookeeper/，你可以选择其他镜像网址去下载，用官网推荐的镜像：http://mirror.bit.edu.cn/apache/zookeeper/。提示：可以直接下载群里的Zookeeper安装文件。

![这里写图片描述](http://img.blog.csdn.net/20160426181714981)

![这里写图片描述](http://img.blog.csdn.net/20160426181726795)

下载zookeeper-3.4.6.tar.gz：

![这里写图片描述](http://img.blog.csdn.net/20160426181747293)

2)	安装Zookeeper
**提示**：下面的步骤发生在master服务器。
以ubuntu14.04举例，把下载好的文件放到/root目录，用下面的命令解压：

```
cd /root
tar -zxvf zookeeper-3.4.6.tar.gz
```

解压后在/root目录会多出一个zookeeper-3.4.6的新目录，用下面的命令把它剪切到指定目录即安装好Zookeeper了：

```
cd /root
mv zookeeper-3.4.6 /usr/local/spark
```

之后在/usr/local/spark目录会多出一个zookeeper-3.4.6的新目录。下面我们讲如何配置安装好的Zookeeper。

3)	配置Zookeeper
提示：下面的步骤发生在master服务器。
a.	配置.bashrc
打开文件：vi /root/.bashrc
在PATH配置行前添加：

```
export ZOOKEEPER_HOME=/usr/local/spark/zookeeper-3.4.6
```

最后修改PATH：

```
export PATH=${JAVA_HOME}/bin:${ZOOKEEPER_HOME}/bin:${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin:${SCALA_HOME}/bin:${SPARK_HOME}/bin:${SPARK_HOME}/sbin:${HIVE_HOME}/bin:${KAFKA_HOME}/bin:$PATH
```

使配置的环境变量立即生效：

```
source /root/.bashrc
```

b. 创建data目录

	

```
cd $ZOOKEEPER_HOME
mkdir data
```

c.	创建并打开zoo.cfg文件

```
cd $ZOOKEEPER_HOME/conf
cp zoo_sample.cfg zoo.cfg
vi zoo.cfg
```

d.	配置zoo.cfg

配置Zookeeper的日志和服务器身份证号等数据存放的目录。 千万不要用默认的/tmp/zookeeper目录，因为/tmp目录的数据容易被意外删除。 dataDir=../data Zookeeper与客户端连接的端口 clientPort=2181 #在文件最后新增3行配置每个服务器的2个重要端口：Leader端口和选举端口 server.A=B：C：D：其中 A 是一个数字，表示这个是第几号服务器； B 是这个服务器的hostname或ip地址；  C 表示的是这个服务器与集群中的 Leader 服务器交换信息的端口； D 表示的是万一集群中的 Leader 服务器挂了，需要一个端口来重新进行选举， #选出一个新的 Leader，而这个端口就是用来执行选举时服务器相互通信的端口。  如果是伪集群的配置方式，由于 B 都是一样，所以不同的 Zookeeper 实例通信  端口号不能一样，所以要给它们分配不同的端口号。

```
server.1=master:2888:3888
server.2=slave1:2888:3888
server.3=slave2:2888:3888
```

e.	创建并打开myid文件

```
cd $ZOOKEEPER_HOME/data
touch myid
vi myid
```

f.	配置myid
按照zoo.cfg的配置，myid的内容就是1。

4)	同步master的安装和配置到slave1和slave2，在master服务器上运行下面的命令

```
cd /root
scp ./.bashrc root@slave1:/root
scp ./.bashrc root@slave2:/root
cd /usr/local/spark
scp -r ./zookeeper-3.4.6 root@slave1:/usr/local/spark
scp -r ./zookeeper-3.4.6 root@slave2:/usr/local/spark
```

在slave1服务器上运行下面的命令

```
vi $ZOOKEEPER_HOME/data/myid
```

按照zoo.cfg的配置，myid的内容就是2。
在slave2服务器上运行下面的命令

```
vi $ZOOKEEPER_HOME/data/myid
```

按照zoo.cfg的配置，myid的内容就是3。

5)	启动Zookeeper服务
在master服务器上运行下面的命令

```
zkServer.sh start
```

在slave1服务器上运行下面的命令

```
source /root/.bashrc
zkServer.sh start
```

在slave1服务器上运行下面的命令

```
source /root/.bashrc
zkServer.sh start
```

6)	验证Zookeeper是否安装和启动成功
在master服务器上运行命令：jps和zkServer.sh status

```
root@master:/usr/local/spark/zookeeper-3.4.6/bin# jps
3844 QuorumPeerMain
4790 Jps
zkServer.sh status
root@master:/usr/local/spark/zookeeper-3.4.6/bin# zkServer.sh status
JMX enabled by default
Using config: /usr/local/spark/zookeeper-3.4.6/bin/../conf/zoo.cfg
Mode: follower
```

在slave1服务器上运行命令：jps和zkServer.sh status

```
source /root/.bashrc
root@slave1:/usr/local/spark/zookeeper-3.4.6/bin# jps
3462 QuorumPeerMain
4313 Jps
root@slave1:/usr/local/spark/zookeeper-3.4.6/bin# zkServer.sh status
JMX enabled by default
Using config: /usr/local/spark/zookeeper-3.4.6/bin/../conf/zoo.cfg
Mode: follower
```

在slave2服务器上运行命令：jps和zkServer.sh status

```
root@slave2:/usr/local/spark/zookeeper-3.4.6/bin# jps
4073 Jps
3277 QuorumPeerMain
root@slave2:/usr/local/spark/zookeeper-3.4.6/bin# zkServer.sh status
JMX enabled by default
Using config: /usr/local/spark/zookeeper-3.4.6/bin/../conf/zoo.cfg
Mode: leader
```
至此，代表Zookeeper已经安装和配置成功。

**2、安装和配置Kafka**

本课中用到的Kafka版本是Kafka-2.10-0.9.0.1。
1)	下载Kafka
进入http://kafka.apache.org/downloads.html，左键单击kafka_2.10-0.9.0.1.tgz。提示：可以直接下载群里的Kafka安装文件。

![这里写图片描述](http://img.blog.csdn.net/20160426182359352)

下载kafka_2.10-0.9.0.1.tgz

![这里写图片描述](http://img.blog.csdn.net/20160426182418843)

2)	安装Kafka
**提示**：下面的步骤发生在master服务器。
以ubuntu14.04举例，把下载好的文件放到/root目录，用下面的命令解压：

```
cd /root
tar -zxvf kafka_2.10-0.9.0.1.tgz
```

解压后在/root目录会多出一个kafka_2.10-0.9.0.1的新目录，用下面的命令把它剪切到指定目录即安装好Kafka了：

```
cd /root
mv kafka_2.10-0.9.0.1 /usr/local
```

之后在/usr/local目录会多出一个kafka_2.10-0.9.0.1的新目录。下面我们讲如何配置安装好的Kafka。

3)	配置Kafka
**提示**：下面的步骤发生在master服务器。
a.	配置.bashrc
打开文件：vi /root/.bashrc
在PATH配置行前添加：

```
export KAFKA_HOME=/usr/local/kafka_2.10-0.9.0.1
```

最后修改PATH：
export PATH=${JAVA_HOME}/bin:${ZOOKEEPER_HOME}/bin:${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin:${SCALA_HOME}/bin:${SPARK_HOME}/bin:${SPARK_HOME}/sbin:${HIVE_HOME}/bin:${KAFKA_HOME}/bin:$PATH
-	使配置的环境变量立即生效：source /root/.bashrc
b.	打开server.properties
-	cd $ZOOKEEPER_HOME/config
-	vi server.properties
c.	配置server.properties
broker.id=0
port=9092
zookeeper.connect=master:2181,slave1:2181,slave2:2181
4)	同步master的安装和配置到slave1和slave2，在master服务器上运行下面的命令

```
cd /root
scp ./.bashrc root@slave1:/root
scp ./.bashrc root@slave2:/root
cd /usr/local
scp -r ./kafka_2.10-0.9.0.1 root@slave1:/usr/local
scp -r ./kafka_2.10-0.9.0.1 root@slave2:/usr/local
```

在slave1服务器上运行下面的命令

```
vi $KAFKA_HOME/config/server.properties
```

修改broker.id=1。
在slave2服务器上运行下面的命令

```
vi $KAFKA_HOME/config/server.properties
```

修改broker.id=2。
5)	启动Kafka服务
在master服务器上运行下面的命令

```
cd $KAFKA_HOME/bin
kafka-server-start.sh ../config/server.properties &
```

在slave1服务器上运行下面的命令

```
source /root/.bashrc
cd $KAFKA_HOME/bin
kafka-server-start.sh ../config/server.properties &
```

在slave2服务器上运行下面的命令

```
source /root/.bashrc
cd $KAFKA_HOME/bin
kafka-server-start.sh ../config/server.properties &
```

6)	验证Kafka是否安装和启动成功
在任意服务器上运行命令创建Topic“HelloKafka”：

```
kafka-topics.sh --create --zookeeper master:2181,slave1:2181,slave2:2181 --replication-factor 3 --partitions 1 --topic HelloKafka
```

在任意服务器上运行命令为创建的Topic“HelloKafka”生产一些消息：

```
kafka-console-producer.sh --broker-list master:9092,slave1:9092,slave2:9092 --topic HelloKafka
```

输入下面的消息内容：

```
This is DT_Spark!
I’m Rocky!
Life is short, you need Spark!
```

在任意服务器上运行命令从指定的Topic“HelloKafka”上消费（拉取）消息：

```
kafka-console-consumer.sh --zookeeper master:2181,slave1:2181,slave2:2181 --from-beginning --topic HelloKafka
```

过一会儿，你会看到打印的消息内容：

```
This is DT_Spark!
I’m Rocky!
Life is short, you need Spark!
```

在任意服务器上运行命令查看所有的Topic名字：

```
kafka-topics.sh --list --zookeeper master:2181,slave1:2181,slave2:2181
```

在任意服务器上运行命令查看指定Topic的概况：

```
kafka-topics.sh --describe --zookeepermaster:2181,slave1:2181,slave2:2181 --topic HelloKafka
```

至此，代表Kafka已经安装和配置成功！

**课程笔记来源:** DT大数据梦工厂IMF传奇行动课程学员整理。YY直播永久课堂频道68917580每晚8点准时开课!
**主编辑**：王家林
**编写人**：周飞












