    bin/flume-ng agent --conf conf/ --conf-file job/netcat-flume-logger.conf --name a1 -Dflume.root.logger=INFO,console
    
    bin/flume-ng agent -n a1 -c conf -f job/files-flume-logger.conf -Dflume.root.logger=INFO,console

    bin/flume-ng agent --conf conf/ --conf-file job/group2/flume1.conf --name a1