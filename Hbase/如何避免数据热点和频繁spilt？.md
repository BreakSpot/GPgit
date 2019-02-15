# 如何避免数据热点和频繁spilt？

写热点，我们总是往最大start-key的region写记录，之前分裂出来的region不会再被写数据，有点被打进冷宫的赶脚，它们都处于半满状态，这样的分布也是不利的，没有显示分布式并发的优势。

写比较频率的场景下，数据增长快，split的次数也会增多，split是比较耗时耗资源的，并且split会使server有一段时间的停顿，影响性能，分的区不是我们想要的。

随机散列与预分区。二者结合起来，是比较完美的，预分区一开始就预建好了一部分region,这些region都维护着自已的start-end keys，再配合上随机散列，写数据能均等地命中这些预建的region，就能解决上面的那些缺点，大大地提高了性能。

- 随机散列：1、时间戳高位散列占位；

​	2、hash：rowkey转为hash再转为bytes，加上本身id 转为bytes,组成rowkey；

​	3、partition：rowkey=id取模+rowkey。

- 预分区：

```shell
create 'testtable', 'common', 'data', {SPLITS => ['1','2','3']}
```

设置3个数，将表分为4个region:

startrowkey和endrowkey为 **(-∞, 1),  [1, 2),  [2,3),  [3,+∞)**

- 设置监控，分散集中，手动分裂

  监控每个region的大小

  在预分区的多个region中，分散储存，不要太均匀，几个处于快满的状态，准备分裂，其他处于半满状态，不会太影响整体性能

  ```
  split 'forced_table', 'b' //其中forced_table 为要split的table , ‘b’ 为split点
  ```

  ​	