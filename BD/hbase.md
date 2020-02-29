
## 示例程序运行
    hadoop jar lib/hbase-mapreduce-2.2.2.jar rowcounter weibo
    hadoop jar lib/hbase-mapreduce-2.2.2.jar importtsv -Dimporttsv.columns=HBASE_ROW_KEY,info:name,info:color fruit hdfs://master:9000/fruit.tsv
## 复制数据
    hadoop jar hbase-1.0-SNAPSHOT.jar com.atguigu.mr.FruitDriver fruit fruit1
## 导入数据
    hadoop jar lib/hbase-mapreduce-2.2.2.jar importtsv -Dimporttsv.separator=',' -Dimporttsv.columns=info:name,HBASE_ROW_KEY,info:url,info:focus weibo hdfs://master:9000/hivedatas/test/test.csv
    
    hbase-daemon.sh stop thrift

```xml
   <property>
        <name>hbase.rootdir</name>
        <value>hdfs://master:9000/hbase</value> 
    </property> 
    <property>
        <name>hbase.cluster.distributed</name>
        <value>true</value>
    </property>
    <property>
        <name>hbase.master.port</name> 
        <value>16000</value> 
    </property>
    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>master,slave01,slave02</value>
    </property> 
    <property>
        <name>hbase.zookeeper.property.dataDir</name>
        <value>/home/neu/data/zkdata</value>
    </property> 
    <property>
        <name>hbase.unsafe.stream.capability.enforce</name>
        <value>false</value>
    </property>
```

