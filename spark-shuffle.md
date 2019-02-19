



![img](E:\gitProgram\VacationReview\img\20170521190607899.png)



### 运行时三种ShuffleWriter实现的选择

1、没有map端聚合操作，且RDD的Partition数小于200，使用BypassMergeSortShuffleWriter。
2、没有map端聚合操作，RDD的Partition数小于16777216，且Serializer支持relocation，使用UnsafeShuffleWriter。
3、上述条件都不满足，使用SortShuffleWriter。



### 普通运行机制

在该模式下，数据会先写入一个数据结构，聚合算子写入Map，一边通过Map局部聚合，一遍写入内存。Join算子写入ArrayList直接写入内存中。然后需要判断是否达到阈值，如果达到就会将内存数据结构的数据写入到磁盘，清空内存数据结构。

在溢写磁盘前，先根据key进行排序，排序过后的数据，会分批写入到磁盘文件中。默认批次为10000条，数据会以每批一万条写入到磁盘文件。写入磁盘文件通过缓冲区溢写的方式，每次溢写都会产生一个磁盘文件，也就是说一个task过程会产生多个临时文件。

最后在每个task中，将所有的临时文件合并，这就是merge过程，此过程将所有临时文件读取出来，一次写入到最终文件。意味着一个task的所有数据都在这一个文件中。同时单独写一份索引文件，标识下游各个task的数据在文件中的索引，start offset和end offset。

这样算来如果第一个stage 50个task，每个Executor执行一个task，那么无论下游有几个task，就需要50个磁盘文件。 

![](E:\gitProgram\VacationReview\img\20180724232542567.jpg)



### bypass机制

bypass机制运行条件：

- shuffle map task数量小于spark.shuffle.sort.bypassMergeThreshold参数的值。
- 不是聚合类的shuffle算子（比如reduceByKey）。

在这种机制下，当前stage的task会为每个下游的task都创建临时磁盘文件。将数据按照key值进行hash，然后根据hash值，将key写入对应的磁盘文件中（个人觉得这也相当于一次另类的排序，将相同的key放在一起了）。最终，同样会将所有临时文件依次合并成一个磁盘文件，建立索引。

该机制与未优化的hashshuffle相比，没有那么多磁盘文件，下游task的read操作相对性能会更好。

该机制与sortshuffle的普通机制相比，在readtask不多的情况下，首先写的机制是不同，其次不会进行排序。这样就可以节约一部分性能开销。 

![bypass](E:\gitProgram\VacationReview\img\20180724233509220.jpg)