### zookeeper群起群关
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

### jps
    #!/bin/bash

    for i in master slave01 slave02
        do
                    echo "************$i************"
                ssh $i 'jps'
        done
