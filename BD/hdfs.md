## 5.1 NN和2NN工作机制

​		思考:NameNode 中的元数据是存储在哪里的？

​         首先，我们做个假设，如果存储在NameNode节点的磁盘中，因为经常需要进行随机访问，还有响应客户请求，必然是效率过低。因此，元数据需要存放在内存中。但如果只存在内存中，一旦断电，元数据丢失，整个集群就无法工作了。因此产生在磁盘中备份元数据的FsImage。

​		这样又会带来新的问题，当在内存中的元数据更新时，如果同时更新FsImage，就会导致效率过低，但如果不更新，就会发生一致性问题，一旦NameNode节点断电，就会产生数据丢失。因此，引入Edits文件(只进行追加操作，效率很高)。每当元数据有更新或者添加元数据时，修改内存中的元数据并追加到Edits中。这样，一旦NameNode节点断电，可以通过FsImage和Edits的合并，合成元数据。

​		但是，如果长时间添加数据到Edits中，会导致该文件数据过大，效率降低，而且一旦断电，恢复元数据需要的时间过长。因此，需要定期进行FsImage和Edits的合并，如果这个操作由NameNode节点完成，又会效率过低。因此，引入一个新的节点SecondaryNamenode，专门用于FsImage和Edits的合并。

NN和2NN工作机制，如图3-14所示。         

1. 第一阶段：NameNode启动

	（1）第一次启动NameNode格式化后，创建Fsimage和Edits文件。如果不是第一次启动，直接加载编辑日志和镜像文件到内存。

	（2）客户端对元数据进行增删改的请求。

	（3）NameNode记录操作日志，更新滚动日志。

	（4）NameNode在内存中对数据进行增删改。

2. 第二阶段：Secondary NameNode工作

​    （1）Secondary NameNode询问NameNode是否需要CheckPoint。直接带回NameNode是否检查结果。

​    （2）Secondary NameNode请求执行CheckPoint。

​    （3）NameNode滚动正在写的Edits日志。

​    （4）将滚动前的编辑日志和镜像文件拷贝到Secondary NameNode。

​    （5）Secondary NameNode加载编辑日志和镜像文件到内存，并合并。

​    （6）生成新的镜像文件fsimage.chkpoint。

​    （7）拷贝fsimage.chkpoint到NameNode。

​    （8）NameNode将fsimage.chkpoint重新命名成fsimage。

工作机制详解： 

Fsimage：NameNode内存中元数据序列化后形成的文件。 

 Edits：记录客户端更新元数据信息的每一步操作（可通过Edits运算出元数据）。 

​		NameNode启动时，先滚动Edits并生成一个空的edits.inprogress，然后加载Edits和Fsimage到内存中，此时NameNode内存就持有最新的元数据信息。Client开始对NameNode发送元数据的增删改的请求，这些请求的操作首先会被记录到edits.inprogress中（查询元数据的操作不会被记录在Edits中，因为查询操作不会更改元数据信息），如果此时NameNode挂掉，重启后会从Edits中读取元数据的信息。

​		然后，NameNode会在内存中执行元数据的增删改的操作。  由于Edits中记录的操作会越来越多，Edits文件越来越大，导致NameNode启动加载Edits时会很慢，所以需要对Edits和Fsimage进行合并（合并就是将Edits和Fsimage加载到内存中，照着Edits中的操作一步步执行，最终形成新的Fsimage。SecondaryNameNode的作用就是帮助NameNode进行Edits和Fsimage的合并工作。 

​		 SecondaryNameNode首先会询问NameNode是否需要CheckPoint（触发CheckPoint需满足两个条件中的任意一个，定时时间到和Edits中写满了）。直接带回NameNode是否检查结果。SecondaryNameNode执行CheckPoint操作，首先会让NameNode滚动Edits并生成一个空的edits.inprogress，滚动Edits的目的是给Edits打个标记，以后所有新操作都写入edits.inprogress，其他未合并的Edits和Fsimage会拷贝到SecondaryNameNode的本地，然后将拷贝的Edits和Fsimage加载到内存中进行合并，生成fsimage.chkpoint，然后将fsimage.chkpoint拷贝给NameNode，重命名为Fsimage后替换掉原来的Fsimage。NameNode在启动时就只需要加载之前未合并的Edits和Fsimage即可，因为合并过的Edits中的元数据信息已经被记录在Fsimage中。  