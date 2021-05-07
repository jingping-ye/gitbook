# Kafka的安装

## 1. Kafka的安装 <a id="kafka&#x7684;&#x5B89;&#x88C5;"></a>

### 1.1.1. kafka搭建 <a id="kafka&#x642D;&#x5EFA;"></a>

注意:我的电脑是win10的以下都是基于win10的安装

kafka环境基于zookeeper,zookeeper环境基于JAVA-JDK。

### 1.1.2. 安装JAVA-JDK <a id="&#x5B89;&#x88C5;java-jdk"></a>

下载地址：[https://www.oracle.com/technetwork/java/javase/downloads/jdk12-downloads-5295953.html](https://www.oracle.com/technetwork/java/javase/downloads/jdk12-downloads-5295953.html)

#### 安装JDK <a id="&#x5B89;&#x88C5;jdk"></a>

#### 添加环境变量 <a id="&#x6DFB;&#x52A0;&#x73AF;&#x5883;&#x53D8;&#x91CF;"></a>

注意 : 第二部本来需要安装zookeeper的但是现在kafka上面自带这个了！可以安装可以不安装！我不找刺激就不安装了！

### 1.1.3. 安装kafka <a id="&#x5B89;&#x88C5;kafka"></a>

#### 下载 <a id="&#x4E0B;&#x8F7D;"></a>

下载地址：[http://kafka.apache.org/downloads](http://kafka.apache.org/downloads) 下载kafka\_2.12-2.3.0.tgz。

#### 安装 <a id="&#x5B89;&#x88C5;"></a>

将下载好的压缩包解压到本地即可。

#### 配置 <a id="&#x914D;&#x7F6E;"></a>

```text
    1.打开config目录下的server.properties文件
    2.修改log.dirs=E:\\kafkalogs
    3.打开config目录下的zookeeper.properties文件
    4.修改dataDir=E:\\kafka\\zookeeper
```

#### 启动 <a id="&#x542F;&#x52A8;"></a>

注意 : 记得啊执行cmd记得以管理员的身份启动不然启动不了！两次都需要管理员身份（另外记得走到kafka你解压的目录执行下面的命令啊

```text
    先执行：bin\windows\zookeeper-server-start.bat config\zookeeper.properties

    再执行：bin\windows\kafka-server-start.bat config\server.properties
```

### 1.1.4. 进行单机实例测试简单使用 <a id="&#x8FDB;&#x884C;&#x5355;&#x673A;&#x5B9E;&#x4F8B;&#x6D4B;&#x8BD5;&#x7B80;&#x5355;&#x4F7F;&#x7528;"></a>

windows使用的是路径E:\Kafka\kafka\_2.12-2.0.0\bin\windows下批处理命令，如有问题，参见

#### 步骤： <a id="&#x6B65;&#x9AA4;&#xFF1A;"></a>

#### 1）启动kafka内置的zookeeper <a id="1&#xFF09;&#x542F;&#x52A8;kafka&#x5185;&#x7F6E;&#x7684;zookeeper"></a>

.\bin\windows\zookeeper-server-start.bat .\config\zookeeper.properties

出现binding to port ...表示zookeeper启动成功，不关闭页面

#### 2）kafka服务启动 ，成功不关闭页面 <a id="2&#xFF09;kafka&#x670D;&#x52A1;&#x542F;&#x52A8;-&#xFF0C;&#x6210;&#x529F;&#x4E0D;&#x5173;&#x95ED;&#x9875;&#x9762;"></a>

.\bin\windows\kafka-server-start.bat .\config\server.properties

#### 3）创建topic测试主题kafka，成功不关闭页面 <a id="3&#xFF09;&#x521B;&#x5EFA;topic&#x6D4B;&#x8BD5;&#x4E3B;&#x9898;kafka&#xFF0C;&#x6210;&#x529F;&#x4E0D;&#x5173;&#x95ED;&#x9875;&#x9762;"></a>

.\bin\windows\kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test

#### 4）创建生产者产生消息，不关闭页面 <a id="4&#xFF09;&#x521B;&#x5EFA;&#x751F;&#x4EA7;&#x8005;&#x4EA7;&#x751F;&#x6D88;&#x606F;&#xFF0C;&#x4E0D;&#x5173;&#x95ED;&#x9875;&#x9762;"></a>

.\bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic test

#### 5）创建消费者接收消息，不关闭页面 <a id="5&#xFF09;&#x521B;&#x5EFA;&#x6D88;&#x8D39;&#x8005;&#x63A5;&#x6536;&#x6D88;&#x606F;&#xFF0C;&#x4E0D;&#x5173;&#x95ED;&#x9875;&#x9762;"></a>

.\bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic test --from-beginning

