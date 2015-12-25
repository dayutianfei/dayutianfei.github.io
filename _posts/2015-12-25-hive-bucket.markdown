---
layout: post
title: Hive中关于hash分区的使用
date: 2015-12-25
comments: false
tags: hive
archive: false
---
先看一下Hive的建表语法，详细参考[网址](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL)

{% highlight sql %}

CREATE [TEMPORARY] [EXTERNAL] TABLE [IF NOT EXISTS] [db_name.]table_name 
  [(col_name data_type [COMMENT col_comment], ...)]
  [COMMENT table_comment]
  [PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)]
  [CLUSTERED BY (col_name, col_name, ...) [SORTED BY (col_name [ASC|DESC], ...)]
     INTO num_buckets BUCKETS]
  [SKEWED BY (col_name, col_name, ...)]
     ON ((col_value, col_value, ...), (col_value, col_value, ...), ...)
     [STORED AS DIRECTORIES]
  [
   [ROW FORMAT row_format] 
   [STORED AS file_format]
     | STORED BY 'storage.handler.class.name' [WITH SERDEPROPERTIES (...)]
  ]
  [LOCATION hdfs_path]
  [TBLPROPERTIES (property_name=property_value, ...)]
  [AS select_statement];

{% endhighlight %}

对于Hive而言，除了使用Partition进行分区控制之外，还可以针对某个或某些个字段进一步的细化，即Hash方式分桶。

分桶的好处有：
> * 加快数据提取速度，sampling；
> * 提升查询效率，如mapside join；

但是，需要注意：clustered by 和 sorted by 不会影响数据的导入，需要用户自己负责导入的数据的正确性。

另外，``'set hive.enforce.bucketing = true'`` 可以自动控制上一轮reduce的数量从而适配bucket的个数；用户也可以自主设置``mapred.reduce.tasks``去适配bucket个数，推荐使用``'set hive.enforce.bucketing = true'``。
