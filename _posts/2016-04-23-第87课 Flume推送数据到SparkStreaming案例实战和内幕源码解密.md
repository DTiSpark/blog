本课是由DT大数据梦工厂推出的IMF传奇行动Spark课程的第84课，本期内容主要有：
（1）StreamingContext功能及源码剖析；
（2）DStream功能及源码剖析；
（3）Receiver功能及源码剖析；
（4）StreamingContext、DStream、Receiver流程分析。

一、StreamingContext功能及源码剖析
-------------------------
1、通过Spark Streaming对象jssc，创建应用程序主入口，并连上Driver上的接收数据服务端口9999写入源数据。

![这里写图片描述](http://img.blog.csdn.net/20160425175627434)

2、	Spark Streaming的主要功能有：主程序的入口提供了各种创建DStream的方法接收各种流入的数据源（例如：Kafka、Flume、Twitter、ZeroMQ和简单的TCP套接字等），通过构造函数实例化Spark Streaming对象时，可以指定master URL、appName、或者传入SparkConf配置对象、或者已经创建的SparkContext对象；
将接收的数据流传入DStreams对象中；通过Spark Streaming对象实例的start方法启动当前应用程序的流计算框架或通过stop方法结束当前应用程序的流计算框架。

![这里写图片描述](http://img.blog.csdn.net/20160425175905397)


二、DStream功能及源码剖析
----------------
1、DStream是RDD的模板，DStream是抽象的，RDD也是抽象。
2、DStream的具体实现子类如下图所示：

![这里写图片描述](http://img.blog.csdn.net/20160425180256758)

3、以StreamingContext实例的socketTextSteam方法为例，其执行完的结果返回DStream对象实例，其源码调用过程如下图：

![这里写图片描述](http://img.blog.csdn.net/20160425180320240)

![这里写图片描述](http://img.blog.csdn.net/20160425180552634)

![这里写图片描述](http://img.blog.csdn.net/20160425180602416)

![这里写图片描述](http://img.blog.csdn.net/20160425180613587)

![这里写图片描述](http://img.blog.csdn.net/20160425180631369)

![这里写图片描述](http://img.blog.csdn.net/20160425180646009)

socket.getInputStream获取数据，while循环来存储储蓄数据(内存、磁盘)。

三、 Receiver功能及源码剖析
------------------
1、Receiver代表数据的输入，接收外部输入的数据，如从Kafka上抓取数据；
2、Receiver运行在Worker节点上；
3、Receiver在Worker节点上抓取Kafka分布式消息框架上的数据时，具体实现类是KafkaReceiver；
4、Receiver是抽象类，其抓取数据的实现子类如下图所示：

![这里写图片描述](http://img.blog.csdn.net/20160425180816200)

5、如果上述实现类都满足不了您的要求，您自己可以定义Receiver类，只需要继承Receiver抽象类来实现自己子类的业务需求。

四、 StreamingContext、DStream、Receiver结合流程分析
------------------------------------------
![这里写图片描述](http://img.blog.csdn.net/20160425180848572)

（1）inputStream代表了数据输入流(如：Socket、Kafka、Flume等)
（2）Transformation代表了对数据的一系列操作，如flatMap、map等
（3）outputStream代表了数据的输出，例如wordCount中的println方法：

![这里写图片描述](http://img.blog.csdn.net/20160425180923791)

![这里写图片描述](http://img.blog.csdn.net/20160425180937749)

![这里写图片描述](http://img.blog.csdn.net/20160425180950671)

数据数据在流进来之后最终会生成Job，最终还是基于Spark Core的RDD进行执行：在处理流进来的数据时是DStream进行Transformation由于是StreamingContext所以根本不会去运行，StreamingContext会根据Transformation生成”DStream的链条”及DStreamGraph，而DStreamGraph就是DAG的模板，这个模板是被框架托管的。当我们指定时间间隔的时候，Driver端就会根据这个时间间隔来触发Job而触发Job的方法就是根据OutputDStream中指定的具体的function,例如wordcount中print，这个函数一定会传给ForEachDStream，它会把函数交给最后一个DStream产生的RDD，也就是RDD的print操作，而这个操作就是RDD触发Action。

总结：
使用Spark Streaming可以处理各种数据来源类型，如：数据库、HDFS，服务器log日志、网络流，其强大超越了你想象不到的场景，只是很多时候大家不会用，其真正原因是对Spark、spark streaming本身不了解。

**课程笔记来源:** DT大数据梦工厂IMF传奇行动课程学员整理。YY直播永久课堂频道68917580每晚8点准时开课!
**主编辑：**王家林
**编写人：**姜伟、唐陈昊、龚湄燕及其IMF-Spark Steaming企业级开发实战小组

![这里写图片描述](http://img.blog.csdn.net/20160425181120901)










