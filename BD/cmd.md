# 一、脚本

+ zookeeper群起群关

    ```shell
    #!/bin/bash
    
    case $1 in
    "start"){
        for i in neu@master neu@slave01 neu@slave02
        do
                echo "************$i************"
                ssh $i 'source /etc/profile && /home/neu/zookeeper/bin/zkServer.sh start'
        done
    };;
    "stop"){
        for i in neu@master neu@slave01 neu@slave02
        do
                echo "************$i************"
                ssh $i 'source /etc/profile && /home/neu/zookeeper/bin/zkServer.sh stop'
        done
    };;
    
    esac
    ```

    

+ jps

    ```shell
    #!/bin/bash
    
    for i in master slave01 slave02
        do
                echo "************$i************"
                ssh $i 'jps'
        done
    ```





# 二、Flume

+ 运行命令

```
 bin/flume-ng agent --conf conf/ --conf-file job/group4/flume.conf --name a1 -Dflume.root.logger=INFO,console
```



# 三、Kafka

+ 创建消费者

	```
	bin/kafka-console-consumer.sh --topic hello --bootstrap-server localhost:9092 --from-beginning
	```

+ 创建topic

	```
	bin/kafka-topics.sh --create --zookeeper master:2181 --topic hello --partitions 2 --replication-factor 2
	```

+ 创建生产者

	```
	bin/kafka-console-producer.sh --topic sensor --broker-list localhost:9092
	```

+ 查看topic

	```
	bin/kafka-topics.sh  --describe --topic second --zookeeper master:2181
	```

+ 删除topic

	```
	bin/kafka-topics.sh --delete --zookeeper master:2181 --topic first
	```

+ 列出所有topic

	```
	bin/kafka-topics.sh --list --zookeeper master:2181
	```

	

# 四、Spark

+ wordcount

	```scala
	sc.textFile("hdfs://master:9000/user/neu/input/wordcount.txt").flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_).collect
	```

	